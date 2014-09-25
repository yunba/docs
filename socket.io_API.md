# Socket.io API

Yunba 的 SDK 按照实现的方式，可以分为两种，一种是所谓原生 SDK，一种是基于 Socket.IO 的 SDK。

例如像 Android, iOS, C 等这些原生 SDK，实现了完整的二进制协议栈，在运算能力有限、带宽有限的平台上，可以很好的工作。

另外一类像 JavaScript SDK（非 node.js)，就是基于 Socket.IO API 实现的。

相对于原生 SDK，基于 Socket.io API 的 SDK 代码体积小，开发周期短，适合为小众语言快速开发 SDK。

Socket.IO 是在 WebSocket 基础上开发的一种基于 HTTP 协议的常链接通讯方式，使用起来跟原生的 Socket 一样方便，
特别是在 Web App 开发中，使用得越来越多。

## 使用 Socket.IO API

这里以 Python 为例子，演示通过调用 Yunba Socket.IO API 快速集成。

## 安装 Socket.IO Client

> 参考：[https://pypi.python.org/pypi/socketIO-client](https://pypi.python.org/pypi/socketIO-client)

```bash
pip install -U socketIO-client
```

## init
与 socket.io 服务器建立连接。

```python
socketIO = SocketIO('sock.yunba.io', 3000)
```

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
host | String | socket.io API server，默认值 'sock.yunba.io'
port | number | socket.io API 端口，默认值 3000


## socketconnectack
`init` 建立连接成功后的回调。

```python
{"name":"socketconnectack","args":[{"msg":"你已经通过websocket与服务器链接上。"}]}
```

## connect
触发 connect 命令，与 socket.io 服务器确认身份。

```python
socketIO.emit('connect', {'appkey': '52fcc04c4dc903d66d6f8f92'})
```

## connack
`connect` 成功后的回调。

```python
{"name":"connack","args":[{"success":true}]}
```

## subscribe

订阅一个频道。

```python
socketIO.emit('subscribe', {'topic': 'testtopic1'})
```

## suback

`subscribe` 订阅成功回调。

```python
{"name":"suback","args":[{"success":true}]}
```

## publish

发布一个消息。

```python
socketIO.emit('publish', {'topic': 'channel1', 'msg': 'hello, Yunba', 'qos': 1})
```

## puback
发布 `publish`, `publish_to_alias` 成功回调。

```python
{"name":"puback","args":[{"success":true}]}
```

## set_alias

### 功能
App  可以调用此函数来绑定账号，用户名，每个用户只能指定一个别名。

### 函数原型

```python
socketIO.emit('set_alias', {'alias': 'mytestalias1'})
```

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
alias | String | 用户设置的别名信息，只支持英文数字下划线，长度不超过50个字符


## set_alias_ack

### 功能
设置别名成功回调。

回调参数:

```python
{"success": true}
```

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
success | boolen | 设置成功返回 true，否则返回 false

## get_alias

### 功能
App  可以调用此函数来获取当前用户的别名。

### 函数原型

```python
socketIO.emit('get_alias')
```

## alias

### 功能
读取别名回调。

```python
{'alias': 'mytestalias1'}
```

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
alias | string | 用户当前别名


## publish_to_alias

### 功能
向用户别名发送消息, 用于实现点对点的消息发送。

### 函数原型

```python
socketIO.emit('publish_to_alias', {'alias': 'mytestalias1', 'msg': "hello to alias"})
```

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
alias| String | 用户设置的别名信息，只支持英文数字下划线，长度不超过50个字符
msg | String | 向目标别名的订阅者发布的消息

## get_alias_list

### 功能
App  可以调用此函数来获取订阅输入 Topic 下面所有的用户的别名。

### 函数原型

```python
socketIO.emit('get_alias_list', {'topic': 'testtopic1'})
```

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
topic | String | app 订阅的的频道，topic 只支持英文数字下划线，长度不超过50个字符,数组的长度不超过100

## get_alias_list_ack
`get_alias_list` 结果回调。

### 示例
```python
'''succ'''
{'data': {'alias': ['mytestalias1'], 'occupancy': 1}, 'success': True}
'''failed'''
{success: false, error_msg: 'Broker Error'}
```

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
success | boolean | 成功返回 true, 否则返回 false
data.alias | List | 订阅的 `alias` 列表，success 为 true 时有效
error_msg | String | 错误信息，success 为 false 时有效

## get_topic_list

### 功能
App 可以查询用户订阅的频道列表，如果不传入参数 alias， 则是获取当前用户的频道列表,如果输入参数 alias，则是获取目标 alias 的频道列表。

### 函数原型

```python
socketIO.emit('get_topic_list', {'alias': 'mytestalias1'})
```

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
alias | String | 用户设置的别名信息，只支持英文数字下划线，长度不超过50个字符

## get_topic_list_ack

### 功能
`get_topic_list` 结果回调。

### 示例
```python
'''succ'''
{'data': {'topics': ['testtopic2']}, 'success': True}
'''failed'''
{'success': False, error_msg: 'Broker Error'}
```

### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
success | boolean | 成功返回 true, 否则返回 false
data.topics | List | 订阅的 `topic` 列表，success 为 true 时有效
error_msg | String | 错误信息，success 为 false 时有效

## publish2

### 功能
`publish` 升级版本，支持更多参数。目前支持的参数。

### 函数原型

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
topic | String | app 订阅的的频道，topic 只支持英文数字下划线，长度不超过50个字符,数组的长度不超过100
msg | String | 向订阅者发布的消息
opts | Dict | 可选项。`publish2` 扩展参数。

> 目前支持的 publish2 扩展参数

`publish2` 扩展参数都是可选项，如果不填写参数，`publish2` 的行为与 `publish` 一样。

名称 | 类型 | 说明
--------- | ------- | -----------
qos | number | 如果不填，默认为 1
apn_json | dict | 如果不填，则不会发送APN
messageId | String | 消息 ID，64 位整型数转化成 string。如果不填，由系统自动生成
time_to_live | number | 离线消息保留时间值，单位是秒(例如2天 2\*24\*3600)，当前默认值为5天

## publish2_to_alias

### 功能
`publish2` 的 alias 版本。

### 函数原型

```python
    socketIO.emit('publish2_to_alias', {'alias': 'alias_mqttc_sub', 'msg': "hello to alias from publish2_to_alias"});
```

### 参数说明
* 参考 `publish_to_alias`
* 支持 `publish2` 扩展参数

> publish2_to_alias, publish_to_alias 对比

差异| publish_to_alias | publish2_to_alias
----| --------| -----
当 alias 不存在 | 返回 puback | 返回发布错误，error 3 (no uid found)

## publish2_ack
发布 `publish2`, `publish2_to_alias` 成功回调。

```python
{"name":"publish2_ack","args":[{"success":true}]}
```

## publish2_recvack

> recvack 是付费服务，免费用户可能不能正常使用。

### 功能

第一个目标用户（且不是消息发布者）收到消息给服务器回复 `puback` 后，给消息发布者发送 `recvack`，消息发布者通过这个通知，可以了解第一个收到消息的用户的 alias 和 时间戳。

### 函数原型

```python
on_publish2_recvack {u'data': u'{"timestamp":1411374510357,"alias":"alias_mqttc_sub"}', u'success': True, u'messageId': u'11839467508410466019'}
```

### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
success | boolean | 成功返回 true, 否则返回 false
data.timestamp | number | recvack 收到的时间戳，时区为 GMT
data.alias | String | recvack 用户的 alias
messageId | String | 消息 ID，与 publish 时的 ID 对应


## 例子

> Python 例子

```python
from socketIO_client import SocketIO
import logging
logging.basicConfig(level=logging.INFO)

def on_socket_connect_ack(args):
    print 'on_socket_connect_ack', args
    socketIO.emit('connect', {'appkey': '52fcc04c4dc903d66d6f8f92'})

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
            'apn_json':  {"aps":{"sound":"bingbong.aiff","badge": 3, "alert":"douban"}}
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
socketIO.on('publish2_ack', on_publish2_ack)
socketIO.on('publish2_recvack', on_publish2_recvack)
socketIO.on('get_state_ack', on_get_state_ack)
socketIO.on('alias', on_alias)				# get alias callback

socketIO.wait()
```
