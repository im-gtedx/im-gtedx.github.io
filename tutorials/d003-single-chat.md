我们先从最简单的环节入手。此场景类似于微信的私聊、微博的私信和 QQ 单聊。

我们创建了一个统一的概念来描述聊天的各种场景：[Conversation](https://leancloud.cn/docs/realtime_v2.html#hash24460545)（会话）。

在LeanCloud的[《实时通信开发指南》](https://leancloud.cn/docs/realtime_v2.html#hash819244670) 里也有相关的详细介绍。

## 发送消息

![single chat](https://leancloud.cn/docs/images/tom-and-jerry-avatar.png)

Tom 想给 Jerry 发一条消息，实现代码如下：

```
import CacheApp from "../../cache";

// Tom 用自己的个人标识UID，获取 ImClient 对象实例
const tomClient = imService.createIMClient(GLOBAL.User.uid);
imClient.setCache(new CacheApp()); // 实例化ImClient之后必须先设置缓存

let params = {title:"Tom & Jerry",user_ids:["9896d0783afa55ff194248ecc408baeb"]};

tomClient.createConversation(params)
        .then(conversation => {
        	 // 发送消息
        	 let txtMsg = new TextMessage("耗子，起床啦");
             return conversation.send(txtMsg);
        }).then(message => {
             GLOBAL.Toast("发送成功");
        }).catch(error => {
         	 GLOBAL.Toast(error.message);
        });
```

> 注意
>
> GLOBAL.User 参见《{@tutorial d009-signin}》部分进行全局的初始化。
>
> `imService.createIMClient()` 这个方法表示开始连接 Gtedx IM 云端服务器（即启动实时通信服务），**它在整个使用周期内只需要调用一次**。用户退出实时通信服务（断开 Gtedx IM 云端服务器连接）时需要在 {@link ImClient} 对象上调用 `ImClient.close()`。
>
> 这两个方法是成对使用的，在聊天过程中只需要调用一次，**无需多次调用**。
>
> 说明：
>
> CacheApp  是 React Native 移动端对SDK [{@link Cache}] 类的实现. 
>
> CacheWeb  是 React Web 前端对SDK [{@link Cache}] 类的实现. [TODO 待实现]
>
> 缓存实现方案由SDK提供,开发者负责集成. 具体详细信息参见《{@tutorial d007-cache}》

执行完以上代码，会在我们的后台表格中多了一行{@link Conversation}数据，其字段含义如下：

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

> 每次调用 `createConversation()` 方法，都会生成一个新的 Conversation 实例，即便使用相同 user_ids 和 title 也是如此。如果想要不重复创建相同成员的对话，请参阅{@tutorial d006-conversation} 。

## 接收消息

要让 Jerry 收到 Tom 的消息，需要这样写：

```
import {Event as ET} from '@npm/im';
import CacheApp from "../../cache";

// Jerry 登录
const jerryClient = imService.createIMClient(GLOBAL.User.uid);
imClient.setCache(new CacheApp()); // 实例化ImClient之后必须先设置缓存

jerryClient.on(ET.MESSAGE, this._onMessage);
jerryClient.on(ET.ERROR, this._onError);

_onMessage = (message) => {
    GLOBAL.Toast("message = " + JSON.stringify(message));
}

_onError = (error) => {
   GLOBAL.Toast("error = " + JSON.stringify(error));
}
```