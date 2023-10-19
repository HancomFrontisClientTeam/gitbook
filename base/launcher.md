---
description: PC런처에 대해 기술한다
---

# 🔱 Launcher

### 런처 Storage 경로

* 스토리지 계정 -> arzmetadevsta -> arzmeta-dev-container -> ArzMetaPCBuild



### 런처 파일명 폴더명 통일

<figure><img src="../.gitbook/assets/image (48).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../.gitbook/assets/image (55).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (56).png" alt=""><figcaption></figcaption></figure>

### (테스트확인) OpenVersion.bin

* Dev / Sta / Live에 ArzMeta 실행 및 업데이트가 잘되었는지 확인하기 위한 OpenVersion.bin이 존재합니다.

<figure><img src="../.gitbook/assets/image (57).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (58).png" alt=""><figcaption></figcaption></figure>

해당 파일 내부에는 Dev / Sta / Live 라고 적힌 OpenVersion.bin파일 존재합니다.

<figure><img src="../.gitbook/assets/image (59).png" alt=""><figcaption></figcaption></figure>

* 해당 텍스트를 Dev / Sta / Live 로 변경하면 해당하는 다운로드 Uri로 자동 설정 되고 런처를 끄고 재시작  시 해당하는 최신버전의 업데이트가 진행됩니다.\
  (대문자 소문자를 정확히 입력해 주세요!)
