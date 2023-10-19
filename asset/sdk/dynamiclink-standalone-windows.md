---
description: Unity에서 Standalone Windows 딥링크를 지원하지 않기 때문에 외부 에셋을 사용하고자 합니다.
---

# DeepLink - Standalone Windows

## Asset Import 및 Setting

* <mark style="color:red;">Build Settings - Player Settings - Other Settings - Configuration 설정 변경 필수</mark>

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

* 해당 Asset을 import 후 DeepLinkingForWindows/Example.cs에서 발생하는 KeyValue 에러 해결법

1. using System.Linq; 추가
2. foreach문 아래와 같이 변경

```csharp
foreach ((string key, string value) in query.Select(x => (x.Key, x.Value)))
{
    Debug.Log($"{key}={value}");
}
```

## 초기화&#x20;

Start()에서 urischeme 초기화와 Deep Link가 활성화 됐을 때 사용할 이벤트를 등록한다.

```csharp
// Assets\_DEV\Script\MobileShare\DynamicLinkSetting.cs
public void Start()
{
#if UNITY_STANDALONE_WIN || UNITY_EDITOR_WIN
            WindowsDeepLinking.Initialize("arzmetadeeplink");
            WindowsDeepLinking.deepLinkActivated += OnPCDynamicLinkReceived;

            Debug.Log("PC DynamicLinkSetting Initialized.");
            initialized = true;
#endif
        }
```

프론트엔드에서 "arzmeta" scheme를 주로 사용하여 충돌이 있어 딥링크 관련 scheme는 "arzmetadeeplink"로 설정하였습니다.( PC버전 로그인 시, url을 오픈하면서 cmd.exe가 실행되어 다이나믹 링크가 수신되는 문제가 있었음)

{% hint style="warning" %}
원활한 딥링크 수신을 위해서는 윈도우 키  + R -> regedit 입력 후 확인 -> 컴퓨터\HKEY\_CLASSES\_ROOT\ 하위에 arzmeta 폴더가 있으면 삭제부탁드립니다.
{% endhint %}

WindowsDeepLinking.Initialize("urischeme")에서 레지스트리 편집기에 설정한 urischeme와 Standalone Windows 앱 실행파일을 연결짓는다.&#x20;

<figure><img src="../../.gitbook/assets/image (46).png" alt=""><figcaption><p>지정한scheme가 레지스트리 편집기에 등록된 모습</p></figcaption></figure>

## Deep Link 생성

위 초기화 단계에서 설정한 uri scheme과 동일하게 scheme를 설정해야 합니다. Deep Link에 해당 scheme가 초기화 단계에서 등록된 uri scheme와 동일하면 해당 scheme에 연결된 Standalone Windows 앱 실행 파일을 실행시키는 방식입니다.

<pre class="language-csharp"><code class="lang-csharp">// Assets\_DEV\Script\MobileShare\CreateShareLink.cs
public static void CreateLink(ShareLinkInfo info, NativeShare.ShareResultCallback callback = null)
{
    string tempUrl, finalUrl, content;

    // content에 들어갈 레이아웃과 데이터 세팅
    ContentData contentData = SetContentData(info);

    try
    {
#if UNITY_EDITOR || UNITY_STANDALONE
        // DynamicLinkSetting에서 설정한 urischeme과 동일하게 scheme을 설정해야 합니다.
        tempUrl = WebUtility.UrlDecode($"arzmetadeeplink:/{contentData.parameter}");
        // {Single.Web.HomepageUrl}/<a data-footnote-ref href="#user-content-fn-1">linkType</a>/{roomCode} 형태의 url로 변환 (자체 API)
        finalUrl = ShortenURL(SetLinkType(info.<a data-footnote-ref href="#user-content-fn-2">roomType</a>), info.roomCode, tempUrl);
    }
    catch (Exception e)
    {
        Debug.LogError(e.Message);
        finalUrl = "https://arzmeta.page.link/welcome";
    }

    // 레이아웃에 필요한 데이터 세팅하기
    if (info.roomType == SHARELINK_TYPE.MYROOM_ENTER)
        content = string.Format(contentData.layout.mainLayout, info.roomName, finalUrl);
    else
        content = string.Format(contentData.layout.mainLayout, info.nickName, info.roomName, info.description,
                                    contentData.topic, info.startTime, info.roomCode, contentData.passwordField, finalUrl);

    Debug.Log(content);
#if UNITY_EDITOR || UNITY_STANDALONE
    SceneLogic.instance.OpenToast&#x3C;Toast_Basic>()
            .ChainToastData(new ToastData_Basic(TOASTICON.Current, new MasterLocalData("office_reception_Invitation")));
    Util.CopyToClipboard(content);
#endif
}
</code></pre>

실제 url 동작은 tempUrl 링크로 동작하지만 사용자에게 공유되는 보여지는 url은 finalUrl 링크이고, 특정 scheme(arzmetadeeplink)로 생성한 링크가 실제 링크처럼 동작할 수 있게 자체 API로 변환해줍니다.

CreateShareLink.cs에서 공유 목적(roomType : MYROOM\_ENTER, OFFICE\_ENTER, OFFICE\_INFO)에 따라 공유 링크 레이아웃을 마스터테이블로부터 받아와서(SetShareLayout()) 각 레이아웃에 필요한 데이터를 세팅(SetContentData())합니다.

세팅된 공유 링크 내용을 클립보드에 복사하여 원하는 곳에 공유합니다.

## Deep Link 수신 이벤트 함수 작성

DeepLink가 수신되었을 때, uri를 파라미터로 갖는 이벤트 함수를 작성합니다. 수신 uri에는 수신 이후 로직 구현을 위한 Query를 포함하고 있어 Dictionary 형태로 파싱하여 사용합니다.

```csharp
// Assets\_DEV\Script\MobileShare\DynamicLinkSetting.cs
private void OnPCDynamicLinkReceived(string uri)
        {
            var url = Uri.UnescapeDataString(uri);
            Debug.Log($"[Received PC Dynamic Link] Absolute Uri: {url}");

            if (string.IsNullOrEmpty(url))
            {
                Debug.Log("질의가 없습니다.");
            }
            else
            {
                Dictionary<string, string> query = ParseQueryString(url);

                foreach ((string key, string value) in query.Select(x => (x.Key, x.Value)))
                {
                    Debug.Log($"{key}={value}");
                }

                // 로직 실행을 위해 Assets\_DEV\Script\MobileShare\JoinShareLink.cs의 OnParamReceived() 이벤트 호출
                // 해당 이벤트 초기화는 Assets\_DEV\Script\MobileShare\DynamicLinkSetting.cs의Awake()에서 해줌
                onParam?.Invoke(query);
            }
        }
```



[^1]: 1 : MYROOM,

    2 : OFFICE

[^2]: OFFIICE\_INFO,&#x20;

    OFFICE\_ENTER,

    &#x20;MYROOM\_ENTER,

    &#x20; &#x20;
