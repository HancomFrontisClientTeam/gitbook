---
description: 사용자가 콘텐츠 이용 시 필요 데이터를 캐싱하여 전역으로 사용할 수 있는 클래스
---

# LocalPlayerData

* Static 클래스로, 캐싱한 데이터를 전역으로 사용할 수 있다
* 사용자와 직접적인 관련이 있으며 전역에서 필요로 하는 데이터를 들고 있다
* 로그인 시 주어지는 사용자 데이터를 주로 캐싱

## 1.  구성

### 1) LocalPlayerData

● 로그인 시 받아오는 사용자 데이터 캐싱

* 사용자 데이터 리스폰스 부분

<details>

<summary>async Task Co_SetLocalPlayerData(LoginPacketRes res)</summary>

```csharp
// WebAccount.cs

// 해당 메소드는 상위에서 호출 (AutoLogin, (아즈메타 로그인)Login)
public async void SetLocalPlayerData(LoginPacketRes res) => await Co_SetLocalPlayerData(res);

    private bool isAppInfoDone, isMemberInfo;
    async Task Co_SetLocalPlayerData(LoginPacketRes res)
    {
        isAppInfoDone = isMemberInfo = false;

        // 1.
        // 상위 호출 API의 리스폰스로 사용자 데이터가 온다.
        // (MemberCode, NickName, ProviderType...)
        LocalPlayerData.SetMemberData(res.memberInfo);
        
        // 2.
        // 상위 API가 정상적으로 호출 시 AppInfo API 호출
        // (OnfContentsInfos, BannerInfo, ScreenInfo...)
        Single.Web.member.GetAppInfo((str) =>
        {
            (생략) // 데이터 Packet 클래스에 따라 스위치문으로 파싱해준다.
            
            LocalPlayerData.SetAppInfo(appInfoRes);

            isAppInfoDone = true;
        });
        
        // 3.
        // 상위 API가 정상적으로 호출 시 MemberInfo API 호출
        // (AvatarInfo, SocialLoginInfo, InteriorItemInvens...)
        Single.Web.member.GetMemberInfo((memberInfoRes) =>
        {
            LocalPlayerData.SetMemberInfo(memberInfoRes);

            isMemberInfo = true;
        });

        await UniTask.WaitUntil(() => isAppInfoDone && isMemberInfo);

        SuccessLogin();
    }
```

</details>

* 사용자 데이터 캐싱 부분

{% tabs %}
{% tab title="1. SetMemberData" %}
```csharp
// ● [MemberData] 로그인 시 받아오는 데이터 
// (MemberCode, NickName, ProviderType...)

    private static MemberData MemberData { get; set; } = new MemberData();
    public static void SetMemberData(MemberData memberData = null, bool isReset = false)
    {
        // 리스폰스가null일 시 초기화
        MemberData = memberData ?? new MemberData();

        // PlayerPrefs 로컬 저장하는 것은 리셋 시에만 값 초기화
        ProviderType = isReset || MemberData.providerType > 0 ? MemberData.providerType : ProviderType;
        LoginToken = isReset || !string.IsNullOrEmpty(MemberData.loginToken) ? MemberData.loginToken : LoginToken;
    }

// ● 사용을 위한 프로퍼티
// - 해당 데이터 클래스의 변수대로 static 프로퍼티를 선언 (※신규 변수 추가 시 프로퍼티도 선언)
// - MemberData에 연결하여 바로 메인 데이터까지 변경

    /// <summary>
    /// 서버 발급 사용자 고유 ID
    /// </summary>
    public static string MemberID { get => MemberData.memberId; set => MemberData.memberId = value; }

    /// <summary>
    /// 멤버 고유 코드(외부 보이는 용)
    /// </summary>
    public static string MemberCode { get => MemberData.memberCode; set => MemberData.memberCode = value; }

    (생략)
```
{% endtab %}

{% tab title="2. SetAppInfo" %}
```csharp
// ● [AppInfo] 앱 데이터 
// (OnfContentsInfos, BannerInfo, ScreenInfo...)

    private static GetAppInfoPacketRes AppInfo { get; set; } = new GetAppInfoPacketRes();
    public static void SetAppInfo(GetAppInfoPacketRes appInfo = null)
    {
        // 리스폰스가 null일 시 초기화
        AppInfo = appInfo ?? new GetAppInfoPacketRes();
    }
    
// ● 사용을 위한 프로퍼티
// - 해당 데이터 클래스의 변수대로 static 프로퍼티를 선언 (※신규 변수 추가 시 프로퍼티도 선언)
// - AppInfo에 연결하여 바로 메인 데이터까지 변경

    /// <summary>
    /// 기타 정보
    /// </summary>
    public static OnfContentsInfo[] OnfContentsInfos { get => AppInfo.onfContentsInfo; set => AppInfo.onfContentsInfo = value; }

    /// <summary>
    /// 배너 정보
    /// </summary>
    public static _BannerInfo[] BannerInfo { get => AppInfo.bannerInfo; set => AppInfo.bannerInfo = value; }

    (생략)
```
{% endtab %}

{% tab title="3. SetMemberInfo" %}
```csharp
// ● [MemberInfo] 사용자 사용자 관련 데이터
//(AvatarInfo, SocialLoginInfo, InteriorItemInvens...)

    private static GetMemberInfoPacketRes MemberPersonalInfo { get; set; } = new GetMemberInfoPacketRes();
    public static void SetMemberInfo(GetMemberInfoPacketRes memberInfo = null, bool isReset = false)
    {
        // 리스폰스가 null일 시 초기화
        MemberPersonalInfo = memberInfo ?? new GetMemberInfoPacketRes();

        // 아바타 옷 정보 예외처리
        // 리셋이거나 avatarInfos 데이터가 없을 때 디폴트 의상으로 데이터 변경
        if (isReset || MemberPersonalInfo.avatarInfos == null || MemberPersonalInfo.avatarInfos.Count == 0)
        {
            // 마스터 매니저 및 데이터 존재 여부 체크
            if (Single.MasterData == null || Single.MasterData.dataAvatarResetInfo == null)
                return;

            AvatarInfo = Single.ItemData.GetAvatarResetInfo().ToDictionary(x => x.Key, x => x.Value);
        }
    }

// ● 사용을 위한 프로퍼티
// - 해당 데이터 클래스의 변수대로 static 프로퍼티를 선언 (※신규 변수 추가 시 프로퍼티도 선언)
// - MemberPersonalInfo에 연결하여 바로 메인 데이터까지 변경
 
    /// <summary>
    /// 1 : 헤어, 2 : 상의, 3 : 하의, 4 : 전신 코스튬, 5 : 신발, 6 : 악세서리 
    /// </summary>
    public static Dictionary<string, int> AvatarInfo { get => MemberPersonalInfo.avatarInfos; set => MemberPersonalInfo.avatarInfos = value; }

    /// <summary>
    /// 내 명함 전체 정보
    /// </summary>
    public static BizCardInfo[] BusinessCardInfos { get => MemberPersonalInfo.businessCardInfos; set => MemberPersonalInfo.businessCardInfos = value; }

    (생략)
```
{% endtab %}
{% endtabs %}

● 로그아웃 시 데이터 리셋 메소드&#x20;

<details>

<summary>public static void ResetData()</summary>

```csharp
// 로그아웃 및 탈퇴 시 호출
// 해당 로직에 추가해야 되는 처리가 있을 시 주석 상세히 기재

    public static void ResetData()
    {
        DEBUG.LOG("Player Data를 초기화했습니다!", eColorManager.WEB);
        // 데이터 리셋
        SetMemberData(isReset: true);
        SetMemberInfo(isReset: true);
        SetAppInfo();

        // 소셜 로그인
#if (UNITY_ANDROID || UNITY_IOS) && !UNITY_EDITOR
        if (Method.IsSocialLogined()) GamePot.logout();
#endif

        // 소셜계정 로그인에 필요한 ID 같은 것
        Method.AccountToken = string.Empty;

        // 마이룸 정보
        Method.roomCode = string.Empty;
        Method.roomOwnerName = string.Empty;

        // 채팅 정보
        Method.IsLogined = false;
        Method.IsFirst = true;
        ResetChat();

        StopHeartBeat();

        DEBUG.LOG("Social Login Data를 초기화했습니다!", eColorManager.WEB);
    }

    public static void ResetChat()
    {
        Method.dmChat.Clear();
        Method.userList.Clear();
        Single.Socket.item_S_ChatDM_Handler -= Single.Socket.ChatDataHolder;
    }

    public static void StopHeartBeat()
    {
        if (Application.isPlaying)
        {
            // 사용자 중복 체크 중단
            Single.Web.member.StopHeartBeat();
        }
    }
```

</details>



### 2) LocalPlayerData\_Method

● 사용자 데이터를 활용한 전역 기능메소드

* LocalPlayerData 클래스의 파셜이며, 내부 Static 클래스로 분할하였다.
* 메소드의 성격에 따라 클래스 분리&#x20;
* LocalPlayerData:  사용자 데이터 캐싱 및 프로퍼티, 데이터리셋
* LocalPlayerData\_Method:  사용자 데이터를 이용한 그외 전역 메소드

<figure><img src="../../../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

{% hint style="danger" %}
(2023.11.16 기준) 각자 필요에 의하여 작성된 메소드이기 때문에 개수가 많으며, 간혹 사용자 데이터를 사용하지 않는 메소드도 있다. 차후 클래스 이동이나 정리가 필요할 듯 하다.
{% endhint %}

