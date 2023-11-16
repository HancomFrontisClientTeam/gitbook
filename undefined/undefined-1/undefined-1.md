---
description: >-
  Mobile Version : GamePot SDK, Standalone Windows Version : 자체 API를 사용하여 계정 연동
  기능을 구현하였습니다.
---

# 계정 연동 및 전환 기능

{% hint style="info" %}
모바일 버전과 Standalone Windows 버전은 전처리문으로 나누어 코드 작성되어있음
{% endhint %}

<details>

<summary>관련 스크립트</summary>

계정 연동 :[ ](https://hancom.dooray.com/wiki/3447245024658035709/3574835411885222890)Assets\\\_DEV\Script\UI\View\View\_Account.cs

계정 전환 : Assets\\\_DEV\Script\UI\Popup\Popup\_ConvertAccount.cs

</details>

<figure><img src="../../.gitbook/assets/image (48).png" alt=""><figcaption></figcaption></figure>

## 전체적인 로직

1. 아즈폰 - 설정 -  계정 화면에서 각 소셜 로그인(NAVER, GOOGLE, APPLE) 버튼 클릭 시, 해당 소셜 로그인 페이지를 3D Webview에 띄워 로그인을 한다.
2. <mark style="color:green;">모바일의 경우</mark>, API를 통해 다른 계정에 연동되어 있는지 확인 후 있으면 계정 전환을 위한 팝업에 현재 로그인 계정 정보와 전환하고 싶은 계정 정보를 넘겨주고 없으면 로컬 데이터를 추가하여 현재 로그인된 계정 정보를 업데이트한다. <mark style="color:blue;">Stnadalone의 경우</mark>, 서버에서 보내주는 콜백으로 다른 계정에 연동되어 있는지 확인 후 모바일과 같은 로직으로 계정 전환 팝업을 띄우거나 현재 로그인된 계정 정보를 업데이트한다.

<figure><img src="../../.gitbook/assets/image (50).png" alt=""><figcaption><p>계정 전환 팝업</p></figcaption></figure>

3. 현재 계정 연동 상태에 따라 토글 상태를 업데이트한다.

## Mobile Version(Android, iOS)

[GamePot 페이지](../../asset/sdk/firebase/gamepot.md) 참조

## Standalone Windows Version

Standalone 버전의 경우, Chromium 프로세스 종료를 해야 웹뷰 관련 설정(브라우저 언어 설정, useragent 설정)을 할 수 있습니다.

서버 측에 일반 로그인인지 계정 연동인지 알려주기 위해 3D WebView의 UserAgent를LoginData(isLinked=true, memberId=현재 로그인되어있는 memberID)로 설정해야 합니다.

```csharp
await StandaloneWebView.TerminateBrowserProcess();

// 현재 언어 상태에 따라 브라우저 언어를 세팅하는 방향으로 변경하면 좋을 것 같습니다.
StandaloneWebView.SetCommandLineArguments("--lang=ko-KR");

string userAgent = $"LoginData(isLinked=true, memberId={LocalPlayerData.MemberID})";
Web.SetUserAgent(userAgent);
```

{% embed url="https://support.vuplex.com/articles/chromium-processes" %}
Vuplex 3D Webview 참조 문서
{% endembed %}
