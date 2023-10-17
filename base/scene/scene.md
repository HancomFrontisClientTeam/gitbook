---
description: 씬 구성도
---

# Scene 구성도

## 1. Naming

* 공통 네이밍 : '**Scene\_**' + 씬의 특성에 따른 구분 + 씬 이름

<table><thead><tr><th width="140">구분</th><th>설명</th></tr></thead><tbody><tr><td>Land_</td><td>최상위 계층. 인 월드의 허브가 되는 씬. (예 - Arz, Busan)</td></tr><tr><td>Zone_</td><td>중간 계층. Land의 하위 씬. 사용자가 공용으로 사용하는 건물 내부나 공간 등에 붙인다. (예 - GameZone, Store)</td></tr><tr><td>Room_</td><td>하위 계층. Zone의 하위 씬. 사용자 혹은 일부가 개인적 및 소규모로 사용하는 공간 등에 붙인다. (예 - MyRoom)</td></tr></tbody></table>



## 2. Out World

{% hint style="info" %}
* 로고 => 로그인 및 계정 생성까지의 플로우
* 플레이어가 생성되지 않고 UI 화면만 조작한다.
{% endhint %}

#### 1 ) Scene\_Base\_Logo 로고씬

<table data-full-width="false"><thead><tr><th width="204">구분</th><th>실행 작업</th></tr></thead><tbody><tr><td>● Awake</td><td><ul><li>그래픽 퀄리티 세팅 </li><li>기본 시스템 언어 로드</li></ul></td></tr><tr><td>● PreMaster Load</td><td><ul><li>디버그 UI 비/활성화 여부를 위한 세팅 </li><li>(프리팹명: IngameDebugConsole)</li></ul><p><img src="../../.gitbook/assets/image (17).png" alt=""><img src="../../.gitbook/assets/image (14).png" alt=""></p></td></tr><tr><td>● 로고씬 진행</td><td><ul><li>로고 스플레쉬</li><li>싱글톤 초기화</li><li>그 외 초기화가 필요한 작업</li><li><a href="../manager/webmanager.md">WebManager</a> 초기화</li><li><a href="../manager/masterdatamanager.md">MasterDataManager</a> 초기화</li></ul></td></tr></tbody></table>

1-1 ) [WebManager](../manager/webmanager.md)

* 초기화 시 게이트웨이 데이터 다운로드
* ★ 정상적으로 데이터를 받을 시에만 로고씬 진행 가능
* 진행 불가능 시 에러 팝업 푸쉬

1-2 ) [MasterDataManager](../manager/masterdatamanager.md)

* 마스터 데이터 다운로드 및 파싱
* ★ 로컬라이징 데이터 포함 파싱

{% hint style="info" %}
마스터 데이터 초기화 이전에는 기존 로컬라이징 데이터를 사용할 수 없으므로 해당 로직 이전에 SystemLocalization을 로드하여 사용 중이다.
{% endhint %}

#### 2 ) Scene\_Base\_Title 타이틀씬

{% hint style="info" %}
로그인(아즈메타, 소셜), 비밀번호 찾기, 신규 계정 생성, 자동로그인 등의 작업 수행
{% endhint %}

* 흐름도

<img src="../../.gitbook/assets/file.excalidraw (2).svg" alt="" class="gitbook-drawing">

#### 3 ) Scene\_Base\_Lobby

{% hint style="info" %}
신규 계정 생성 시 해당 씬에서 아바타 및 프로필 생성
{% endhint %}

3-1 ) 아바타 프리셋 선택

* (2023.10.16 기준) 4개의 프리셋이 있다.
* 마스터 데이터의 프리셋 시트에서 데이터를 가져와 사용한다.

3-2 ) 아즈 프로필 생성

* 닉네임 : 중복 불가능, 금칙어 불가능, 2\~12 자 영어 대소문자, 한글 음절 및 초성, 숫자 입력 가능
* 상태메시지 : 최대 120자. 제한 없음.

```csharp
// 상태메시지를가입력하지 않는다면 Null일 수 있다. 
// 이 때에는 Null 체크를 하여 로컬라이징을 넣어주어야 한다.
if (string.IsNullOrEmpty(data.stateMessage))
{
    // 반가워요! 저는 {0} 입니다!\n우리 모두 아즈랜드에서 만나요!
    Util.SetMasterLocalizing(txtmp_StateMessage, new MasterLocalData("3066", data.nickname));
}
else
{
    Util.SetMasterLocalizing(txtmp_StateMessage, data.stateMessage);
}
```

#### 3 ) Scene\_Base\_Patch&#x20;

{% hint style="info" %}
로딩, 어드레서블 패치 등 다운로드 및 로드 요소가 있을 시 사용하는 씬
{% endhint %}

* 현재 사용하지 않음



## 3. In World

* **플레이어가 존재**하며 돌아다니며 **멀티플레이**
* 계층관계 : Land > Zone > Room 순
* Scene\_Room\_MyRoom
  * Out World에서 In World로 전환되는 최초 씬
  * 마이룸 꾸미기, 액자친꾸미기, 친구 초대하기 ...
* Scene\_Land\_Arz
  * 모든 In World의 허브 씬
  *
* Scene\_

<img src="../../gitbook/.gitbook/assets/file.excalidraw (4).svg" alt="씬 흐름도" class="gitbook-drawing">
