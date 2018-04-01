我们将单聊和群聊（包括聊天室）的消息发送和接收都依托于 {@link Conversation} 这个统一的概念进行操作，所以开发者需要强化理解的一个概念就是：**SDK 层面不区分单聊和群聊**。

对话的管理包括**「成员管理」**和**「属性管理」**两个方面。

在讲解下面的内容之前，我们先使用 Jerry 的身份登录并创建一个多人对话。后面的举例中 `jerry` 指 Jerry 登录的 client，conversation 指创建好的这个对话，CONVERSATION_ID 指这个对话的 ID。

```
let imClient = imservice.createImClient('Jerry')
imClient.createConversation({
    user_ids: ['Bob', 'Harry', 'William'],
  });
}).then(function(conversation) {
  var CONVERSATION_ID = conversation.id;
  // now we have jerry, conversation and CONVERSATION_ID
})
```

## 创建会话

```
jerry.createConversation({
  user_ids: ['Bob', 'Harry', 'William'],
  title: '周末滑雪'
  unique: false,
});
```

参数说明：

- user_ids - 会话的初始成员列表。在会话创建成功后，这些成员会收到和邀请加入会话一样的相应通知。
- title - 对话的名字，主要是用于标记对话，让用户更好地识别会话。
- unique - 是否创建唯一对话，当其为 true 时，如果当前已经有**相同成员**的对话存在则返回该对话，否则会创建新的对话。该值默认为 false。

## 会话的成员管理

成员管理，是在对话中对成员的一个实时生效的操作，一旦操作成功则不可逆。

#### 成员变更接口

成员变更操作接口简介如下表：

| 操作目的     | 接口名                |
| ------------ | --------------------- |
| 自身主动加入 | `Conversation#join`   |
| 添加其他成员 | `Conversation#add`    |
| 自身主动退出 | `Conversation#quit`   |
| 移除其他成员 | `Conversation#remove` |

#### 成员变更事件 【待实现】



#### 添加成员

##### 自身主动加入【待实现】

Tom 想主动加入 Jerry、Bob、Harry 和 William 的对话，以下代码将帮助他实现这个功能：

```
let imClient = imService.createImClient('Tom');

imClient.getConversation(CONVERSATION_ID)
.then(function(conversation) {
  return conversation.join();
  
}).then(function(conversation) {
  console.log('加入成功', conversation[ConversationField.MemberIds]);
  // 加入成功 ['Bob', 'Harry', 'William', 'Tom']
  
}).catch(console.error.bind(console));
```

##### 添加其他成员

Jerry 想再把 Mary 加入到对话中，需要如下代码帮助他实现这个功能：

```
conversation.add(['Mary'])
.then(function(conversation) {
  console.log('添加成功', conversation[ConversationField.MemberIds]);
  // 添加成功 ['Bob', 'Harry', 'William', 'Tom', 'Mary']
  
}).catch(console.error.bind(console));
```

##### 添加成员相关事件【待实现】

TODO

#### 移除成员

##### 自身退出对话【待实现】

Tom 主动从对话中退出，他需要如下代码实现需求：

```
conversation.quit().then(function(conversation) {
  console.log('退出成功', conversation[ConversationField.MemberIds]);
  // 退出成功 ['Bob', 'Harry', 'William', 'Mary']
}).catch(console.error.bind(console));
```

##### 移除其他成员

Harry 被 William 从对话中删除。实现代码如下：

```
let imClient = imService.createImClient('William');

imClient.getConversation(CONVERSATION_ID)
.then(function(conversation) {
  return conversation.remove(['Harry']);
}).then(function(conversation) {
  console.log('移除成功', conversation[ConversationField.MemberIds]);
  // 移除成功 ['Bob', 'William', 'Mary']
}).catch(console.error.bind(console));
```

##### 移除成员相关事件【待实现】



#### 查询成员数量【待实现】

除了直接访问 `conversation[ConversationField.MemberIds].length`，也可以通过 `Conversation#count` 方法获得当前对话的成员数量：

```
conversation.count()
.then(function(membersCount) {
  console.log(membersCount);
}).catch(console.error.bind(console));
```

## 会话的属性管理

{@link Conversation}其字段含义如下：

| name        | type    | description                        |
| :---------- | :------ | :--------------------------------- |
| id          | string  | 会话对象主键                       |
| title       | string  | 会话标题                           |
| icon        | string  | 会话图标                           |
| manager_id  | string  | 会话管理员                         |
| remark      | string  | 会话备注                           |
| user_count  | numeric | 会话成员数量                       |
| attrs       | string  | 前台附加参数                       |
| create_time | string  | 会话创建时间                       |
| type        | string  | 会话类型:SINGLE_CHAT or GROUP_CHAT |
| user_ids    | list    | 会话成员id列表                     |

#### 名称

这是一个全员共享的属性，它可以在创建时指定，也可以在日后的维护中被修改。

Tom 想建立一个名字叫「喵星人」 对话并且邀请了好友 Black 加入对话：

```
tom.createConversation({
  user_ids: ['Black'],
  title: '喵星人',
}).then(function(conversation) {
  console.log('创建成功。id: ' + conversation.id + ' name: ' + conversation.name);
}).catch(console.error.bind(console));
```

Black 发现对话名字不够酷，他想修改成「聪明的喵星人」 ，他需要如下代码：

```
black.getConversation(CONVERSATION_ID)
.then(function(conversation) {
  conversation.name = '聪明的喵星人';
  return conversation.save();
}).then(function(conversation) {
  console.log('更新成功。name: ' + conversation.name);
}).catch(console.error.bind(console));
```

## 会话的查询




