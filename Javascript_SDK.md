# Yunba JavaScript SDK 使用文档

通过利用 Yunba Javascript SDK 提供的接口 API，你可以很方便的在智能手机、平板电脑、网站等终端应用上使用 Yunba 的各种消息服务。

## 获取 SDK

* [下载最新SDK](https://github.com/yunba/yunba-javascript-sdk)

## 新手上路

调试工具可以用 Chrome 本身自带 JavaScript Console, 其它浏览器可能需要安装相应插件。例如 FireFox 需要安装 FireBug。

### 第一步：引入 SDK

Yunba JavaScript SDK 依赖于 socket.io，所以要确保 socket.io 被先引入。
	
### 第二步：创建 Yunba 实例

```javascript
var yunba = new Yunba({server: 'sock.yunba.io', port: 3000, appkey: appkey});
```

### 第三步：初始化并连接消息服务器

```javascript
yunba.init(function (success) {
	if (success) {
		yunba.connect_v2(function (success, msg, sessionid) {
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

如果你想接收一个频道的消息，你得先使用 `subscribe()` 方法订阅该频道，
然后用`set_message_cb()` 方法设置收到消息时调用的回调函数来接收消息。

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

你可以使用 `publish()` 方法向所有订阅 my_topic 频道的终端发布一条“你好！Yunba。”消息。

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

将上面的例子扩展一下，我们就可以利用 Yunba 实现在 JavaScript 和其他平台之间实时通讯。

# Yunba JavaScript SDK API

## init

### 说明
与 socket.io 服务器建立连接。

### 基本使用

```javascript
yunba.init(init_callback, reconnect_callback)
```

### 参数说明

名称 | 类型 | 说明
--------- | ------- |  -----------
init_callback | function | 参数可选，通知 init 是否成功。参数 sucess: false/true
reconnect_callback | function | 参数可选，连接断开后，调用该回调重连


## connect

### 说明
yunba 实例初始化后只表明与服务器建立了 socket 连接，还需要通过 `connect()`方法连接上消息服务器。连接上消息服务器后才开始收发消息。

### 基本使用

```javascript
yunba.connect(callback)
```

### 参数说明
名称 | 类型 | 说明
--------- | ------- |  -----------
callback | function | 参数可选，连接成功后会调用 callback

## connect_v2

### 说明
与connect功能一致，不同的是此接口会将会话状态保存下来（保存到url的query参数中），当由于网络不稳定等原因发生重新连接后会话状态不会丢失（包括离线消息、已订阅的频道和别名）。

### 基本使用

```javascript
yunba.connect_v2(callback)
```

### 参数说明
名称 | 类型 | 说明
--------- | ------- |  -----------
callback | function | 参数可选，连接成功后会调用 callback

## connect_by_sessionid

### 说明
与connect功能一致，不同的是此接口使用特定的会话ID进行连接，连接后的会话状态与上次连接一致（包括离线消息、已订阅的频道和别名）。

### 基本使用

```javascript
yunba.connect_by_sessionid(sessionid, callback)
```

### 参数说明
名称 | 类型 | 说明
--------- | ------- |  -----------
sessionid | string | 参数必选, 会话ID（connect_v2连接成功后的回调函数会返回此参数）
callback | function | 参数可选，连接成功后会调用 callback

## connect_by_customid

### 说明
与connect_by_sessionid功能一致，不同的是此接口可以设定自定义的会话ID。

### 基本使用

```javascript
yunba.connect_by_customid(customid, callback)
```

### 参数说明
名称 | 类型 | 说明
--------- | ------- |  -----------
customid | string | 参数必选, 自定义会话ID
callback | function | 参数可选，连接成功后会调用 callback

## set_message_cb

### 说明
设置收到消息时调用的回调函数。

### 基本使用

```javascript
yunba.set_message_cb(cb)
```

### 参数说明
名称 | 类型 | 说明
--------- | ------- |  -----------
cb | function | 参数必选, 通过该回调函数监听所收听频道的推送消息。传递回来的参数为 data 是一个 object，含有消息频道(data.topic)与消息内容(data.msg)

## subscribe

### 说明
通过 `subscribe()` 收听一个频道后，你就可以接收消息服务器向该频道推送的消息了。

### 基本使用

```javascript
yunba.subscribe(obj,cb)
```
	
### 参数说明
名称 | 类型 | 说明
--------- | ------- |  -----------
obj | object |  参数必选，obj 包含两个字段，obj.topic 表示准备收听的频道，obj.qos 表示 qos 级别（可选，默认为 1）
cb  | functon | 参数可选，收听成功或失败后的回调函数。传递回来的参数有 success、granted，sucees 为 true 表示收听成功，否则收听失败。如果收听成功则返回 granted，granted 为一个 object，含有两个字段，分别为收听的频道名称（granted.topic）和该频道的 qos 级别(granted.qos)

## unsubscribe

### 说明
你可以通过 `unsubscribe()` 取消对一个频道的收听。
程序关闭之前建议先进行 `unsubscribe()` 操作，再关闭程序。

### 基本使用

```javascript
yunba.unsubscribe(obj,cb)
```

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
obj | object | 参数必选，目前版本之要求 obj 包含一个属性字段为 topic，即准备取消收听的频道
cb | function | 参数可选，取消收听某频道成功或失败都会回调该函数。传递过来的参数有 success、msg。success 为 true 表示取消收听成功，否则表示失败。如果失败，则返回错误信息 msg

## publish

### 说明
Yunba 客户端实例可以通过 `publish()` 向某频道发布消息。

### 基本使用

```javascript
yunba.publish(obj,cb)
```

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
obj    | object | 参数必选，obj 含有三个属性字段，分别为要发送的 目标频道(obj.topic:string)、消息体(obj.msg:string) 和 消息级别(obj.qos:number)，其中 obj.qos 为可选，默认值为 1
cb    | function | 参数可选，不管消息发布是否成功或失败都会回调此函数。传递回的参数有 success、msg。success 值为 true 表示消息发布成功，否则发送失败。如果发送失败，则返回错误消息 msg

## publish2

### 说明
`publish` 升级版本，支持更多参数。

### 基本使用

```javascript
yunba.publish2(obj,cb)
```
### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
obj    | object | 参数必选，obj 含有三个个属性字段，分别为要发送的 目标频道(obj.topic:string)、消息体(obj.msg:string) 和 扩展参数(obj.opts:dict)
cb    | function | 参数可选，不管消息发布是否成功或失败都会回调此函数。传递回的参数有 success、msg。success 值为 true 表示消息发布成功，否则发送失败。如果发送失败，则返回错误消息 msg


### 使用示例

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

### 扩展参数说明

`publish2` 扩展参数 opts 是可选项，如果不填写参数，`publish2` 的行为与 `publish` 相同。

名称 | 类型 | 说明
--------- | ------- | -----------
qos | number | 如果不填，默认为 1
apn_json | dict | 如果不填，则不会发送APN
messageId | String | 消息 ID，64 位整型数转化成 string。如果不填，由系统自动生成
time_to_live | number | 离线消息保留时间值，单位是秒(例如2天 2\*24\*3600)，当前默认值为5天

## publish_to_alias

### 说明
向用户别名发送消息, 用于实现点对点的消息发送。

### 基本使用

```javascript
yunba.publish_to_alias(obj, cb)
```

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
obj    | object | 参数必选，obj 含有三个属性字段，分别为要发送的 目标别名(obj.alias:string)、消息体(obj.msg:string) 和 消息级别(obj.qos:number)，其中 obj.alias 为用户设置的别名信息，只支持英文数字下划线，长度不超过50个字符 obj.qos 为可选，默认值为 1
cb    | function | 参数可选，不管消息发布是否成功或失败都会回调此函数。传递回的参数有 success、msg。success 值为 true 表示消息发布成功，否则发送失败。如果发送失败，则返回错误消息 msg

## publish2_to_alias

### 说明
`publish2` 的 alias 版本。

### 基本使用

```javascript
yunba.publish2(obj,cb)
```
### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
obj    | object | 参数必选，obj 含有三个个属性字段，分别为要发送的 目标别名(obj.alias:string)、消息体(obj.msg:string) 和 扩展参数(obj.opts:dict)
cb    | function | 参数可选，不管消息发布是否成功或失败都会回调此函数。传递回的参数有 success、msg。success 值为 true 表示消息发布成功，否则发送失败。如果发送失败，则返回错误消息 msg


### 使用示例

```javascript
yunba.publish2_to_alias({
    'alias': 'myAlias',
    'msg': 'publish_2_alias_message',
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

### 扩展参数说明

`publish2_to_alias` 扩展参数 opts 是可选项。

名称 | 类型 | 说明
--------- | ------- | -----------
qos | number | 如果不填，默认为 1
apn_json | dict | 如果不填，则不会发送APN
messageId | String | 消息 ID，64 位整型数转化成 string。如果不填，由系统自动生成
time_to_live | number | 离线消息保留时间值，单位是秒(例如2天 2\*24\*3600)，当前默认值为5天

## get_state

### 说明
可以通过 `get_state()` 查看在线状态

### 基本使用

```javascript
yunba.get_state(alias,cb)
```

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
alias | String | 参数必选，参数为要查询状态的 alias 名称
cb | function | 参数可选，无论查询结果如何都会回调此函数。传递回的参数有 success、data、error_msg。查询成功 success 为 true 否则为 false，data 表示在线状态，success 为 false 时 error_msg 有效。

## get_alias_list

### 说明
通过调用此函数可以获取订阅该 topic 下所有用户的别名。

### 基本使用

```javascript
yunba.get_alias_list(topic, cb)
```

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
topic | String | app 订阅的的频道，topic 只支持英文数字下划线，长度不超过50个字符,数组的长度不超过100
cb | function | 无论查询结果如何都会回调此函数。传递回的参数有 success、data.alias、error_msg。查询成功 success 为 true 否则为 false，data.alias 为订阅的 `alias` 列表，类型 List，success 为 true 时有效，success 为 false 时 error_msg 有效。

## disconnect

### 说明
与 `connect()` 相对，通过 `disconnect()` 可以断开与消息服务器的连接。

### 基本使用：

```javascript
msg.disconnect(cb)
```

### 参数说明
名称 | 类型 |  说明
--------- | ------- |  -----------
cb | function | 参数可选，不管断开连接失败还是成功，都会回调此函数。传递回的参数有 success、msg。如果 success 值为 true 表示成功，否则表示失败。如果失败，则返回错误消息msg

> JavaScript SDK 别名(Alias) 相关 API 代码已经完成并开源，文档正在完善中。暂时可以参考 examples 中对这部分 API 的使用方法。