> 发送富媒体消息之前，是需要通过SDK将富媒体文件上传到我们的 IM OSS 服务器上的。
>
> 具体上传步骤请参考《{@tutorial d031-file-upload}》部分，以及{@link ImFile} 实例。

## 图片消息

#### 发送图片消息的步骤

1.  获取文件的uri、mime_type、name之后，拼接对象参数
2. 通过 {@link ImClient#createFile} 构造{@link ImFile} 实例
3.  通过调用 {@link ImFile#save} 方法，将文件上传到IM OSS服务器
4. 最后使用{@link ImFile}实例作为参数，构造{@link ImageMessage}实例发送即可

```javascript
// 发送图片
let params = {file: 'uri', type: 'mime',name:'name'};
let imFile = GLOBAL.ImClient.createFile(params);
imFile.save()
    .then(result => {
        let conversation = this.props.conversation;
        let imageMessage = new ImageMessage(imFile);
        imageMessage.setAttributes({...GLOBAL.User, ...result});
        return conversation.send(imageMessage);
    })
    .then(message => {
        GLOBAL.Toast("发送成功");
    }).catch(e => {
    GLOBAL.Toast(e.message);
});
```


