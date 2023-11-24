---
description: IOS빌드방법에 대해 기술한다.
cover: >-
  https://www.macworld.com/wp-content/uploads/2023/09/ios-17-logo-on-an-iPhone-4.jpg?quality=50&strip=all
coverY: 0
layout:
  cover:
    visible: true
    size: hero
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# 📱 IOS Build

**1. 플랫폼 변경 (**※ 이미 해당 플랫폼이라면 생략**)**

* File => Build Setting의 Platform 항목에서 IOS플랫폼으로 변경 (현재 플랫폼이 아닐 시 Switch Platform 클릭)

<figure><img src="../../.gitbook/assets/image (124).png" alt=""><figcaption></figcaption></figure>

* iOS 로 플랫폼 스위칭 하기
  * 변경할 플랫폼으로 iOS 를 선택 한다.
  * Switch Platform 버튼을 눌러준다

<figure><img src="../../.gitbook/assets/image (125).png" alt=""><figcaption></figcaption></figure>

**2. IOS 빌드 설정**

2-1. 회사 이름 / 앱 이름 / 앱 버전 설정 하기

* Gateway 에 설정된 버전에 맞춰 앱 버전 넘버를 설정해 준다. \
  **(TestFlight 업로드를 위해서는 이전 업로드 버전보다 빌드 버전을 높게 설정해야 한다.)**

2-2. 빌드 버전 설정

<figure><img src="../../.gitbook/assets/image (126).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (127).png" alt=""><figcaption></figcaption></figure>

~~3-3. Identification 설정~~ (자동 적용됨)

*   Apple 개발자 콘솔에서 Team ID, Bundle ID 정보 얻어오기

    * 애플 콘솔 접속하기 [https://developer.apple.com/](https://developer.apple.com/)
    * 로그인 후 Account 선택

    <figure><img src="../../.gitbook/assets/image (128).png" alt=""><figcaption></figcaption></figure>

    * 만일 HANCOM FRONTIS Coporation 프로젝트가 보이지 않는다면 회사 애플계정 관리자에게 자신의 Apple ID를 개발자 등록 요청을 해야 한다
* Certificates, Identifiers & Profiles 선택

<figure><img src="../../.gitbook/assets/image (129).png" alt=""><figcaption></figcaption></figure>

* 좌측 메뉴 리스트에서 Identifiers 메뉴 선택

<figure><img src="../../.gitbook/assets/image (130).png" alt=""><figcaption></figcaption></figure>

* 현재 빌드 하려고 하는 프로젝트에 맞는 App ID 를 선택해 준다(위 이미지 빨간색 라인
* Team ID 와 Bundle ID가 유니티 빌드시 필요한 값 이기 때문에 기억해 둔다.

<figure><img src="../../.gitbook/assets/image (131).png" alt=""><figcaption></figcaption></figure>

* Bundle Identifier, TeamID 등을 설정
  * 첨부된 스크린샷 처럼 설정 해 주면 된다.

<figure><img src="../../.gitbook/assets/image (132).png" alt=""><figcaption></figcaption></figure>

* Agora 사용을 위한, Camera, Microphone 사용에 대한 메시지 넣어주기
  * Camera Usage Description: This app use the camera to use video chat.
  * Microphone Usage Description: This app use microphone to use voice chat.

<figure><img src="../../.gitbook/assets/image (133).png" alt=""><figcaption></figcaption></figure>

**3. iOS Post Build Process 설정 하기**

* **CustomBuildProcessor.cs** 파일
* 현재 기본적인 부분은 세팅이 되어 있으니 코드를 확인
* iOS 빌드에 필요한 추가 요소가 있을 경우 코드 수정

**4. Gpresto iOS 설정하기**

<figure><img src="../../.gitbook/assets/image (134).png" alt=""><figcaption></figcaption></figure>

\*\* 현재 병합 편의를 위해 AOS와 동일한 브랜치를 사용함으로써 Mac Studio에서 iOS에 맞게 설정한 뒤 변경점을 Commit하지 않고 있는 상태임.\
**따라서, 아래 변경점에 대해서 Commit을 하지도 변경점 취소도 하지 말 것**

<figure><img src="../../.gitbook/assets/image (135).png" alt=""><figcaption></figcaption></figure>

**5. Xcode 프로젝트 추출(빌드) 하기**

<figure><img src="../../.gitbook/assets/image (136).png" alt=""><figcaption></figcaption></figure>

**6. GAMEPOT SDK 라이브러리 사용을 위한 설정 하기**

* 아즈메타 유니티 프로젝트 폴더를 오른쪽 버튼을 눌러 "폴더에서 새로운 터미널 열기"

<figure><img src="../../.gitbook/assets/image (137).png" alt=""><figcaption></figcaption></figure>

* 터미널에서 다음 컴맨드를 입력해 실행
  * ./podinstall.sh
* 유니티에서 빌드해 나온 Xcode Project 경로를 지정

<figure><img src="../../.gitbook/assets/image (138).png" alt=""><figcaption></figcaption></figure>

**7. Xcode 빌드 및 실행 하기**

**1-1.** Xcode 에서 프로젝트 열기

* 빌드가 완료된 폴더를 들어가서 **Unity-iPhone.xcworkspace** 파일을 열어준다.

<figure><img src="../../.gitbook/assets/image (139).png" alt=""><figcaption></figcaption></figure>

* Xcode 에서 앱 이름 및 빌드 버전 확인하기

<figure><img src="../../.gitbook/assets/image (140).png" alt=""><figcaption></figcaption></figure>

* 구글 로그인 권한 팝업에 뜨는 앱 이름 수정
  * Build Settings - User-Defined - PRODUCT\_NAME\_APP 변경
    * ProductName -> arzMETA\
      \*\* (영어로 하지 않으면 GamePot 관련 에러가 발생할 수 있음)

<figure><img src="../../.gitbook/assets/image (141).png" alt=""><figcaption></figcaption></figure>

* 아이폰 연결 및 빌드 하기
  * 아이폰을 연결하면 스크린샷 처럼 내 Device 가 목록에 있는걸 볼 수 있다.

<figure><img src="../../.gitbook/assets/image (142).png" alt=""><figcaption></figcaption></figure>

* info.plist 수정 (XCode15 대응)\*\*\*\*\*
  * ios http 보안이슈 때문
  * Allows Arbitrary Loads for Media 제거

<figure><img src="../../.gitbook/assets/image (143).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (144).png" alt=""><figcaption></figcaption></figure>

* 참고자료 : [http://milennium9.godohosting.com/wordpress/?p=313](http://milennium9.godohosting.com/wordpress/?p=313)



* Other Linker Flags 수정 (XCode15 대응) \*\*\*\*\*
  * 버튼 누른후 -ld64 추가 (ID아니고 LD임)

<figure><img src="../../.gitbook/assets/image (145).png" alt=""><figcaption><p><br></p></figcaption></figure>

참고자료

* [https://developer.apple.com/documentation/xcode-release-notes/xcode-15-release-notes#Linking](https://developer.apple.com/documentation/xcode-release-notes/xcode-15-release-notes#Linking)
* [https://forum.unity.com/threads/xcode-15-linker-error.1502147/](https://forum.unity.com/threads/xcode-15-linker-error.1502147/)
* [github.com/firebase/firebase-ios-sdk/issues/11516](http://github.com/firebase/firebase-ios-sdk/issues/11516)
* [https://developer.apple.com/forums/thread/735426](https://developer.apple.com/forums/thread/735426)



* 빌드 및 실행하기
  * 아이폰을 연결한 상태에서 플레이 버튼을 누르면, 빌드 + install 과정을 통해 아이폰으로 실행할 수 있다.
  * 스크린샷 빨간색 테두리 오른쪽 하단 휴지통을 누르면 기존 로그가 삭제 된다.
  * 스크린샷 빨간색 테두리 오른쪽 하단 휴지통 오른쪽 파란색 버튼을 누르면, 왼쪽 윈도우가 비활성화 되어 로그창을 더 크게 볼 수가 있다.

<figure><img src="../../.gitbook/assets/image (147).png" alt=""><figcaption></figcaption></figure>



* 디버깅 하기
  * 아이폰이 연결된 상태에서 플레이 버튼을 누르면, Xcode 하단 오른쪽 창에서 로그를 확인 할 수 있다.

<figure><img src="../../.gitbook/assets/image (148).png" alt=""><figcaption></figcaption></figure>



**8. 테스트 플라이트에 배포하기**

* 상단 메뉴의 Product - Archive하면 아래 팝업이 뜨면 Distribute App 클릭 후 순차적으로 다음 클릭 (만일 아래 팝업을 닫았다면 상단 메뉴의 Window - Organizer 클릭)

<figure><img src="../../.gitbook/assets/image (149).png" alt=""><figcaption></figcaption></figure>

**9. 심사 올리기**

* App Store Connect 페이지에 로그인([https://appstoreconnect.apple.com](https://appstoreconnect.apple.com)) 후, 업로드 하고자하는 앱(아즈메타) 선택
* iOS 앱 옆에 파란 + 클릭 (파란 + 는 업데이트할 게 있는 경우에만 뜬다)

<figure><img src="../../.gitbook/assets/image (150).png" alt=""><figcaption></figcaption></figure>

* 앱스토어에 업로드할 버전 번호 입력(이전 번호보다 높아야 함) 후 생성 버튼 클릭

<figure><img src="../../.gitbook/assets/image (151).png" alt=""><figcaption></figcaption></figure>

* 하단 빌드 섹션에 파란색 빌드 추가버튼 클릭

<figure><img src="../../.gitbook/assets/image (152).png" alt=""><figcaption></figcaption></figure>

* 최근 빌드 버전으로 선택 후 완료 버튼 -> 오른쪽 상단에 저장버튼 -> 기획팀에 버전 정보 작성 요청 (심사에 추가)

<figure><img src="../../.gitbook/assets/image (153).png" alt=""><figcaption></figcaption></figure>
