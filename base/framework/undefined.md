---
description: 웹 API 통신 및 Http 통신, 웹소켓 에러 발생 시 에러 팝업 노출
---

# 공통 에러 팝업

* 예시API나 웹소켓 등 리퀘스트 시 에러가 발생함을 알릴 때 필요
* Popup\_Basic에 속해 있으며, 기본 형태이다.

<figure><img src="../../.gitbook/assets/image (118).png" alt=""><figcaption><p> 예시 - 로그인 비밀번호 비일치 시 에러</p></figcaption></figure>



## 1. 메소드 흐름 구조도

<img src="../../.gitbook/assets/file.excalidraw (21).svg" alt="" class="gitbook-drawing">

## 2. 구분

### 1) API 에러 팝업

* 서버팀 제공 API 관련 에러 팝업 (WebManager.cs 참고)

{% hint style="info" %}
일반적으로 API 호출 시 에러를 반환할 때 약속된 Error Code 외에 의도적으로  Http 에러를 함께 내어 WebRequest 과정에서 Catch문으로 빠지게 만든다. 그래야 Success 외의 값이 Error 처리되기 때문이다. Http 에러는 보통 403 등으로 온다.&#x20;

일부  API 리스폰스의 경우 Success가 아닌데도  Http 에러를 내지 않고 오는 것이 있다.&#x20;

(View\_Account.cs 계정 연동 - NET\_E\_ALREADY\_LINKED\_OTHER\_ACCOUNT)
{% endhint %}

#### ● 호출 방법

```csharp
// CheckResponseError를 통해 자체적으로 데이터 세팅 후 PushPopup을 해주기 때문에
// GetPopup으로 가져온 뒤 메소드를 호출해준다.
// 매개변수는 int.
SceneLogic.instance.GetPopup<Popup_Basic>().CheckResponseError(response.error);
```

#### ● 신규 추가 방법

1\) WebError.cs에 Enum 추가

{% hint style="info" %}
&#x20;[arzMETA-LIBRARY/libs/constants/src/index.ts](https://github.com/arzMETA/arzMETA-LIBRARY/blob/main/libs/constants/src/index.ts)&#x20;

arzMETA 프로젝트 레포지토리에 서버측이 에러 상수 업데이트
{% endhint %}

<figure><img src="../../.gitbook/assets/image (119).png" alt=""><figcaption></figcaption></figure>

2\) Popup\_Basic.cs의 CheckResponseError 메소드에 case 추가

<figure><img src="../../.gitbook/assets/image (120).png" alt=""><figcaption></figcaption></figure>

{% hint style="warning" %}
신규 에러 추가에 따른 로컬라이징이 필요할 경우 기획팀에 문의
{% endhint %}

### 2) Http 서버 에러 팝업

* 일반적으로 사용되는 Http 서버 관련 에러 팝업 (WebManager.cs 참고)

#### ● 호출 방법

```csharp
// CheckHttpResponseError를 통해 자체적으로 데이터 세팅 후 PushPopup을 해주기 때문에
// GetPopup으로 가져온 뒤 메소드를 호출해준다.
// 매개변수는 string.
SceneLogic.instance.GetPopup<Popup_Basic>().CheckHttpResponseError(_message);
```

#### ● 신규 추가 방법

1\) API 리스폰스 시 약속된 Error Code와 같이 오는 Http 에러가 아닐 시 체크

2\) 에러 팝업 처리가 필요한 Http 에러일 경우 추가

<figure><img src="../../.gitbook/assets/image (121).png" alt=""><figcaption></figcaption></figure>

### 3) Web Socket 에러 팝업

* 서버팀 제공 웹소켓 관련 에러 팝업 (SocketManager.cs 참고)

{% hint style="info" %}
※ bool 값을 반환하는 이유

오류 발생 시 해당 오류 이후 코드가 실행되지 않아야 할 때 If문을 통해 return 한다.
{% endhint %}

#### ● 호출 방법

```csharp
// CheckWebSocketResponseError를 통해 자체적으로 데이터 세팅 후 PushPopup을 해주기 때문에
// GetPopup으로 가져온 뒤 메소드를 호출해준다.
// 매개변수는 string.

// 반환 필요 없을 시
SceneLogic.instance.GetPopup<Popup_Basic>().CheckWebSocketResponseError(message);
// 반환 필요 시
if (SceneLogic.instance.GetPopup<Popup_Basic>().CheckWebSocketResponseError(targetFriendData.code.ToString())) return;
```

#### ● 신규 추가 방법





웹소켓 깃북

이넘 추가

스위치문 추가
