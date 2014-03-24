# Socket.io API

## init
```python
socketIO = SocketIO('sock.yunba.io', 3000)

```
### init 回调
```python
{"name":"socketconnectack","args":[{"msg":"你已经通过websocket与服务器链接上。"}]}
```

## connect 
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
