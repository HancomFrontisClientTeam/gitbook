---
description: 채팅(룸 채팅, 1:1채팅), 시스템 알림(친구 요청, 오피스 알림) 등을 담당하는 매니저
---

# SocketManager

### WebSocketServer 문서 (손재명  연구원님)

{% embed url="https://jaemyeongson.gitbook.io/arzmeta-socketserver-docs/" %}

<details>

<summary>스크립트 특징 및 사용 방법</summary>

*   #### Singleton으로 작성되어 어디서든 접근 가능

    <pre><code><strong>Single.socket.[사용하고자 하는 함수]
    </strong></code></pre>
*   #### Method 이름 설명

    ```
    C_[함수기능] : 클라이언트 -> 서버
    S_[함수기능] : 서버 -> 클라이언트
    ```

<!---->

*   #### 소켓 연결 - 최초 로그인 할 때, 한 번만 연결함

    ```
    Single.socket.SocketIO3Connect();
    ```

```csharp
public void SocketIO3Connect()
{
            // 사용자의 jwtAccessToken, sessionID 로 연결 및 인증
            ConnectSocket();
            
            // 서버에서 콜백으로 보내주는 패킷 등록
            // 현재 Basic, Chatting, Screen & Banner로 region이 나눠져있음
            AddListener();
}

private void AddListener()
{
            // Basic
            socketManager.Socket.On<ConnectResponse>(SocketIOEventTypes.Connect, OnConnected);
            socketManager.Socket.On<ConnectResponse>(SocketIOEventTypes.Disconnect, Disconnected);
            socketManager.Socket.On<string>("S_PlayerConnected", (userInfo) => S_PlayerConnected(userInfo));
            socketManager.Socket.On<string>("S_DropPlayer", (message) => S_DropPlayer(message));

            // Chatting
            socketManager.GetSocket("/chatting").On<string>("S_SendMessage", (message) => S_GetMessage(message));
            socketManager.GetSocket("/chatting").On<string>("S_SendDirectMessage", (message) => S_GetDirectMessage(message));
            socketManager.GetSocket("/chatting").On<string>("S_SystemMessage", (message) => S_GetSystemMessage(message)); 
            
            // Screen & Banner
            socketManager.Socket.On("S_ScreenList", screenList);
            socketManager.Socket.On("S_BannerList", bannerList);
} 
```

</details>

{% hint style="warning" %}
기능추가에 따라 내용 추가 필요
{% endhint %}

{% tabs %}
{% tab title="Basic" %}
<pre class="language-csharp"><code class="lang-csharp">private void OnConnected(ConnectResponse obj);
private void DisConnected(ConnectResponse obj);

// 서버에 최초 접속 시, 정상적으로 접속이 되었을 때 서버에서 클라이언트로 전달해주는 패킷
private void S_PlayerConnected(string userInfo);

// 웹서버 에러로 인해 서버에서 클라이언트로 연결 해제 요청 전달해주는 패킷
private void S_DropPlayer(string message);

// 실시간 서버에서 <a data-footnote-ref href="#user-content-fn-1">닉네임 변경 패킷</a> 받았을 때 웹소켓에도 닉네임 변경됐다고 전달해주는 패킷
public void C_ChangeNickname();
</code></pre>
{% endtab %}

{% tab title="Chatting" %}
<pre class="language-csharp"><code class="lang-csharp">// <a data-footnote-ref href="#user-content-fn-2">채팅이 가능한 Scene</a> 로드 시, <a data-footnote-ref href="#user-content-fn-3">전체(룸) 채팅</a>의 경우에만 추가됨 (SceneLogic.cs)
private void C_EnterChatRoom();

// 전체(룸) 채팅의 경우, 클라이언트가 보내는 메세지 패킷
public void C_ChatMessage(Item_sendChat _message);

// 1:1 채팅의 경우, 클라이언트가 보내는 메세지 패킷
// Item_DMData : 채팅 대상 닉네임, 메세지
public void C_ChatDirectMessage(Item_sendDM _item);

// Scene 이동 시, 호출 필요
public void C_ExitChatRoom();

// 채팅이 가능한 Scene에서 채팅이 가능한 Scene으로 이동 시, 호출 필요
public void C_ExitAndEnterChatRoom(string roomId);
</code></pre>

```csharp
// 전체(룸) 채팅의 경우, 서버가 보내는 메세지 패킷
private void S_GetMessage(string _message)
{
    // Json String 형태의 데이터 -> Item_sendChat 형태로 변환
    Item_sendChat chatData = JsonConvert.DeserializeObject<Item_sendChat>(_message);
    
    // View_Chat 스크립트의 월드 채팅 데이터를세팅하는 함수 호출
    viewChat.LoadChatData(chatData);
}

// 1:1 채팅의 경우, 서버가 보내는 메세지 패킷
private void S_GetDirectMessage(string _message)
{
    // Json String 형태의데이터 -> Item_receiveDM 형태로 변환
    Item_receiveDM dmData = JsonConvert.DeserializeObject<Item_receiveDM>(_message);
    
    // View_Chat 스크립트의 1:1 채팅 데이터를 세팅하는 함수 호출
    viewChat.LoadDMData(dmData);
}

// 시스템 채팅의 경우, 서버가 보내는 메세지 패킷
private void S_GetSystemMessage(string _message)
{
    viewChat.LoadSystemData(_message);
}
```
{% endtab %}

{% tab title="Screen & Banner" %}
[jisu kim](http://127.0.0.1:5000/u/UsfhXfYQ4IhDdXQLDbzwkzlblB43 "mention") <mark style="background-color:orange;">작성 부탁드립니다.</mark>
{% endtab %}
{% endtabs %}

{% hint style="info" %}
시스템 메세지의 경우, 웹소켓 전체 공통 에러코드 페이지 참조
{% endhint %}

{% embed url="https://jaemyeongson.gitbook.io/arzmeta-socketserver-docs/~/changes/fUTTv4ufjINTfq9qPliD/undefined-3#systemchaterror" %}

{% hint style="info" %}
웹 패킷 테스트를 위한 Socket.IO 관리자페이지
{% endhint %}

{% embed url="http://20.41.116.6:8080/#/" %}
Dev Server Port : 3910, Staging Server Port : 3920
{% endembed %}

{% embed url="http://20.196.213.15:8080/#/" %}
Live Server
{% endembed %}

[^1]: S\_SET\_NICKNAME\_NOTICE

[^2]: SceneLogic.instance.isPanelChat == true

[^3]: CHAT\_TYPE.ALL
