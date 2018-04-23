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

- ImEvent.End 同端登录事件

  > 同一端是指 {@link Platform.App} 端或 {@link Platform.Web} 端
  >
  > 同一端同时登录，开发者应该手动调用 {@link ImClient#close}方法关闭连接。
  >
  > 此时从业务上做的应该是踢出当前用户到登录页面。

  ```javascript
  imClient.on(ET.End, this._onEnd);

  _onEnd = (message) => {
      GLOBAL.Toast(message);
      this.imClient.close().then(() => {
          GLOBAL.Toast("开发者手动登出成功");
      }).catch(error => {
          GLOBAL.Toast(error.message);
      });
  }

  ```


  > 此处开发者应该手动退出应用程序了，如果，想注重用户体现的话，可以在收到此事件的`_onEnd`方法中，做一定的处理。比如：收到此事件后，给用户两种选项：① 重新登录 ② 退出登录
  >
  > 当用户点击重新登录的时候，应该调用{@link ImClient#reopen}方法，SDK会自动再次建立连接。

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