# 运行 Yunba Socket.io Python Demo

本文以 Python 为例，演示 Yunba Socket.io API 的使用。
<br>
本文涉及的运行环境如下：

* Mac OS X 10.11.1
* virtualenv 13.1.2
* socketIO-client 0.6.5

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

###1. 在云巴 Portal 上创建新应用
请参考 [运行 Yunba Android Demo](https://github.com/yunba/docs/blob/master/quickstart/demo/Demo_Android.md) 
一文中的该步骤的做法，获得一个 AppKey。

###2. 修改 Python 程序
新建一个文本文件，保存下面的 Python 示例程序。比如取名为 python_demo.py。
将代码中的 appkey 替换为步骤 1 中从 Portal 获得的 AppKey。<br>

```python
from socketIO_client import SocketIO
import logging
logging.basicConfig(level=logging.INFO)

def on_socket_connect_ack(args):
    print 'on_socket_connect_ack', args
    socketIO.emit('connect', {'appkey': 'XXXXXXXXXXXXXXXXXXXXXXXX', 'customid': 'python_demo'})

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
socketIO.on('alias', on_alias)                # get alias callback

socketIO.wait()

```

###3. 准备好与之通讯的客户端

为了演示消息收发的功能，我们用同一个 AppKey 来创建一个新的集成了云巴 SDK 的客户端程序，与之进行通讯。
<br>
例如，就使用 Yunba Android SDK 中提供的 Demo 程序，使用 python_demo 中的 AppKey 替换 Android Demo 中的 AppKey。
创建方法可参考 [运行 Yunba Android Demo](https://github.com/yunba/docs/blob/master/quickstart/demo/Demo_Android.md) 一文。
<br>
为了收到 python_demo 发来的消息，需在 Android Demo 端订阅“testtopic2”、“t2thi”，
并将别名设置为 “alias_mqttc_sub”，如图所示：

![socketio_client_setting.png](https://raw.githubusercontent.com/yunba/docs/master/image/for_quickstart/socketio_client_setting.png)

###4. 收发消息

在 Mac Terminal 中，激活虚拟环境。假设 python 文件就在当前路径下，在虚拟环境下，运行 python_demo.py，如下：

>$ source $VIRTUAL_ENV/bin/activate

>$ python python_demo.py 

运行成功后，会在 Terminal 中打印出执行的信息。而 Android Demo 端，也可以收到消息，如下：

![socketio_client_recv.png](https://raw.githubusercontent.com/yunba/docs/master/image/for_quickstart/socketio_client_recv.png)

同样地，可以通过 Android Demo 的 MAIN 标签页下的“Msg to publish”项向 python_demo 订阅的频道发送消息。<br>
另外，在代码中可以看到，python_demo 的别名已设置为“mytestalias1”。
可以通过 Android Demo 的 API 标签页下的 “Msg to Alias” 项发送一对一消息给 python_demo。<br>
发送和接收的截图如下：

![socketio_to_topic.png](https://raw.githubusercontent.com/yunba/docs/master/image/for_quickstart/socketio_to_topic.png)

![socketio_to_alias.png](https://raw.githubusercontent.com/yunba/docs/master/image/for_quickstart/socketio_to_alias.png)

![socketio_term_recv.png](https://raw.githubusercontent.com/yunba/docs/master/image/for_quickstart/socketio_term_recv.png)

更多用法和功能，请参考 [socket.io API 手册](http://yunba.io/docs2/socket.io_API/)。
