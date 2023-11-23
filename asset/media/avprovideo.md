---
description: AVPro 영상재생 에셋에 대해 기술한다
cover: >-
  https://opengraph.githubassets.com/92ffa6a1ee62be6bff726ee3bb34765bbe699c84766b8570a1fcad5f0e9c66ab/RenderHeads/UnityPlugin-AVProVideo
coverY: 263
---

# AVProVideo

{% embed url="https://assetstore.unity.com/packages/tools/video/avpro-video-core-edition-181844" %}

## 미디어파일( ex - .mp4, .mkv)나 미디어 url(ex - 유튜브) 등을간단하게 재생할 수 있는 에셋 입니다.

* **주요 기능**
* **주요 인터페이스**
* **이벤트 추가**

## 주요 기능

> 미디어의 주소, 실행, 이벤트추가 등

```csharp
// 예제 코드
mediaPlayer.MediaPath.Path; // 미디어 주소 가져오기
mediaPlayer.OpenMedia(); // 미디어 실행하기
```

##

## 주요 인터페이스

* MediaPlayer.**Info**

> 미디어의 길이, 프레임레이트, 높이, 너비 등의 정보를 담당한다.

```csharp
// 예제 코드
mediaPlayer.Info.GetDuration();
mediaPlayer.Info.GetVideoFrameRate();
mediaPlayer.Info.GetVideoWidth();
mediaPlayer.Info.GetVideoHeight();
```

* MediaPlayer.**Control**

> 미디어의 재생, 정지, 되감기, 뮤트 등 미디어의제어기능을 담당한다.

```csharp
// 예제 코드
mediaPlayer.Control.Play();
mediaPlayer.Control.Pause();
mediaPlayer.Control.Stop();
mediaPlayer.Control.MuteAudio(true);
```

##

## 이벤트추가

> 구독할 수 있는 다양한 이벤트를 생성합니다.

* 이벤트 추가

{% tabs %}
{% tab title="Inspector" %}
* MediaPlayer 컴포넌트 내에 있는 Events 항목 추가

![](<../../.gitbook/assets/image (1) (1) (1) (1).png>)
{% endtab %}

{% tab title="Scripting" %}
```csharp
using UnityEngine;
using RenderHeads.Media.AVProVideo;

[RequireComponent(typeof(MediaPlayer))]
public class EventsExample : MonoBehaviour 
{
    void Awake()
    {
        // The method HandleEvent will be called whenever an event is triggered
        GetComponent<MediaPlayer>().Events.AddListener(HandleEvent);
    }

    // This method is called whenever there is an event from the MediaPlayer
    void HandleEvent(MediaPlayer mp, MediaPlayerEvent.EventType eventType, ErrorCode code)
    {
        Debug.Log("MediaPlayer " + mp.name + " generated event: " + eventType.ToString());
        if (eventType == MediaPlayerEvent.EventType.Error)
        {
            Debug.LogError("Error: " + code);
        }
    }
}
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
인스펙터 방식이나 스크립팅 방식 둘중 편한 방식을 사용하시면 됩니다.
{% endhint %}

* 이벤트 종류

<figure><img src="../../.gitbook/assets/image (8) (1) (1).png" alt=""><figcaption></figcaption></figure>

{% embed url="https://assetstore.unity.com/packages/tools/video/avpro-video-core-edition-181844" %}
