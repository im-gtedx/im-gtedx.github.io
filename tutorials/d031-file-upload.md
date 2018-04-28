> 文件路径：统一使用 [File URI](https://en.wikipedia.org/wiki/File_URI_scheme) 进行标识。
>
> 对于React Native，开发者根据Android和IOS两大平台的系统，自行获取URI格式的文件路径。
>
> 对于React，开发者根据Linux、Mac、Windows三大平台的系统，使用 [Web API](https://developer.mozilla.org/en-US/docs/Mozilla/JavaScript_code_modules/OSFile.jsm/OS.Path) 进行获取。
>
> 最终的格式：`file:///myfilepath/extensions/filename.png`
>
> 参考资料：
>
> [《Content-Disposition》](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Content-Disposition)	
>
> [《FormData》](https://developer.mozilla.org/zh-CN/docs/Web/API/FormData) 
>
> [《FormData 对象的使用》](https://developer.mozilla.org/zh-CN/docs/Web/API/FormData/Using_FormData_Objects) 
>
> [《encodeURI()》](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/encodeURI) 

## 文件上传

我们要创建一个{@link ImFile}实例，然后调用该实例的save方法。

```javascript
let params = {file: file_uri,name:"gtedx.png"};
let imFile = imClient.createFile(params);
imFile.save()
    .then(fileInfo => {
        console.log(fileInfo);
    }).catch(e => {
        console.log(e.message);
    });
```

#### 参数对象-params

```json
{file: 'file_uri',type:'image/png',name:'file_name'}
```

| 属性       | 类型   | 描述   | 必填   |
| :--------- | :----- | :----- | :----- |
| file | string | FIle URI | 是 |
| type | string | MimeType | 否：如果不填，则SDK会根据文件的扩展名动态填写 |
| name | string | File Name | 否：如果不填，则SDK会根据文件名称动态填写 |

> 获取文件对应的MIME_TYPE的参考库
>
> Mobile [react-native-mime-types](https://github.com/Liroo/react-native-mime-types) 
>
> Web      [mime-types](https://github.com/jshttp/mime-types)  [mime](https://github.com/broofa/node-mime)

#### 结果对象-fileInfo

```json
{
    "name": "gtedx.png", 
    "extension": "png",
    "mime_type": "image/png",
    "size": 154879,
    "width": 1440,// 图片会返回宽高
    "height": 1424,// 图片会返回宽高
    "server_url": "https://path20180426105856_name.jpg"
}
```

| 属性       | 类型   | 描述   |
| :--------- | :----- | :----- |
| name | string | 文件名 |
| extension | string | 扩展名 |
| mime_type | string | MimeType |
| size | integer | 文件大小-[单位：byte] |
| width | string | 宽-[一般用于图片文件] |
| height | string | 高-[一般用于图片文件] |
| server_url | string | 文件公开地址 |


> 具体参见{@link ImFile}实例

## 文件下载



