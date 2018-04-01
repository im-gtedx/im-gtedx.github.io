各种事件的应用场景可以参考{@link ImEvent}

## {@link ImClient}的相关事件

* ImEvent.Message 收发消息事件

  ```javascript
  imClient.on(ET.Message, this._onMessage);

  _onMessage = (message) => {
      GLOBAL.Toast("message = " + JSON.stringify(message));
  }
  ```

* ImEvent.Conversations 会话自动刷新事件

  ```javascript
  imClient.on(ET.Conversations, this._onConversation);

  _onConversation = (conversations) => {
      this.setState({data: conversations});
  }
  ```


* ImEvent.Error 错误事件

  ```javascript
  imClient.on(ET.Error, this._onError);

  _onError = (error) => {
      GLOBAL.Toast("error = " + error.toString());
  }
  ```

## {@link Conversation}的相关事件

- ImEvent.Message 会话收发消息事件

  ```javascript
  conversation.on(ET.Message, this._onReceiveMessage);

  _onReceiveMessage = (message) => {
      // update UI
  }
  ```

- ImEvent.ConversationClearMessages 清除当前会话下的消息事件

  ```
  conversation.on(ET.ConversationClearMessages, this._onClearMessage);

  _onClearMessage = () => {
      this.setState({data: []});
  }
  ```

## 内部事件

> 暂时不对外暴露


## 网络事件【待实现】

注意：在网络中断的情况下，所有的消息收发和对话操作都会失败。开发者应该监听与网络状态相关的事件并更新 UI，以免影响用户的使用体验。

当网络连接出现中断、恢复等状态变化时，SDK 会在 ImService 实例上派发以下事件：

- `disconnect`：与服务端连接断开，此时聊天服务不可用。
- `offline`：网络不可用。
- `online`：网络恢复。
- `schedule`：计划在一段时间后尝试重连，此时聊天服务仍不可用。
- `retry`：正在重连。
- `reconnect`：与服务端连接恢复，此时聊天服务可用。

```
imService.on('disconnect', function() {
  console.log('服务器连接已断开');
});
imService.on('offline', function() {
  console.log('离线（网络连接已断开）');
});
imService.on('online', function() {
  console.log('已恢复在线');
});
imService.on('schedule', function(attempt, delay) {
  console.log(delay + 'ms 后进行第' + (attempt + 1) + '次重连');
});
imService.on('retry', function(attempt) {
  console.log('正在进行第' + (attempt + 1) + '次重连');
});
imService.on('reconnect', function() {
  console.log('与服务端连接恢复');
});
```

在 `schedule` 与 `retry` 事件之间，开发者可以调用 `ImService#retry` 方法手动进行重连。

在浏览器中，SDK 会通过 Network Information API 感知到网络的变化自动进入离线状态，在进入离线状态时会派发 `offline` 事件，在恢复在线时会派发 `online` 事件。在其他环境中可以通过调用 `ImService#pause` 与 `ImService#resume` 方法来手动进入或离开离线状态，可以实现实时通信在 App 被切到后台挂起、切回前台恢复等功能。

在断线重连的过程中，SDK 也会在所有的 ImClient 实例上派发同名的事件。ImService 与 ImClient 上的同名事件是先后同步派发的，唯一的例外是 `reconnect` 事件。在网络连接恢复，ImService 上派发了 `reconnect` 事件之后，ImClient 会尝试重新登录，成功后再派发 `reconnect` 事件。所以，ImService 的 `reconnect` 事件意味着 ImService 实例的 API 能够正常使用了，ImClient 的 `reconnect` 事件意味着 ImClient 实例的 API 能够正常使用了。

下面显示的是一次典型的断线重连过程中 SDK 派发的事件：

| 时间线                        | 事件派发者         | 事件                              | 说明                                           |
| ----------------------------- | ------------------ | --------------------------------- | ---------------------------------------------- |
| 网络断开                      | ImService,ImClient | `disconnect`                      | 服务端连接断开                                 |
|                               | ImService,ImClient | `offline`                         | 离线                                           |
| 网络恢复                      | ImService,ImClient | `online`                          | 恢复在线                                       |
|                               | ImService,ImClient | `schedule`(attempt=0, delay=1000) | 计划 1s 后重连                                 |
| +1s                           | ImService,ImClient | `retry`(attempt=0)                | 尝试第一次重连                                 |
| +0.2s重连失败                 | ImService,ImClient | `schedule`(attempt=1, delay=2000) | 计划 2s 后进行第二次重连                       |
| +1.5s调用 `imService.retry()` | ImService,ImClient | `retry`(attempt=0)                | 在 2s 内，手动进行重试，重连次数重置           |
| +0.2s                         | ImService          | `reconnect`                       | 服务端连接恢复，此时可以创建新的客户端了       |
| +0.2s                         | ImClient           | `reconnect`                       | 客户端重新登录上线，此时该客户端可以收发消息了 |