---
description: ScrollRect를 사용할 때, Viewport 크기에 맞게 아이템을 생성해 재사용 가능
---

# GPM / InfiniteScroll (DynamicScroll)

{% embed url="https://github.com/nhn/gpm.unity/tree/main" %}

## 사용 목적

{% hint style="info" %}
ScrollView Content 내의 각각의 Item의 사이즈가 유동적이어야 하는 경우 사용한다.
{% endhint %}

## 사용 방법

1. ScrollRect를 사용하는 게임오브젝트에 Infinite Scroll 스크립트를 컴포넌트로 추가한다.
2. Infinite Scroll 스크립트의 Item Prefab에 아이템 프리팹을 연결한다 ( 해당 아이템은 InfiniteScrollItem을 상속받고 있어야 한다. )
3.  아이템 데이터는 InfiniteScrollItem을 상속받은 클래스 내에서 Content의 Data를 적용하여 사용

    ```csharp
    public override void UpdateData(InfiniteScrollData scrollData) 
    {
        base.UpdateData(scrollData);
        
        // InfiniteScrollData 콘텐츠로 데이터 적용
    }
    ```
4. 아이템 동적 크기 조정
   1. Dynamic Item Size 옵션 활성화
   2. InfiniteScrollItem 상속받은 클래스 내의 SetSize()를 통해 변경하거나 OnUpdateItemSize() 호출해 스크롤에 반영
5. 스크롤 그리드 적용
   1. Layout의 Values Size : 그리드를 분할할 크기 설정
   2. Element # Value : 각 Element를 분할할 비율 설정
6. 스크롤뷰 상태 변화에 따라 호출되는 이벤트
   1. OnChangeValue : 스크롤뷰의 값이 변경되었을 때
   2. OnChangeActiveItem : 스크롤 아이템이 보이거나 사라질 때
   3. OnStartLine : 스크롤뷰의 시작 지점인지 여부가 바뀔 때
   4. OnEndLine : 스크롤뷰의 마지막 지점인지 여부가 바뀔때 호출하는 이벤트

## 핵심 함수

```csharp
// 콘텐츠 요소를 보여줄 데이터(아이템) 추가
public void InsertData(InfiniteScrollData data);

// 삽입한 데이터 업데이트
public void UpdataData(InfiniteScrollData data);

// 모든 데이터 업데이트
public void UpdateAllData();

// 삽입한 데이터 삭제
public void RemoveData(InfiniteScrollData data);
public void RemoveData(int dataIndex);

// 모든 데이터 삭제
public void Clear();

// 모든 데이터로 콘텐츠 이동
public void MoveToFirstData();

// 마지막 데이터로 콘텐츠 이동
public void MoveToLastData();

// 콘텐츠가 마지막 데이터로 이동했는지 확인
public bool IsMoveToLastData();

// 해당 데이터로 콘텐츠 이동
public void MoveTo(InfiniteScrollData data, MoveToType moveToType, float time = 0);
public void MoveTo(int dataIndex, MoveToType moveType, float time = 0);

// 스크롤뷰 크기가 변경되었을 때, Infinite Scroll에서 크기 변경 처리
public void ResizeScrollView();
```

## 사용 구조도

<img src="../../.gitbook/assets/file.excalidraw (2) (1) (1).svg" alt="Infinite Scroll 사용 구조도" class="gitbook-drawing">
