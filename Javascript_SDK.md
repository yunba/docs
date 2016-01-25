# Yunba JavaScript SDK 使用文档

通过利用 Yunba Javascript SDK 提供的接口 API，你可以很方便地在智能手机、平板电脑、网站等终端应用上使用 Yunba 的各种消息服务。
	

## 获取 SDK

  [下载最新SDK](https://github.com/yunba/yunba-javascript-sdk)


## 新手上路

调试工具可以用 Chrome 本身自带 JavaScript Console，其它浏览器可能需要安装相应插件。例如 FireFox 需要安装 FireBug。


### 第一步：引入 SDK

Yunba JavaScript SDK 依赖于 Socket.IO，所以要确保 Socket.IO 被先引入。
请参考（SDK 中的）Demo 分别引入 Socket.IO 和 yunba-js-sdk。


### 第二步：创建 Yunba 实例

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

如果你想接收一个频道的消息，你得先使用 `subscribe` 方法订阅该 [频道]( https://github.com/yunba/kb/blob/master/%E9%A2%91%E9%81%93%E5%92%8C%E5%88%AB%E5%90%8D.md)，
然后用 `set_message_cb` 方法设置收到消息时调用的回调函数来接收消息。

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

你可以使用 `publish` 方法向所有 `订阅` my_topic 频道的终端发布一条 “你好！Yunba。” 消息。

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


# Yunba JavaScript SDK API

## init

### 说明

与 Socket.IO 服务器建立连接。


### 基本使用

```javascript
yunba.init(init_callback, reconnect_callback)
```


### 参数说明

名称 | 类型 | 说明
--------- | ------- |  -----------
init_callback | function | 参数可选，通知 init 是否成功。参数 sucess: false/true。
reconnect_callback | function | 参数可选，连接断开后，调用该回调重连。


### 使用示例

```javascript
yunba.init(function (success) {
    if (success) {
        yunba.connect_by_customid(customid, callback) ...
    }
}, function () {
    yunba.connect_by_customid(customid, callback) ...
});
```


## connect

### 说明

**兼容旧版本接口，无法保存会话状态（包括离线消息、已订阅的频道和别名），推荐使用 connect_by_customid 代替。**

**在浏览器支持并开启 cookie 的情况下，会自动生成一个 uid 并保存到 cookie 中然后进行 connect_by_customid 连接，若不支持或没开启 cookie 则只会进行普通的 connect 连接。**

yunba `init` 实例初始化后只表明与服务器建立了 socket 连接，还需要通过 `connect` 方法（推荐使用 `connect_by_customid`）连接上消息服务器。连接上消息服务器后才开始收发消息。


### 基本使用

```javascript
yunba.connect(callback)
```


### 参数说明

名称 | 类型 | 说明
--------- | ------- |  -----------
callback | function | 参数可选，连接成功后会调用 callback。


### 使用示例

```javascript
yunba.connect(function (success, msg) {
    if (!success) {
        console.log(msg);
    }
});
```


## connect_by_customid

### 说明

与 `connect` 功能一致，不同的是此接口使用特定的会话 ID 进行连接，连接后的会话状态与上次连接一致（包括 [离线消息](https://github.com/yunba/kb/blob/master/%E4%BA%91%E5%B7%B4%E7%9A%84%E7%A6%BB%E7%BA%BF%E6%B6%88%E6%81%AF.md) 、已订阅的 [频道](https://github.com/yunba/kb/blob/master/%E9%A2%91%E9%81%93%E5%92%8C%E5%88%AB%E5%90%8D.md#%E9%A2%91%E9%81%93topic) 和 [别名](https://github.com/yunba/kb/blob/master/%E9%A2%91%E9%81%93%E5%92%8C%E5%88%AB%E5%90%8D.md#%E5%88%AB%E5%90%8Dalias)）。

**同时使用多个相同 customid 进行连接时只有一个连接是有效的。**

**注**：customid 和 alias 没有任何关系，如果想通过 customid 发消息，还需要将 alias 设置为 customid。参见 `set_alias` 。


### 基本使用

```javascript
yunba.connect_by_customid(customid, callback)
```


### 参数说明

名称 | 类型 | 说明
--------- | ------- |  -----------
customid | string | 参数必选，自定义会话 ID。
callback | function | 参数可选，连接成功后会调用 callback。


### 使用示例

```javascript
yunba.connect_by_customid('your_app_user_id', function (success, msg, sessionid) {
    if (success) {
        console.log(sessionid);
    } else {
        console.log(msg);
    }
});
```


## disconnect

### 说明

与 `connect` 相对，通过 `disconnect` 可以断开与消息服务器的连接。


### 基本使用：

```javascript
msg.disconnect(cb)
```


### 参数说明

名称 | 类型 |  说明
--------- | ------- |  -----------
cb | function | 参数可选，不管断开连接失败还是成功，都会回调此函数。传递回的参数有 success、msg。如果 success 值为 true 表示成功，否则表示失败。如果失败，则返回错误消息 msg。


### 使用示例

```javascript
yunba.disconnect(function (success, msg) {
    if (!success) {
        console.log(msg);
    }
});
```


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
cb | function | 参数必选，通过该回调函数监听所 `订阅` [频道](https://github.com/yunba/kb/blob/master/%E9%A2%91%E9%81%93%E5%92%8C%E5%88%AB%E5%90%8D.md#%E9%A2%91%E9%81%93topic) 的推送消息。传递回来的参数 data 是一个 object，含有消息频道（data.topic）与消息内容（data.msg）。如果消息为 presence 消息，data 中会多一个 presence 字段，其中包含 action、[alias](https://github.com/yunba/kb/blob/master/%E9%A2%91%E9%81%93%E5%92%8C%E5%88%AB%E5%90%8D.md#%E5%88%AB%E5%90%8Dalias) 和 timestamp。


### 使用示例

```javascript
yunba.set_message_cb(function (data) {
    console.log(data.topic);
    console.log(data.msg);
});
```


## subscribe

### 说明

通过 `subscribe` 收听一个 [频道]( https://github.com/yunba/kb/blob/master/%E9%A2%91%E9%81%93%E5%92%8C%E5%88%AB%E5%90%8D.md) 后，你就可以接收消息服务器向该频道发布的消息了。


### 基本使用

```javascript
yunba.subscribe(obj,cb)
```
	
	
### 参数说明

名称 | 类型 | 说明
--------- | ------- |  -----------
obj | object |  参数必选，obj 包含两个字段，obj.topic 表示准备收听的频道，obj.qos 表示 [qos](https://github.com/yunba/kb/blob/master/QoS.md) 级别（可选，默认为 1）。
cb  | functon | 参数可选，收听某频道成功或失败都会回调该函数。传递过来的参数有 success、msg。success 为 true 表示收听成功，否则表示失败。如果失败，则返回错误信息 msg。


### 使用示例

```javascript
yunba.subscribe({'topic': 'my_topic'}, function (success, msg) {
    if (!success) {
        console.log(msg);
    }
});
```


## subscribe_presence

### 说明

调用此函数 `subscribe_presence` 来监听 Topic（[频道]( https://github.com/yunba/kb/blob/master/%E9%A2%91%E9%81%93%E5%92%8C%E5%88%AB%E5%90%8D.md)）下面所有用户的上、下线 及 订阅（或取消订阅）该频道的事件通知，成功后用户状态一旦发生变化会向 Topic + '/p' 频道发送消息。

### 基本使用

```javascript
yunba.subscribe_presence(obj,cb)
```
	
	
### 参数说明

名称 | 类型 | 说明
--------- | ------- |  -----------
obj | object |  参数必选，obj 包含两个字段，obj.topic 表示准备收听的频道，obj.qos 表示 [qos](https://github.com/yunba/kb/blob/master/QoS.md) 级别（可选，默认为 1）。
cb  | functon | 参数可选，收听某频道成功或失败都会回调该函数。传递过来的参数有 success、msg。success 为 true 表示收听成功，否则表示失败。如果失败，则返回错误信息 msg。


### 使用示例

```javascript
yunba.subscribe_presence({'topic': 'my_topic'}, function (success, msg) {
    if (!success) {
        console.log(msg);
    }
});
```


## unsubscribe

### 说明

你可以通过 `unsubscribe` 取消对一个频道的收听。


### 基本使用

```javascript
yunba.unsubscribe(obj,cb)
```


### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
obj | object | 参数必选，目前版本只要求 obj 包含一个属性字段为 topic，即准备取消收听的 [频道]( https://github.com/yunba/kb/blob/master/%E9%A2%91%E9%81%93%E5%92%8C%E5%88%AB%E5%90%8D.md)。
cb | function | 参数可选，取消收听某频道成功或失败都会回调该函数。传递过来的参数有 success、msg。success 为 true 表示取消收听成功，否则表示失败。如果失败，则返回错误信息 msg。


### 使用示例

```javascript
yunba.unsubscribe({'topic': 'my_topic'}, function (success, msg) {
    if (!success) {
        console.log(msg);
    }
});
```


## unsubscribe_presence

### 说明

调用此函数 `unsubscribe_presence` 来取消监听 Topic（[频道]( https://github.com/yunba/kb/blob/master/%E9%A2%91%E9%81%93%E5%92%8C%E5%88%AB%E5%90%8D.md)）下面所有用户的上、下线 及 订阅（或取消订阅）该频道的事件通知。


### 基本使用

```javascript
yunba.unsubscribe_presence(obj,cb)
```


### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
obj | object | 参数必选，目前版本只要求 obj 包含一个属性字段为 topic，即准备取消收听的频道。
cb | function | 参数可选，取消收听某频道成功或失败都会回调该函数。传递过来的参数有 success、msg。success 为 true 表示取消收听成功，否则表示失败。如果失败，则返回错误信息 msg。


### 使用示例

```javascript
yunba.unsubscribe_presence({'topic': 'my_topic'}, function (success, msg) {
    if (!success) {
        console.log(msg);
    }
});
```


## publish

### 说明

Yunba 客户端实例可以通过 `publish` 向某频道发布消息。那么任何 `订阅` 此 Topic 的 Client 都会接收到消息。

**注**：需要先 `subscribe` （订阅）该频道


### 基本使用

```javascript
yunba.publish(obj,cb)
```


### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
obj    | object | 参数必选，obj 含有四个属性字段，分别为要发送的 目标 [频道]( https://github.com/yunba/kb/blob/master/%E9%A2%91%E9%81%93%E5%92%8C%E5%88%AB%E5%90%8D.md)（obj.topic:string）、消息体（obj.msg:string）、MessageId （obj.messageId:number/string） 和 [消息级别](https://github.com/yunba/kb/blob/master/QoS.md)（obj.qos:number），其中 obj.qos 为可选，默认值为 1。
cb    | function | 参数可选，不管消息发布是成功或失败都会回调此函数。传递回的参数有 success、msg。success 值为 true 表示消息发布成功，否则发送失败。如果发送成功返回含有 messageId 的 msg，发送失败返回错误消息 msg。


### 使用示例

```javascript
yunba.publish({'topic': 'my_topic', 'msg': 'test_message','messageId': 199900724, 'qos': 1}, function (success, msg) {
    if (!success) {
        console.log(msg);
    }
});
```


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
obj    | object | 参数必选，obj 含有三个属性字段，分别为要发送的目标 [频道]( https://github.com/yunba/kb/blob/master/%E9%A2%91%E9%81%93%E5%92%8C%E5%88%AB%E5%90%8D.md)（obj.topic:string）、消息体（obj.msg:string）和 扩展参数（obj.opts:dict）。
cb    | function | 参数可选，不管消息发布是成功或失败都会回调此函数。传递回的参数有 success、msg。success 值为 true 表示消息发布成功，否则发送失败。如果发送成功返回含有 messageId 的 msg ，发送失败返回错误消息 msg。


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

`publish2` 扩展参数 opts 是可选项，如果不填写参数，`publish2` 的行为与 `publish` 相似（除了 apn_json 参数）。

名称 | 类型 | 说明
--------- | ------- | -----------
qos | number | 如果不填，默认为 1，参数设置参考 [QoS]( https://github.com/yunba/kb/blob/master/QoS.md)。
apn_json | dict | 如果不填，则不会发送 iOS 端的 APNs 消息；而 `publish` 会发送默认的 APNs 消息。APNs 具体可参考：[Apple 官方文档](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html) 和 [生成 APNs 证书文档](https://github.com/yunba/docs/blob/master/support/knowledge_base/create_APNs_certificate.md)。
messageId | String | 消息 ID，64 位整型数转化成 string。如果不填，由系统自动生成。
time_to_live | number | [离线消息](https://github.com/yunba/kb/blob/master/%E4%BA%91%E5%B7%B4%E7%9A%84%E7%A6%BB%E7%BA%BF%E6%B6%88%E6%81%AF.md) 保留时间值，单位是秒（例如 2 天 2\*24\*3600），当前默认值为 5 天。


## publish_to_alias

### 说明

向设置该 [别名](https://github.com/yunba/kb/blob/master/%E9%A2%91%E9%81%93%E5%92%8C%E5%88%AB%E5%90%8D.md#%E5%88%AB%E5%90%8Dalias) 的对象发送消息，用于实现点对点的消息发送。

**注**：需要先 `setAlias` 进行别名设置。


### 基本使用

```javascript
yunba.publish_to_alias(obj, cb)
```


### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
obj    | object | 参数必选，obj 含有四个属性字段，分别为要发送的 目标别名（obj.alias:string）、消息体（obj.msg:string）、MessageId（obj.messageId:number/string） 和 [消息级别](https://github.com/yunba/kb/blob/master/QoS.md)（obj.qos:number）。其中 obj.alias 为用户设置的别名信息，只支持英文、数字和下划线，长度不超过 50 个字符，obj.qos 为可选，默认值为 1。
cb    | function | 参数可选，不管消息发布是否成功或失败都会回调此函数。传递回的参数有 success、msg。success 值为 true 表示消息发布成功，否则发送失败。如果发送成功返回含有 messageId 的 msg ，发送失败返回错误消息 msg。


### 使用示例

```javascript
yunba.publish_to_alias({
    'alias': 'my_alias',
    'msg': 'test_message', 
    'messageId': 199900724, 
    'qos': 1
    }, function (success, msg) {
        if (!success) {
            console.log(msg);
        }
    });
```


## publish2_to_alias

### 说明

`publish2` 的 alias 版本，比 `publish_to_alias` 支持更多参数。

**注**：需要先 `setAlias` 进行 [别名](https://github.com/yunba/kb/blob/master/%E9%A2%91%E9%81%93%E5%92%8C%E5%88%AB%E5%90%8D.md#%E5%88%AB%E5%90%8Dalias) 设置。


### 基本使用

```javascript
yunba.publish2_to_alias(obj,cb)
```


### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
obj    | object | 参数必选，obj 含有三个属性字段，分别为要发送的 目标别名（obj.alias:string）、消息体（obj.msg:string） 和 扩展参数（obj.opts:dict）。其中 obj.alias 为用户设置的别名信息，只支持英文、数字和下划线，长度不超过 50 个字符。
cb    | function | 参数可选，不管消息发布是否成功或失败都会回调此函数。传递回的参数有 success、msg。success 值为 true 表示消息发布成功，否则发送失败。如果发送成功返回含有 messageId 的 msg ，发送失败返回错误消息 msg。


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
qos | number | 如果不填，默认为 1，参数设置参考 [QoS]( https://github.com/yunba/kb/blob/master/QoS.md)。
apn_json | dict | 如果不填，则不会发送 iOS 端的 APNs 消息。APNs 具体可参考：[Apple 官方文档](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html) 和 [生成 APNs 证书文档](https://github.com/yunba/docs/blob/master/support/knowledge_base/create_APNs_certificate.md)。
messageId | String | 消息 ID，64 位整型数转化成 string。如果不填，由系统自动生成。
time_to_live | number | [离线消息](https://github.com/yunba/kb/blob/master/%E4%BA%91%E5%B7%B4%E7%9A%84%E7%A6%BB%E7%BA%BF%E6%B6%88%E6%81%AF.md) 保留时间值，单位是秒（例如 2 天 2\*24\*3600），当前默认值为 5 天。


## set_alias

### 说明

App 可以调用此函数来绑定账号，用户名，每个用户只能指定一个 [别名](https://github.com/yunba/kb/blob/master/%E9%A2%91%E9%81%93%E5%92%8C%E5%88%AB%E5%90%8D.md#%E5%88%AB%E5%90%8Dalias)。


### 基本使用

```javascript
yunba.set_alias(alias, cb);
```


### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
obj  | object | 参数必选，obj 含有一个属性字段，为要设置的 别名（obj.alias:string）。obj.alias 为用户设置的别名信息，只支持英文、数字和下划线，长度不超过 50 个字符。
cb   | function | 参数可选，无论 alias 是否设置成功都会回调此函数。传递回的参数有 data。data.success 值为 true 表示 alias 设置成功，否则为失败。如果失败，则返回错误消息 data.msg。


### 使用示例

```javascript
yunba.set_alias({'alias': 'my_alias'}, function (data) {
    if (!data.success) {
        console.log(data.msg);
    }
});
```


## get_alias

### 说明

App 可以调用此函数来获取当前用户的 [别名](https://github.com/yunba/kb/blob/master/%E9%A2%91%E9%81%93%E5%92%8C%E5%88%AB%E5%90%8D.md#%E5%88%AB%E5%90%8Dalias)。


### 基本使用

```javascript
yunba.get_alias(cb);
```


### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
cb   | function | 传递回的参数 data.alias 为当前用户的别名。

```javascript
yunba.get_alias(function(data) {
    console.log(data.alias);
});
```


## get_state

### 说明

可以通过 `get_state` 查看传入的用户 [别名](https://github.com/yunba/kb/blob/master/%E9%A2%91%E9%81%93%E5%92%8C%E5%88%AB%E5%90%8D.md#%E5%88%AB%E5%90%8Dalias) 的在线状态


### 基本使用

```javascript
yunba.get_state(alias,cb)
```


### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
alias | String | 参数必选，参数为要查询状态的 alias 名称。
cb | function | 参数可选，无论查询结果如何都会回调此函数。传递回的参数有 success、data、data.messageId、error_msg。查询成功 success 为 true 否则为 false，data 表示在线状态，success 为 false 时 error_msg 有效。


### 使用示例

```javascript
yunba.get_state(alias, function (data) {
    if (data.success) {
        console.log(data.data);
    } else {
        console.log(data.error_msg);
    }
});
```


## get_topic_list

### 说明

可以查询用户订阅的 [频道]( https://github.com/yunba/kb/blob/master/%E9%A2%91%E9%81%93%E5%92%8C%E5%88%AB%E5%90%8D.md) 列表，如果不传入参数 alias， 则是获取当前用户的频道列表，如果输入参数 alias，则是获取目标 alias 的频道列表。


### 基本使用

```javascript
yunba.get_topic_list(alias, cb)
```

### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
alias | String | 用户设置的 [别名](https://github.com/yunba/kb/blob/master/%E9%A2%91%E9%81%93%E5%92%8C%E5%88%AB%E5%90%8D.md#%E5%88%AB%E5%90%8Dalias) 信息，只支持英文数字下划线，长度不超过 50 个字符。
cb | function | 无论查询结果如何都会回调此函数。传递回的参数有 success、data.topics、error_msg。查询成功 success 为 true，否则为 false。data.topics 为订阅的 `topic` 列表，类型 List，success 为 true 时有效。success 为 false 时 error_msg 有效。


### 使用示例

```javascript
yunba.get_topic_list('my_alias', function (success, data) {
    if (success) {
        data.topics.forEach(function (topic) {
            console.log(topic);
        });
    } else {
        console.log(data.error_msg);
    }
});
```


## get_alias_list

### 说明

通过调用此函数可以获取该 topic 下所有订阅用户的 [别名](https://github.com/yunba/kb/blob/master/%E9%A2%91%E9%81%93%E5%92%8C%E5%88%AB%E5%90%8D.md#%E5%88%AB%E5%90%8Dalias)。


### 基本使用

```javascript
yunba.get_alias_list(topic, cb)
```


### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
topic | String | App 订阅的 [频道]( https://github.com/yunba/kb/blob/master/%E9%A2%91%E9%81%93%E5%92%8C%E5%88%AB%E5%90%8D.md)，topic 只支持英文数字下划线，长度不超过 50 个字符，数组的长度不超过 100。
cb | function | 无论查询结果如何都会回调此函数。传递回的参数有 success、data.alias、error_msg。查询成功 success 为 true，否则为 false。data.alias 为订阅的 `alias` 列表，类型 List，success 为 true 时有效。success 为 false 时 error_msg 有效。


### 使用示例

```javascript
yunba.get_alias_list('my_topic', function (success, data) {
    if (success) {
        data.alias.forEach(function (alias) {
            console.log(alias);
        });
    } else {
        console.log(data.error_msg);
    }
});
```
