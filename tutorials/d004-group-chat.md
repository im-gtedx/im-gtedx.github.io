对于多人同时参与的固定群组，目前有成员人数限制。

## 发送消息

Tom 想建立一个群，把自己好朋友都拉进这个群，然后给他们发消息，他需要做的事情是：

1. 建立一个朋友列表
2. 新建一个对话，把朋友们列为对话的参与人员
3. 发送消息


```JavaScript
import CacheApp from "../../cache";

// Tom 用自己的个人标识UID，获取 ImClient 对象实例
const tomClient = imService.createIMClient(GLOBAL.User.uid);
imClient.setCache(new CacheApp()); // 实例化ImClient之后必须先设置缓存

let params = {title:"Tom with his friends",user_ids:["JerryUid","BobUid","HarryUid"]};

// 创建与 Jerry,Bob,Harry 之间的对话
tomClient.createConversation(params)
        .then(conversation => {
        	 // 发送消息
        	 let txtMsg = new TextMessage("浅水喧哗，深水静默");
             return conversation.send(txtMsg);
        }).then(message => {
             GLOBAL.Toast("发送成功");
        }).catch(error => {
         	 GLOBAL.Toast(error.message);
        });
```



## 接收消息

群聊的接收消息与单聊的接收消息在代码写法上是一致的。



```
import {Event as ET} from '@npm/im';
import CacheApp from "../../cache";

// Bob 登录
const jerryClient = imService.createIMClient(GLOBAL.User.uid);
imClient.setCache(new CacheApp()); // 实例化ImClient之后必须先设置缓存

jerryClient.on(ET.Message, this._onMessage);
jerryClient.on(ET.Error, this._onError);

// Harry 登录
const jerryClient = imService.createIMClient(GLOBAL.User.uid);
jerryClient.on(ET.Message, this._onMessage);
jerryClient.on(ET.Error, this._onError);
        
_onMessage = (message) => {
    GLOBAL.Toast("message = " + JSON.stringify(message));
}

_onError = (error) => {
   GLOBAL.Toast("error = " + JSON.stringify(error));
}
```
以上由 Tom发送的消息，Bob 和 Harry 在上线时都会收到。

由此可以看出，**群聊和单聊本质上都是对话**，只是参与人数不同。单聊是一对一的对话，群聊是多对多的对话。

用户在开始聊天之前，需要先登录 Gtedx IM 云端。这个登录并不需要用户名和密码认证，只是与 Gtedx IM 云端建立一个长连接，所以只需要传入一个唯一标识作为当前用户的 `uid` 即可。

Gtedx IM  云端只要求` uid` 在应用内唯一、不超过**64 个字符**的字符串即可，具体用什么数据由应用层决定。

Gtedx IM SDK 在内部会为每一个 ` uid` 创建唯一的 `ImClient` 实例，也就是说多次使用相同的 ` uid` 创建出来的实例还是同一个。因此，如果要支持同一个客户端内多账号登录，只要使用不同的 ` uid` 来创建多个实例即可。我们的 SDK 也支持多账户同时登录。