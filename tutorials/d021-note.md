* 页面与页面之间的传值

  * 注意

    > 无论您采用 React Navigation 还是 React Router，在页面之间传递的{@link Conversation}实例都是一个引用(句柄)，只要引用指向的实例存在于内存中，您无论在哪个页面操作的实例都是同一个。尤其要注意一下。

  * 场景

    > 当用户从**会话列表**页面点击一个**会话**，进入了**聊天页面**，此时从前一个页面将{@link Conversation}实例传了进去。而在**聊天页面**我们根据组件化思想，将其分解为**消息展示组件**和**消息输入组件**，然后再将其接收到的{@link Conversation}实例分别传入这两个组件，在这种情况下的{@link Coversation}实例始终是同一个。

    ```javascript
    	// 会话列表页面

    	navigation.navigate(RouteNames.Messages, {conversation: conversation});

    	// 进入聊天页面

        render() {
            let conversation = this.props.navigation.state.params.conversation;
            return (
                <View style={styles.container}>
                    <MsgList style={styles.msg_list} conversation={conversation}/>
                    <InputPanel style={styles.msg_input} conversation={conversation}/>
                </View>
            );
        }
    ```

    ​

* 数据库之间的迁移

  > 目前没有实现，所以，在更新客户端的时候，请卸载完后，再重新安装。

* 在会话列表页面的UI展示中，每一个会话都需要一个Item UI去展示。

  > 注意在 Item UI 中 Conversation 注册监听的方式，暂时是在render方法中。 原因不详，SDK测试没有问题，应该是组件的问题

  ```javascript
    render() {
        let {conversation} = this.props;
        // TODO 暂时在此注册 | EventEmitter 在其他地方无法注册，后期修复.
        // 暂时通过removeListener，修复render渲染导致的多次注册的问题.
        conversation.removeListener(ET.Message, this._onLastMessage);
        conversation.on(ET.Message, this._onLastMessage);
    }
  ```

  ​

  ​