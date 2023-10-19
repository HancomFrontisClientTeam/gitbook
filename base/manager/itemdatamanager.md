---
description: 캐싱한 MasterData 중 Item 관련 데이터를 중심으로 개별 파싱 및 기능 메소드 활용 매니저
---

# ItemDataManager

## 1. 분류 기준

* 각 분류대로 데이터를 캐싱한 변수가 있다.

<table><thead><tr><th width="233">1순위 분류 (ITEM_TYPE)</th><th>2순위 분류 (CATEGORY_TYPE)</th></tr></thead><tbody><tr><td>NOMAL</td><td>consumable = 1001 // 소모품 <br>product = 1002 // 생산품 <br>material = 1003 // 재료 <br>tool = 1004 // 도구<br>vehicle = 1005 // 탈것 <br>pet = 1006 // 펫 <br>other = 1007 // 기타</td></tr><tr><td>INTERIOR</td><td>furniture = 2001 // 가구 <br>decoration = 2002 // 장식 <br>specialty = 2003 // 특수 <br>floor = 2004 // 바닥</td></tr><tr><td>COSTUME</td><td><p>hair = 3001 // 헤어 </p><p>top = 3002 // 상의 </p><p>bottom = 3003 // 하의 </p><p>onepiece = 3004 // 전신 </p><p>shoes = 3005 // 신발 </p><p>accessory = 3006 // 액세서리</p></td></tr><tr><td>NFT</td><td><p>nft_hair = 4001 // NFT 헤어 </p><p>nft_top = 4002 // NFT 상의 </p><p>nft_bottom = 4003 // NFT 하의</p><p>nft_onepiece = 4004 // NFT 전신 </p><p>nft_shoes = 4005 // NFT 신발 </p><p>nft_accessory = 4006 // NFT 액세서리 </p><p>nft_special = 4007 // NFT 스페셜</p></td></tr></tbody></table>

{% hint style="warning" %}
(2023.10.16 기준)&#x20;

* KTMF NFT 코스튬은 2023개다.
* 각 ItemId 마다 5개의 파츠가 한 세트이며, NFT의 파츠 데이터 시트가 별도로 존재한다.
* NFT의 낱개 파츠는 클라이언트단에서 보여지지 않는다. 무조건 세트로만 존재.
{% endhint %}



## 2.  중요 메소드

### 1 ) Get Data

*

<details>

<summary></summary>



</details>

<details>

<summary></summary>



</details>

<details>

<summary></summary>



</details>

### 2) Convert

*

<details>

<summary></summary>



</details>

<details>

<summary></summary>



</details>

<details>

<summary></summary>



</details>

<details>

<summary></summary>



</details>

<details>

<summary></summary>



</details>

<details>

<summary></summary>



</details>

### 3) Avatar Data 관련

*

<details>

<summary></summary>



</details>

<details>

<summary></summary>



</details>

<details>

<summary></summary>



</details>

### 4) 썸네일

4-1) 코스튬 썸네일 관리







리소스폴더 로드

스토리지 로드

4-2) 가구 썸네일 관리

제네레이트
