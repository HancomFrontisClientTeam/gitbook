---
description: 일반적으로 사용하는 싱글톤 클래스를 간편하게 생성&캐싱하는 클래스
---

# Singleton

* 몇가지 작업만 해주면 간단하게 싱글톤 클래스를 만들 수 있습니다.

{% hint style="warning" %}
싱글톤클래스 생성 시 꼭 알려주세요!!
{% endhint %}

***

{% hint style="success" %}
선언, 캐싱, 초기화, 호출
{% endhint %}

#### 선언

![](<../../.gitbook/assets/image (8) (2).png>) -->![](<../../.gitbook/assets/image (5) (2).png>)

#### 캐싱

> **Singleton.cs** 내부에 있는 **Single** 클래스 하위에 아래 구문 추가

<div align="left">

<figure><img src="../../.gitbook/assets/image (9) (1) (1).png" alt=""><figcaption></figcaption></figure>

</div>

#### 초기화

> **Scene\_Logo.cs** 하위에 **InitSingleton** 함수에 아래 구문 추가

<div align="left">

<figure><img src="../../.gitbook/assets/image (5) (2) (1).png" alt=""><figcaption></figcaption></figure>

</div>

#### 호출

> **Single.xxx.** 으로 사용

![](<../../.gitbook/assets/image (6) (1) (1).png>)
