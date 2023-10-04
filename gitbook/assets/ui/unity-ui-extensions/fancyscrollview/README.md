---
description: 애니메이션을 포함한 스크롤뷰
---

# FancyScrollView

{% embed url="https://setchi.jp/FancyScrollView/api/FancyScrollView.html" %}

* 디스플레이에 필요한 만큼의 셀만 생성되며, 해당 셀은 재사용됨(Pooling)
* Context를 통해 스크롤 뷰에서 셀이 클릭되었음을 감지하고 스크롤 뷰에서 셀에 명령을 내리는 형태
* <mark style="background-color:purple;">**\[ Scroller 컴포넌트의 Inertia 옵션 ON && Inertia - Snap - Enable 옵션 ON ]**</mark> 스냅 기능
  * 스크롤이 멈추기 직전의 가장 가까운 셀로 이동&#x20;
  * <mark style="background-color:purple;">**\[ Inertia - Snap 하위 옵션 지정 가능 ]**</mark> 감소하는 속도,  시작되는 속도, 이동할 시간 및 완화에 대한 임계값 지정 가능
  * FancyScrollRect 및 FancyGridView는 지원하지 않음
