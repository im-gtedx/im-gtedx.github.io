消息是一个对话的基本组成部分，我们支持的消息类型有：

- 文本消息：`TextMessage`
- 图像消息：`ImageMessage` 
- 音频消息：`AudioMessage` 【待实现】
- 视频消息：`VideoMessage` 【待实现】
- 文件消息：`FileMessage` 【待实现】
- 位置消息：`LocationMessage` 【待实现】

## 消息类详解

#### 消息类型之间的关系

![消息类结构图](https://raw.githubusercontent.com/im-gtedx/Image/master/message_arch.png)

#### 消息类均包含以下属性

> {@link MessageField} 类是SDK提供的消息类{@link Message}的属性枚举类。具体包括以下属性：

| 属性       | 类型   | 描述         |
| :--------- | :----- | :----------- |
| message_id | string | 消息ID |
| message_type | string | 消息类型, 具体参见【{@link MessageType}】 |
| message | string | 文本消息或图片消息的描述 |
| file | string | 文件 URI - 是指发送者的本地文件的URI，在消息的发送方有效 |
| uri | string | 网络 URI - 是指文件的网络URI，在服务器中存在 |
| conversation_id | string | 消息所属的会话ID |
| send_time | string | 消息发送时间 |
| send_by | string | 消息发送者 |
| io | string | 消息传输方向，有两种取值`true : send` 或 `false : received` |
| attrs | string | 自定义属性, 具体参见下方《自定义消息属性》部分。 |

>消息ID生成规则【`message_id` = uid + uuid】

## 自定义消息属性

在某些场景下，开发者需要在发送消息时附带上自己业务逻辑需求的自定义属性，比如发送消息时携带设备名称，或发送消息携带自己的个人信息。开发者可以通过自定义消息属性实现这一需求。

**【场景一】发送文本消息给对方，让其知道我用的是什么手机跟他在聊天：**

```javascript
let message = new TextMessage("静水流深");
message.setAttributes({
  phone: 'IPhone X',
});
conversation.send(message).then(function() {
  console.log('发送成功');
}).catch(console.error.bind(console));

```

接收时可以读取这一属性：

```javascript
client.on('message', function(message) {
  console.log(message.getAttributes().phone); // IPhone X
});

```

**【场景二】在跟对方聊天的详情页，发送消息携带自己的个人信息，从而展示出头像和昵称。**

```javascript
let message = new TextMessage("宁静致远");
// GLOBAL.User
const user = {
    id: "9896d0783afa55ff194248ecc408baeb",
    name: "Gtedx",
    avatar: "https://avatar.jpg"
}
message.setAttributes(user);

conversation.send(message).then(function() {
  console.log('发送成功');
}).catch(console.error.bind(console));

```

接收时可以读取这一属性：

```javascript
client.on('message', function(message) {
  console.log(message.getAttributes()); 
  // {id: "9896d0783afa55ff194248ecc408baeb",name: "Gtedx",avatar: "https://avatar.jpg"}
});

```

> 所有的 `{@link TypedMessage}` 消息都支持 `attrs` 这一属性。


## 消息的有效期

一个会话的消息记录会在缓存本地，只要用户不清除缓存或清除当前会话的消息，会一直存在于本地。Gtedx IM 云端只负责转发消息。对于离线消息，云端会暂时缓存，等用户再次登录或建立连接，就会一次性推给客户端。

#### 清除所有的本地消息

```
imClient.clearMessages()
    .then(() => {
        GLOBAL.Toast("消息已清空");
    })
    .catch(e => {
        GLOBAL.Toast(e.message);
    });
```

#### 清除会话的本地消息

```
conversation.clearMessages()
    .then(() => {
        GLOBAL.Toast("清空聊天记录成功");
     }).catch(e => {
        GLOBAL.Toast(e.message);
     });
```

## 消息发送选项

消息发送选项用于在发送消息时定义消息的一些特性。包含以下特性：

#### 成员提醒@

发送消息的时候可以显式地指定这条消息提醒某一个或者一些人:

```javascript
const message = new TextMessage(`@Tom`).setMentionList('Tom').mentionAll();
```

或者也可以提醒所有人：

```javascript
const message = new TextMessage(`@all`).mentionAll();
```

消息的接收方，可以通过读取消息的提醒列表来获取哪些 client Id 被提醒了：

```javascript
client.on('message', function messageEventHandler(message, conversation) {
  var mentionList = message.getMentionList();
});
```

消息有一个标识位，用来标识是否提醒了当前对话的全体成员:

```javascript
client.on('message', function messageEventHandler(message, conversation) {
  var mentionedAll = message.mentionedAll;
});
```

消息另一个标识位用来标识当前用户是否被提醒，SDK 通过读取消息是否提醒了全体成员和当前 client id 是否在被提醒的列表里这两个条件计算出来当前用户是否被提醒：

```javascript
client.on('message', function messageEventHandler(message, conversation) {
  var mentioned = receivedMessage.mentioned;
});
```

#### 消息回执【待实现】


##### 送达回执



##### 已读回执



## 未读消息

未读消息数量通知功能是根据平台{@link Platform}，在平台本地实现的。

SDK 会在 {@link Conversation} 上维护 `{@link ConversationField.UnreadCount}` 字段，这个字段在变化时 `ImClient` 会派发 `{@link ImEvent.Conversations}` 事件。这个字段会在下面这些情况下发生变化：

- 登录时，IM SDK 通知会话的未读消息数
- 收到在线消息
- 用户将对话标记为已读

开发者应当监听 `{@link ImEvent.Conversations}` 事件，在对话列表界面上更新这些对话的未读消息数量。

> 与会话的被动查询是同一个事件，

#### 会话列表的未读消息更新

```javascript
imClient.on(ImEvent.Conversations, this._onConversation);

_onConversation = (conversations) => {
    if (conversations && conversations.length > 0) {
        // 会话列表
        let data = conversations.slice();
        this.setState({data});

        // 未读消息
        let unreadMessageCount = 0;
        data.forEach(cs => {
           unreadMessageCount += cs[ConversationField.UnreadCount];
        });
        this.setState({unreadMessageCount});
    } else {
        this.setState({data: [], unreadMessageCount: 0});
    }
}

```

#### 会话实例的未读消息更新

```javascript
/**直接在Item UI中获取Conversation的ConversationField.UnreadCount展示即可*/
render() { 
    let {conversation} = this.props;
    let title = conversation[ConversationField.Title];
    let avatar = conversation[ConversationField.Icon];
    let unread_count = conversation[ConversationField.UnreadCount];
    return <View/>
}
```

清除对话未读消息数的唯一方式是调用 `{@link Conversation#read}` 方法将对话标记为已读，一般来说开发者至少需要在下面两种情况下将对话标记为已读：

- 在对话列表点击某对话进入到对话页面时
- 用户正在某个对话页面聊天，并在这个对话中收到了消息时

```javascript
// 进入到对话页面时标记其为已读
conversation.read().then(function(conversation) {
  console.log('对话已标记为已读');
}).catch(console.error.bind(console));

// 当前聊天的对话收到了消息立即标记为已读
currentConversation.on('message', function() {
  currentConversation.read().catch(console.error.bind(console));
})

```

> 【待实现】后期根据业务可能会需要。
>
> 当用户标记某个对话为已读时，该用户其他在线的客户端也会得到通知，SDK 会自动将该对话的未读消息数更新为 0。

