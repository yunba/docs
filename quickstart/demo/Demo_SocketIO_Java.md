# 运行 Yunba Socket.IO Java Demo

本文介绍如何运行 [yunba-socket.io-java-sdk](https://github.com/yunba/yunba-socket.io-java-sdk) 中的 Demo。Yunba 的 SDK 按照实现的方式，可以分为两种，一种是所谓原生 SDK，一种是基于 Socket.IO 的 SDK。Yunba Socket.IO Java SDK 是基于 Socket.IO 的 SDK。

**对于 Java 开发者，推荐使用云巴的 [Java SDK](https://github.com/yunba/yunba-java-sdk)。**

本文涉及的运行环境如下：

* Mac OS X 10.11.2
* Eclipse 4.5.1

## 准备工作

###1. 注册云巴开发者账号
打开 [云巴官方网站](http://yunba.io "云巴官方网站")，点击右上角的“注册”按钮创建账号。  

###2. 下载云巴 Socket.IO Java SDK
下载 [yunba-socket.io-java-sdk](https://github.com/yunba/yunba-socket.io-java-sdk)，并解压。

###3. 导出 socketio.jar
在 Eclipse 中打开 yunba-socket.io-java-sdk，导出为 socketio.jar 文件备用。

## 详细步骤

###1. 在云巴 Portal 上创建新应用
请参考 [运行 Yunba Android Demo](http://yunba.io/docs2/android_demo) 
一文中的该步骤的做法，获得一个 AppKey。

###2. 修改 APPKEY
在 Eclipse 中导入 yunba-socket.io-java-sdk 工程，将 examples/basic/BasicExample.java 里的 APPKEY 改为上一步中自己创建的 AppKey。

```java
private static String APPKEY = "XXXXXXXXXXXXXXXXXXXXXXX";
```

###3. 导入 socketio.jar
在 Eclipse 中，将 socketio.jar 导入到该工程的 Build Path 下。

**注**：对于这个 Demo 来说，工程里包含了 src 目录，因此不导入 socketio.jar 也可以使用。

###4. 运行

为了测试消息收发，可以增加订阅、发布的相关代码：

```java
public void onConnAck(JSONObject json) throws Exception {
    System.out.println("onConnAck success " + json.get("success"));
    socket.emit("subscribe", new JSONObject("{'topic': 'news'}"));
    socket.emit("publish", new JSONObject("{'topic': 'news', 'msg': 'hello form java socket.io client', 'qos': 1}"));
}
```

代码修改好后，在 Eclipse 的 Package Explorer 面板中，右击 examples 文件夹下的 BasicExample.java，选择 Run as --> Java Application。
应用运行后，可以看到 Console 中打印的日志，包含建立连接、消息收发、心跳包等信息。

**注**：默认情况下，基于 Socket.IO 的 SDK 的心跳为 30 秒一次。

```bash
一月 05, 2016 3:42:59 下午 io.socket.IOConnection transportMessage
信息: < 1::
Connection established
一月 05, 2016 3:42:59 下午 io.socket.IOConnection transportMessage
信息: < 5:::{"name":"socketconnectack","args":[{"msg":"socket.io connected"}]}
Server triggered event 'socketconnectack'
onSocketConnectAck
一月 05, 2016 3:42:59 下午 io.socket.IOConnection sendPlain
信息: > 5:::{"args":[{"appkey":"XXXXXXXXXXXXXXXXXX","customid":"f02bf150-c653-4557-973f-8526b078d736"}],"name":"connect"}
一月 05, 2016 3:43:01 下午 io.socket.IOConnection transportMessage
信息: < 5:::{"name":"connack","args":[{"success":true,"sessionid":"567a4a754407a3cd028aaf6b-f02bf150-c653-4557-973f-8526b078d736"}]}
Server triggered event 'connack'
onConnAck success true
一月 05, 2016 3:43:01 下午 io.socket.IOConnection sendPlain
信息: > 5:::{"args":[{"topic":"news"}],"name":"subscribe"}
一月 05, 2016 3:43:01 下午 io.socket.IOConnection sendPlain
信息: > 5:::{"args":[{"msg":"hello form java socket.io client","qos":1,"topic":"news"}],"name":"publish"}
一月 05, 2016 3:43:01 下午 io.socket.IOConnection transportMessage
信息: < 5:::{"name":"suback","args":[{"success":true,"topic":"news","messageId":"531014746925719552"}]}
Server triggered event 'suback'
一月 05, 2016 3:43:01 下午 io.socket.IOConnection transportMessage
信息: < 5:::{"name":"puback","args":[{"success":true,"messageId":"531014747101880320"}]}
Server triggered event 'puback'
conPubAck success true msg id 531014747101880320
一月 05, 2016 3:43:01 下午 io.socket.IOConnection transportMessage
信息: < 5:::{"name":"message","args":[{"topic":"news","msg":"hello form java socket.io client"}]}
Server triggered event 'message'
一月 05, 2016 3:43:29 下午 io.socket.IOConnection transportMessage
信息: < 2::
一月 05, 2016 3:43:29 下午 io.socket.IOConnection sendPlain
信息: > 2::
```

更多相关说明，请参考 [SDK 的 Readme 文件](https://github.com/yunba/yunba-socket.io-java-sdk/blob/master/README.markdown) 以及 [Socket.IO API 手册](http://yunba.io/docs2/socket.io_API/)。
