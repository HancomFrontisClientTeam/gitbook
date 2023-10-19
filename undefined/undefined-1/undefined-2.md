---
description: 전체 채팅, 1:1 채팅, 시스템 채팅
---

# 채팅 기능

<details>

<summary>관련 스크립트</summary>

[웹소켓 : Assets\\\_DEV\Script\Framework\Manager\SocketManager.cs](../../base/manager/socketmanager.md)

채팅 UI : Assets\\\_DEV\_Private\SJE\ChatModeController.cs

채팅 기능 : Assets\\\_DEV\Script\UI\View\View\_Chat.cs

[채팅 스크롤 아이템 : Assets\\\_DEV\Script\UI\Item\Item\_Chat.cs](../../asset/ui/gpm-infinitescroll-dynamicscroll.md)

</details>

## 초기화

SceneLogin\_Base에서 씬에 따라 채팅 기능 사용 여부에 따른 bool값 설정

```csharp
// Assets\_DEV\Script\Framework\UI\SceneLogic_Base.cs
private void InitChat()
{
    var sceneType = GetSceneType();

    // TODO : 필요시 씬 추가해야 함
    switch (sceneType)
    {
        // 아웃월드
        case SceneName.Scene_Base_Loading:
        case SceneName.Scene_Base_Lobby:
        case SceneName.Scene_Base_Logo:
        case SceneName.Scene_Base_Patch:
        case SceneName.Scene_Base_Title:
            isChatEnabled = false;
            break;
        case SceneName.Scene_Land_Arz:
        case SceneName.Scene_Land_Busan:
        case SceneName.Scene_Room_Consulting:
        case SceneName.Scene_Room_Lecture:
        case SceneName.Scene_Room_Lecture_22Christmas:
        case SceneName.Scene_Room_Meeting:
        case SceneName.Scene_Room_Meeting_22Christmas:
        case SceneName.Scene_Room_Meeting_Office:
        case SceneName.Scene_Room_Exposition_Booth:
        case SceneName.Scene_Room_MyRoom:
        case SceneName.Scene_Zone_Conference:            
        case SceneName.Scene_Zone_Festival:
        case SceneName.Scene_Zone_Game
        case SceneName.Scene_Zone_Office:
        case SceneName.Scene_Zone_Store:
        case SceneName.Scene_Zone_Vote:
        case SceneName.Scene_Zone_Exposition:
        // 채팅 기능을 사용하지는 않지만 웹서버에 현재 접속 씬이름을 넘기기 위해 -> hud에서 채팅 UI 자체가 안뜸
        case SceneName.Scene_Room_JumpingMatching:
        case SceneName.Scene_Room_OXQuiz:
        default:
            isChatEnabled = true;
            break;
    }
}
```

