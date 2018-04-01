Tom 要退出当前的登录状态或要切换账户，方法如下：

```
tomClient.close()
	.then(() => {
		GLOBAL.Toast("退出成功");
     })
     .catch(e => {
         GLOBAL.Toast(e.message);
     });
```

