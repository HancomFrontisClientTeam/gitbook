---
description: 무한 스크롤(FancyScrollRect 및 FancyGridView는 무한 스크롤을 지원하지 않음)
---

# 03\_InfiniteScroll

* FancyScrollView에서 Loop 옵션이 On일 경우, 마지막 셀이 첫 번째 셀 앞에 정렬되고 첫번째 셀이 마지막 셀 뒤에 정렬되도록 셀이 순환됨
* Scroller의 MoveType을 Unrestricted로 설정해 스크롤 범위를 무제한으로 설정

<img src="../../../../gitbook/.gitbook/assets/file.excalidraw (3).svg" alt="Infinite Scroll Example 스크립트 구조 및 핵심 변수와 함수 설명" class="gitbook-drawing">

***

## Custom ScrollView Script (feat. 한효주 주임 연구원)

사용 방법 : 스크롤뷰 오브젝트에 해당 스크립트를 상속받은 컴포넌트 추가

{% tabs %}
{% tab title="AnimScrollView_Custom" %}
**\[ 설명 ]**

* 유한 오브젝트 재활용 스크롤뷰 Base Class (애니메이션 있음)

**\[ 사용 아이템 스크립트 ] - 아이템으로 사용할 프리팹에 해당 스크립트를 상속받은 컴포넌트 추가**

FancyCell\_Custom\<T> - AnimScrollView\_Custom에 아이템 프리팹 연결
{% endtab %}

{% tab title="LoopScrollView_Custom" %}
**\[ 설명 ]**

* 무한 오브젝트 재활용 스크롤뷰 Base Class (애니메이션 있음)

**\[ 사용 아이템 스크립트 ] - 아이템으로 사용할 프리팹에 해당 스크립트를 상속받은 컴포넌트 추가**

FancyCell\_Custom\<T> - LoopScrollView\_Custom에 아이템 프리팹 연결
{% endtab %}
{% endtabs %}

<img src="../../../../gitbook/.gitbook/assets/file.excalidraw (2) (1) (1).svg" alt="Custom ScrollView(무한 스크롤 지원) 사용 구조" class="gitbook-drawing">
