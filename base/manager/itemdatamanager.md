---
description: 캐싱한 MasterData 중 Item 관련 데이터를 중심으로 개별 파싱 및 기능 메소드 활용 매니저
---

# ItemDataManager

* 아이템 관련 총체적 데이터를 가져와 캐싱한다.
* (2023.10.17 기준) 가구, 코스튬, NFT 코스튬, 그 외 아이템 데이터가 있다.

## 1. 분류 기준

* 각 분류대로 데이터를 캐싱한 변수가 있다.

<table><thead><tr><th width="233">1순위 분류 (ITEM_TYPE)</th><th>2순위 분류 (CATEGORY_TYPE)</th></tr></thead><tbody><tr><td>NOMAL // 일반</td><td>consumable = 1001 // 소모품 <br>product = 1002 // 생산품 <br>material = 1003 // 재료 <br>tool = 1004 // 도구<br>vehicle = 1005 // 탈것 <br>pet = 1006 // 펫 <br>other = 1007 // 기타</td></tr><tr><td>INTERIOR // 가구및소품</td><td>furniture = 2001 // 가구 <br>decoration = 2002 // 장식 <br>specialty = 2003 // 특수 <br>floor = 2004 // 바닥</td></tr><tr><td>COSTUME // 의상</td><td><p>hair = 3001 // 헤어 </p><p>top = 3002 // 상의 </p><p>bottom = 3003 // 하의 </p><p>onepiece = 3004 // 전신 </p><p>shoes = 3005 // 신발 </p><p>accessory = 3006 // 액세서리</p></td></tr><tr><td>NFT // NFT 의상</td><td><p>nft_hair = 4001 // NFT 헤어 </p><p>nft_top = 4002 // NFT 상의 </p><p>nft_bottom = 4003 // NFT 하의</p><p>nft_onepiece = 4004 // NFT 전신 </p><p>nft_shoes = 4005 // NFT 신발 </p><p>nft_accessory = 4006 // NFT 액세서리 </p><p>nft_special = 4007 // NFT 스페셜</p></td></tr></tbody></table>

{% hint style="warning" %}
(2023.10.16 기준)&#x20;

* KTMF NFT 코스튬은 2023개다.
* 각 ItemId 마다 5개의 파츠가 한 세트이며, NFT의 파츠 데이터 시트가 별도로 존재한다.
* NFT의 낱개 파츠는 클라이언트단에서 보여지지 않는다. 무조건 세트로만 존재.
{% endhint %}



## 2.  중요 메소드

### 1 ) Get Data

* 카테고리 분류 별 데이터 묶음을 가져온다.

<details>

<summary>public TableBaseData GetCategoryData(ITEM_TYPE type)</summary>

```csharp
// 카테고리 내의 전체 아이템 정보 가져오기
// 1순위 분류 기준 ITEM_TYPE 데이터 묶음을 반환
public TableBaseData<Item> GetCategoryData(ITEM_TYPE type)
{
    TableBaseData<Item> itemData = null;
    switch (type)
    {
        case ITEM_TYPE.NOMAL: itemData = NomalTypeItemData; break;
        case ITEM_TYPE.INTERIOR: itemData = InteriorTypeItemData; break;
        case ITEM_TYPE.COSTUME: itemData = CostumeTypeItemData; break;
        case ITEM_TYPE.NFT: itemData = NFTTypeItemData; break;
        default: break;
    }
    return itemData;
}
```

</details>

<details>

<summary>public TableBaseData GetItemData(...)</summary>

```csharp
// 하위 카테고리 내의 전체 아이템 정보 가져오기
// 2순위 분류 기준 CATEGORY_TYPE 데이터 묶음을 반환

// 편의를 위한 오버로딩
public TableBaseData<Item> GetItemData(AVATAR_PARTS_TYPE type)
{
    return GetItemData(AvatarToCategory(type));
}
// 본체 메소드
public TableBaseData<Item> GetItemData(CATEGORY_TYPE type)
{
    TableBaseData<Item> itemData = null;

    switch (type)
    {
        case CATEGORY_TYPE.consumable: itemData = ConsumableItemData; break;
        case CATEGORY_TYPE.product: itemData = ProductItemData; break;
        case CATEGORY_TYPE.material: itemData = MaterialItemData; break;
        case CATEGORY_TYPE.tool: itemData = ToolItemData; break;
        ...
    }
    return itemData;
}
```

</details>

<details>

<summary>public List GetMaterials(int itemid)</summary>

```csharp
// 다중 메테리얼 데이터 가져오기
// 리스트의 순서를 지정해줄 수 있는 인덱스가 없기 때문에 마스터 시트에서 메테리얼의 순서를 잘 넣어주어야 한다.
// itemid를 넣으면 메테리얼 시트 데이터에서 조회하여 메테리얼 이름 리스트를 반환해준다
public List<string> GetMaterials(int itemid)
{
    return Single.MasterData.dataItemMaterial.GetList()
        .Where(x => x.itemId == itemid)
        .Select(x => x.material)
        .ToList();
}
```

</details>

###

### 2) Convert

* 데이터 형변환을 하여 편리하게 사용할 수 있게 한다.

<details>

<summary>public string GetAvatarInfoJson()</summary>

```csharp
// 현재 LocalPlayerData에 저장된 아바타 세팅 정보 Json으로 변환
public string GetAvatarInfoJson()
{
    return JsonConvert.SerializeObject(LocalPlayerData.AvatarInfo);
}
```

</details>

<details>

<summary>public Dictionary&#x3C;string, int> ConvertJsonToDic(string json)</summary>

```csharp
// Json 데이터를 Dictionary<string, int> 형태로 변환
// 아바타 세팅 데이터 형식으로 변환하기 위한 메소드
public Dictionary<string, int> ConvertJsonToDic(string json)
{
    return JsonConvert.DeserializeObject<Dictionary<string, int>>(json).ToDictionary(x => x.Key, x => x.Value);
}
```

</details>

<details>

<summary>public AVATAR_PARTS_TYPE CategoryToAvatar(CATEGORY_TYPE type)</summary>

```csharp
// AVATAR_PARTS_TYPE을 CATEGORY_TYPE으로 변환한다
// 해당 enum의 이름이 온전히 일치해야 한다
public AVATAR_PARTS_TYPE CategoryToAvatar(CATEGORY_TYPE type)
{
    return Util.String2Enum<AVATAR_PARTS_TYPE>(type.ToString())
}
```

</details>

<details>

<summary>public CATEGORY_TYPE AvatarToCategory(AVATAR_PARTS_TYPE type)</summary>

```csharp
// CATEGORY_TYPE을 AVATAR_PARTS_TYPE으로 변경한다
// 해당 enum의 이름이 온전히 일치해야 한다
public CATEGORY_TYPE AvatarToCategory(AVATAR_PARTS_TYPE type)
{
    return Util.String2Enum<CATEGORY_TYPE>(type.ToString());
}
```

</details>

<details>

<summary>public Dictionary&#x3C;string, int> ConvertNFTCostumData(...)</summary>

```csharp
// NFT는 Item 시트에 ItemId가 있으나 5개의 파츠가 세트인 아이템이기 때문에 
// 해당 아이디로 별도의 NFT 세트 데이터에서 조회해야 한다

// 1) NFT 코스튬 Id의 데이터를 조회하여 딕셔너리 데이터로 변환 (오버로딩)
public Dictionary<string, int> ConvertNFTCostumData(int id)
{
    var data = new Dictionary<string, int> { { Util.EnumInt2String(AVATAR_PARTS_TYPE.nft_special), id } };
    return ConvertNFTCostumData(data);
}
// 2) NFT 코스튬 Id의 여부를 확인하고 해당 데이터를 조회하여 딕셔너리 데이터로 변환
// Id가 없을 시 데이터 그대로 반환
public Dictionary<string, int> ConvertNFTCostumData(Dictionary<string, int> data)
{
    if (data.TryGetValue(Util.EnumInt2String(AVATAR_PARTS_TYPE.nft_special), out int value))
    {
        if (value > 0)
        {
            return Single.MasterData.dataKtmfSpecialItem.GetDictionary_intint()
                .Where(x => x.Key.Item1 == value)
                .ToDictionary(x => ConvertEnumStr(x.Value.partsId), x => x.Value.partsId);
        }
    }
    return data;
}
```

</details>

<details>

<summary>private string ConvertEnumStr(int id)</summary>

```csharp
// NFT 코스튬 enum 마지막 숫자만 가져와 AVATAR_PARTS_TYPE의 int값 string으로 사용
// NFT enum과는 이름이 일치할 수 없기 때문
private string ConvertEnumStr(int id)
{
    var str = Single.MasterData.dataItem.GetData(id).categoryType.ToString();
    return str[str.Length - 1].ToString();
}
```

</details>

###

### 3) Avatar Data 관련

* 아바타 데이터 관련 기능 메소드

<details>

<summary>public void AvatarSettingInit()</summary>

```csharp
// 마스터 데이터에서 지정한 기본 의상 데이터를 LocalPlayerData에 덮어씌운다
public void AvatarSettingInit()
{
    LocalPlayerData.AvatarInfo = Single.MasterData.dataAvatarResetInfo.GetList().ToDictionary(x => x.partsType.ToString(), x => x.itemId);
}
```

</details>

<details>

<summary>public Dictionary&#x3C;string, int> GetAvatarResetInfo()</summary>

```csharp
// 마스터 데이터에서 지정한 기본 의상 데이터를 Dictionary<string, int> 형태로 반환한다
public Dictionary<string, int> GetAvatarResetInfo()
{
    if (Single.MasterData.dataAvatarResetInfo != null)
    {
        return Single.MasterData.dataAvatarResetInfo.GetList().ToDictionary(x => x.partsType.ToString(), x => x.itemId);
    }
    return null;
}
```

</details>

<details>

<summary>public bool IsWearNFTCostume(Dictionary&#x3C;string, int> data)</summary>

```csharp
// NFT 의상을 입고 있는지 여부
public bool IsWearNFTCostume(Dictionary<string, int> data)
{
    if (data.TryGetValue(Util.EnumInt2String(AVATAR_PARTS_TYPE.nft_special), out int value))
    {
        return value > 0; 
    }
    return false;
}
```

</details>

###

### 4) 썸네일

<table data-full-width="true"><thead><tr><th width="304">방식</th><th width="180">사용처</th><th>설명</th></tr></thead><tbody><tr><td>리소스 폴드 로드</td><td>아바타 일반 의상</td><td>Panel_Costume에서 보이는 코스튬 아이템 썸네일</td></tr><tr><td>스토리지 로드</td><td>NFT 의상</td><td><p>스토리지에 2023개의 이미지가 업로드 되어있으며</p><p>다운로드 받아서 사용</p></td></tr><tr><td>프리팹 로드 + 썸네일  제네레이트</td><td>가구  및 인테리어</td><td>(23.10.24 기준) 프리팹 오브젝트를 로드하여 썸네일 제네레이터로<br>스프라이트 생성 및 저장</td></tr></tbody></table>



