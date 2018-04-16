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

> 注意
>
> 当没有手动调用此方法关闭{@link ImClient#close}的连接时，在用户不停地按下返回键或后退按钮时，

