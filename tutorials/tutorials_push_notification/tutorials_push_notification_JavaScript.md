# 消息推送开发指南之 JavaScript 篇

## 准备工作

### 1. 下载云巴 JavaScript SDK
打开 [云巴开发者页面](http://yunba.io/developers "云巴开发者页面")，下载最新版本的 JavaScript SDK。

### 2. 安装调试工具
Chrome 可使用自带的 JavaScript Console，其它浏览器可能需要安装相应插件。例如 FireFox 需要安装 FireBug。

### 3. 注册云巴开发者账号
打开 [云巴官方网站](http://yunba.io "云巴官方网站")，点击右上角的“注册”按钮创建账号。  

## 详细步骤

### 1. 在云巴 Portal 上创建新应用
请参考 [运行 Yunba Android Demo](https://github.com/yunba/docs/blob/master/quickstart/demo/Demo_Android.md) 
一文中的该步骤的做法，获得一个 AppKey。

### 2. 集成 Yunba JavaScript SDK

#### 2.1 创建 Yunba 实例

示例代码如下，代码中的 “appkey” 需使用步骤 1 中在 Portal 创建新应用时获取到的 AppKey。
```javascript
var yunba = new Yunba({server: 'sock.yunba.io', port: 3000, appkey: 'XXXXXXXXXXXXXXXXXXXXXXXX'});
```


#### 2.2 初始化并连接消息服务器

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

#### 2.3 订阅频道

如果你想接收一个频道的消息，你得先使用 `subscribe()` 方法订阅该频道，
然后用`set_message_cb()` 方法设置收到消息时调用的回调函数来接收消息。
**注**：频道是在调用 `subscribe()` 方法时创建的。
所以，只有客户端先进行 `subscribe()`，然后服务端再进行 `publish()`，才能确保客户端收到消息。

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

#### 2.4 发布消息

通过上面的代码订阅了 my_topic 后，
就可以使用 `publish()` 方法向所有订阅了 my_topic 频道的终端发消息了。

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

另外，还可以使用 `publish2()` 方法，带上更多的参数：qos、apn_json、time_to_live 以及 messageId。
```javascript
yunba.publish2({
    'topic': 'myTopic',
    'msg': 'publish2message',
    'opts': {
        'qos': 1,
        'time_to_live': 36000,
        'apn_json': {
            'aps': {'sound': 'bingbong.aiff', 'badge': 3, 'alert': 'yunba'}
        },
        'messageId': '11833652203486491113'
    }
}, cb)
```

将上面的例子扩展一下，就可以利用 Yunba 的服务，实现 JavaScript 下的消息推送功能了。
Yunba JavaScript SDK 包里的 examples 文件夹下，有相关的示例程序，
可参考 [运行 Yunba JavaScript Demo](https://github.com/yunba/docs/blob/master/quickstart/demo/Demo_JavaScript.md "运行 Yunba JavaScript Demo") 一文运行该程序，
更好地理解 JavaScript SDK 的使用。
