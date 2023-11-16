---
description: 오브젝트 재활용 스크롤 뷰
---

# 07\_ScrollRect

#### ○ 해당 스크롤 뷰를 사용하는 경우

* 일반적인 스크롤 뷰 기능이 필요할 시
* Loop가 필요하지 않을 시 (해당 스크롤뷰는 Loop 기능이 지원되지 않음)

<figure><img src="../../../../.gitbook/assets/ScrollRect.gif" alt=""><figcaption></figcaption></figure>

***

<details>

<summary>목차</summary>

[#undefined-2](07\_scrollrect.md#undefined-2 "mention")

[#undefined-3](07\_scrollrect.md#undefined-3 "mention")

[#scrollview\_custom](07\_scrollrect.md#scrollview\_custom "mention")

[#undefined-4](07\_scrollrect.md#undefined-4 "mention")

</details>

***

## ● 구조도

<img src="../../../../.gitbook/assets/file.excalidraw (10).svg" alt="" class="gitbook-drawing">

***

## ● 주요 변수 및 기능 메소드

#### ▷ ScrollView.cs

<table><thead><tr><th width="418.3333333333333">변수</th><th>설명</th></tr></thead><tbody><tr><td>[SerializeField] float cellSize = 100f;</td><td>CellPrefab에 연결한 프리팹의 높이값과 일치시켜주어야 한다.</td></tr><tr><td>[SerializeField] GameObject cellPrefab = default;</td><td>FancyScrollRectCell을 상속받은 스크립트를 가진 프리팹을 연결</td></tr><tr><td>public int DataCount => ItemsSource.Count;</td><td>현재 생성된 셀의 총 개수를 알 수 있다.</td></tr></tbody></table>

<details>

<summary>public void OnCellClicked(Action&#x3C;int> callback)</summary>

```csharp
// 셀 선택 시 액션 호출
scrollview.OnCellClicked((int) => { /*선택된 셀의 인덱스가 넘어온다*/ });
```

</details>

<details>

<summary>public void UpdateData(IList items)</summary>

```csharp
// 아이템(셀)을 스크롤뷰에 생성
// 기존 아이템 전부 삭제 후 새로 받은 아이템으로 다시 생성

List<ItemData> datas = new List<ItemData>();
datas.add(new ItemData()); // 아이템 전체 데이터를 리스트로 만든다

scrollview.UpdateData(datas);
```

</details>

<details>

<summary>public void ScrollTo(int index, float duration, Ease easing, Alignment alignment = Alignment.Middle)</summary>

```csharp
// 지정한 인덱스의 셀까지 스크롤링
// (지정한 인덱스, 걸리는 시간, 이동 방식, 기준 피벗)

scrollView.ScrollTo(1, 0.3f, Ease.InOutQuint, Alignment.Lower);
```

</details>

<details>

<summary>public void JumpTo(int index, Alignment alignment = Alignment.Middle)</summary>

```csharp
 // 지정한 인덱스의 셀까지 바로 스크롤
 // (지정한 인덱스, 기준 피벗)
 
 scrollView.JumpTo(10, Alignment.Lower);
```

</details>

#### ▷ Cell.cs

<table><thead><tr><th width="325">변수</th><th>설명</th></tr></thead><tbody><tr><td>public int Index { get; set; }</td><td>오브젝트의 고유 인덱스</td></tr><tr><td>public virtual bool IsVisible { get; }</td><td>오브젝트가 하이라키에서 활성화 상태인지의 여부</td></tr><tr><td>protected TContext Context { get; }</td><td>선택된 셀 인덱스 및 셀 클릭 액션</td></tr></tbody></table>

<details>

<summary>public override void Initialize()</summary>

```csharp
// Awake의 역할
// 버튼이 있을 시 AddListener 등록
public override void Initialize()
{
    button.onClick.AddListener(() => Context.OnCellClicked?.Invoke(Index));
}
```

</details>

<details>

<summary>public override void UpdateContent(ItemData itemData)</summary>

<pre class="language-csharp"><code class="lang-csharp">// 생성 및 하이라키에서 활성화 됐을 시 해당 메소드 호출
// 호출 될 때마다 데이터가 들어온다
// 데이터를 받아와 처리하는 코드를 작성

public override void UpdateContent(ItemData itemData)
{
<strong>    message.text = itemData.Message;
</strong>
    var selected = Context.SelectedIndex == Index;
    image.color = selected 
        ? new Color32(0, 255, 255, 100)
        : new Color32(255, 255, 255, 77);
}
</code></pre>

</details>

<details>

<summary>protected override void UpdatePosition(float normalizedPosition, float localPosition)</summary>

```csharp
// 해당 셀의 위치값을 얻을 수 있다.
```

</details>

***

## ● 실무 사용 ScrollView\_Custom

(feat. 한효주 주임 연구원)

#### ○ 스크립트명: ScrollView\_Custom

#### ○ 위치: Assets\\\_DEV\Script\UI\Scroll\UIExtensions\_Custom\ScrollView\_Custom

{% hint style="warning" %}
기존 에셋 사용 시 ScrollView\<ItemData, Context>와 같이 TItemData의 형태를 ScrollView에서 지정하고 있다.

이 경우, 여러 곳에서 다른 형태의 Data를 사용하기 위해서는 부모 데이터 클래스를 상속받아 자식 데이터 클래스에서 필요 데이터를 추가 하는 형태로 변경되어야 한다.

따라서 Custom 스크립트로 파생하여, 빈 클래스인 Item\_Data를 FancyScrollRect의 TItemData 속성으로 지정하고, 자식데이터 클래스에서 상속받는 방식으로 변경하였다.
{% endhint %}

### ● 추가주요 변수 및 기능 메소드

#### ▷ ScrollView\_Custom.cs

상속한 FancyScrollRect\<TItemData, TContext> 클래스에 각 \<Item\_Data, Context>을 지정하여 상속받은 것 외 추가 변경 없음.

![](<../../../../.gitbook/assets/image (13).png>)

#### ▷ FancyScrollRectCell\_Custom.cs

상속한 FancyScrollRectCell\<TItemData, TContext> 클래스에 각 \<Item\_Data, Context>을 지정하여 상속받았다.

![](<../../../../.gitbook/assets/image (14).png>)

아이템 스크립트를 작성하여 FancyScrollRectCell\_Custom를 상속받아 사용한다.

![](<../../../../.gitbook/assets/image (15).png>)

<table><thead><tr><th width="418.3333333333333">변수</th><th>설명</th></tr></thead><tbody><tr><td>protected UIBase uIBase;</td><td>UIBase를 상속받지 못하므로 외부 컴포넌트에서 가져와 초기화 시킨다.<br>(기본적으로 RequireComponent가 설정되어 있음)</td></tr><tr><td>private bool isInit = false;</td><td>UIBase 최초 1회 초기화</td></tr></tbody></table>

<details>

<summary>protected virtual void SetMemberUI()</summary>

```csharp
// 기존 UIBase를 상속받는 스크립트와 마찬가지로 SetMemberUI를 호출할 수 있다.

// Item_Friend.cs
    protected override void SetMemberUI()
    {
        #region TMP_Text
        txtmp_Nickname = uIBase.GetUI_TxtmpMasterLocalizing(nameof(txtmp_Nickname));
        txtmp_StateMessage = uIBase.GetUI_TxtmpMasterLocalizing(nameof(txtmp_StateMessage));

        uIBase.GetUI_TxtmpMasterLocalizing("txtmp_Follow", new MasterLocalData("arztalk_friend_follow"));
        uIBase.GetUI_TxtmpMasterLocalizing("txtmp_Spawn", new MasterLocalData("arztalk_friend_bring"));
        uIBase.GetUI_TxtmpMasterLocalizing("txtmp_Offline", new MasterLocalData("arztalk_friend_break"));
        #endregion

         (생략)
    }
```

</details>

<details>

<summary>public override void UpdateContent(Item_Data itemData) => SetContent();</summary>

```csharp
// 스크롤 뷰 아이템이 생성 및 활성화될 시 UpdateContent가 호출되어 데이터를 받는다. 
// 기본적으로 SetContent()를 호출하므로 
// 오버라이딩 시 데이터를 받고난 후 base.UpdateContent(itemData)를 호출해야 한다.

// Item_Friend.cs
    public override void UpdateContent(Item_Data itemData)
    {
        if (itemData is Item_FriendData _data)
        {
            data = _data;

            base.UpdateContent(itemData);
        }
    }
```

</details>

<details>

<summary>protected virtual void SetContent()</summary>

```csharp
// 받아온 데이터로 아이템 콘텐츠 세팅한다.

// Item_Friend.cs
    protected override void SetContent()
    {
        if (txtmp_Nickname != null)
        {
            txtmp_Nickname.text = data.nickname;
        }

        (생략)
    }
```

</details>

<details>

<summary>public void InitModule()</summary>

```csharp
// UIBase 초기화 및 FancyScrollRectCell_Custom에 있는 SetMemberUI() 호출
// 기본적으로 Awake()에서 호출한다.
// 강제로 초기화 시켜주어야 하는 경우 직접 호출한다.
```

</details>

### ● 구조도

{% tabs %}
{% tab title="ScrollView_Custom" %}
<img src="../../../../.gitbook/assets/file.excalidraw (1) (2).svg" alt="" class="gitbook-drawing">
{% endtab %}

{% tab title="예시 - View_FriendList" %}
<img src="../../../../.gitbook/assets/file.excalidraw (7).svg" alt="" class="gitbook-drawing">
{% endtab %}
{% endtabs %}

### ● 사용 방법

1\) 스크롤 뷰의 아이템이 될 프리팹에 FancyScrollRectCell\_Custom을 상속받은 스크립트를 추가한다.

2\) ScrollView\_Custom : FancyScrollRect\<Item\_Data, Context>의 Cell Prefab에 1)을 연결한다. (오브젝트를 직접 끌어넣거나, 스크립트로 연결하는 것 모두 가능)

### ● 컴포넌트 추가 예시

1\) 하이라키에서 Unity 기본 제공 Scroll View를 Panel / Popup / View 등의 하위에 추가

2\) 기존 UI에서 Scroll Rect 컴포넌트를 삭제하고 Scroller와 Scroll View\_Custom 컴포넌트 추가

3\) Scroll View\_Custom 의 Cell Container에 하위의 Content를 연결

4\) Cell Prefab에 FancyScrollRectCell\_Custom을 상속받은 스크립트를 가지고 있는 프리팹 오브젝트 연결

※ Scroll View\_Custom 컴포넌트 추가 예시

![](<../../../../.gitbook/assets/image (10) (1).png>)

※ Cell 스크립트(FancyScrollRectCell\_Custom를 상속받음) 프리팹 오브젝트 예시

![](<../../../../.gitbook/assets/image (11) (1).png>)

5\) Scroll View의 Cell Size의 값과 Cell Prefab의 높이 값을 일치시켜주어야 한다
