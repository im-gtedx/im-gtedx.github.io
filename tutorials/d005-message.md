消息是一个对话的基本组成部分，我们支持的消息类型有：

- 文本消息：`TextMessage`
- 图像消息：`ImageMessage` 【待实现】
- 音频消息：`AudioMessage` 【待实现】
- 视频消息：`VideoMessage` 【待实现】
- 文件消息：`FileMessage` 【待实现】
- 位置消息：`LocationMessage` 【待实现】

## 消息类详解

#### 消息类型之间的关系

![消息类结构图](https://raw.githubusercontent.com/li-jun-bo/DreamImage/master/uml/d005-message.png)

#### 消息类均包含以下属性

> {@link MessageField} 类是SDK提供的消息类{@link Message}的属性枚举类。具体包括以下属性：

| 属性       | 类型   | 描述         |
| :--------- | :----- | :----------- |
| message_id | string | 消息ID |
| message_type | string | 消息类型, 具体参见【{@link MessageType}】 |
| message | string | 文本消息或图片消息的描述 |
| conversation_id | string | 消息所属的会话ID |
| send_time | string | 消息发送时间 |
| send_by | string | 消息发送者 |
| io | string | 消息传输方向，有两种取值`true : send` 或 `false : received` |
| attrs | string | 自定义属性, 具体参见下方《自定义消息属性》部分。 |

>消息ID生成规则【`message_id` = uid + uuid】

## 自定义消息属性

在某些场景下，开发者需要在发送消息时附带上自己业务逻辑需求的自定义属性，比如发送消息时携带设备名称，或发送消息携带自己的个人信息。开发者可以通过自定义消息属性实现这一需求。

**【场景一】发送文本消息给对方，让其知道我用的是什么手机跟他在聊天：**

```
let message = new TextMessage("静水流深");
message.setAttributes({
  phone: 'IPhone X',
});
conversation.send(message).then(function() {
  console.log('发送成功');
}).catch(console.error.bind(console));

```

接收时可以读取这一属性：

```
client.on('message', function(message) {
  console.log(message.getAttributes().phone); // IPhone X
});

```

**【场景二】在跟对方聊天的详情页，发送消息携带自己的个人信息，从而展示出头像和昵称。**

```
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

```
client.on('message', function(message) {
  console.log(message.getAttributes()); 
  // {id: "9896d0783afa55ff194248ecc408baeb",name: "Gtedx",avatar: "https://avatar.jpg"}
});

```

> 所有的 `{@link TypedMessage}` 消息都支持 `attrs` 这一属性。


## 消息的有效期

一个会话的消息记录会在缓存本地，只要用户不清除缓存或清除当前会话的消息，会一直存在于本地。Gtedx IM 云端只负责转发消息。对于离线消息，云端会暂时缓存，等用户再次登录或建立连接，就会一次性推给客户端。



## 消息发送选项【待实现】

消息发送选项用于在发送消息时定义消息的一些特性。包含以下特性：

#### 成员提醒@



#### 消息回执



##### 送达回执



##### 已读回执



## 未读消息 【待实现】

#### 未读消息数更新通知



#### 离线消息通知