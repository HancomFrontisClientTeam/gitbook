---
description: Unity에서 Standalone Windows 딥링크를 지원하지 않기 때문에 외부 에셋을 사용하고자 합니다.
---

# DynamicLink - Standalone Windows

* <mark style="color:red;">Build Settings - Player Settings - Other Settings - Configuration 설정 변경 필수</mark>

<figure><img src="../../.gitbook/assets/image (1) (2).png" alt=""><figcaption></figcaption></figure>

* 해당 Asset을 import 후 DeepLinkingForWindows/Example.cs에서 발생하는 KeyValue 에러 해결법

1. using System.Linq; 추가
2. foreach문 아래와 같이 변경

```
foreach ((string key, string value) in query.Select(x => (x.Key, x.Value)))
{
    Debug.Log($"{key}={value}");
}
```

* WindowsDeepLinking.Initialize("demo.scheme"); -> WindowsDeepLinking.Initialize("arzmeta");
