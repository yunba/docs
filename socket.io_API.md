# Socket.IO API

Yunba 的 SDK 按照实现的方式，可以分为两种，一种是所谓原生 SDK，一种是基于 Socket.IO 的 SDK。

例如像 Android, iOS, C 等这些原生 SDK，实现了完整的二进制协议栈，在运算能力有限、带宽有限的平台上，可以很好地工作。

另外一类像 JavaScript SDK（非 node.js)，就是基于 Socket.IO API 实现的。

相对于原生 SDK，基于 Socket.IO API 的 SDK 代码体积小，开发周期短，适合为小众语言快速开发 SDK。

Socket.IO 是在 WebSocket 基础上开发的一种基于 HTTP 协议的长连接通讯方式，使用起来跟原生的 Socket 一样方便，
特别是在 Web App 开发中，使用得越来越多。

## 使用 Socket.IO API

这里以 Python 为例子，演示通过调用 Yunba Socket.IO API 快速集成。

## 安装 Socket.IO Client

Python 请使用 0.6.5 版本的 [Socket.IO-client](https://pypi.python.org/pypi/socketIO-client)；Node.js 请使用 0.9.17 版本的  Socket.IO-client。

```bash
pip install -U socketIO-client==0.6.5
```

## init
### 功能

与云巴 Socket.IO 服务器建立连接。

### 代码示例

```python
socketIO = SocketIO('sock.yunba.io', 3000)
```

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
host | String | Socket.IO API Server，默认值 'sock.yunba.io'。
port | Number | Socket.IO API 端口，默认值 3000。


## socketconnectack
### 功能

`init()`连接成功后的回调。

### 代码示例

```python
{"name":"socketconnectack","args":[{"msg":"socket.io connected"}]}
```

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
msg | String | 连接信息。

## connect
### 功能

触发 connect 命令，与 Socket.IO 服务器确认身份。以下两种方式任选其一即可，推荐使用 [AppKey](https://github.com/yunba/kb/blob/master/AppKey.md#appkey) + CustomID 方式进行连接，可保证每次连接都能使用相同的 Session。

### 代码示例

```python
socketIO.emit('connect', {'appkey': '52fcc04c4dc903d66d6f8f92'})       # 使用 AppKey 进行连接
socketIO.emit('connect', {'appkey': '52fcc04c4dc903d66d6f8f92', 'customid': 'userid'})       # 使用 AppKey 和自定义的会话 ID 进行连接
```
### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
appkey | String | 在云巴 Portal 申请到的应用的 AppKey。
customid | String | 用户自定义的会话 ID。

## connack
### 功能

`connect()`成功后的回调。

### 代码示例

```python
{"name":"connack","args":[{"success":true}, {"sessionid": "123456789XXXX"}]}
```

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
success | Boolean | 成功返回 true，失败返回 false。
sessionid | String | Session ID。

## subscribe
### 功能

增加订阅一个 [频道](https://github.com/yunba/kb/blob/master/频道和别名.md#频道topic)。成功订阅后，App 可以收到来自该频道的消息。新增订阅不会影响已有的订阅。

### 代码示例

```python
socketIO.emit('subscribe', {'topic': 'testtopic1', 'messageId':'XXXXXXXXXXXXXXXXXXXX'})
```

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
topic | String | App 订阅的频道。只支持英文数字下划线，长度不超过 50 个字符。
messageId|String| 参数可选。指定的 Message ID。与 `suback` 回应中的 messageId 相对应。


## suback
### 功能

`subscribe()`订阅成功后的回调。

### 代码示例

```python
{"name":"suback","args":[{"success":true},{"topic":"testtopic1"},{"messageId": "XXXXXXXXXXXXXXXXXXXXX"}]}
```

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
success | Boolean | 成功返回 true，失败返回 false。
topic |String | 订阅的频道名称。
messageId|String|Message ID。对应 `subscribe()` 时指定的 Message ID。如未指定，则系统会自动分配一个 ID。 


## unsubscribe
### 功能

取消对某个 [频道](https://github.com/yunba/kb/blob/master/频道和别名.md#频道topic) 的订阅。成功取消后，就不会再收到来自该频道的消息。

### 代码示例

```python
socketIO.emit('unsubscribe', {'topic': 'testtopic1', 'messageId':'XXXXXXXXXXXXXXXXXXXX'})
```

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
topic | String | 取消订阅的频道。只支持英文数字下划线，长度不超过 50 个字符。
messageId|String| 参数可选。指定的 Message ID。与 `unsuback` 回应中的 messageId 相对应。

## unsuback
### 功能

`unsubscribe()`订阅成功后的回调。

### 代码示例

```python
{"name":"unsuback","args":[{"success":true}, {"topic":"testtopic1"}, {"messageId": "XXXXXXXXXXXXXXXXXXXXX"}]}
```

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
success | Boolean | 成功返回 true，失败返回 false。
topic |String | 取消订阅的频道名称。
messageId|String|Message ID。对应 `unsubscribe()` 时指定的 Message ID。如未指定，则系统会自动分配一个 ID。 

## publish
### 功能

向某个 [频道](https://github.com/yunba/kb/blob/master/频道和别名.md#频道topic) 发布消息。成功发布后，所有订阅此频道的客户端都会收到消息。

### 代码示例

```python
socketIO.emit('publish', {'topic': 'channel1', 'msg': 'hello, Yunba', 'qos': 1})
```

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
topic | String | App 订阅的频道。只支持英文数字下划线，长度不超过 50 个字符。
msg | String | 发布的内容。
qos | Number | 服务质量等级。有三种取值：“0”表示最多送达一次；“1”表示最少送达一次；“2”表示保证送达且仅送达一次。默认为“1”。详见 [QoS](https://github.com/yunba/kb/blob/master/QoS.md) 的说明。

## puback
### 功能

`publish()`、`publish_to_alias()`、`publish2()`和`publish2_to_alias()`成功的回调。

### 代码示例

```python
{"name":"puback","args":[{"success":true, "messageId": "11842355493944946011"}]}
```

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
success | Boolean | 成功返回 true，失败返回 false。
messageId | String | 消息 ID，与发布消息时的 ID 对应。如果发布时未指定，则云巴系统会生成一个 ID。

## set_alias
### 功能

用来设置用户名，即绑定账号。每个用户只能指定一个 [别名](https://github.com/yunba/kb/blob/master/频道和别名.md#别名alias)。

**将`set_alias()`的`alias`参数设置为空字符串即可取消别名。**

### 代码示例

```python
socketIO.emit('set_alias', {'alias': 'mytestalias1'})
```

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
alias | String | 用户设置的别名信息，只支持英文数字下划线，长度不超过 50 个字符。


## set_alias_ack
### 功能

`set_alias()`设置别名成功后的回调。

### 代码示例

```python
{"success": true}
```

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
success | Boolean | 成功返回 true，失败返回 false。

## get_alias
### 功能

获取当前用户的 [别名](https://github.com/yunba/kb/blob/master/频道和别名.md#别名alias)。

### 代码示例

```python
socketIO.emit('get_alias')
```

## alias

### 功能
`get_alias()`获取别名的回调。

### 代码示例

```python
{'alias': 'mytestalias1'}
```

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
alias | String | 用户当前的别名。


## publish_to_alias

### 功能
向某个 [别名](https://github.com/yunba/kb/blob/master/频道和别名.md#别名alias) 发布消息。发布成功后，该别名的客户端会收到消息。

### 代码示例

```python
socketIO.emit('publish_to_alias', {'alias': 'mytestalias1', 'msg': "hello to alias"})
```

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
alias| String | 目标别名。只支持英文数字下划线，长度不超过 50 个字符。
msg | String | 向目标别名发布的消息。

## get_alias_list

### 功能
获取某 [频道](https://github.com/yunba/kb/blob/master/频道和别名.md#频道topic) 下的所有订阅者的 [别名](https://github.com/yunba/kb/blob/master/频道和别名.md#别名alias) 列表，别名个数及状态。

### 代码示例

```python
socketIO.emit('get_alias_list', {'topic': 'testtopic1'})
```

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
topic | String | 目标频道。

## get_alias_list_ack
`get_alias_list()`的回调。

### 代码示例

```python
# 成功
{'data': {'alias': ['mytestalias1'], 'occupancy': 1}, 'success': true}

# 失败
{'success': false, error_msg: 'Broker Error'}
```

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
success | Boolean | 成功返回 true，否则返回 false。
data.alias | List | 订阅了该`topic`的`alias`的列表，success 为 true 时有效。
data.occupancy | Number | 订阅了该`topic`的`alias`个数，success 为 true 时有效。
error_msg | String | 错误信息，success 为 false 时有效。

## get_topic_list

### 功能
查询用户订阅的 [频道](https://github.com/yunba/kb/blob/master/频道和别名.md#频道topic) 列表。如果传入参数 alias，是获取目标用户（[别名](https://github.com/yunba/kb/blob/master/频道和别名.md#别名alias)）的频道列表；如果不传入参数 alias，则是获取当前用户的频道列表。

### 代码示例

```python
socketIO.emit('get_topic_list', {'alias': 'mytestalias1'})
```

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
alias | String | 用户设置的别名信息，只支持英文数字下划线，长度不超过 50 个字符。

## get_topic_list_ack

### 功能
`get_topic_list()`结果回调。

### 代码示例

```python
# 成功
{'data': {'topics': ['testtopic2']}, 'success': True}

# 失败
{'success': false, error_msg: 'Broker Error'}
```

### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
success | Boolean | 成功返回 true，失败返回 false。
data.topics | List | 订阅的`topic`列表，success 为 true 时有效。
error_msg | String | 错误信息，success 为 false 时有效。

## publish2

### 功能
`publish()`的升级版本，支持更多参数。

### 代码示例

```python
socketIO.emit('publish2', {
    'topic': 'topic1',
    'msg': 'from publish2',
    "opts": {
        'qos': 1,
        'apn_json':  {"aps":{"sound":"bingbong.aiff","badge": 3, "alert":"douban"}}
        'messageId': '11833652203486491113'
    }
})
```

### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
topic | String | 目标频道。只支持英文数字下划线，长度不超过 50 个字符。
msg | String | 向订阅者发布的消息。
opts | Dict | 可选项。参考`publish2()`[扩展参数](#扩展参数说明)。

### 扩展参数说明

`publish2()`与`publish2_to_alias()`的扩展参数都是可选项。如果不填写参数，则`publish2()`/`publish2_to_alias()`的行为与`publish()`/`publish_to_alias()`相似，只有一点不同：`publish()`/`publish_to_alias()`会发送默认的 APN，而`publish2()`/`publish2_to_alias()`如果不填写 apn_json，则不会发送 APN。

名称 | 类型 | 说明
--------- | ------- | -----------
qos | Number | 服务质量等级。有三种取值：“0”表示最多送达一次；“1”表示最少送达一次；“2”表示保证送达且仅送达一次。默认为“1”。详见 [QoS](https://github.com/yunba/kb/blob/master/QoS.md) 的说明。
apn_json | Dict | 如果不填，则不会发送 APN。 APN 参考：[Apple 官方文档](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html) 及云巴 [相关文档](https://github.com/yunba/kb/blob/master/APNs/Payload.md)。
messageId | String | 消息 ID，64 位整型数转化成 String。发布消息时可以指定，如果不填，则由系统自动生成。
time_to_live | Number | 用来设置 [离线消息](https://github.com/yunba/kb/blob/master/%E4%BA%91%E5%B7%B4%E7%9A%84%E7%A6%BB%E7%BA%BF%E6%B6%88%E6%81%AF.md) 保留多久。单位为秒（例如，3600 代表 1 小时），默认值为 5 天，最大不超过 15 天。

## publish2_to_alias

### 功能
`publish_to_alias()`的升级版本，支持更多参数。

### 代码示例

```python
socketIO.emit('publish2_to_alias', {'alias': 'alias_mqttc_sub', 'msg': "hello to alias from publish2_to_alias"});
```

### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
alias | String | 目标别名。只支持英文数字下划线，长度不超过 50 个字符。
msg | String | 向别名发布的消息。
opts | Dict | 可选项。参考`publish2_to_alias`[扩展参数](#扩展参数说明)。


### 扩展参数说明

`publish2()`与`publish2_to_alias()`的扩展参数都是可选项。如果不填写参数，则`publish2()`/`publish2_to_alias()`的行为与`publish()`/`publish_to_alias()`相似，只有一点不同：`publish()`/`publish_to_alias()`会发送默认的 APN，而`publish2()`/`publish2_to_alias()`如果不填写 apn_json，则不会发送 APN。

名称 | 类型 | 说明
--------- | ------- | -----------
qos | Number | 服务质量等级。有三种取值：“0”表示最多送达一次；“1”表示最少送达一次；“2”表示保证送达且仅送达一次。默认为“1”。详见 [QoS](https://github.com/yunba/kb/blob/master/QoS.md) 的说明。
apn_json | Dict | 如果不填，则不会发送 APN。 APN 参考：[Apple 官方文档](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html) 及云巴 [相关文档](https://github.com/yunba/kb/blob/master/APNs/Payload.md)。
messageId | String | 消息 ID，64 位整型数转化成 String。发布消息时可以指定，如果不填，则由系统自动生成。
time_to_live | Number | 用来设置 [离线消息](https://github.com/yunba/kb/blob/master/%E4%BA%91%E5%B7%B4%E7%9A%84%E7%A6%BB%E7%BA%BF%E6%B6%88%E6%81%AF.md) 保留多久。单位为秒（例如，3600 代表 1 小时），默认值为 5 天，最大不超过 15 天。

## get_state

### 功能
查询用户的在线状态。

### 代码示例

```python
socketIO.emit('get_state', {'alias': 'mytestalias1'})
```

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
alias | String | 目标 [别名](https://github.com/yunba/kb/blob/master/频道和别名.md#别名alias)。

## get_state_ack

### 功能
`get_state()`的回调。

### 代码示例

```python
# 成功
{'data': 'online', 'success': true}

# 失败
{'success': false, error_msg: 'Broker Error'}
```

### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
success | Boolean | 成功返回 true，失败返回 false。
data | String | 在线状态。在线为 online，离线为 offline。
error_msg | String | 错误信息，success 为 false 时有效。

## recvack

> recvack 是付费服务，免费用户可能无法正常使用。

### 功能

第一个目标用户（且不是消息发布者）收到消息给服务器回复`puback`后，给消息发布者发送`recvack`，消息发布者通过这个通知，可以了解第一个收到消息的用户的 alias 和 时间戳。

### 代码示例

```python
recvack {u'data': u'{"timestamp":1411374510357,"alias":"alias_mqttc_sub"}', u'success': True, u'messageId': u'11839467508410466019'}
```

### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
success | Boolean | 成功返回 true，失败返回 false。
data.timestamp | Number | recvack 收到的时间戳，时区为 GMT。
data.alias | String | recvack 用户的 alias。
messageId | String | 消息 ID，与 publish 时的 ID 对应。


## 例子

> Python 例子 (socketIO-client==0.5.5)

```python
from socketIO_client import SocketIO
import logging
logging.basicConfig(level=logging.INFO)

def on_socket_connect_ack(args):
    print 'on_socket_connect_ack', args
    socketIO.emit('connect', {'appkey': '52fcc04c4dc903d66d6f8f92', 'customid': 'python_demo'})

def on_connack(args):
    print 'on_connack', args
    socketIO.emit('subscribe', {'topic': 'testtopic2'})

    print "publish2_to_alias"
    socketIO.emit('publish2_to_alias', {'alias': 'alias_mqttc_sub', 'msg': "hello to alias from publish2_to_alias"});

def on_puback(args):
    print 'on_puback', args
    if args['messageId'] == '11833652203486491112':
        print '  [OK] publish with given messageId'

def on_suback(args):
    print 'on_suback', args
    socketIO.emit('publish', {'topic': 'testtopic2', 'msg': 'from python', 'qos': 1})

    socketIO.emit('publish', {
        'topic': 'testtopic2',
        'msg': 'from python with given messageId',
        'qos': 1,
        'messageId': '11833652203486491112'
        })

    socketIO.emit('publish2', {
        'topic': 't2thi',
        'msg': 'from publish2',
        "opts": {
            'qos': 1,
            'apn_json':  {"aps":{"sound":"bingbong.aiff","badge": 3, "alert":"douban"}},
            'messageId': '11833652203486491113'
        }
    })

    socketIO.emit('set_alias', {'alias': 'mytestalias1'})

def on_message(args):
    print 'on_message', args

def on_set_alias(args):
    print 'on_set_alias', args

    socketIO.emit('publish2', {
        'topic': 'testtopic2',
        'msg': 'from publish2',
        "opts": {
            'qos': 1,
            'apn_json':  {"aps":{"sound":"bingbong.aiff","badge": 3, "alert":"douban"}}
        }
    })


    socketIO.emit('get_alias')
    socketIO.emit('publish_to_alias', {'alias': 'mytestalias1', 'msg': "hello to alias"})
    socketIO.emit('get_topic_list', {'alias': 'mytestalias1'})
    socketIO.emit('get_state', {'alias': 'mytestalias1'})

def on_get_alias(args):
    print 'on_get_alias', args

def on_alias(args):
    socketIO.emit('get_alias_list', {'topic': 'testtopic2'})
    print 'on_alias', args

def on_get_topic_list_ack(args):
    print 'on_get_topic_list_ack', args

def on_get_alias_list_ack(args):
    print 'on_get_alias_list_ack', args

def on_publish2_ack(args):
    print 'on_publish2_ack', args
    if args['messageId'] == '11833652203486491113':
        print '  [OK] publish2 with given messageId'

def on_publish2_recvack(args):
    print 'on_publish2_recvack', args

def on_get_state_ack(args):
    print 'on_get_state_ack', args

socketIO = SocketIO('sock.yunba.io', 3000)
socketIO.on('socketconnectack', on_socket_connect_ack)
socketIO.on('connack', on_connack)
socketIO.on('puback', on_puback)
socketIO.on('suback', on_suback)
socketIO.on('message', on_message)
socketIO.on('set_alias_ack', on_set_alias)
socketIO.on('get_topic_list_ack', on_get_topic_list_ack)
socketIO.on('get_alias_list_ack', on_get_alias_list_ack)
socketIO.on('puback', on_publish2_ack)
socketIO.on('recvack', on_publish2_recvack)
socketIO.on('get_state_ack', on_get_state_ack)
socketIO.on('alias', on_alias)				# get alias callback

socketIO.wait()
```
