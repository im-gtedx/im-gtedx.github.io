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