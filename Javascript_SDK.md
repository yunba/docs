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

这里，需要在云巴 [Portal](https://github.com/yunba/kb/blob/master/Portal.md#portal) 上创建新应用，用获取到的 [AppKey](https://github.com/yunba/kb/blob/master/AppKey.md#appkey) 创建 Yunba 实例。

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

如果你想接收一个频道的消息，你得先使用`subscribe()`方法订阅该 [频道]( https://github.com/yunba/kb/blob/master/%E9%A2%91%E9%81%93%E5%92%8C%E5%88%AB%E5%90%8D.md)，
然后用`set_message_cb()`方法设置收到消息时调用的回调函数来接收消息。

**注**：`subscribe()`的前提是客户端已经成功连上服务器。为了确保在连接成功以后再进行订阅，可以将`subscribe()`写到`connect_by_customid()`的回调函数中，在 success 时才执行。

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


# Yunba JavaScript SDK API

## init

### 功能

与 Socket.IO 服务器建立连接。


### 基本使用

```javascript
yunba.init(init_callback, reconnect_callback)
```


### 参数说明

名称 | 类型 | 说明
--------- | ------- |  -----------
init_callback | Function | 参数可选。通知 init 是否成功。参数 success: 成功返回 true，失败返回 false。
reconnect_callback | Function | 参数可选。连接断开后，调用该回调重连。


### 代码示例

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

### 功能

**兼容旧版本接口，无法保存会话状态（包括离线消息、已订阅的频道和别名），推荐使用 connect_by_customid 代替。**

**在浏览器支持并开启 cookie 的情况下，会自动生成一个 uid 并保存到 cookie 中然后进行 connect_by_customid 连接，若不支持或没开启 cookie 则只会进行普通的 connect 连接。**

yunba `init()`实例初始化后，只表明与服务器建立了 socket 连接，还需要通过`connect()`方法（推荐使用`connect_by_customid()`）连接上消息服务器。连接上消息服务器后，才开始收发消息。


### 基本使用

```javascript
yunba.connect(callback)
```


### 参数说明

名称 | 类型 | 说明
--------- | ------- |  -----------
callback | Function | 参数可选。连接成功后会调用 callback。


### 代码示例

```javascript
yunba.connect(function (success, msg) {
    if (!success) {
        console.log(msg);
    }
});
```


## connect_by_customid

### 功能

与`connect()`功能一致，不同的是此接口使用特定的会话 ID 进行连接，连接后的会话状态与上次连接一致（包括 [离线消息](https://github.com/yunba/kb/blob/master/%E4%BA%91%E5%B7%B4%E7%9A%84%E7%A6%BB%E7%BA%BF%E6%B6%88%E6%81%AF.md)、已订阅的 [频道](https://github.com/yunba/kb/blob/master/%E9%A2%91%E9%81%93%E5%92%8C%E5%88%AB%E5%90%8D.md#%E9%A2%91%E9%81%93topic) 和 [别名](https://github.com/yunba/kb/blob/master/%E9%A2%91%E9%81%93%E5%92%8C%E5%88%AB%E5%90%8D.md#%E5%88%AB%E5%90%8Dalias)）。

**同时使用多个相同 customid 进行连接时只有一个连接是有效的。**

**注**：customid 和 alias 没有任何关系，如果想通过 customid 发消息，还需要将 alias 设置为 customid。参见`set_alias()`。


### 基本使用

```javascript
yunba.connect_by_customid(customid, callback)
```


### 参数说明

名称 | 类型 | 说明
--------- | ------- |  -----------
customid | String | 参数必选。自定义会话 ID。
callback | Function | 参数可选。连接成功后会调用 callback。


### 代码示例

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

### 功能

与`connect()`相对，通过`disconnect()`可以断开与消息服务器的连接。


### 基本使用

```javascript
msg.disconnect(cb)
```

### 参数说明

名称 | 类型 |  说明
--------- | ------- |  -----------
cb | Function | 参数可选。不管断开连接失败还是成功，都会回调此函数。传递回的参数有 success、msg。如果 success 值为 true 表示成功，否则表示失败。如果失败，则返回错误消息 msg。


### 代码示例

```javascript
yunba.disconnect(function (success, msg) {
    if (!success) {
        console.log(msg);
    }
});
```


## set_message_cb

### 功能

设置收到消息时调用的回调函数。


### 基本使用

```javascript
yunba.set_message_cb(cb)
```


### 参数说明

名称 | 类型 | 说明
--------- | ------- |  -----------
cb | Function | 参数必选。通过该回调函数监听所订阅 [频道](https://github.com/yunba/kb/blob/master/%E9%A2%91%E9%81%93%E5%92%8C%E5%88%AB%E5%90%8D.md#%E9%A2%91%E9%81%93topic) 的推送消息。传递回来的参数 data 是一个 object，包含消息频道（data.topic）与消息内容（data.msg）。如果消息为 [presence](https://github.com/yunba/kb/blob/master/Presence.md) 消息，data 中会多一个 presence 字段，其中包含 action、[alias](https://github.com/yunba/kb/blob/master/%E9%A2%91%E9%81%93%E5%92%8C%E5%88%AB%E5%90%8D.md#%E5%88%AB%E5%90%8Dalias) 和 timestamp。


### 代码示例

```javascript
yunba.set_message_cb(function (data) {
    console.log(data.topic);
    console.log(data.msg);
});
```


## subscribe

### 功能

增加订阅一个 [频道](https://github.com/yunba/kb/blob/master/频道和别名.md#频道topic)。成功订阅后，可以收到来自该频道的消息。新增订阅不会影响已有的订阅。

### 基本使用

```javascript
yunba.subscribe(obj,cb)
```
    
    
### 参数说明

名称 | 类型 | 说明
--------- | ------- |  -----------
obj | Object |  参数必选。obj 包含 obj.topic 和 obj.qos 两个字段。obj.topic 表示准备收听的频道；obj.qos 为可选，表示服务质量等级，有三种取值：“0”表示最多送达一次；“1”表示最少送达一次；“2”表示保证送达且仅送达一次。默认为“1”。详见 [QoS](https://github.com/yunba/kb/blob/master/QoS.md) 的说明。
cb  | Function | 参数可选。收听某频道成功或失败都会回调该函数。传递过来的参数有 success、msg。success 为 true 表示收听成功，否则表示失败。如果失败，则返回错误信息 msg。


### 代码示例

```javascript
yunba.subscribe({'topic': 'my_topic'}, function (success, msg) {
    if (!success) {
        console.log(msg);
    }
});
```

## subscribe_presence

### 功能

监听某 [频道]( https://github.com/yunba/kb/blob/master/%E9%A2%91%E9%81%93%E5%92%8C%E5%88%AB%E5%90%8D.md) 下所有用户的上、下线及订阅、取消订阅该频道的事件通知。调用成功后，可以收到所有用户的状态变化。

注意：`subscribe_presence` 只能监听已经设置了 [别名](https://github.com/yunba/kb/blob/master/频道和别名.md#别名alias) 的客户端，未设置 别名 的客户端不会被监听。

参见 [云巴的实时在线](https://github.com/yunba/kb/blob/master/Presence.md)。

### 基本使用

```javascript
yunba.subscribe_presence(obj,cb)
```
    
    
### 参数说明

名称 | 类型 | 说明
--------- | ------- |  -----------
obj | Object |  参数必选。obj 包含 obj.topic 和 obj.qos 两个字段。obj.topic 表示准备收听的频道；obj.qos 为可选，表示服务质量等级，有三种取值：“0”表示最多送达一次；“1”表示最少送达一次；“2”表示保证送达且仅送达一次。默认为“1”。详见 [QoS](https://github.com/yunba/kb/blob/master/QoS.md) 的说明。
cb  | Function | 参数可选。收听某频道成功或失败都会回调该函数。传递过来的参数有 success、msg。success 为 true 表示收听成功，否则表示失败。如果失败，则返回错误信息 msg。


### 代码示例

```javascript
yunba.subscribe_presence({'topic': 'my_topic'}, function (success, msg) {
    if (!success) {
        console.log(msg);
    }
});
```


## unsubscribe

### 功能

取消对某一个 [频道](https://github.com/yunba/kb/blob/master/%E9%A2%91%E9%81%93%E5%92%8C%E5%88%AB%E5%90%8D.md) 的收听。


### 基本使用

```javascript
yunba.unsubscribe(obj,cb)
```


### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
obj | Object | 参数必选。目前版本只要求 obj 包含一个属性字段为 topic，即准备取消收听的 [频道]( https://github.com/yunba/kb/blob/master/%E9%A2%91%E9%81%93%E5%92%8C%E5%88%AB%E5%90%8D.md)。
cb | Function | 参数可选。取消收听某频道成功或失败都会回调该函数。传递过来的参数有 success、msg。success 为 true 表示取消收听成功，否则表示失败。如果失败，则返回错误信息 msg。


### 代码示例

```javascript
yunba.unsubscribe({'topic': 'my_topic'}, function (success, msg) {
    if (!success) {
        console.log(msg);
    }
});
```


## unsubscribe_presence

### 功能

取消对某 [频道](https://github.com/yunba/kb/blob/master/%E9%A2%91%E9%81%93%E5%92%8C%E5%88%AB%E5%90%8D.md) 下所有用户上、下线及订阅、取消订阅该频道的事件通知的监听。

参见 [云巴的实时在线](https://github.com/yunba/kb/blob/master/Presence.md)。

### 基本使用

```javascript
yunba.unsubscribe_presence(obj,cb)
```


### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
obj | Object | 参数必选。目前版本只要求 obj 包含一个 topic 字段，即准备取消收听的频道。
cb | Function | 参数可选。取消收听某频道成功或失败都会回调该函数。传递过来的参数有 success、msg。success 为 true 表示取消收听成功，否则表示失败。如果失败，则返回错误信息 msg。


### 代码示例

```javascript
yunba.unsubscribe_presence({'topic': 'my_topic'}, function (success, msg) {
    if (!success) {
        console.log(msg);
    }
});
```


## publish

### 功能

向某个 [频道](https://github.com/yunba/kb/blob/master/频道和别名.md#频道topic) 发布消息。成功发布后，所有订阅此频道的客户端都会收到消息。

**注**：接收端需要先通过`subscribe()`订阅该频道。


### 基本使用

```javascript
yunba.publish(obj,cb)
```

### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
obj    | Object | 参数必选。obj 含有四个属性字段，分别为 目标 [频道](https://github.com/yunba/kb/blob/master/%E9%A2%91%E9%81%93%E5%92%8C%E5%88%AB%E5%90%8D.md)（obj.topic:string）、消息体（obj.msg:string）、MessageId（obj.messageId:number/string）和 [消息级别](https://github.com/yunba/kb/blob/master/QoS.md)（obj.qos:number），其中 obj.qos 为可选，表示服务质量等级，有三种取值：“0”表示最多送达一次；“1”表示最少送达一次；“2”表示保证送达且仅送达一次。默认为“1”。详见 [QoS](https://github.com/yunba/kb/blob/master/QoS.md) 的说明。
cb    | Function | 参数可选。不管消息发布是成功或失败都会回调此函数。传递回的参数有 success、msg。success 值为 true 表示消息发布成功，否则发送失败。如果发送成功返回含有 messageId 的 msg，发送失败返回错误消息 msg。


### 代码示例

```javascript
yunba.publish({'topic': 'my_topic', 'msg': 'test_message','messageId': 199900724, 'qos': 1}, function (success, msg) {
    if (!success) {
        console.log(msg);
    }
});
```


## publish2

### 功能

`publish()`的升级版本，支持更多参数。


### 基本使用

```javascript
yunba.publish2(obj,cb)
```

### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
obj    | Object | 参数必选。obj 含有三个属性字段，分别为要发送的目标 [频道]( https://github.com/yunba/kb/blob/master/%E9%A2%91%E9%81%93%E5%92%8C%E5%88%AB%E5%90%8D.md)（obj.topic:string）、消息体（obj.msg:string）和 [扩展参数](#扩展参数说明)（obj.opts:dict）。
cb    | Function | 参数可选。不管消息发布是成功或失败都会回调此函数。传递回的参数有 success、msg。success 值为 true 表示消息发布成功，否则发送失败。如果发送成功返回含有 messageId 的 msg，发送失败返回错误消息 msg。


### 扩展参数说明

`publish2()`与`publish2_to_alias()`的扩展参数都是可选项。如果不填写参数，则`publish2()`/`publish2_to_alias()`的行为与`publish()`/`publish_to_alias()`相似，只有一点不同：`publish()`/`publish_to_alias()`会发送默认的 APN，而`publish2()`/`publish2_to_alias()`如果不填写 apn_json，则不会发送 APN。

名称 | 类型 | 说明
--------- | ------- | -----------
qos | Number | 服务质量等级。有三种取值：“0”表示最多送达一次；“1”表示最少送达一次；“2”表示保证送达且仅送达一次。默认为“1”。详见 [QoS](https://github.com/yunba/kb/blob/master/QoS.md) 的说明。
apn_json | Dict | 如果不填，则不会发送 APN。 APN 参考：[Apple 官方文档](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html) 及云巴 [相关文档](https://github.com/yunba/kb/blob/master/APNs/Payload.md)。
messageId | String | 消息 ID，64 位整型数转化成 String。发布消息时可以指定，如果不填，则由系统自动生成。
time_to_live | Number | 用来设置 [离线消息](https://github.com/yunba/kb/blob/master/%E4%BA%91%E5%B7%B4%E7%9A%84%E7%A6%BB%E7%BA%BF%E6%B6%88%E6%81%AF.md) 保留多久。单位为秒（例如，3600 代表 1 小时），默认值为 5 天，最大不超过 15 天。


### 代码示例

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

## publish_to_alias

### 功能

向某个 [别名](https://github.com/yunba/kb/blob/master/频道和别名.md#别名alias) 发布消息。发布成功后，该别名的客户端会收到消息。用于实现点对点的消息发送。

**注**：接收方需要先通过`setAlias()`设置别名。


### 基本使用

```javascript
yunba.publish_to_alias(obj, cb)
```

### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
obj    | Object | 参数必选。obj 含有四个属性字段，分别为 目标别名（obj.alias:string）、消息体（obj.msg:string）、MessageId（obj.messageId:number/string） 和 [消息级别](https://github.com/yunba/kb/blob/master/QoS.md)（obj.qos:number）。其中 obj.alias 为用户设置的别名信息，只支持英文、数字和下划线，长度不超过 50 个字符。obj.qos 为可选，表示服务质量等级，有三种取值：“0”表示最多送达一次；“1”表示最少送达一次；“2”表示保证送达且仅送达一次。默认为“1”。详见 [QoS](https://github.com/yunba/kb/blob/master/QoS.md) 的说明。
cb    | Function | 参数可选。不管消息发布是否成功或失败都会回调此函数。传递回的参数有 success、msg。success 值为 true 表示消息发布成功，否则发送失败。如果发送成功返回含有 messageId 的 msg，发送失败返回错误消息 msg。


### 代码示例

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

### 功能

`publish_to_alias()`的升级版本，支持更多参数。

**注**：接收方需要先通过`setAlias()`设置 [别名](https://github.com/yunba/kb/blob/master/%E9%A2%91%E9%81%93%E5%92%8C%E5%88%AB%E5%90%8D.md#%E5%88%AB%E5%90%8Dalias)。


### 基本使用

```javascript
yunba.publish2_to_alias(obj,cb)
```

### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
obj    | Object | 参数必选。obj 含有三个属性字段，分别为 目标别名（obj.alias:string）、消息体（obj.msg:string） 和 [扩展参数](#扩展参数说明)（obj.opts:dict）。其中 obj.alias 为用户设置的别名信息，只支持英文、数字和下划线，长度不超过 50 个字符。
cb    | Function | 参数可选。不管消息发布是否成功或失败都会回调此函数。传递回的参数有 success、msg。success 值为 true 表示消息发布成功，否则发送失败。如果发送成功返回含有 messageId 的 msg，发送失败返回错误消息 msg。


### 扩展参数说明

`publish2()`与`publish2_to_alias()`的扩展参数都是可选项。如果不填写参数，则`publish2()`/`publish2_to_alias()`的行为与`publish()`/`publish_to_alias()`相似，只有一点不同：`publish()`/`publish_to_alias()`会发送默认的 APN，而`publish2()`/`publish2_to_alias()`如果不填写 apn_json，则不会发送 APN。

名称 | 类型 | 说明
--------- | ------- | -----------
qos | Number | 服务质量等级。有三种取值：“0”表示最多送达一次；“1”表示最少送达一次；“2”表示保证送达且仅送达一次。默认为“1”。详见 [QoS](https://github.com/yunba/kb/blob/master/QoS.md) 的说明。
apn_json | Dict | 如果不填，则不会发送 APN。 APN 参考：[Apple 官方文档](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html) 及云巴 [相关文档](https://github.com/yunba/kb/blob/master/APNs/Payload.md)。
messageId | String | 消息 ID，64 位整型数转化成 String。发布消息时可以指定，如果不填，则由系统自动生成。
time_to_live | Number | 用来设置 [离线消息](https://github.com/yunba/kb/blob/master/%E4%BA%91%E5%B7%B4%E7%9A%84%E7%A6%BB%E7%BA%BF%E6%B6%88%E6%81%AF.md) 保留多久。单位为秒（例如，3600 代表 1 小时），默认值为 5 天，最大不超过 15 天。

### 代码示例

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


## set_alias

### 功能

用来设置用户名，即绑定账号。每个用户只能指定一个 [别名](https://github.com/yunba/kb/blob/master/频道和别名.md#别名alias)。

**将`set_alias()`的`alias`参数设置为空字符串即可取消别名。**


### 基本使用

```javascript
yunba.set_alias(alias, cb);
```


### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
obj  | Object | 参数必选。obj 含有一个属性字段，为要设置的 别名（obj.alias:string）。obj.alias 为用户设置的别名信息，只支持英文、数字和下划线，长度不超过 50 个字符。
cb   | Function | 参数可选。无论 alias 是否设置成功都会回调此函数。传递回的参数有 data。data.success 值为 true 表示 alias 设置成功，否则为失败。如果失败，则返回错误消息 data.msg。


### 代码示例

```javascript
yunba.set_alias({'alias': 'my_alias'}, function (data) {
    if (!data.success) {
        console.log(data.msg);
    }
});
```


## get_alias

### 功能

获取当前用户的 [别名](https://github.com/yunba/kb/blob/master/%E9%A2%91%E9%81%93%E5%92%8C%E5%88%AB%E5%90%8D.md#%E5%88%AB%E5%90%8Dalias)。


### 基本使用

```javascript
yunba.get_alias(cb);
```


### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
cb   | Function | 传递回的参数 data.alias 为当前用户的别名。

```javascript
yunba.get_alias(function(data) {
    console.log(data.alias);
});
```


## get_state

### 功能

可以通过`get_state()`查看传入的用户 [别名](https://github.com/yunba/kb/blob/master/%E9%A2%91%E9%81%93%E5%92%8C%E5%88%AB%E5%90%8D.md#%E5%88%AB%E5%90%8Dalias) 的在线状态。


### 基本使用

```javascript
yunba.get_state(alias,cb)
```


### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
alias | String | 参数必选。参数为要查询状态的 alias 名称。
cb | Function | 参数可选。无论查询结果如何都会回调此函数。传递回的参数有 success、data、data.messageId、error_msg。查询成功 success 为 true 否则为 false，data 表示在线状态，success 为 false 时 error_msg 有效。


### 代码示例

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

### 功能

查询用户订阅的 [频道](https://github.com/yunba/kb/blob/master/频道和别名.md#频道topic) 列表。如果传入参数 alias，是获取目标用户（[别名](https://github.com/yunba/kb/blob/master/频道和别名.md#别名alias)）的频道列表；如果不传入参数 alias，则是获取当前用户的频道列表。


### 基本使用

```javascript
yunba.get_topic_list(alias, cb)
```

### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
alias | String | 用户设置的 [别名](https://github.com/yunba/kb/blob/master/%E9%A2%91%E9%81%93%E5%92%8C%E5%88%AB%E5%90%8D.md#%E5%88%AB%E5%90%8Dalias) 信息，只支持英文数字下划线，长度不超过 50 个字符。
cb | Function | 无论查询结果如何都会回调此函数。传递回的参数有 success、data.topics、error_msg。查询成功 success 为 true，否则为 false。data.topics 为订阅的`topic`列表，类型 List，success 为 true 时有效。success 为 false 时 error_msg 有效。


### 代码示例

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

### 功能

通过调用此函数可以获取该 topic 下所有订阅用户的 [别名](https://github.com/yunba/kb/blob/master/%E9%A2%91%E9%81%93%E5%92%8C%E5%88%AB%E5%90%8D.md#%E5%88%AB%E5%90%8Dalias)。


### 基本使用

```javascript
yunba.get_alias_list(topic, cb)
```


### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
topic | String | App 订阅的 [频道]( https://github.com/yunba/kb/blob/master/%E9%A2%91%E9%81%93%E5%92%8C%E5%88%AB%E5%90%8D.md)，topic 只支持英文数字下划线，长度不超过 50 个字符，数组的长度不超过 100。
cb | Function | 无论查询结果如何都会回调此函数。传递回的参数有 success、data.alias、error_msg。查询成功 success 为 true，否则为 false。data.alias 为订阅的`alias`列表，类型 List，success 为 true 时有效。success 为 false 时 error_msg 有效。


### 代码示例

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
