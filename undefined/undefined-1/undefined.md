---
description: >-
  Mobile Version : GamePot SDK, Standalone Windows Version : 자체 API를 사용하여 소셜 로그인
  기능을 구현하였습니다.
---

# 소셜 로그인 기능

{% hint style="info" %}
모바일 버전과 Standalone Windows 버전은 전처리문으로 나누어 코드 작성되어있음
{% endhint %}

<details>

<summary>관련 스크립트</summary>

Assets\\\_DEV\Script\UI\Panel\Panel\_SocialLogin.cs

</details>

<figure><img src="../../.gitbook/assets/image (24) (1).png" alt=""><figcaption></figcaption></figure>

## 전체적인 로직

1. 각 소셜 로그인(NAVER, GOOGLE, APPLE) 버튼 클릭 시, 해당 소셜 로그인 페이지를 3D Webview에 띄워 로그인을 한다.

<figure><img src="../../.gitbook/assets/image (25) (1).png" alt=""><figcaption><p>3D Webiew Login Page</p></figcaption></figure>

2. 로그인 성공 시, <mark style="color:green;">모바일의 경우</mark>에는 로그인 성공 콜백 함수에서 GamePot을 통해 소셜 로그인 정보(NUserInfo)를 저장하고 <mark style="color:blue;">Standalone 버전의 경우</mark>에는 서버에서 보내주는 로그인 정보를 포함한 콜백을 받아 파싱하여  Dictionary 형태로 소셜 로그인 정보를 저장한다. 그리고 accountToken을 사용해 탈퇴 여부를 확인한 후 확인/취소 버튼에 따라 로그인을 시키거나 로그아웃을 시킨다.

<figure><img src="../../.gitbook/assets/image (28).png" alt=""><figcaption><p>탈퇴 확인 팝업</p></figcaption></figure>

## Mobile Version(Android, iOS)

[GamePot 페이지](../../asset/sdk/firebase/gamepot.md) 참조

## Standalone Windows Version

Standalone 버전의 경우, Chromium 프로세스 종료를 해야 웹뷰 관련 설정(브라우저 언어 설정, useragent 설정)을 할 수 있습니다.

```csharp
await StandaloneWebView.TerminateBrowserProcess();

// 현재 언어 상태에 따라 브라우저 언어를 세팅하는 방향으로 변경하면 좋을 것 같습니다.
StandaloneWebView.SetCommandLineArguments("--lang=ko-KR");
```

{% embed url="https://support.vuplex.com/articles/chromium-processes" %}
Vuplex 3D Webview 참조 문서
{% endembed %}
