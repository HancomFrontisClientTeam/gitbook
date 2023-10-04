# 네이밍규칙



* UI의 네이밍규칙엔 **접두사**와 **접미사**가 존재
* 접두사는 크게 **View형** 접두사와 **Element형** 접두사가 존재
* 접미사는 **오브젝트의 이름**
* **View형 접두사**
  * 설명
    * UI Element 요소를 감싸는 View형 접두사
    * 각 Scene 해당에 View가 미리 배치되어야 함
  * 종류
    * Panel
      * PanelBase 상속
      * 씬에 1개만 활성화 (예외상황 존재할 수 있음)
      * 연출방식은 FadeOut / FadeIn
    * Popup
      * PopupBase 상속
      * 씬에 여러개 활성화 될 수 있음
      * 연출방식은 ZoomIn / ZoomOut
    * View
      * UIBase 상속
      * Panel이나 Popup 하위에 존재하는 View
      * View하위 변수들은 View스크립트가 관리함
      * 상위의 View에서 하위View의 변수에 직접접근 불가
    * Item
      * UIBase 상속
      * 인스턴스하게 생성되는 View
      * Assets/\_DEV/Resources/Addressable/Prefab/Item 하위에 프리팹 위치시킬 것
  * 호출 방법
    * SceneLogic.Instance. 으로 호출 (SceneLogic에 캐싱되어 있음)
    * UIBase를 상속받았으면 SceneLogic.Instance. 생략가능
    * Class이름과 Prefab이름을 일치화 시킬것
    * GetPanel\<T>()
    * PushPanel\<T>()
    * PopPanel\<T>()



<table><thead><tr><th width="124.33333333333331">접두사</th><th>상속클래스</th><th>설명</th></tr></thead><tbody><tr><td><strong>Panel_</strong></td><td>PanelBase</td><td></td></tr><tr><td><strong>Popup_</strong></td><td>PopupBase</td><td></td></tr><tr><td><strong>View_</strong></td><td>UIBase</td><td></td></tr><tr><td><strong>Item_</strong></td><td>UIBase</td><td></td></tr></tbody></table>

* GetUI<**T**>(nameof(**name**))



* **Element형 접두사**
  * **설명**
  * **종류**
* **Element종류 UI**

| 접두사              |   | 설명 |
| ---------------- | - | -- |
| **txtmp\_**      |   |    |
| **img\_**        |   |    |
| **btn\_**        |   |    |
| **tog\_**        |   |    |
| **togplus\_**    |   |    |
| **input\_**      |   |    |
| **scrollview\_** |   |    |
| **go\_**         |   |    |







