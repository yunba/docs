# 运行 Yunba Java Demo

本文介绍如何运行 [yunba-java-sdk](https://github.com/yunba/yunba-java-sdk) 中的 Demo。

本文涉及的运行环境如下：

* Mac OS X 10.11.2
* bash 3.2.57
* Eclipse 4.5.1
* [Apache Ant 1.9.6](https://ant.apache.org/index.html)

## 准备工作

###1. 注册云巴开发者账号
打开 [云巴官方网站](http://yunba.io "云巴官方网站")，点击右上角的 “注册” 按钮创建账号。  

###2. 下载云巴 Java SDK
下载 [yunba-java-sdk](https://github.com/yunba/yunba-java-sdk)，并解压。

###3. 编译生成 jar
在终端下，进入 SDK 所在的目录，用 ant 编译生成 SDK 的 jar 文件。
```bash
$ ant sdkjar
```
运行命令后，会根据 build.xml 的内容，在 SDK 的 dist 文件夹下生成出 yunba-java-sdk.jar 文件。
```bash
sdkjar:
      [jar] Building jar: /yunba-java-sdk-master/dist/yunba-java-sdk.jar

BUILD SUCCESSFUL
```

###4. 导入工程和 jar
打开 Eclipse，新建一个空白的 Java 工程，拖入 YunBaDemo.java 文件。此时会出现大量报错，只需导入上一个步骤中生成的 yunba-java-sdk.jar 文件即可解决。步骤如下。

右击工程名称，选择 Properties --> Java Build Path，在 Libraries 标签页下选择 Add External JARs，然后在弹出的窗口中，指定 yunba-java-sdk.jar 文件。

## 详细步骤

###1. 在云巴 Portal 上创建新应用
在云巴 [Portal](http://yunba.io/docs2/portal) 上创建新应用，并获得一个 [AppKey](http://yunba.io/docs2/appkey)。

###2. 修改 APPKEY
将 src/YunBaDemo.java 里的 APPKEY 改为上一步骤中自己创建的 AppKey。
```java
final MqttAsyncClient mqttAsyncClient = MqttAsyncClient.createMqttClient("XXXXXXXXXXXXXXXXXXXXXXX");
```

###3. 运行

在 [YunBaDemo.java](https://github.com/yunba/yunba-java-sdk/blob/master/src/YunBaDemo.java) 中，给出了 [初始化](http://yunba.io/docs2/java_api#createmqttclient)、[连接云巴服务器](http://yunba.io/docs2/java_api#connect)，以及成功连接后的 [订阅 Topic](http://yunba.io/docs2/java_api#subscribe)、
[向 Topic 发布消息](http://yunba.io/docs2/java_api#publish)、[设置 Alias](http://yunba.io/docs2/java_api#setalias)、[向 Alias 发布消息](http://yunba.io/docs2/java_api#publishtoalias)的示例。

可直接运行 Demo 客户端，进行消息收发的测试。客户端运行后，可以通过 [Portal](http://yunba.io/docs2/portal) 向该客户端发布消息，或通过集成其他的云巴 SDK 来与之通信。

更多说明，请参考 [Java API 文档](http://yunba.io/docs2/java_api)。
