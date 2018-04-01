聊天记录一直是客户端开发的一个重点，QQ 和 微信的解决方案都是依托客户端做缓存，当收到一条消息时就按照自己的业务逻辑存储在客户端的文件或者是各种客户端数据库中。

**而我们的Gtedx IM SDK，也是如此实现的。唯一的区别有两点：① 我们采用的JavaScript写的SDK，所以数据缓存的方案实现有两套(RN-Realm)|(React-LokiJS)。② 缓存实现方案由SDK提供，但要开发者手动集成到应用中**

> 我们的 SDK 对于普通的对话消息自动保存在本地，开发者可以通过{@link Conversation#getMessages}方法来获取该对话的所有历史消息。
>
> 而Gtedx IM SDK云端，会根据用户在线和不在线两种情况进行不同的消息处理。
>
> 当用户发送消息对方在线时，云端只负责转发消息，不会对消息进行存储；
>
> 当用户发送消息对方离线时，云端会暂时保存当前会话的消息，直到对方上线时，推送给对方后会将消息自动删除。



获取该对话中所有的历史消息，通常在第一次进入对话时使用：

```
conversation.getMessages()
.then(function(messages) {
	// update UI
}).catch(console.error.bind(console));
```

对于翻页加载更多历史消息的场景，我们暂不提供该功能。

## Cache for React Native

我们在React Native原生平台上，使用的Realm数据库作为缓存方案的实现。由于Realm出色的性能，在查询数据方面采用的是**懒加载**方案，即一次性查询出所有的数据飞快，但是只有在使用数据的时候，才去从数据中加载数据。

所以，如果您想实现**分页加载**的功能，您大可以将数据一次性查询出来，然后利用数组的 [API](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array) 进行逻辑上处理。

```javascript
var animals = ['ant', 'bison', 'camel', 'duck', 'elephant'];

console.log(animals.slice(0, 2));
// expected output: Array ["ant",bison"]

console.log(animals.slice(2, 4));
// expected output: Array ["camel", "duck"]
```
#### 移动端缓存集成

1. 参考[Realm官网](https://realm.io/docs/javascript/latest)，安装好Realm

   > 也可以参考我个人博客里面翻译的[《Realm for JavaScript》](https://blog.lijunbo.com/categories/database/) 

2. 将[Demo](https://gitlab-ce.gtedx.com/npm/im_demo)项目下的`js/cache`目录下的文件复制到你的项目中

   ![cache](https://raw.githubusercontent.com/im-gtedx/Image/master/cache_index.png)

3. 在创建{@link ImClient}实例后，给其设置缓存

   ```javascript
   componentDidMount() {
       this.imService = new ImService(GLOBAL.Options);
       this.imClient = this.imService.createIMClient(GLOBAL.User.uid);
       this.imClient.setCache(new CacheApp()); // 实例化ImClient之后必须先设置缓存
       this.imClient.getConversations();

       this.imClient.on(ET.Conversations, this._onConversation);
       this.imClient.on(ET.Message, this._onMessage);
       this.imClient.on(ET.Error, this._onError);

       GLOBAL.ImClient = this.imClient;
   }


   _onConversation = (conversations) => {
       this.setState({data: conversations});
   }

   _onMessage = (message) => {
       GLOBAL.Toast("message = " + JSON.stringify(message));
   }

   _onError = (error) => {
       GLOBAL.Toast("error = " + error.toString());
   }
   ```




## Cache for React Web

而在Web平台上我们采用**[LokiJS](http://lokijs.org/)**，一个内存数据库。对于用户来说，没有大量的数据可供查询。所以对于消息列表的数据展示，开发者只要一次性查询出来，展示到页面上并滑动到底部即可。

#### 前端缓存集成

> 此缓存方案待实现

