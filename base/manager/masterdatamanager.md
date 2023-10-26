---
description: Web API호출하여 받은 master데이터를 캐싱&활용하는 매니저
---

# MasterDataManager

## 1. 마스터 데이터 신규 추가

* 마스터 시트 작성  (기획팀) → 마스터 데이터 갱신 및 클래스 추가(서버팀) → 필요 데이터 활용을 위한 코드 작업(클라팀)

{% hint style="warning" %}
<pre><code><strong>1) MasterDataDefine.cs의 클래스는 서버팀 측에서 자동으로 업데이트하므로 
</strong><strong>클라가 작성해도 데이터가 덮어씌워지기 때문에 해당 스크립트에서는 작업을 하지 말 것.
</strong><strong>2) MasterDataManager에서 변수로 선언하여 사용하는 클래스 중에 삭제가 된다면 
</strong>프로젝트에 오류가 발생하여 진행이 불가해지므로 신속하게 수정해야한다.
</code></pre>
{% endhint %}

```csharp
// MasterDataDefine.cs에 마스터 데이터 클래스가 추가삭제된다.
// 예시 MasterDataDefine.cs 참고
public class AdContents 
{
	public int  id; // 아이디
	public int  moneyType; // 재화 타입
	public string reward; // 보상
};

public class AppendType 
{
	public int  type; // 타입
	public string name; // 이름
};

...
```



## 2. MasterDataManager 스크립트 작성

### 1 ) 변수 선언

* 변수명은 자유이나 대체로 앞에 data- 를 붙이는 것이 약속이다.

```csharp
// 예시
public TableBaseData<ExampleClass> dataExampleClass { get; private set; }
```

### 2 ) switch문 캐싱

* LoadMasterData(string) 함수 내부 switch문에 해당 클래스명 추가

<pre class="language-csharp"><code class="lang-csharp">// 예시
// 키 값이 1개일 시
// 변수.SetDictionary(변수.LoadTable(x.Value.ToString()).ToDictionary())의 형태는 동일함
<strong>case "ExampleClass":
</strong>    {
<strong>        dataExampleClass = new TableBaseData&#x3C;ExampleClass>();
</strong>        dataExampleClass.SetDictionary(dataExampleClass.LoadTable(x.Value.ToString()).ToDictionary(x => x.(키값), x => x));
    }
    break;
// 키 값이 2개일 시 (튜플)
case "ExampleClass":
    {
        dataExampleClass = new TableBaseData&#x3C;ExampleClass>();
        dataExampleClass.SetDictionary(dataExampleClass.LoadTable(x.Value.ToString()).ToDictionary(x => (x.(키값1), x.(키값2)), x => x));
    }
    break;
</code></pre>



### 3 ) TableBaseData\<T> 데이터 사용 방법

* 마스터 데이터 파싱 및 데이터 사용 시 편의를 위한 클래스
* 중요 메소드

○ 파싱

<details>

<summary>LoadTable</summary>

```csharp
// 데이터를 List<T> 형태로 저장

// 1) Json -> List (파싱)
public List<T> LoadTable(string json)
// 2) List -> List (단순저장)
public List<T> LoadTable(List<T> list)
```

</details>

<details>

<summary>SetDictionary</summary>

```csharp
// 데이터를 Dictionary의 형태로 저장

// 메소드의 이름은 전부 동일
// 1) 키값 int
public void SetDictionary(Dictionary<int, T> dic)
// 2) 키값 string
public void SetDictionary(Dictionary<string, T> dic)
// 3) 키값 int, int (튜플)
public void SetDictionary(Dictionary<(int, int), T> dic)
// 4) 키값 int, string (튜플)
public void SetDictionary(Dictionary<(int, string), T> dic)
```

</details>

○ 데이터 사용

<details>

<summary>GetList</summary>

```csharp
// 캐싱된 데이터 리스트를 전부 가져온다.
public List<T> GetList()
```

</details>

<details>

<summary>GetDictionary</summary>

```csharp
// 캐싱된 데이터 딕셔너리를 전부 가져온다.

// 딕셔너리 키값의 형식에 따라 호출하는 메소드가 다름
// 1) 키값 int
public Dictionary<int, T> GetDictionary_int()
// 2) 키값 string
public Dictionary<string, T> GetDictionary_string()
// 3) 키값 int, int (튜플)
public Dictionary<(int, int), T> GetDictionary_intint()
// 4) 키값 int, string (튜플)
public Dictionary<(int, string), T> GetDictionary_intstring()
```

</details>

<details>

<summary>GetData</summary>

```csharp
// 개별 데이터를 가져온다.

// 메소드의 이름은 전부 동일
// 1) 키값 int
public T GetData(int key)
// 2) 키값 string
public T GetData(string key)
// 3) 키값 int, int (튜플)
public T GetData(int item1, int item2)
// 4) 키값 int, string (튜플)
public T GetData(int item1, string item2)
```

</details>



{% hint style="info" %}
마스터 데이터 확인 방법

Url 주소 : StorageUrl(게이트웨이에서 가져올 수 있음) + "master/master.json"

1\) 익스플로러

* 익스플로러 주소창에 Url 주소 검색

2\) 포스트맨

* Get으로 설정
*   Url 주소를 Send

    <figure><img src="../../.gitbook/assets/image (16) (1).png" alt=""><figcaption></figcaption></figure>
{% endhint %}
