# SceneLogic

SceneLogic은 각 씬의 시작점 이다.

기존 SceneLogic의 사이즈가 커짐에 따라 **분할화(Partial)** 하였다.\
(3가지로 분할, 추후 사이즈가 커지면 추가될 수 있음)

**SceneLogic** : 기본 베이스가 되는 함수 모음, 각 Partial 함수들을 실행함\
**SceneLogic\_Base** : 분할화 하기 애매한 함수들 모음. 크기가 커지면 따로 분할화 할 수 있음.\
**SceneLogic\_UI** : UI 요소**(Panel, Popup, Toast ...)**등을 **캐싱&호출** 하는 함수\
**SceneLogic\_Network** : **방 입장&퇴장** 관련 함수, **포탈 이동** 함수



<img src="../../.gitbook/assets/file.excalidraw (1).svg" alt="" class="gitbook-drawing">
