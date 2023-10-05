---
description: 애니메이션 오브젝트 재활용 스크롤 뷰
---

# 03\_InfiniteScroll

#### ○ 해당 스크롤 뷰를 사용하는 경우

* Loop 기능이 필요할 시
* 애니메이션 등 효과가 필요할 시

***

<details>

<summary>목차</summary>

[#undefined-2](03\_infinitescroll.md#undefined-2 "mention")

[#undefined-3](03\_infinitescroll.md#undefined-3 "mention")

[#loopscrollview\_custom](03\_infinitescroll.md#loopscrollview\_custom "mention")

</details>

***

## ● 구조도

<img src="../../../../.gitbook/assets/file.excalidraw (3).svg" alt="Infinite Scroll Example 스크립트 구조 및 핵심 변수와 함수 설명" class="gitbook-drawing">

***

## ● 주요 변수 및 기능 메소드

{% hint style="info" %}
FancyScrollView에서 Loop 옵션이 On일 경우, 마지막 셀이 첫 번째 셀 앞에 정렬되고 첫번째 셀이 마지막 셀 뒤에 정렬되도록 셀이 순환됨

Scroller의 MoveType을 Unrestricted로 설정해 스크롤 범위를 무제한으로 설정
{% endhint %}

#### ▷ ScrollView.cs

<table><thead><tr><th width="465">변수</th><th>설명</th></tr></thead><tbody><tr><td>[SerializeField] Scroller scroller = default;</td><td>유니티 기본 컴포넌트</td></tr><tr><td>[SerializeField] GameObject cellPrefab = default;</td><td>FancyCell을 상속받은 스크립트를 가진 프리팹을 연결</td></tr></tbody></table>

<details>

<summary>protected override void Initialize()</summary>

```csharp
// Awake의 역할
// OnCellClicked(클릭 이벤트) 
// OnValueChanged(포지션 변경 이벤트)
// OnSelectionChanged(선택된 셀 변경 이벤트)
protected override void Initialize()
{
    base.Initialize();

    Context.OnCellClicked = SelectCell;

    scroller.OnValueChanged(UpdatePosition);
    scroller.OnSelectionChanged(UpdateSelection);
}
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

<summary>public void SelectCell(int index)</summary>

```csharp
// 원하는 인덱스의 셀 선택
// 기본적으로 셀 클릭 시 실행
 scrollView.SelectCell(0);
```

</details>

#### ▷ Cell.cs

<table><thead><tr><th width="472">변수</th><th>설명</th></tr></thead><tbody><tr><td>public int Index { get; set; }</td><td>오브젝트의 고유 인덱스</td></tr><tr><td>public virtual bool IsVisible { get; }</td><td>오브젝트가 하이라키에서 활성화 상태인지의 여부</td></tr><tr><td>protected TContext Context { get; }</td><td>선택된 셀 인덱스 및 셀 클릭 액션</td></tr><tr><td>[SerializeField] Animator animator = default;</td><td>애니메이터 컴포넌트 필수</td></tr></tbody></table>

<details>

<summary>static class AnimatorHash</summary>

```csharp
// 해시에 연결한 애니메이션의 이름을 동일하게 넣어주어야 작동
static class AnimatorHash
{
    public static readonly int Scroll = Animator.StringToHash("해당 애니메이션 이름");
}
```

</details>

<details>

<summary>public override void UpdateContent(ItemData itemData)</summary>

```csharp
// 생성 및 하이라키에서 활성화 됐을 시 해당 메소드 호출
// 호출 될 때마다 데이터가 들어온다
// 데이터를 받아와 처리하는 코드를 작성

public override void UpdateContent(ItemData itemData)
{
    message.text = itemData.Message;
    messageLarge.text = Index.ToString();

    var selected = Context.SelectedIndex == Index;
    imageLarge.color = image.color = selected
        ? new Color32(0, 255, 255, 100)
        : new Color32(255, 255, 255, 77);
}
```

</details>

<details>

<summary>public override void UpdatePosition(float position)</summary>

```csharp
public override void UpdatePosition(float position)
{
    currentPosition = position;

    if (animator.isActiveAndEnabled)
    {
        // 해당 코드를 통해 스크롤 시 애니메이션이 재생
        animator.Play(AnimatorHash.Scroll, -1, position);
    }

    animator.speed = 0;
}
```

</details>

<details>

<summary>void OnEnable()</summary>

```csharp
// 활성화 될 때마다 애니메이션을 통한 셀의 위치를 조정하므로
// 해당 코드는 OnEnable에서 필수로 작성해야 한다.
UpdatePosition(currentPosition);
```

</details>

***

## ● 실무 사용 LoopScrollView\_Custom

(feat. 한효주 주임 연구원)

#### ○ 스크립트명: LoopScrollView\_Custom

#### ○ 위치: Assets\\\_DEV\Script\UI\Scroll\UIExtensions\_Custom\LoopScrollView\_Custom

{% hint style="warning" %}
기존 에셋 사용 시 ScrollView\<ItemData, Context>와 같이 TItemData의 형태를 ScrollView에서 지정하고 있다.

이 경우, 여러 곳에서 다른 형태의 Data를 사용하기 위해서는 부모 데이터 클래스를 상속받아 자식 데이터 클래스에서 필요 데이터를 추가 하는 형태로 변경되어야 한다.

따라서 Custom 스크립트로 파생하여, 빈 클래스인 Item\_Data를 FancyCell의 TItemData 속성으로 지정하고, 자식데이터 클래스에서 상속받는 방식으로 변경하였다.
{% endhint %}

### ● 추가주요 변수 및 기능 메소드

#### ▷ LoopScrollView\_Custom.cs

상속한 FancyScrollView\<TItemData, TContext> 클래스에 각 \<Item\_Data, Context>을 지정하여 상속받았다.

<figure><img src="../../../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

<table><thead><tr><th width="475">변수</th><th>설명</th></tr></thead><tbody><tr><td>public bool IsUpdate = true;</td><td>셀 선택 시 업데이트</td></tr><tr><td>public bool IsScrollTo = true;</td><td>셀 선택 시 스크롤</td></tr></tbody></table>

#### ▷ FancyCell\_Custom.cs

상속한 FancyCell\<TItemData, TContext> 클래스에 각 \<Item\_Data, Context>을 지정하여 상속받았다.

<figure><img src="../../../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

아이템 스크립트를 작성하여 FancyScrollRectCell\_Custom를 상속받아 사용한다.

<figure><img src="../../../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

<table><thead><tr><th width="418.3333333333333">변수</th><th>설명</th></tr></thead><tbody><tr><td>protected UIBase uIBase;</td><td>UIBase를 상속받지 못하므로 외부 컴포넌트에서 가져와 초기화 시킨다.<br>(기본적으로 RequireComponent가 설정되어 있음)</td></tr><tr><td>private bool isInit = false;</td><td>UIBase 최초 1회 초기화</td></tr><tr><td>[SerializeField] protected Animator animator;</td><td>애니메이션이 연결되어있는 애니메이터. </td></tr><tr><td>protected static string animatorHashName;</td><td>애니메이션 이름 변수</td></tr><tr><td>protected float currentPosition = 0;</td><td>포지션 변수</td></tr></tbody></table>

<details>

<summary>protected virtual void SetMemberUI()</summary>

```csharp
// 기존 UIBase를 상속받는 스크립트와 마찬가지로 SetMemberUI를 호출할 수 있다.

// Item_AvatarPreset.cs
    protected override void SetMemberUI()
    {
        #region TMP_Text
        txtmp_PresetName = uIBase.GetUI_TxtmpMasterLocalizing(nameof(txtmp_PresetName));
        #endregion

        #region etc
        panel_Preset = SceneLogic.instance.GetPanel<Panel_Preset>();

        go_RawImage = uIBase.GetChildGObject(nameof(go_RawImage)).GetComponent<RawImage>();
        if (go_RawImage != null)
        {
            leanDragPanel = go_RawImage.GetComponent<LeanDragPanel>();
        }
        #endregion
    }
```

</details>

<details>

<summary>public override void UpdateContent(Item_Data itemData) => SetContent();</summary>

```csharp
// 스크롤 뷰 아이템이 생성 및 활성화될 시 UpdateContent가 호출되어 데이터를 받는다. 
// 기본적으로 SetContent()를 호출하므로 
// 오버라이딩 시 데이터를 받고난 후 base.UpdateContent(itemData)를 호출해야 한다.

// Item_AvatarPreset.cs
    public override void UpdateContent(Item_Data itemData)
    {
        if(itemData is Item_AvatarPresetData _data)
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

// Item_AvatarPreset.cs
     protected override void SetContent()
    {
        if (go_RawImage != null)
        {
            go_RawImage.texture = data.texture;
        }

        (생략)
    }
```

</details>

<details>

<summary>protected virtual void SetAnimatorHash()</summary>

```csharp
// protected static class AnimatorHash이 스테틱 클래스이므로
// 사용이 용이하도록 해쉬를 변수로 빼냄
protected virtual void SetAnimatorHash() { }
```

</details>

<details>

<summary>public void InitModule()</summary>

```csharp
// UIBase 초기화 및 FancyCell_Custom에 있는 SetMemberUI() 호출
// 기본적으로 Awake()에서 호출한다.
// 강제로 초기화 시켜주어야 하는 경우 직접 호출한다.
```

</details>

###

### ● 구조도

{% tabs %}
{% tab title="LoopScrollView_Custom" %}
<img src="../../../../.gitbook/assets/file.excalidraw (1).svg" alt="" class="gitbook-drawing">
{% endtab %}

{% tab title="예시 - Panel_Preset" %}
<img src="../../../../.gitbook/assets/file.excalidraw (7).svg" alt="" class="gitbook-drawing">
{% endtab %}
{% endtabs %}

### ● 사용 방법

1\) 스크롤 뷰의 아이템이 될 프리팹에 FancyCell\_Custom을 상속받은 스크립트를 추가한다.

2\) LoopScrollView\_Custom : FancyScrollView\<Item\_Data, Context>의 Cell Prefab에 1)을 연결한다. (오브젝트를 직접 끌어넣거나, 스크립트로 연결하는 것 모두 가능)

3\) Scroller 컴포넌트를 연결한다.

### ● 컴포넌트 추가 예시

1\) 하이라키에서 Unity 기본 제공 Scroll View를 Panel / Popup / View 등의 하위에 추가

2\) 기존 UI에서 Scroll Rect 컴포넌트를 삭제하고 Scroller와 LoopScrollView\_Custom 컴포넌트 추가

3\) LoopScrollView\_Custom 의 Cell Container에 하위의 Content를 연결

4\) Cell Prefab에 FancyCell\_Custom을 상속받은 스크립트를 가지고 있는 프리팹 오브젝트 연결

※ LoopScrollView\_Custom 컴포넌트 추가 예시

<figure><img src="../../../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

※ Cell 스크립트(FancyCell\_Custom를 상속받음) 프리팹 오브젝트 예시

<figure><img src="../../../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>
