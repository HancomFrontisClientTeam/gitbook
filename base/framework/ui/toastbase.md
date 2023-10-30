---
description: 토스트 팝업 관련 베이스 클래스
---

# ToastBase

{% hint style="info" %}
아즈메타 내의 기본토스트 팝업 정의

* 상단 구역에서 보인다
* 떴다가 일정 시간(변동 가능)이 지난 후 자동으로 꺼지는 팝업
* 다른 UI를 막지 않는 팝업
* 기존 팝업과는 달리 스택에 쌓이지 않는다
* 여러 개의 토스트 팝업이 동시에 호출 시 코루틴에 담겨 순차적으로 실행
{% endhint %}

<figure><img src="../../../.gitbook/assets/ToastPopup.gif" alt=""><figcaption><p>예시 - Toast_Basic</p></figcaption></figure>

## 1. 실행 구조도

<img src="../../../.gitbook/assets/file.excalidraw (13).svg" alt="" class="gitbook-drawing">

## 2. 중요 메소드

| 변수                                | 설명                                                                                                                                                                                |
| --------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| public bool isTopPriority = false | <ul><li>다른 토스트 팝업보다 우선시 되어 떠야할 경우 true 체크</li><li>체크된 토스트 팝업이 호출되면 현재 열린 토스트 팝업이 꺼지며 이후 코루틴에 등록된 토스트 팝업이 전부 취소된다 </li><li>(기획팀과 상의 되지 않은 기능이라 임의로 로직을 작성하였음. 차후 수정 요망.)</li></ul> |

<details>

<summary>public virtual void ChainToastData(object data)</summary>

```csharp
// 외부에서 토스트 호출 시 데이터를 입력하는 public 메소드

// 예시
SceneLogic.instance.OpenToast<Toast_Basic>()
.ChainToastData(new ToastData_Basic(TOASTICON.Current, new MasterLocalData("5201")));

// 본 메소드
public virtual void ChainToastData(object data) 
{ 
    if (isTopPriority) ClearCoroutine();
    // 토스트가 호출되어 데이터가 들어올 시 직렬 코루틴 Queue에 추가되어 차례로 실행.
    Util.ProcessQueue_Coroutine(Cons.Toast, Co_ToastPopup(data), tag: Cons.Toast);
}
```

</details>

<details>

<summary>protected virtual void SavaData(object data) { }</summary>

<pre class="language-csharp"><code class="lang-csharp">// data 파라미터에서 받은 데이터는 
// 자식 클래스에서 해당 토스트 팝업 데이터 형식에 맞는 클래스로 정보 변환

// 예시 Toast_Basic
protected override void SavaData(object data)
{
    // ToastData_Basic은 Toast_Basic 전용으로 사용되는 데이터 클래스
    if (data is <a data-footnote-ref href="#user-content-fn-1">ToastData_Basic </a>_data)
    {
        // 자식 클래스에서 해당 데이터 변수 저장
        toastData = _data;
        curToastTime = toastData.time;
    }
}
</code></pre>

</details>

<details>

<summary>protected virtual void SetContent() { }</summary>

```csharp
// SavaData에서 받은 데이터로 UI를 세팅한다
// 자식 클래스에서 작성

// 예시 Toast_Basic
protected override void SetContent()
{
    if (txtmp_Title != null)
    {
        if (!string.IsNullOrEmpty(toastData.descText))
        {
            Util.SetMasterLocalizing(txtmp_Title, toastData.descText);
        }
        else if (toastData.descLocal != null)
        {
            Util.SetMasterLocalizing(txtmp_Title, toastData.descLocal);
        }
    }
    ...
}
```

</details>

<details>

<summary>public void ToastPopupOpen(object data)</summary>

```csharp
// 토스트 팝업 열기
// 기존 OpenToast와는 달리 직렬 코루틴에 추가하지 않는다
// data를 직접 넣으므로 ChainToastData를 하지 않아도 된다
```

</details>

<details>

<summary>public void ToastPopupClose()</summary>

```csharp
// 토스트 팝업 닫기
// 버튼을 누르거나 했을 때 토스트 팝업이 닫혀야 한다면 호출
```

</details>

<details>

<summary>public IEnumerator Co_ToastPopup(object data)</summary>

```csharp
// 토스트 팝업 코어 메소드
// 직렬 코루틴에 등록되는 코루틴

// 직렬 코루틴에 데이터까지 포함되어 등록해야하므로 data를 받는다
public IEnumerator<float> Co_ToastPopup(object data)
{
    // 데이터 저장 및 세팅
    SavaData(data);
    SetContent();

    if (curToastTime < 0f) yield break;

    // 토스트 열고 닫기
    yield return Timing.WaitUntilDone(Co_OpenToastPopup());
    yield return Timing.WaitUntilDone(Co_CloseToastPopup());
}
```

</details>

<details>

<summary>protected virtual void ClearCoroutine()</summary>

```csharp
// 직렬 코루틴 Queue에 등록된 모든 토스트 삭제
```

</details>

## 3. 응용 방법

* ToastBase를 상속받아서 활용하는 방법
* ToastBase는 UIBase를 상속받아 UIFramework를 사용할 수 있다.
* ToastBase 기본 기능: 토스트 팝업 오픈&클로즈 애니메이션, 아이콘 로드 가상 함수
* 기본 기능 외 추가 기능은 각 Toast 마다 다르므로 각 스크립트에서 구현한다.

{% tabs %}
{% tab title="예시 - Toast_Basic" %}
* 추가  기능: 팝업 desc 텍스트, 토스트 아이콘

<figure><img src="../../../.gitbook/assets/ToastPopup.gif" alt=""><figcaption><p>예시 - Toast_Basic</p></figcaption></figure>

* 데이터 클래스

<figure><img src="../../../.gitbook/assets/image (60).png" alt=""><figcaption></figcaption></figure>

1\) SetMemberUI 오버라이딩

<pre class="language-csharp"><code class="lang-csharp">    protected override void <a data-footnote-ref href="#user-content-fn-2">SetMemberUI</a>()
    {
        base.SetMemberUI(); // ToastBase에서 오버라이딩 중이기 때문에 필수로 작성해야함

        #region TMP_Text
        txtmp_Title = GetUI_TxtmpMasterLocalizing("txtmp_Title");
        #endregion
    }
</code></pre>

2\) SavaData 오버라이딩

```csharp
// SetData 함수를 공동으로 사용하기 위해 object로 받고
// 해당 스크립트에서 필요한 형태로 변환하여 사용한다.
// 외부 호출 시 SetData에 해당 데이터 클래스를 넣는다.
    protected override void SavaData(object data)
    {
        // ToastData_Basic는 Toast_Basic 전용 데이터 클래스
        if (data is ToastData_Basic _data)
        {
            toastData = _data;
            curToastTime = toastData.time;
        }
    }
```

3\) SetContent 오버라이딩

```csharp
// SavaData에서 캐싱한 데이터로 UI 세팅
    protected override void SetContent()
    {
        if (txtmp_Title != null)
        {
            if (!string.IsNullOrEmpty(toastData.descText))
            {
                Util.SetMasterLocalizing(txtmp_Title, toastData.descText);
            }
            else if (toastData.descLocal != null)
            {
                Util.SetMasterLocalizing(txtmp_Title, toastData.descLocal);
            }
        }

       ...
    }
```
{% endtab %}

{% tab title="예시 - Toast_TwoBtn " %}
* 추가 기능: 팝업 desc 텍스트, 버튼 두 개, 버튼 액션, 카운트다운

<figure><img src="../../../.gitbook/assets/ToastPopupTwoBtn.gif" alt=""><figcaption><p>예시 - Toast_TwoBtn</p></figcaption></figure>

* 데이터 클래스

<figure><img src="../../../.gitbook/assets/image (61).png" alt=""><figcaption></figcaption></figure>

1\) SetMemberUI 오버라이딩

```csharp
    protected override void SetMemberUI()
    {
        base.SetMemberUI(); // ToastBase에서 오버라이딩 중이기 때문에 필수로 작성해야함

        #region TMP_Text
        txtmp_Title = GetUI_TxtmpMasterLocalizing(nameof(txtmp_Title));
        txtmp_Confirm = GetUI_TxtmpMasterLocalizing(nameof(txtmp_Confirm), defaultConfirmLocal);
        txtmp_Cancel = GetUI_TxtmpMasterLocalizing(nameof(txtmp_Cancel), defaultCancelLocal);
        txtmp_Seconds = GetUI_TxtmpMasterLocalizing(nameof(txtmp_Seconds));
        #endregion

        ...
    }
```

2\) SavaData 오버라이딩

```csharp
// SetData 함수를 공동으로 사용하기 위해 object로 받고
// 해당 스크립트에서 필요한 형태로 변환하여 사용한다.
// 외부 호출 시 SetData에 해당 데이터 클래스를 넣는다.
    protected override void SavaData(object data)
    {
        // ToastData_TwoBtn은 Toast_TwoBtn 전용 데이터 클래스
        if (data is ToastData_TwoBtn _data)
        {
            toastData = _data;
            curToastTime = toastData.time;
        }
    }
```

3\) SetContent 오버라이딩

```csharp
// SavaData에서 캐싱한 데이터로 UI 세팅
    protected override void SetContent()
    {
        // 초기화
        InitData();

        SoundNomal();

        if (txtmp_Title != null)
        {
            Util.SetMasterLocalizing(txtmp_Title, toastData.titleLocal);
        }

        if (toastData.action != null)
        {
            confirmAction = toastData.action.confirmAction;
            cancelAction = toastData.action.cancelAction;
        }
       ...
    }
```
{% endtab %}
{% endtabs %}

## 4. 사용(호출) 방법

* 기본 호출 메소드  OpenToast\<T>()

<pre><code>// 팝업과는 달리 ChainToastData를 해주어야 토스트가 실행되기 때문에 필수로 넣어주어야 한다.
// OpenToast만 하면 해당 타입명으로 리턴되어 Get처럼 사용할 수 있다

// 1) SceneLogic에 있는 OpenToast가 오리진
// UIBase를 상속받지 않은 스크립트에서 토스트를 호출할 때 사용
<strong>SceneLogic.instance.OpenToast&#x3C;T>().ChainToastData(new 토스트데이터클래스());
</strong>
// 2) UIBase의 OpenToast
// UIBase에서 SceneLogic쪽의문 OpenToast를 끌어다 사용
// SceneLogic.instance. 생략 가능
OpenToast&#x3C;T>().ChainToastData(new 토스트데이터클래스());
</code></pre>

[^1]: ```
    ToastData_Basic 
    ```

[^2]: 
