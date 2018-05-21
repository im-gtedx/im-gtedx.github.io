## 安装

建议使用 NPM 安装 SDK，在终端运行以下命令：

```
// 稳定版(发版时使用)
npm i -S git+ssh://git@gitlab-ce.gtedx.com:npm/im.git
npm i -S git+https://git@gitlab-ce.gtedx.com:npm/im.git
// 开发版(开发迭代时使用,通过添加新功能或者修复bug之后,其后的版本号会随之更新)
npm i -S git+ssh://git@gitlab-ce.gtedx.com:npm/im.git#V0.0.5
npm i -S git+https://git@gitlab-ce.gtedx.com:npm/im.git#V0.0.5
```

>  版本号遵循《[语义化版本 2.0.0](https://semver.org/lang/zh-CN/)》
>
> 注意 : 使用此种方式(git+ssh)安装的包, 再使用 `npm update` 是无法更新私有模块，想更新只能重新安装一次.

## 引用

SDK 暴露（export）的成员, 请参见 [API--->Modules--->Overview]

在 Node.js 或其他支持 CommonJS 模块规范的环境中使用，按以下方法进行 导入.

```
import * as IM from '@npm/im';
// 或者单独引入某个成员
import {ImService} from '@npm/im';
```

## 初始化

按照上面的方式拿到 `ImService` 类后，可以按照下面用法初始化一个 `ImService` 实例，在下面的文档中如果出现了未定义的 `imService` 指的均是这个实例。

```
import {Env, Platform} from '@npm/im';

// 配置运行平台 | 配置运行的环境.
const options = { 
    platform: Platform.App,  // 如果不设置默认为{@link Platform.App}
    env: Env.Dev, // 如果不设置默认为{@link Env.Dev}
    client_id:"uuid"// 32位的UUID
}

const imService = new ImService(options);
```

> 注意: uuid 32位
>
> ​	`客户端自己生成并缓存，下次用户再次启动应用继续使用，除非用户清除应用缓存`
>
> 建议
>
> ① 将 *options* 配置项单独放在一个配置类里面进行对象的初始化和统一导出．
>
> ② 赋值给全局变量  GLOBAL.Options．
>
> ③ 因为这些配置项目是全局动能用得到数据, 方便后期引用，避免重复导入．
>
> 示例如下：config.js

```
import {Env, Platform} from '@npm/im';

const options = { 
    platform: Platform.App,  // 如果不设置默认为{@link Platform.App}
    env: Env.Dev // 如果不设置默认为{@link Env.Dev}
}

export {options};
```

在程序一开始初始化的部分进行全局变量的赋值

    import {options} from '../../config';
    GLOBAL.Options=  options;

> Platform 是 SDK 提供的枚举类，根据**运行平台**目前有两种取值. 参见[{@link Platform}]
>
> Env 是 SDK 提供的枚举类，根据**运行环境**目前有两种取值. 参见[{@link Env}]

