---
description: AOS, iOS에서 소셜 로그인, 공지사항 기능(추후 Standalone Windows와 동일한 방식으로 변경 예정)을 사용하고 있습니다.
---

# GamePot

{% hint style="info" %}
사용 버전 : GAMEPOT\_UNITY\_SDK\_20220217.unitypackage

GamePot Project ID : 34687e3f-7c50-44a8-86e6-9836b47e1c21
{% endhint %}

{% hint style="warning" %}
* GamePot SDK는 Unity Editor에서 동작하지 않음
  * 테스트를 위해서는 빌드를 해야 하기 때문에 가능한 선에서는 아래 첨부된 Sample Project에서(Dynamic Link는 포함되어 있지 않음) 테스트하시는 것을 추천드립니다.
* Standalone 버전에서 지원되는 GamePot SDK : 구글, 애플 로그인
* GamePot을 개발하면서 생기는 버그들은 대체로 아래 페이지에 포함되어 있습니다.
{% endhint %}

{% embed url="https://drive.google.com/file/d/1RY5DkkI3aG5yQ_uOF9r1C3rvupXMlTSI/view?usp=sharing" %}
GamePot Sample Project with arzMETA
{% endembed %}

<details>

<summary>GamePot 관련 핵심 페이지</summary>

*
*
* GamePot Docs : [https://docs.gamepot.io/](https://docs.gamepot.io/)

<!---->

* <mark style="background-color:yellow;">GamePot FAQ :</mark> [<mark style="background-color:yellow;">https://docs.gamepot.io/undefined/gamepot\_faq#</mark>](https://docs.gamepot.io/undefined/gamepot\_faq)

<!---->

* <mark style="background-color:yellow;">GamePot Troubleshooting :</mark> [<mark style="background-color:yellow;">https://guide.ncloud-docs.com/docs/game-troubleshooting</mark>](https://guide.ncloud-docs.com/docs/game-troubleshooting)

<!---->

* GamePot Dashboard : [https://dashboard.gamepot.ntruss.com/p8j58b5bx0/34687e3f-7c50-44a8-86e6-9836b47e1c21/statistics/pay](https://dashboard.gamepot.ntruss.com/p8j58b5bx0/34687e3f-7c50-44a8-86e6-9836b47e1c21/statistics/pay)(ID: frontishub@gmail.com PWD: Dksdbslzmgksrj1!)

</details>

#### GamePot 고객센터 카카오톡방 내역(230111\~230825)

관련 내용을 해당 페이지에 필요해보이는 부분을 중심으로 정리해두긴 하였으나 참고자료로 올려두었습니다.

{% file src="../../../.gitbook/assets/GamePotKakaoTalk_2023.8.25 13_50-1.txt" %}

***

## GamePot Social Login

```
GamePot.login(NCommon.LoginType);

// 로그인 성공
public void onLoginSuccess(NUserInfo userInfo)
{
}

// 로그인 실패
public void onLoginFailure(NError error)
{
    // error.message를 사용하여 메시지를 표시해 주십시오.
}

// 로그인 취소
public void onLoginCancel()
{
    // 사용자가 로그인을 취소
}

// 강제 업데이트(스토어 버전과 클라이언트 버전이 다를 경우 호출)
public void onNeedUpdate(NAppStatus status)
{
    // 파라미터로 넘어온 status 정보를 토대로 팝업을 만들어 표시해야 합니다. 아래 두 가지 방식 중 한 가지를 선택하여 팝업을 구성해 주십시오.
    // case 1: 개발사에서 직접 UI를 구현한 인게임 팝업 사용
    // case 2: 아래 코드를 호출하여 SDK 자체 팝업 사용
    // GamePot.showAppStatusPopup(status.ToJson());
}

// 점검(대시보드에 점검이 활성화되어 있는 경우 호출)
public void onMainternance(NAppStatus status)
{
     // 파라미터로 넘어온 status 정보를 토대로 팝업을 만들어 표시해야 합니다. 아래 두 가지 방식 중 한 가지를 선택하여 팝업을 구성해 주십시오.
    // case 1: 개발사에서 직접 UI를 구현한 인게임 팝업 사용
    // case 2: 아래 코드를 호출하여 SDK 자체 팝업 사용
    // GamePot.showAppStatusPopup(status.ToJson());
}

// 앱 종료
public void onAppClose()
{
    // 강제 업데이트나 점검 기능을 case 2 방식으로 구현하는 경우 앱을 강제 종료할 수 있으므로 이 곳에서 앱을 종료할 수 있도록 구현해 주십시오.
}

사용하시거나, 콜백 방식의 형태로 사용 부탁드립니다.

GamePot.login(NCommon.LoginType, (resultState, userInfo, appStatus, error) => {
    switch (resultState)
    {
        case NCommon.ResultLogin.SUCCESS:
        // login success
        break;
        case NCommon.ResultLogin.CANCELLED:
        // login cancel
        break;
        case NCommon.ResultLogin.FAILED:
        // login fail
        break;
        case NCommon.ResultLogin.NEED_UPDATE:
            // 파라미터로 넘어온 appStatus 정보를 토대로 팝업을 만들어 표시해야 합니다. 아래 두 가지 방식 중 한 가지를 선택하여 팝업을 구성해 주십시오.
            // case 1: 개발사에서 직접 UI를 구현한 인게임 팝업 사용
            // case 2: 아래 코드를 호출하여 SDK 자체 팝업 사용
            // GamePot.showAppStatusPopup(status.ToJson());
        break;
        case NCommon.ResultLogin.MAINTENANCE:
            // 파라미터로 넘어온 appStatus 정보를 토대로 팝업을 만들어 표시해야 합니다. 아래 두 가지 방식 중 한 가지를 선택하여 팝업을 구성해 주십시오.
            // case 1: 개발사에서 직접 UI를 구현한 인게임 팝업 사용
            // case 2: 아래 코드를 호출하여 SDK 자체 팝업 사용
            // GamePot.showAppStatusPopup(status.ToJson());
        break;
        case NCommon.ResultLogin.APP_CLOSE:
            // 강제 업데이트나 점검 기능을 case 2 방식으로 구현하는 경우 앱을 강제 종료할 수 있으므로 이 곳에서 앱을 종료할 수 있도록 구현해 주십시오.
        break;
        default:
        break;
    }
});
```

### 각 로그인에 필요한 프레임워크

사용하지 않는 프레임워크는 삭제하는 것을 추천드립니다.

<details>

<summary>ex) 사용하지  않는 Facebook 관련 프레임워크</summary>

#### Android

* facebook-applinks-13.2.0
* facebook-bolts-13.2.0&#x20;
* facebook-common-13.2.0&#x20;
* facebook-core-13.2.0&#x20;
* facebook-gamingservices-13.2.0&#x20;
* facebook-login-13.2.0&#x20;
* facebook-messenger-13.2.0&#x20;
* facebook-share-13.2.0&#x20;
* gamepot-channel-facebook

#### iOS

* FBAEMKit.xcframework
* FBSDKCoreKit.xcframework
* FBSDKCoreKit\_Basics.xcframework
* FBSDKLoginKit.xcframework
* GamePotFacebook

</details>

#### Android

Assets/Plugins/Android/libs 폴더에 위치해야 하며 해당 플러그인들은 타겟 플랫폼에 맞게 선택하여 빌드 시 포함되어야 합니다. Assets/Plugins/Android/nativeLibs 폴더는 비우고 빌드 시 포함되지 않게 체크를 해제해주세요.

#### iOS

Assets/Plugins/iOS/Frameworks 폴더에 위치해야 하며 해당 플러그인들은 타겟 플랫폼에 맞게 선택하여 빌드 시 포함되어야 합니다.

<figure><img src="../../../.gitbook/assets/image (34).png" alt=""><figcaption><p>현재 타겟 플랫폼(Android)에 맞게 빌드 시 포함되게 한 예시 이미지</p></figcaption></figure>

{% tabs %}
{% tab title="Google Login" %}
#### Android

* gamepot-channel-google-signin.aar

#### iOS

* GamePotGoogleSignIn.framework
* GoogleSignIn.framework
* GoogleSignInDependencies.framework
{% endtab %}

{% tab title="Naver Login" %}
#### Android

* gamepot-channel-naver.aar

#### iOS

* GamePotNaver.framework
* NaverThirdPartyLogin.framework
{% endtab %}

{% tab title="Apple Login" %}
#### Android

* gamepot-channel-apple-signin.aar

#### iOS

* GamePotApple.framework
{% endtab %}
{% endtabs %}

### GamePot Setting

#### Unity Android Setting

1. Assets/Plugins/Android/mainTemplate.gradle 수정

<figure><img src="../../../.gitbook/assets/image (16).png" alt=""><figcaption><p>gamepot_project_id 연결, gamepot_store 변경</p></figcaption></figure>

2. AndroidManifest.xml MainActivity 수정

<figure><img src="../../../.gitbook/assets/image (17).png" alt=""><figcaption><p>android:name 변경</p></figcaption></figure>

#### Unity iOS Setting

1. ../Assets/Plugins/IOS 경로 하위에 GamePotConfig-Info.plist, GoogleService-Info.plist 추가
2. gamepot\_google\_app\_id의 값은 GoogleService-Info 파일의 CLIENT\_ID 값, gamepot\_google\_url\_schemes의 값은 GoogleService-Info 파일의 REVERSED\_CLIENT\_ID 값 입력

{% embed url="https://guide.ncloud-docs.com/docs/game-gamepotunity#ios-%ED%99%98%EA%B2%BD-%EC%84%A4%EC%A0%95" %}
참고 페이지
{% endembed %}

#### Google Login Setting

> GamePotGoogleSignIn.framework GoogleSignIn.framework GoogleSignInDependencies.framework

1. 구글 Firebase 콘솔에서 획득한 안드로이드용 google-service.json 파일을 Assets/Plugins/Android에 복사)

{% embed url="https://cloud.google.com/firestore/docs/client/get-firebase?hl=ko" %}
Google Cloud에 Firebase 프로젝트 생성 방법
{% endembed %}

2. keystore파일의 SHA-1 값을 Firebase 콘솔에 추가

> \[ Unity Keysotre SHA-1 확인 방법 ]
>
> keystore 경로에서 keytool -keystore arzmetaLogin.keystore -list -v

{% hint style="warning" %}
구글 로그인이 잘되지 않을 경우, 아래의 경우들을 확인해보세요.

1. google-services.json 가 정상적으로 포함되어 있을지
2. 해당 소셜 로그인에 필요한 라이브러리가 정상적으로 빌드에 포함되어 있을지
   1. gamepot-channel-base.aar
   2. gamepot-channel-google-signin.aar
3. minify와 같은 기능이 설정되어 있으나 예외처리가 되지 않은 경우일지
4. 소셜 로그인 진행 전 게임팟 초기화가 되지 않은 부분일지
5. 구글 플레이 콘솔의 앱 서명 키 인증서 기능을 사용하고 있을지
6.  firebase sha-1 값과 빌드에 사용된 키의 sha-1 값이 일치하는지 (apk로 빌드하여 확인도 가능합니다)

    \-> 각 스토어 콘솔의 앱 서명으로 인해 sha-1값이 변경된 경우, 빌드에 사용되는 keystore가 변경된 경우에도 firebase 내 sha-1 값을 갱신해주어야 합니다.
7. 사용하는 라이브러리들은 nativeLibs 폴더가 아닌 libs 폴더로 이동시켜 사용하고, libs 폴더 전체가 빌드 시 포함되도록 적용하고 nativeLibs 폴더는 포함되지 않도록 적용
8. Google Console 내 Google Cloud의 OAuth 2.0 클라이언트 ID가 있어야 하며, API 및 서비스 > Auth 동의 화면 내 게시 상태가 프로덕션 단계여야 하며, 사용자 유형은 외부여야 한다.
9. [firebase 콘솔](broken-reference) > FCM, CMA 기존 방식이 사용 설정 되어 있을지 확인
10. [Gamepot 콘솔](gamepot.md#gamepot) > Auth Key > 구글에 client\_id가 잘 들어갔는지 확인(도움말)참조
{% endhint %}

<figure><img src="../../../.gitbook/assets/image (23).png" alt=""><figcaption><p>&#x3C; 5. 참고 이미지 > 빌드 시, 폴더를 비포함시키는 방법</p></figcaption></figure>

{% embed url="https://console.cloud.google.com/apis/credentials/consent?authuser=1&project=arzmeta-bacde" %}
< 6. 참고 링크 >
{% endembed %}

<figure><img src="../../../.gitbook/assets/image (24).png" alt=""><figcaption><p>&#x3C; 9. 참고 이미지 ></p></figcaption></figure>

#### Apple Login Setting

Apple Developer Identifiers 등록 순서 : AppID - Key(권한 있어야 보임) - Service ID

{% hint style="warning" %}
애플 로그인이 잘되지 않을 경우, 아래의 경우들을 확인해보세요.

1. GamePot 콘솔 > Auth Key > 애플 ID\
   GamePot 콘솔 > Apple ID Login (도움말 참조)
   1. Client ID : com.hancomfrontis.arzmeta.gamepot.services
   2. TEAM ID : Q2B2W3PV27
   3. KEY ID : 8656A9UB85
   4.  PRIVATE KEY

       ```
       -----BEGIN PRIVATE KEY-----
       ```

       ````
       ```
       ````

       ```
       -----END PRIVATE KEY-----
       ```

       ```
       를 포함한 p8의 모든 내용이 포함되어있는지 
       ```
2. Xcode에서 Clean Build 후, 재진행해보기
{% endhint %}

{% file src="../../../.gitbook/assets/AuthKey_8656A9UB85.p8" %}
< 1. AuthKey\_ .p8 파일 >
{% endfile %}

***

## GamePot InApp Purchase

### GamePot InApp Purchase 사용할 경우, 아래 패치를 적용하여 관련 버전 업그레이드 필요

{% file src="../../../.gitbook/assets/google_inappV5_0823_Unity.zip" %}

1\. 첨부파일의 4개 파일 ./Assets/Plugins/Android/libs 경로에 붙여넣기 및 교체

2\. 패치 후 billing-3.0.3.aar 파일은 삭제

***

## <mark style="background-color:yellow;">TroubleShooting</mark>

GamePot SDK를 통해 기능 개발하면서 겪었던 버그들을 정리해두었습니다. 비슷한 버그 발생 시, <mark style="background-color:yellow;">해당 TroubleShooting 문단과</mark> [<mark style="background-color:yellow;">상단의 GamePot 관련 핵심 페이지의 FAQ와 TroubleShooting 페이지</mark>](gamepot.md#gamepot)<mark style="background-color:yellow;">를 함께 참고</mark>하시면 됩니다.

### Unity AOS Build Error(<mark style="color:red;">에러</mark>, <mark style="color:blue;">해결책</mark>)

* <mark style="color:red;">mainTemplate.gradle file is using the old aaptOptions noCompress property definition which does not include types defined by unityStreamingAssets constant.</mark>

<figure><img src="../../../.gitbook/assets/image (18).png" alt=""><figcaption><p><mark style="color:blue;">aaptOptions 변경</mark></p></figcaption></figure>

* <mark style="color:red;">Found plugins with same names, Assets/Plugins/Android/libs/gamepot-channel-facebook.aar and Assets/Plugins/Android/nativeLibs/gamepot-channel-facebook.aar. Delete the one of the duplicate plugins.</mark>

<mark style="color:blue;">=> Assets/Plugins/Android/libs/gamepot-channel-facebook.aar, gamepot-channel-google-signin 삭제</mark>

*   <mark style="color:red;">Starting a Gradle Daemon, 1 incompatible Daemon could not be reused</mark>, use --status for details

    FAILURE: Build completed with 2 failures.

    1: Task failed with an exception.

    \-----------

    \* Where:

    Build file 'C:\work\arzMETA-CLIENT\Temp\gradleOut\unityLibrary\build.gradle' line: 50

    <mark style="color:red;">\* What went wrong:</mark>

    <mark style="color:red;">A problem occurred evaluating project ':unityLibrary'.</mark>

    <mark style="color:red;">> Project with path ':GamePotResources' could not be found in project ':unityLibrary'.</mark>

    \* Try:

    Run with --stacktrace option to get the stack trace. Run with --info or --debug option to get more log output. Run with --scan to get full insights.

    \==============================================================================

    2: Task failed with an exception.

    \-----------

    <mark style="color:red;">\* What went wrong:</mark>

    <mark style="color:red;">A problem occurred configuring project ':unityLibrary'.</mark>

    <mark style="color:red;">> compileSdkVersion is not specified. Please add it to build.gradle</mark>

    \* Try:

    Run with --stacktrace option to get the stack trace. Run with --info or --debug option to get more log output. Run with --scan to get full insights.

    \==============================================================================

    \* Get more help at [https://help.gradle.org](https://help.gradle.org)

    BUILD FAILED in 13s

    Picked up JAVA\_TOOL\_OPTIONS: -Dfile.encoding=UTF-8

    UnityEngine.GUIUtility:ProcessEvent (int,intptr,bool&)

<mark style="color:blue;">=> 폴더명 수정 && MAINTEMPLATE.gradle에서 경로 수정</mark>

<mark style="color:blue;">기존 : ../Assets/Plugins/Android/Firebase</mark>

<mark style="color:blue;">수정 : ../Assets/Plugins/Android/FirebaseApp.androidlib</mark>

<mark style="color:blue;">기존 : ../Assets/Plugins/Android/GamePotResources</mark>

<mark style="color:blue;">수정 : ../Assets/Plugins/Android/GamePotResources.androidlib</mark>

<mark style="color:blue;">implementation project('GamePotResources.androidlib')</mark>

<mark style="color:blue;">implementation project('FirebaseApp.androidlib')</mark>

*   CommandInvokationFailure: Gradle build failed.

    C:\Program Files\Unity\Hub\Editor\2020.3.35f1\Editor\Data\PlaybackEngines\AndroidPlayer\OpenJDK\bin\java.exe -classpath "C:\Program Files\Unity\Hub\Editor\2020.3.35f1\Editor\Data\PlaybackEngines\AndroidPlayer\Tools\gradle\lib\gradle-launcher-6.1.1.jar" org.gradle.launcher.GradleMain "-Dorg.gradle.jvmargs=-Xmx4096m" "assembleRelease"

    stderr\[

    FAILURE: Build failed with an exception.

    <mark style="color:red;">\* What went wrong:</mark>

    <mark style="color:red;">Execution failed for task ':launcher:lintVitalRelease'.</mark>

    <mark style="color:red;">> Configuration with name 'compileClasspath' not found.</mark>

<figure><img src="../../../.gitbook/assets/image (19).png" alt=""><figcaption><p><mark style="color:blue;">mainTemplate.gradle에 추가</mark></p></figcaption></figure>

* <mark style="color:red;">Could not delete old \[Path]\lint-results-release-fatal.html</mark>

<mark style="color:blue;">=> 유니티 재부팅</mark>

* <mark style="color:red;">CommandInvokationFailure: Unable to install APK to device. Please make sure the Android SDK is installed and is properly configured in the Editor.</mark>

<mark style="color:blue;">=> 폰에 설치된 앱 삭제</mark>

*   <mark style="color:red;">additional relocation overflows omitted from the output</mark>

    <mark style="color:red;">clang++.exe: error: linker command failed with exit code 1 (use -v to see invocation)</mark>

<mark style="color:blue;">=> Build Settings에서 Development Build, Script Debugging 끄면 됨(코드가 너무 커서 생기는 오류)</mark>

* <mark style="color:red;">IOException Error</mark>

<mark style="color:blue;">=> 작업관리자 adb.exe 작업끝내기</mark>

### iOS Build Error(<mark style="color:red;">에러</mark>, <mark style="color:blue;">해결책</mark>)

* <mark style="color:red;">Unity Build 시, Cocoapods 문제로 인한 Xcode.Workspace 생성안되는 문제</mark>

<mark style="color:blue;">=> Podfile 수정</mark>

```
source 'https://cdn.cocoapods.org/'

platform :ios, '11.0'

target 'UnityFramework' do
  pod 'Firebase/Analytics', :modular_headers => true
  pod 'Firebase/Core', :modular_headers => true
  pod 'Firebase/DynamicLinks', :modular_headers => true
  pod 'Firebase/Messaging', :modular_headers => true

  pod 'Firebase', :modular_headers => true
  pod 'FirebaseCore', :modular_headers => true
  pod 'GoogleUtilities', :modular_headers => true
end
```

{% hint style="info" %}
Unity 빌드 후, buildPath에 Podfile 복사 -> pod install -> .xcworkspace 열기까지 buildPath만 입력하면 자동으로 실행되게 Shell Script(podinstall.sh)가 작성되어있습니다. 두레이 프로젝트 위키를 참고해주시면 됩니다.
{% endhint %}

{% embed url="https://hancom.dooray.com/wiki/3447245024658035709/3453789867158525282" %}
두레이프로젝트 위키 - Unity iOS 빌드 방법
{% endembed %}

* <mark style="color:red;">Firebase Error</mark>

1. <mark style="color:blue;">별도의 firebase를 붙여서 사용하시는 경우 게임팟 기능을 위하여 이미 포함되어있는 일부 라이브러리와 충돌이 있을 수 있습니다. 아래 링크의 가이드 내용(https://docs.gamepot.io/undefined/gamepot\_troubleshooting#unity-firebase-sdk-firebase-unity-8.7.0)과 같이 중복되는 라이브러리 삭제</mark>
2. <mark style="color:blue;">FirebaseAnalytics.unitypackage, FirebaseMessaging.unitypackage(https://developers.google.com/unity/archive?hl=ko 에서 다운로드 가능) 적용 및 podfile 내 :modular\_headers => true 적용이 필요</mark>

<mark style="color:blue;">참고 페이지)</mark> [<mark style="color:blue;">https://docs.gamepot.io/undefined/gamepot\_troubleshooting#unity-firebase-sdk-firebase-unity-8.7.0</mark>](https://docs.gamepot.io/undefined/gamepot\_troubleshooting#unity-firebase-sdk-firebase-unity-8.7.0)

* <mark style="color:red;">MissingMethodException: System.Collections.Generic.IEnumerable\`1 Google.IOSResolver.GetXcodeTargetGuids(object,bool)</mark>

[https://github.com/firebase/quickstart-unity/issues/622](https://github.com/firebase/quickstart-unity/issues/622)

* <mark style="color:red;">/Users/hancomfrontis/Build\_iOS\_AOS/Libraries/Plugins/IOS/Source/dummy.swift Module map file '/Users/hancomfrontis/Library/Developer/Xcode/DerivedData/Unity-iPhone-bohyrgsrslxijdashypuesbquwpa/Build/Products/ReleaseForRunning-iphoneos/FirebaseCoreInternal/FirebaseCoreInternal.modulemap'</mark>

<mark style="color:blue;">=> Xcode.workspace에서 빌드</mark>

* <mark style="color:red;">UnityAppController 를 상속받는 파일이 하나만 있어야 함(GamePot의GamePotAppDelegate.h, Vuplex의 VXWebViewAppController.h) -> GamePot SDK가 정상적으로 초기화되지 않을 수 있음</mark>

<mark style="color:blue;">=> VXWebViewAppController가 UnityAppController를 상속받게 하고, GamePotAppDelegate.h가 VXWebViewAppController를 상속받게 수정함</mark>

* <mark style="color:red;">Invalid cliend id or web redirect url 에러</mark>

<mark style="color:blue;">=> 애플 콘솔 쪽 설정 오류로 Return URL 내용 정상적으로 설정되었는지 확인</mark>

* <mark style="color:red;">Error occurs from apple server</mark>

<mark style="color:blue;">=> Apple ID Login 입력 정보를 기반으로 애플 서버에서 전달받은 값이 일치하지 않아 발생하는 에러</mark>
