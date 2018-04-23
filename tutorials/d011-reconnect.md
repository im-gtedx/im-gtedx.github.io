Gtedx IM SDK 断线重新连接机制，SDK默认是自动开启的，目前，不对外暴露配置项。

为了让Web端和Mobile端最大限度的兼容，特暴露以下两个方法，供开发者调用。

> 注意：在网络中断的情况下，所有的消息收发和对话操作都会失败。开发者应该监听与网络状态相关的事件并更新 UI，以免影响用户的使用体验。

## Web端

在浏览器的应用中，开发者参考[Network Information API](https://developer.mozilla.org/zh-CN/docs/Web/API/NavigatorOnLine/Online_and_offline_events) 感知到网络的变化，从而判断网络是【在线状态-online事件】还是【离线状态-offline事件】。

```javascript
// online 调用
imService.resume();
// offline 调用
imService.pause();
```

```javascript
// online 调用
imClient.resume();
// offline 调用
imClient.pause();
```

> 注意:{@link ImService} 和 {@link ImClient} 实例上的resume和pause的方法的作用都是一样的。
>
> 区别在于：{@link ImService} 实例管理多个 {@link ImClient} 实例。

## Mobile端

在RN应用中，开发者应该区分两个概念，分别是【网络状态-online | offline】和【前台后台-front | back】

随机组合条件进行判断是否需要调用 {@link ImService#resume} 和 {@link ImService#resume} 两个方法。

比如：① 应用在【前台】的情况下，是否`在线或离线` ② 在应用在【后台】的情况下，是否`在线或离线`

```javascript
imService.resume();
imService.pause();
```

```javascript
imClient.resume();
imClient.pause();
```
> React Native
>
> 网络相关的API - [NetInfo](https://reactnative.cn/docs/0.51/netinfo.html#content) 
>
> 前台后台的API - [AppState](https://reactnative.cn/docs/0.51/appstate.html#content) 