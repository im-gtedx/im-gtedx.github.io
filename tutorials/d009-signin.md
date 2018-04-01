登录到 Gtedx 实时通信服务代码在之前已经演示过，核心的代码如下:

```javas
const imClient = imService.createIMClient(GLOBAL.User.uid);
```

> 建议
>
> ① 将 *user* 配置项以及用户配置项单独放在一个配置类里面进行对象的初始化和统一导出．
>
> ② 赋值给全局变量  GLOBAL.User．
>
> ③ 因为这些配置项目是全局动能用得到数据, 方便后期引用，避免重复导入．
>
> 示例如下：config.js

```
const user = {
    uid: "9896d0783afa55ff194248ecc408baeb",
    name: "Gtedx",
    avatar: "https://avatar.jpg"
}

export {user};
```

在程序一开始初始化的部分进行全局变量的赋值

```javas
import {user} from '../../config';
GLOBAL.User=  user;
```

