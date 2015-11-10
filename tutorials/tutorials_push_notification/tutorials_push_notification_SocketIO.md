# 消息推送开发指南之 Socket.io 篇

云巴暂未提供 Socket.io 的 SDK。本文以 Python 为例，介绍如何使用云巴 Socket.io API 开发消息推送功能。
文中给出的安装、运行等命令仅以 Mac 终端下的命令为例。

## 准备工作

###1. 安装 virtualenv
参考 [如何安装 virtualenv](https://github.com/yunba/docs/blob/master/support/knowledge_base/Install_virtualenv.md) 一文，
安装 virtualenv。安装后，可先不设置虚拟环境，根据第 2 步进行设置。

###2. 安装 socket.io-client
打开 Mac 的 Terminal，按照 [socketIO-client 官方网站](https://pypi.python.org/pypi/socketIO-client) 的说明，输入如下命令：
<br>

设置环境变量：
>$ VIRTUAL_ENV=$HOME/.virtualenv

如果想在下次打开 Terminal 时仍能使用该变量，可用下面的命令，将变量保存到“.bash_profile”文件里：
>$ echo "export VIRTUAL_ENV=$HOME/.virtualenv" >> ~/.bash_profile

设置虚拟路径：
>$ virtualenv $VIRTUAL_ENV

激活虚拟路径：
>$ source $VIRTUAL_ENV/bin/activate

激活后，命令行的开头处会显示出虚拟环境的名称“.virtualenv”，此时，用下面的命令安装 socketIO-client，
将会安装到该目录中去：
>$ pip install -U socketIO-client

有关 virtualenv 的详细用法，请参考 [virtualenv 的官方文档](https://virtualenv.pypa.io/en/latest/userguide.html)。

###3. 注册云巴开发者账号
打开 [云巴官方网站](http://yunba.io "云巴官方网站")，点击右上角的“注册”按钮创建账号。

## 详细步骤

### 1. 在云巴 Portal 上创建新应用
请参考 [运行 Yunba Android Demo](https://github.com/yunba/docs/blob/master/quickstart/demo/Demo_Android.md) 
一文中的该步骤的做法，获得一个 AppKey。

###2. 创建 Python 程序

####2.1 导入 Socket.io 库
```python
from socketIO_client import SocketIO
```

####2.2 初始化并连接服务器
用下面的语句进行初始化：
```python
socketIO = SocketIO('sock.yunba.io', 3000)
```

初始化成功后的回调如下：
```python
{"name":"socketconnectack","args":[{"msg":"你已经通过websocket与服务器链接上。"}]}
```

####2.3 与服务器确认身份
如下所示，使用 appkey + customid 的方式进行连接，
以确保连接后的会话状态与上次连接时一致（包括离线消息、已订阅的频道和别名）。
将下面代码中的“appkey”替换为步骤 1 中获得的 AppKey，将“userid”填上你自定义的会话ID。
```python
socketIO.emit('connect', {'appkey': 'XXXXXXXXXXXXXXXXXXXXXXXXXXXXX', 'customid': 'userid'})       
```

`connect()` 成功后的回调如下：
```python
{"name":"connack","args":[{"success":true}, {"sessionid": "123456789XXXX"}]}
```

#### 2.4 订阅频道

使用 `subscribe()` 方法订阅频道，就可以接收该频道的消息了。
```python
socketIO.emit('subscribe', {'topic': 'testtopic1'})
```

#### 2.5 发布消息

使用 `publish()` 方法向所有订阅了某个频道的客户端发消息：
```python
socketIO.emit('publish', {'topic': 'channel1', 'msg': 'hello, Yunba', 'qos': 1})
```

另外，还可以使用带有更多参数的 `publish2()` 方法：
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

###3. 运行 Python 程序

在运行之前，可以先准备好其他集成了云巴 SDK 的客户端，用来与 Python 实现的客户端进行通信。
需要注意的是，所有客户端都必须使用同一个 AppKey，才能相互通信。

万事俱备之后，在 Mac Terminal 中，激活虚拟环境：

>$ source $VIRTUAL_ENV/bin/activate

假设文件就在当前路径下，文件名为“python_demo.py”，运行命令如下：

>$ python python_demo.py 

在 [运行 Yunba Socket.io Python Demo](https://github.com/yunba/docs/blob/master/quickstart/demo/Demo_SocketIO.md)
 一文中，演示了 Python 实现的示例程序及其运行的方法。可参考这篇文章的说明，运行示例程序，快速上手。
<br>

更多用法和功能，请参考 [socket.io API 手册](http://yunba.io/docs2/socket.io_API/) 
。


