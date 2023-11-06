---
description: PC런처에 대해 기술한다
---

# 🔱 Launcher

### 아즈메타 PC 실행파일 다운로드Storage 경로

* 스토리지 계정 -> arzmetadevsta -> arzmeta-dev-container -> ArzMetaPCBuild

### 아즈메타 PC 런처 이미지 경로

* 스토리지 계정 -> arzmetadevsta -> arzmeta-dev-container -> ArzMetaPCBuild -> arz\_LauncherImage

### 아즈메타 PC Dev런처 다운로드 경로

* arzmeta-dev-homepage-container -> arz\_Dev\_Launcher -> ArzMetaSetup.exe



### 런처에 사용되는 모든 파일/폴더명 통일

<figure><img src="../.gitbook/assets/image (1) (4).png" alt=""><figcaption></figcaption></figure>

* Dev / Sta / Live 3개의 폴더로 나누었습니다.

<figure><img src="../.gitbook/assets/image (52).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (53).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (54).png" alt=""><figcaption></figcaption></figure>

* 테스트 및 배포 시 3가지 이름은 통일해야 합니다.\
  ex) Dev\_build.zip\
  &#x20;     Sta\_build.zip\
  &#x20;     Live\_build.zip
* Version.bin 파일에 이름도 동일해야하며 내부 버전 번호만 변경해서 업로드 해주시면 됩니다.\
  (.bin 파일을 메모장으로 열어 버전을 변경해 주시면 됩니다.)

<figure><img src="../.gitbook/assets/image (2) (3).png" alt=""><figcaption></figcaption></figure>

해당 파일 내부에는 Dev / Sta / Live 라고 적힌 OpenVersion.bin파일 존재합니다.

(VersionFile -> OpenVersion.bin)

<figure><img src="../.gitbook/assets/image (3) (3).png" alt=""><figcaption><p>(Dev / Sta / Live 중  본인이 사용할 경로를 적어주면 됨)</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (59).png" alt=""><figcaption></figcaption></figure>

* 해당 텍스트를 Dev / Sta / Live 로 변경하면 해당하는 다운로드 Uri로 자동 설정 되고 런처를 끄고 재시작  시 해당하는 최신 버전의 업데이트가 진행됩니다.\
  (대문자 소문자를 정확히 입력해 주세요!)



### InnoSetup

<figure><img src="../.gitbook/assets/image (4) (3).png" alt=""><figcaption><p>(폴더 전체 구조)</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption><p>(아즈메타 PC빌드 폴더)</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (7).png" alt=""><figcaption><p>(Version확인을 위한 폴더)</p></figcaption></figure>

1. 위 이미지처럼 WPF로 제작한 런처빌드파일, 다운로드 경로를 불러올 VersionFile폴더, 아즈메타 PC빌드한 파일을 넣어주시면 됩니다.(폴더  전체 구조 이미지)

<figure><img src="../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

2. InnoSetup을 실행 시킵니다.

<figure><img src="../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

3. File에 new 버튼을 클릭합니다.

<figure><img src="../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

4. 정보를 이미지와 같이 입력해줍니다.

<figure><img src="../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

5. 다음을 눌러서 넘어갑니다.

<figure><img src="../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>

6. Browse...을 클릭한 후 아즈메타 런처 실행파일을 선택해 적용합니다.
7. 그 후 아까 미리 준비해놨던 폴더를 드래그하여 빈 공간에 드래그랜 드랍합니다.

<figure><img src="../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (21).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (22).png" alt=""><figcaption></figcaption></figure>

8. 전부 다음으로 넘어갑니다.

<figure><img src="../.gitbook/assets/image (23).png" alt=""><figcaption></figcaption></figure>

9. 인스톨 언어설정 탭에서 English와 Korean을 선택한 후 다음으로 넘어갑니다.

<figure><img src="../.gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (25).png" alt=""><figcaption></figcaption></figure>

10. 정보를 작성하고 Custom Setup icon file: 에 우측 Browse...을 클릭해서 ico파일을 적용해 줍니다. 다음을 눌러줍니다.

<figure><img src="../.gitbook/assets/image (27).png" alt=""><figcaption></figcaption></figure>

11. 체크박스를 해제한 후 다음으로 넘어갑니다.
