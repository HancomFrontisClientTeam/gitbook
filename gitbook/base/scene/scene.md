---
description: 씬 구성도
---

# Scene 구성도

* **Naming**
  * 씬 이름들은 각 조건들에 따라 정해진다.
* **Out World**
  * **플레이어가 없이** 오직 **UI**만으로 이루어진
  * 주로 회원가입, 로그인, 아바타설정 등의 씬이 존재
  * Scene\_Base\_Logo
    * 씬 최초 진입점
    * PreMaster ->&#x20;
    * 업데이트버전체크
    * 매니저  클래스 셋팅
    *
  * Scene\_Base\_Patch :
  * Scene\_Base\_Title :&#x20;
  * Scene\_Base\_Lobby :&#x20;
* **In World**
  * **플레이어가 존재**하며 돌아다니며 **멀티플레이**
  * 계층관계 : Land > Zone > Room 순
  * Scene\_Room\_MyRoom
    * Out World에서 In World로 전환되는 최초 씬
    * 마이룸 꾸미기, 액자친꾸미기,  친구 초대하기 ...
  * Scene\_Land\_Arz
    * 모든 In World의 허브 씬
    *
  * Scene\_



<img src="../../.gitbook/assets/file.excalidraw (4).svg" alt="씬 흐름도" class="gitbook-drawing">

