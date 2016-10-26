# Yunba JavaScript SDK 快速入门

通过利用 Yunba Javascript SDK 提供的接口 API，你可以很方便地在智能手机、平板电脑、网站等终端应用上使用 Yunba 的各种消息服务。
    

## 获取 SDK

[下载最新SDK](https://github.com/yunba/yunba-javascript-sdk)


## 新手上路

调试工具可以用 Chrome 本身自带 JavaScript Console，其它浏览器可能需要安装相应插件。例如 FireFox 需要安装 FireBug。


### 第一步：引入 SDK

Yunba JavaScript SDK 依赖于 Socket.IO，所以要先引入 Socket.IO。
请参考（SDK 中的）Demo 分别引入 Socket.IO 和 yunba-js-sdk。


### 第二步：创建 Yunba 实例

这里，需要在云巴 [Portal](product_kb_portal.md) 上创建新应用，用获取到的 [AppKey](product_kb_app_key.md) 创建 Yunba 实例。

```javascript
var yunba = new Yunba({server: 'sock.yunba.io', port: 3000, appkey: appkey});
```


### 第三步：初始化并连接消息服务器

```javascript
yunba.init(function (success) {
    if (success) {
        yunba.connect_by_customid('your_app_user_id', function (success, msg, sessionid) {
            if (success) {
                console.log('你已成功连接到消息服务器，会话ID：' + sessionid);
            } else {
                console.log(msg);
            }
        });
    }
});
```


### 第四步：订阅频道（Subscribe）

如果你想接收一个频道的消息，你得先使用`subscribe()`方法订阅该 [频道](product_kb_topic_and_alias.md)，
然后用`set_message_cb()`方法设置收到消息时调用的回调函数来接收消息。

>**注**：`subscribe()`的前提是客户端已经成功连上服务器。为了确保在连接成功以后再进行订阅，可以将`subscribe()`写到`connect_by_customid()`的回调函数中，在 success 时才执行。

```javascript
yunba.subscribe({'topic': 'my_topic'}, 
    function (success, msg) {
        if (success) {
            console.log('你已成功订阅频道：my_topic');
        } else {
            console.log(msg);
        }
    }
);

yunba.set_message_cb(function (data) {
    console.log('Topic:' + data.topic + ',Msg:' + data.msg);
});
```


### 第五步：发布消息（Publish）

你可以使用`publish()`方法向所有订阅 my_topic 频道的终端发布一条 “你好！Yunba。” 消息。

```javascript
yunba.publish({'topic': 'my_topic', 'msg': '你好！Yunba。'},
    function (success, msg) {
        if (success) {
            console.log('消息发布成功');
        } else {
            console.log(msg);
        }
    }
);
```

将上面的例子扩展一下，我们就可以利用 Yunba 实现在 JavaScript 和其他平台之间的实时通讯。

