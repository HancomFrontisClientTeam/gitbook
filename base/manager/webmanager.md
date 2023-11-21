---
description: WebAPI를 호출하는 매니저
---

# WebManager

* 게이트 웨이 호출 및 마스터 데이터 로드
* WebAPI 작성 및 호출

## ★ 서버팀  API

* Swagger 스웨거 사이트 이용 중 (2023.11.02 기준)

◆ \[a:zrmeta] 로그인 서버 API

아이디: frontis / 비밀번호: rkskekfk123

{% embed url="https://dev-account.arzmeta.net/docs#/" %}

◆ \[a:zrmeta] 컨텐츠 서버 API

아이디: frontis / 비밀번호: rkskekfk123

{% embed url="https://dev-content.arzmeta.net/docs#/" %}

## 1 . 스크립트 구조도

* API 개수가 많아짐에 따라 스웨거 카테고리 기준으로 스크립트 분배
* WebManager의 변수로 해당 스크립트를 선언하고 Single.Web.(해당 스크립트  변수명).(API) 형식으로 사용한다.
* API 클래스는 Asset/\_Dev/Script/Framework/Manager/WebManager/ 폴더 하위에 생성

<img src="../../.gitbook/assets/file.excalidraw (1).svg" alt="" class="gitbook-drawing">

## 2. 메소드 흐름 구조도

* 초기화 플로우 (게이트웨이 및 마스터 데이터 로드)
* 일반 API 호출은 제외

<img src="../../.gitbook/assets/file.excalidraw (2).svg" alt="" class="gitbook-drawing">

## 3. 중요 메소드

<table><thead><tr><th width="458">변수</th><th>설명</th></tr></thead><tbody><tr><td>public bool IsGateWayInfoResDone { get; private set; }</td><td>Scene_Logo.cs에서 게이트웨이 로드 완료 여부를 알기 위한 bool값.<br>true가 되지 않으면 진입 플로우를 타지 않는다.</td></tr><tr><td>private string GatewayInfoURL</td><td>암호화 된 게이트웨이 주소를 가지고 있는 스토리지 컨테이너.<br>서버측에서 제공한 주소를 넣는다.</td></tr></tbody></table>

● 게이트웨이 관련

<details>

<summary>private async void SetUpGateWayInfo()</summary>

```csharp
// 게이트웨이 데이터 가져오는 메소드
// 최초로 실행되는 Logo씬의 Scene_Logo.cs 에서 호출된다.
// 게이트웨이는 로그인한 기기의 OsType, 서버 상태, 필요한 URL 데이터 등을 가지고 있다.

private async void SetUpGateWayInfo()
{
    // Scene_Logo 코루틴 중에 게이트웨이가 정상적으로 로드되는지 여부에 따라 진입 플로우가 진행
    IsGateWayInfoResDone = false;
    var mainURL = string.Empty;
    
    // Json 파싱 없이 string 형태 그대로 가져온다
    SendRequest<string>(new SendRequestData(eHttpVerb.GET, GatewayInfoURL, string.Empty), (res) => mainURL = SetMainUrl(res));

    await UniTask.WaitUntil(() => !string.IsNullOrEmpty(mainURL));

    var packet = new
    {
        osType = GetOsType(),
        appVersion = Application.version,
    };
    
    // 받아온 string을 복호화 하여 진짜 게이트웨이 주소로 데이터 로드
    SendRequest<string>(new SendRequestData(eHttpVerb.POST, mainURL, string.Empty, packet), SerializeGatewayInfo);
}
```

</details>

<details>

<summary>private void SerializeGatewayInfo(string result)</summary>

<pre class="language-csharp"><code class="lang-csharp">// 게이트웨이에서 정상적인 데이터를 받았을 경우에만 
// IsGateWayInfoResDone가 true가 되며 진입 플로우 진행

private void SerializeGatewayInfo(string result)
{
<strong>    if (string.IsNullOrEmpty(result))
</strong>    {
        OpenReturnToLogoPopup();
        return;
    }
    
<strong>    // 게이트웨이 주소로 받아온 Json 데이터를 파싱
</strong>    var jobj = (JObject)JsonConvert.DeserializeObject(result);
    foreach (var x in jobj)
    {
        switch (x.Key)
        {
            // 보통의 경우 이 헤더로 온다
            case "Gateway":
                GatewayInfo = JsonConvert.DeserializeObject&#x3C;GatewayInfo>(result);

                CheckAppVersionDataReset(GatewayInfo.Gateway.appVersion);

                // 서버에서 보내주는 상태 값에 따라 분기
                switch ((SERVER_STATE)GatewayInfo.Gateway.ServerState.state)
                {
                    case SERVER_STATE.ACTIVATE: IsGateWayInfoResDone = true; break;
                    case SERVER_STATE.INACTIVATE: SetGatewayPopup(GatewayInfo.Gateway.StateMessage.message, () => Util.QuitApplication()); break;
                    case SERVER_STATE.TEST: Debug.Log("Gateway: 테스트 수락된 유저만 진입할 수 있습니다. 본 기능은 아직 개발되지 않았습니다."); break;
                    case SERVER_STATE.NEED_UPDATE: SetGatewayPopup(GatewayInfo.Gateway.StateMessage.message, () => UpdateVersion(GatewayInfo.Gateway.OsType.storeUrl)); break;
                    default: break;
                }
                break;
            default:
                GatewayInfo_Update gatewayInfo_Update = JsonConvert.DeserializeObject&#x3C;GatewayInfo_Update>(result);
                SetGatewayPopup(gatewayInfo_Update.StateMessage.message, () => UpdateVersion(gatewayInfo_Update.OsType.storeUrl));
                break;
        }
        break;
    }
}
</code></pre>

</details>



● API 관련

<details>

<summary>public async void SendRequest&#x3C;T_Res>(SendRequestData data, Action&#x3C;T_Res> _res, Action _error = null)</summary>

```csharp
// 데이터 리퀘스트 외부 호출 메소드

public async void SendRequest<T_Res>(SendRequestData data, Action<T_Res> _res, Action<DefaultPacketRes> _error = null)
{
    // _error가 null일 시 자동으로 Error_Res이 들어간다. (딤 off, 에러 디버그)
    await Co_SendWebRequest(data, _res, _error ?? Error_Res);
}
```

</details>

<details>

<summary>async Task Co_SendWebRequest&#x3C;T_Res>(SendRequestData data, Action&#x3C;T_Res> _res, Action _error = null)</summary>

<pre class="language-csharp"><code class="lang-csharp">// SendRequestData는 데이터 클래스
// _error는 리턴 형식이 DefaultPacketRes 고정이다. 서버측과 합의된 사항.

async Task Co_SendWebRequest&#x3C;T_Res>(SendRequestData data, Action&#x3C;T_Res> _res, Action&#x3C;DefaultPacketRes> _error = null)
{
    string responseJson = string.Empty;
    byte[] jsonBytes = null;
    
    // dim 이 true면 켜준다
    if (data.dim) Single.Scene.SetDimOn(1f);
    // 자동으로 뜨는 에러 팝업을 비/활성화 한다. subUrl기준으로 딕셔너리에 데이터 저장 
    SetIsErrorPopup(data.subUrl, data.isErrorPopup);

    #region 데이터 패킹 및 세팅
    // 데이터 패킹
    if (data.packet != null)
    {
        var requestJson = JsonConvert.SerializeObject(data.packet, Formatting.Indented);
        jsonBytes = Encoding.UTF8.GetBytes(requestJson);
        DEBUG.LOG(data.subUrl + "\n" + requestJson + "\n", eColorManager.Web_Request);
    }
    // UnityWebRequest 세팅
    var uwr = new UnityWebRequest(data.GetURL(), data.httpVerb.ToString())
    {
        uploadHandler = new UploadHandlerRaw(jsonBytes),
        downloadHandler = new DownloadHandlerBuffer()
    };
    // 헤더 세팅
    // 내부 API의 경우 서버측 필요로 인해 SetRequestHeader_jwt에서
    // 암호화한 jwtAccessToken과 sessionId를 헤더에 추가 세팅
    uwr.SetRequestHeader("Content-Type", "application/json");
    SetRequestHeader_jwt(uwr);

    // uwr.timeout = 10; // 타임 아웃을 UniTask에서 처리하도록 수정. BKK
    var cts = new CancellationTokenSource();
    cts.CancelAfterSlim(TimeSpan.FromSeconds(10)); // 타임 아웃 시간. BKK
    #endregion

    #region 데이터 샌드 및 리스폰스
    try
    {
        await uwr.SendWebRequest().WithCancellation(cts.Token);
    }
    catch (UnityWebRequestException exception)
    {
        if (exception.Result == UnityWebRequest.Result.ConnectionError ||
                               exception.Result == UnityWebRequest.Result.ProtocolError ||
                               exception.Result == UnityWebRequest.Result.DataProcessingError)
        {
            DEBUG.LOG(data.subUrl + " " + exception.Error, eColorManager.Web_Response);
            DEBUG.LOG(exception.Text, eColorManager.Web_Response);

            // 자동으로 뜨는 에러 팝업 비/활성화. 
            // 딕셔너리에서 해당 subUrl 키가 있을 시 값이 true면 에러 팝업 띄워줌
            if (GetIsErrorPopup(data.subUrl)) await HandleError(exception.Error, exception.Text);

            if (SceneLogic.instance.GetSceneType() == SceneName.Scene_Base_Title) RefrashTitle();
        }
    }
    // 타임 아웃 예외 처리
    catch (OperationCanceledException exception)
    {
        if (exception.CancellationToken == cts.Token)
        {
            Debug.Log("TimeOut!!");
            await HandleError("Request timeout", "{ error = 0, errorMessage = \"\"}");
        }
    }
    finally
    {
        await UniTask.WaitUntil(() => uwr.isDone);
        // dim 이 true면 꺼주는 작업까지 한세트
        if (data.dim) Single.Scene.SetDimOff(1f);

        responseJson = Util.Beautify(uwr.downloadHandler.text);
        DEBUG.LOG(data.subUrl + "\n" + responseJson + "\n", eColorManager.Web_Response);

        if (uwr.result == UnityWebRequest.Result.Success)
        {
            // T_Res이 string이면 Deserialize 하지 않음
            T_Res resObj = (typeof(T_Res) == typeof(string)) ? (T_Res)(object)responseJson : JsonConvert.DeserializeObject&#x3C;T_Res>(responseJson);

            if (resObj != null)
            {
                _res?.Invoke(resObj);
            }
            else
            {
                SceneLogic.instance.PushPopup&#x3C;Popup_Basic>()
                    .ChainPopupData(new PopupData(POPUPICON.NONE, BTN_TYPE.Confirm, masterLocalDesc: new MasterLocalData("common_error_network_01")));
            }
        }
        else
<strong>        {
</strong>            var resObj = JsonConvert.DeserializeObject&#x3C;DefaultPacketRes>(responseJson);
            if (resObj != null) _error?.Invoke(resObj);
        }
    }
}

</code></pre>

</details>

<details>

<summary>public async void SendWebRequest_MultiPartFormData&#x3C;T_Res>(SendRequestData_MultiPartForm data, Action&#x3C;T_Res> res, Action&#x3C;DefaultPacketRes> error = null)</summary>

<pre class="language-csharp"><code class="lang-csharp">// 멀티파츠폼 데이터 리퀘스트 외부 호출 메소드
public async void SendWebRequest_MultiPartFormData&#x3C;T_Res>(SendRequestData_MultiPartForm data, Action&#x3C;T_Res> res, Action&#x3C;DefaultPacketRes> error = null)
{
<strong>    // _error가 null일 시 자동으로 Error_Res이 들어간다. (딤 off, 에러 디버그)    
</strong>    await Co_SendPostRequest_MultiPartFormData(data, res, error ?? Error_Res);
}
</code></pre>

</details>

<details>

<summary>async Task Co_SendPostRequest_MultiPartFormData&#x3C;T_Res>(SendRequestData_MultiPartForm data, Action&#x3C;T_Res> res, Action&#x3C;DefaultPacketRes> error = null)</summary>

```csharp
// SendRequestData_MultiPartForm 은 데이터 클래스
// _error는 리턴 형식이 DefaultPacketRes 고정이다. 서버측과 합의된 사항.
// 로컬 이미지를 이용하여 썸네일을 보내기 때문에 추가적인 헤더 바운더리 필요

        async Task Co_SendPostRequest_MultiPartFormData<T_Res>(SendRequestData_MultiPartForm data, Action<T_Res> res, Action<DefaultPacketRes> error = null)
        {
            string requestJson;

            if (data.dim) Single.Scene.SetDimOn(1f);

            #region 데이터 패킹 및 세팅
            //1. 멀티파트 폼데이터 셋업
            List<IMultipartFormSection> formData = new List<IMultipartFormSection>();

            if (data.thumbnailPath != "")
            {
#if UNITY_IOS
                byte[] image = data.iosTex.EncodeToPNG();
#else
                byte[] image = File.ReadAllBytes(data.thumbnailPath);
#endif
                string fileName = Uri.EscapeUriString(Path.GetFileName(data.thumbnailPath));
#if UNITY_IOS
                fileName = Util.ConvertIOSExtension(fileName);
#endif
                string extension = "image/" + Path.GetExtension(fileName).Split('.').Last();
                formData.Add(new MultipartFormFileSection("image", image, fileName, extension));
            }


            requestJson = JsonConvert.SerializeObject(data.packet, Formatting.Indented);

            DEBUG.LOG(data.subUrl + "\n" + requestJson + "\n", eColorManager.Web_Request);

            Dictionary<string, string> jsonDic = JsonConvert.DeserializeObject<Dictionary<string, string>>(requestJson);
            foreach (var item in jsonDic)
            {
                //Debug.Log("item.Key : " + item.Key);
                //Debug.Log("item.Value : " + item.Value);
                formData.Add(new MultipartFormDataSection(item.Key, item.Value));
            }

            //2. 폼데이터에 맞게 바운더리 작업
            byte[] boundary = UnityWebRequest.GenerateBoundary();
            byte[] formSections = UnityWebRequest.SerializeFormSections(formData, boundary);//3. 폼데이터에 맞게 업로드 핸들러

            //uwr = UnityWebRequest.Post(url, formData);
            UnityWebRequest uwr = new UnityWebRequest(data.GetURL(), data.httpVerb.ToString())
            {
                timeout = 10,
                uploadHandler = new UploadHandlerRaw(formSections),
                downloadHandler = new DownloadHandlerBuffer()
            };

            uwr.SetRequestHeader("Content-Type", $"multipart/form-data; boundary={Encoding.UTF8.GetString(boundary)}"); //4. 헤더에 바운더리 폼데이터이기때문에 바운더리 값 추가
            SetRequestHeader_jwt(uwr);

            #endregion

            #region 데이터 샌드 및 리스폰스
            try
            {
                await uwr.SendWebRequest(); //쏜다
            }
            catch (UnityWebRequestException exception)
            {
                if (exception.Result == UnityWebRequest.Result.ConnectionError ||
                         exception.Result == UnityWebRequest.Result.ProtocolError ||
                         exception.Result == UnityWebRequest.Result.DataProcessingError)
                {
                    DEBUG.LOG(exception.Error, eColorManager.Web_Response);
                    DEBUG.LOG(exception.Text, eColorManager.Web_Response);

                    await HandleError(exception.Error, exception.Text);

                    Debug.Log(exception.Error);
                }
            }
            finally
            {
                if (data.dim)
                {
                    Single.Scene.SetDimOff(1f);
                    await UniTask.WaitWhile(() => Single.Scene.isDim);
                }
                await UniTask.WaitUntil(() => uwr.isDone);

                string responseJson = Util.Beautify(uwr.downloadHandler.text);
                DEBUG.LOG(data.subUrl + "\n" + responseJson + "\n", eColorManager.Web_Response);

                if (uwr.result == UnityWebRequest.Result.Success)
                {
                    var resObj = JsonConvert.DeserializeObject<T_Res>(responseJson);
                    if (resObj != null)
                    {
                        res.Invoke(resObj);
                    }
                    else
                    {
                        SceneLogic.instance.PushPopup<Popup_Basic>()
                            .ChainPopupData(new PopupData(POPUPICON.NONE, BTN_TYPE.Confirm, masterLocalDesc: new MasterLocalData("common_error_network_01")));
                    }
                }
                else
                {
                    var resObj = JsonConvert.DeserializeObject<DefaultPacketRes>(responseJson);
                    if (resObj != null) error?.Invoke(resObj);
                }
                uwr.Dispose(); //해제
            }
            #endregion
        }
```

</details>

<details>

<summary>private async Task HandleError(string errorMessage, string response)</summary>

```csharp
// UnityWebRequest 실행 시 에러가 발생하여 Catch문에 걸리면 해당 메소드 호출

private async Task HandleError(string errorMessage, string response)
{
    DefaultPacketRes responseError = JsonConvert.DeserializeObject<DefaultPacketRes>(response);

    // 아예 서버 응답이 없을 경우, responseError 가 null 이 들어옴
    if (responseError == null)
    {
        OpenReturnToLogoPopup();
    }
    else
    {
        // http 서버 에러
        // 웹 서버 에러
        if (SceneLogic.instance.GetPopup<Popup_Basic>().CheckHttpResponseError(errorMessage) || 
            SceneLogic.instance.GetPopup<Popup_Basic>().CheckResponseError(responseError.error))
        {
            // 만일 이후 진행이 멈춰야 할 만큼 치명적 에러면 하트비트 코루틴 정지
            member.StopHeartBeat();
        }
    }

    await UniTask.DelayFrame(1);
}
```

</details>



● 에러 팝업 비/활성화 옵션

{% hint style="info" %}
API 중 UI에 보여주지 않지만 데이터의 여부를 알기 위해 호출하는 경우가 있다. 이 때 데이터가 없을 때의 경우 또한 받아서 처리를 해야 하지만 에러 팝업이 자동으로 떠서 로직에 맞지 않을 때가 있다.&#x20;

(어느 씬에 들어가자 마자 '진행 중인 투표가 없습니다' 에러 팝업이 갑자기 뜨는 경우 등)&#x20;

이러한 경우를 위해 에러가 발생하더라도 에러 팝업을 띄우지 않는 bool 값을 받아 처리하는 옵션을 추가 했다.
{% endhint %}

<details>

<summary>IsErrorPopup</summary>

```csharp
// 에러 시 자동으로 뜨는 팝업을 비/활성화 할 수 있는 옵션
// API 호출 시 isErrorPopup 파라미터를 작성하면 된다. true: 활성화, false: 비활성화
// true가 기본값

private Dictionary<string, bool> IsErrorPopupDic = new Dictionary<string, bool>();

// Co_SendWebRequest 메소드에서 호출하여 딕셔너리 추가
// Key 값은 subUrl이다.
public void SetIsErrorPopup(string key, bool isErrorPopup)
{
    if (!IsErrorPopupDic.ContainsKey(key))
    {
        IsErrorPopupDic.Add(key, default);
    }
    IsErrorPopupDic[key] = isErrorPopup;
}

// Key가 있으면 해당 Value값을 리턴
// 없으면 true 리턴
private bool GetIsErrorPopup(string key)
{
    if (IsErrorPopupDic.ContainsKey(key))
    {
        return IsErrorPopupDic[key];
    }
    return true;
}
```

</details>



● 데이터 클래스

<details>

<summary>public class SendRequestData</summary>

```csharp
// 기본 데이터 클래스

public class SendRequestData
{
    // 메소드 형식. eHttpVerb은 enum이다.
    // public enum eHttpVerb
    // {
    //    GET,
    //    POST,
    //    PUT,
    //    DELETE,
    // }
    public eHttpVerb httpVerb = eHttpVerb.GET;

    public string mainUrl = string.Empty;
    public string subUrl = string.Empty;
    public object packet = null;

    // 데이터를 주고 받을 때 dim을 켜는가? true: 켜줌 / false: 안 켜줌
    public bool dim = true;
    // 오류가 발생했을 때 에러 팝업을 띄우는가? true: 띄워줌 / false: 안 띄워줌
    public bool isErrorPopup = true;

    public SendRequestData(eHttpVerb httpVerb, string mainUrl, string subUrl, object packet = null, bool dim = true, bool isErrorPopup = true)
        {
            this.httpVerb = httpVerb;

            this.mainUrl = mainUrl;
            this.subUrl = subUrl;
            this.packet = packet;

            this.dim = dim;
            this.isErrorPopup = isErrorPopup;
        }

    public string GetURL() => mainUrl + subUrl;
}
```

</details>

<details>

<summary>public class SendRequestData_MultiPartForm</summary>

```csharp
// 멀티 폼 데이터용 클래스
// SendRequestData를 상속받아 사용

public class SendRequestData_MultiPartForm : SendRequestData
{
    public string thumbnailPath = "";
    // ios 환경일 시 썸네일에 텍스쳐가 필요
    public Texture2D iosTex = null;

    public SendRequestData_MultiPartForm(eHttpVerb httpVerb, string mainUrl, string subUrl, object packet = null, bool dim = true, bool isErrorPopup = true, string thumbnailPath = "", Texture2D iosTex = null)
            : base(httpVerb, mainUrl, subUrl, packet, dim, isErrorPopup)
        {
            this.thumbnailPath = thumbnailPath;
            this.iosTex = iosTex;
        }
}
```

</details>



## 4. 작성 방법

● 신규 게이트웨이 변수 추가 시

* WebPacket.cs에서 해당 변수를 메소드에 추가

```csharp
// Gateway에 신규 변수 추가 시
// 예시 - WebPacket.cs
public class Gateway
{
    public int osType;
    public string appVersion;
    (생략)
    public string 신규변수; // 추가
}
```

* ServerUrl 추가 시 WebManager에 변수 추가

```csharp
// 예시 - WebPacket.cs
public class ServerInfo
{
    public int serverType;
    public string accountServerUrl;
    (생략)
    public string 신규URL; // 추가
}

// 예시 - WebManager.cs
    private ServerInfo ServerInfo => GatewayInfo.Gateway.ServerType.ServerInfo;
    // Url
    public string AccountServerUrl => ServerInfo.accountServerUrl;
    public string PCAccountServerUrl => ServerInfo.pcAccountServerUrl;
    (생략)
    public string 신규URL => ServerInfo.신규URL; // 추가
```

* 사용자 데이터 (Login, AppInfo, MemberInfo) 추가&#x20;

<pre class="language-csharp"><code class="lang-csharp">// 로그인 시 받아오는 사용자 데이터

// 예시 - WebPacket.cs 변수 추가
public class GetAppInfoPacketRes : DefaultPacketRes
{
    public OnfContentsInfo[] onfContentsInfo;
    public _BannerInfo[] bannerInfo;
    (생략)
    public 신규클래스 신규변수; // 추가
}

// WebManager -> WebAccount.cs에서 해당 API 호출 및 리스폰스를 LocalPlayerData.cs에 전달
public void SetLocalPlayerData(LoginPacketRes res)
    {
        // 1. 로그인 성공 시 사용자 데이터 저장
        LocalPlayerData.SetMemberData(res.memberInfo);
        (생략)
        
        // 로그인 후 정보 가져오기
        // 2. AppInfo 데이터 저장
        Single.Web.member.GetAppInfo_Req((str) =>
        {
            GetAppInfoPacketRes getAppInfoPacketRes = new GetAppInfoPacketRes();
            (생략)
            LocalPlayerData.SetAppInfo(getAppInfoPacketRes);
        });
        // 3. MemberInfo 데이터 저장
        Single.Web.member.GetMemberInfo_Req((memberInfoRes) =>
        {
            LocalPlayerData.SetMemberInfo(memberInfoRes);
            SetEnter();
        });

        (생략)
    }
    
// LocalPlayerData.cs에 저장한다.
// 예시 - LocalPlayerData에 AppInfo 신규 변수 추가 시
    public static GetAppInfoPacketRes appInfo { get; set; } = new GetAppInfoPacketRes();
    public static void SetAppInfo(GetAppInfoPacketRes appInfo = null)
    {
        LocalPlayerData.appInfo = appInfo ?? new GetAppInfoPacketRes();
    }

<strong>// 외부 사용을 위한 프로퍼티
</strong>    #region 개별 변수
    /// &#x3C;summary>
    /// 기타 정보
    /// &#x3C;/summary>
    public static OnfContentsInfo[] OnfContentsInfos { get => appInfo.onfContentsInfo; set => appInfo.onfContentsInfo = value; }
    (생략)
    public static 신규클래스 신규변수 {get => appInfo.신규변수; set => appInfo.신규변수 = value; } // 추가
    #endregion 
</code></pre>



● 신규 API 추가 시

* 서버팀측에서 API 제공 => Swagger 참고

<figure><img src="../../.gitbook/assets/image (2) (1).png" alt=""><figcaption><p>예시 - 친구 즐겨찾기 API</p></figcaption></figure>

```csharp
// 카테고리가 Friend이므로 WebFriend.cs에서 작업
// 신규 카테고리일 시 신규 스크립트 작성 뒤 작업 (네이밍: Web{카테고리 이름})

// 예시 - WebPacket.cs에 해당 API 리퀘스트 클래스 및 리스폰스 클래스 작성
// DefaultPacketRes는 error, errorMessage만 있는 기본 리스폰스 클래스. 
// 리스폰스 클래스는 기본적으로 DefaultPacketRes 상속받아 사용한다.
// 위 이미지 4번 참고
public class BookmarkFriendPacketRes : DefaultPacketRes
{
    public int bookmark;
    public string bookmarkedAt;
}

// 예시 - WebPacket.cs에 API 주소 변수 작성
// 위 이미지 2번 참고
public static string BookmarkFriend = "/api/friend/bookmark";

// 예시 - WebFriend.cs API 작성
// 위 이미지 3번 참고하여 리퀘스트 클래스 작성
// 1번 참고하여 eHttpVerb 선택
public void BookmarkFriend(string friendMemeberCode, Action<BookmarkFriendPacketRes> _res, Action<DefaultPacketRes> _error = null)
{
    // 리퀘스트 클래스는 익명 클래스로 작성 가능
    var packet = new 
    {
        friendMemeberCode
    };

    Single.Web.SendRequest(new SendRequestData(eHttpVerb.POST, ContentServerUrl, WebPacket.BookmarkFriend, packet), _res, _error);
}
```

&#x20;

## 5. 사용 방법

* API 메소드 외부 호출 시 방법

```csharp
// 예시 - 외부 스크립트에서 API 호출
// Single.Web.(해당 클래스).(해당 메소드)
Single.Web.friend.BookmarkFriend(data.memberCode, (res) => 
{ 
    // 정상 데이터 받았을 시 실행 코드
},
(error) =>
{
    // 에러가 발생했을 시 실행 코드 (해당 파라미터 생략 가능)
});
```



{% hint style="info" %}
포스트맨 사용&#x20;

* 프로젝트 내에서 아직 API를 작성하지 않은 상태에서  API 작동을 체크하고 싶을 때
* 프로젝트 내의 API 작동 여부를 교차검증하고 싶을 때

1\) 메소드 형식,  API 주소, 바디 작성

<img src="../../.gitbook/assets/image (3) (1).png" alt="" data-size="original">

2\) 유니티에서 아즈메타 로그인 후 F11를 눌러 암호화된jwtAccessToken과 sessionId 획득

3\) 획득한 데이터를 헤더에 추가

<img src="../../.gitbook/assets/image (4) (1).png" alt="" data-size="original">

4\) Send 하여 API 작동 확인
{% endhint %}
