# Socket.io API

Yunba 的 SDK 按照实现的方式，可以分为两种，一种是所谓原生 SDK，一种是基于 Socket.IO 的 SDK。

例如像 Android, iOS, C 等这些原生 SDK，实现了完整的二进制协议栈，在运算能力有限、带宽有限的平台上，可以很好的工作。

另外一类像 JavaScript SDK（非 node.js)，就是基于 Socket.IO API 实现的。

相对于原生 SDK，基于 Socket.io API 的 SDK 代码体积小，开发周期短，适合为小众语言快速开发 SDK。

Socket.IO 是在 WebSocket 基础上开发的一种基于 HTTP 协议的常链接通讯方式，使用起来跟原生的 Socket 一样方便，
特别是在 Web App 开发中，使用得越来越多。

# 使用 Socket.IO API

这里以 Python 为例子，演示通过调用 Yunba Socket.IO API 快速集成。

## 安装 Socket.IO Client

```python
参考：https://pypi.python.org/pypi/socketIO-client
```

## init
与 socket.io 服务器建立连接。

```python
socketIO = SocketIO('sock.yunba.io', 3000)

```
### init 回调
建立连接成功后，收到回调。

```python
{"name":"socketconnectack","args":[{"msg":"你已经通过websocket与服务器链接上。"}]}
```

## connect
触发 connect 命令，与 socket.io 服务器确认身份。

```python
socketIO.emit('connect', {'appkey': '52fcc04c4dc903d66d6f8f92'})
```

### connect 回调
```python
{"name":"connack","args":[{"success":true}]}
```

## publish
```python
socketIO.emit('publish', {'topic': 'channel1', 'msg': 'hello, Yunba', 'qos': 1})
```
### publish 回调
```python
{"name":"puback","args":[{"success":true}]}
```

## Examples
### Python
```python
from socketIO_client import SocketIO
import logging
logging.basicConfig(level=logging.DEBUG)

def on_connect_response(*args):
    print 'on_connect_response', args
    socketIO.emit('publish', {'topic': 'testtopic1', 'msg': 'from python', 'qos': 1})

def on_puback(*args):
    print 'on_puback', args

socketIO = SocketIO('localhost', 3000)
# socketIO.on('sockconnectack', on_connect_response)
socketIO.on('connack', on_connect_response)
socketIO.on('puback', on_puback)

socketIO.emit('connect', {'appkey': '52fcc04c4dc903d66d6f8f92'})
socketIO.wait()
```
