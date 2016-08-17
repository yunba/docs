# 运行 Yunba C# Demo

本文介绍如何运行 Yunba C# SDK 中的 Sample Demo 示例程序。

本文涉及的运行环境如下：

* Windows 8.1
* Microsoft Visual Studio 2015
* YunBa C# SDK

## 准备工作

### 1. 下载云巴 C# SDK
这里使用的是 Yunba C# SDK ，这个工程是在 Visual Studio 2013 下 build 的。
可打开[其 GitHub 页](https://github.com/yunba/yunba-csharp-sdk)，下载 Zip 文件。


### 2. 注册云巴开发者账号
打开 [云巴官方网站](https://yunba.io)，点击右上角的“注册”按钮创建账号。  

## 详细步骤

### 1. 在云巴 Portal 上创建新应用
请参考 [运行 Yunba Android Demo](android_demo_quick_start.md) 
一文中的该步骤的做法，获得一个 AppKey。

### 2. 打开工程
本文以 [Yunba C# SDK](https://github.com/yunba/yunba-csharp-sdk) 的 Sample 工程为例，演示 C# SDK 的使用。

解压下载的 Zip 文件，在 Visual Studio 2013 中打开 "MqttDotNet.sln" 工程。如图：


![csharppng_demo_mqtt_dot_net.png](https://raw.githubusercontent.com/yunba/docs/master/image/csharppng_demo_mqtt_dot_net.png)


右击 Sample 工程，选择“设为启动项目”。


![csharppng_demo_select_project.png](https://github.com/yunba/docs/blob/master/image/csharppng_demo_select_project.png)


### 3. 运行程序
运行程序，会生成 "\yunba-csharp-sdk-master\Sample\bin\Debug" 路径下的 "Sample.exe"。
在控制台进入该路径，启动 "Sample.exe" ，同时输入 AppKey，如图: 

![csharppng_demo_run_project.png](https://raw.githubusercontent.com/yunba/docs/master/image/csharppng_demo_run_project.png)

连接成功之后会显示 "Client connected",可以按任意键继续往下执行。如图:

![csharppng_demo_connected.png](https://raw.githubusercontent.com/yunba/docs/master/image/csharppng_demo_connected.png)

按任意键之后将出现如下提示:

![csharppng_demo_enter_commonId.png](https://raw.githubusercontent.com/yunba/docs/master/image/csharppng_demo_enter_commonId.png)

可以先订阅话题 `subscribe()` :

![csharppng_demo_subscribe.png](https://raw.githubusercontent.com/yunba/docs/master/image/csharppng_demo_subscribe.png)

再发布消息 `publish()`。发布成功后，使用同一 AppKey 并且已订阅该 Topic 的其它客户端将收到消息。使用同一个 AppKey 的客户端在该 Topic 下发布的消息，C# 端也将收到。

![csharppng_demo_publish_message.png](https://raw.githubusercontent.com/yunba/docs/master/image/csharppng_demo_publish_message.png)


详细的程序逻辑，请参考项目源程序。


**注**：

如需清除用户信息，可删除工程 "bin/Debug" 路径下所有以 ".config" 为后缀名的文件。

