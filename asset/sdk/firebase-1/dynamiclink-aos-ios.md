---
description: >-
  2025년 8월 25일에 해당 서비스가
  종료됨.(https://firebase.google.com/support/dynamic-links-faq?hl=ko)
---

# DynamicLink - AOS, iOS

> Firebase DynamicLink는 [변고경 연구원의 인수인계자료](../../../undefined/undefined.md) 5번 참고부탁드립니다.

## Firebase DynamicLinks 생성

* GitHub 링크: [https://github.com/firebase/firebase-unity-sdk/releases/tag/v10.3.0](https://github.com/firebase/firebase-unity-sdk/releases/tag/v10.3.0)
* [Unity로 동적 링크 만들기  |  Firebase Dynamic Links (google.com)](https://firebase.google.com/docs/dynamic-links/unity/create?hl=ko)
* [Unity로 동적 링크 수신  |  Firebase Dynamic Links (google.com)](https://firebase.google.com/docs/dynamic-links/unity/receive?hl=ko)

```csharp
// Assets\_DEV\Script\MobileShare\CreateShareLink.cs
public static void CreateLink(ShareLinkInfo info, NativeShare.ShareResultCallback callback = null)
{
        string tempUrl, finalUrl, content;

        // content에 들어갈 레이아웃과 데이터 세팅
        ContentData contentData = SetContentData(info);

        try
        {
            // 다이나믹 링크 생성(longUrl)해서 짧은 링크(finalUrl)로 변환
            tempUrl = Single.DynamicLink.CreateDynamicLinkWithParam(contentData.parameter);
            finalUrl = ShortenURL(SetLinkType(info.roomType), info.roomCode, tempUrl);
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
        
        // 생성된 다이나믹 링크 NativeShare에 전달
        MobileShareLink(contentData.layout.shareTitleLayout, content, callback);
    }
```

## Package Import 및 Setting

사용할 패키지 FirebaseDynamicLinks\_10\_3.unitypackage import후 Gamepot과 충돌되는 플러그인 제거하기

<figure><img src="https://lh5.googleusercontent.com/KJiWVLQQkp7QX2s0euuQB6_mwj2NCgS4OnyinQiA2ZQ5IuGMm5eqjr84RxTQgd1DR1Q2sGYmgRD6k4zw5sYxVQzg4kvAk5QdEbgBFtxkHYTsrlUOTUwTt92lg-YGbUqntLzJBUlYGPUn3oQj6aNFLN0" alt=""><figcaption><p>중복되는 플러그인 제거 후 플러그인리스트</p></figcaption></figure>

{% embed url="https://guide.ncloud-docs.com/docs/game-troubleshooting" %}
GamePot 연동 시, 중복 플러그인 제거하는 방법
{% endembed %}

## 초기화&#x20;

Start()에서 Firebase Dependency 상태 체크하여 초기화하고, Dynamic Link가 활성화 됐을 때 사용할 이벤트를 등록한다.

<pre class="language-csharp"><code class="lang-csharp">// Assets\_DEV\Script\MobileShare\DynamicLinkSetting.cs
private void Awake()
{
    StartCoroutine(Co_Init());
}
<strong>
</strong><strong>IEnumerator Co_Init()
</strong>{
    // JoinShareLink 객체 생성될 때까지 기다렸다가 이벤트 등록
    yield return new WaitUntil(() => Single.JoinShareLink);
    onParam.AddListener(Single.JoinShareLink.OnParamReceived);
#if UNITY_ANDROID || UNITY_IOS

    FirebaseApp.CheckAndFixDependenciesAsync().ContinueWithOnMainThread(task =>
    {
        // 디펜던시 체크하여 결과가 DependencyStatus.Available이면 초기화.
        var dependencyStatus = task.Result;
        if (dependencyStatus == DependencyStatus.Available)
        {
            DynamicLinks.DynamicLinkReceived += OnDynamicLinkReceived;
            Debug.Log("DynamicLinkSetting Initialized.");
            initialized = true;
        }
        else
        {
            Debug.LogError("Could not resolve all Firebase dependencies: " + dependencyStatus);
        }
    });
#endif
        }
</code></pre>

프론트엔드에서 "arzmeta" scheme를 주로사용하여 충돌이 있어 임의로 arzmetadeeplink로 설정하였습니다.( PC버전 로그인 시, url을 오픈하면서 cmd.exe가 실행되어 다이나믹링크가 수신되는 문제가 있었음)

## Dynamic Link 수신 이벤트 함수 작성

DynamicLink가 수신되었을 때, sender object와 ReceivedDynamicLinkEventArgs를 파라미터로 갖는 이벤트 함수를 작성합니다. ReceivedDynamicLinkEventArgs에는 url이 포함되어 있고 해당 url에는 수신 이후 로직 구현을 위한 Query를 포함하고 있어 Dictionary 형태로 파싱하여 사용합니다.

```csharp
// Assets\_DEV\Script\MobileShare\DynamicLinkSetting.cs
private void OnDynamicLinkReceived(object sender, ReceivedDynamicLinkEventArgs args)
{
// Android에서 다이나믹 링크가 받아지지 않는 버그 해결을 위해 추가한 코드
#if UNITY_ANDROID
    using (AndroidJavaClass unityPlayer = new AndroidJavaClass("com.unity3d.player.UnityPlayer"))
    {
        using (var activity = unityPlayer.GetStatic<AndroidJavaObject>("currentActivity"))
        {
            var intent = activity.Call<AndroidJavaObject>("getIntent");
            intent.Call("removeExtra", "com.google.firebase.dynamiclinks.DYNAMIC_LINK_DATA");
            intent.Call("removeExtra", "com.google.android.gms.appinvite.REFERRAL_BUNDLE");
        }
    }
#endif

    // Escape Code가 포함되는 이상한 버그가 있어서 Uri.UnescapeDataString() 사용
    var url = Uri.UnescapeDataString(args.ReceivedDynamicLink.Url.AbsoluteUri);

    Debug.Log($"[Received Dynamic Link] Absolute Uri: {url} / Url: {args.ReceivedDynamicLink.Url} / Sender: {sender}");

    // url 파싱해서 처리
    // Escape Code가 포함되는 이상한 버그가 있어서 Uri.UnescapeDataString() 사용
    var encodedQuery = Uri.UnescapeDataString(args.ReceivedDynamicLink.Url.Query);

    var query = WebUtility.UrlDecode(encodedQuery);

    if (string.IsNullOrEmpty(query))
    {
        Debug.Log("질의가 없습니다.");
    }
    else
    {
        var paramDic = GetParameterDictionary(query);

        // 로직 실행을 위해 Assets\_DEV\Script\MobileShare\JoinShareLink.cs의 OnParamReceived() 이벤트 호출
        // 해당 이벤트 초기화는 Assets\_DEV\Script\MobileShare\DynamicLinkSetting.cs의Awake()에서 해줌
        onParam?.Invoke(paramDic);
    }
}
```
