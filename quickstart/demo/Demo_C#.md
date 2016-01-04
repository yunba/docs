# 运行 Yunba C# Demo

本文介绍如何运行 Yunba C# SDK 中的 Sample Demo 示例程序。
<br>
本文涉及的运行环境如下：

* Windows 8.1
* Microsoft Visual Studio 2015
* YunBa C# SDK

## 准备工作

###1. 下载云巴 C# SDK
这里使用的是 Yunba C# SDK ，这个工程是在 Visual Studio 2013 下 build 的。<br>
可打开[其 GitHub 页](https://github.com/yunba/yunba-csharp-sdk)，下载 Zip 文件。<br>


###2. 注册云巴开发者账号
打开 [云巴官方网站](http://yunba.io "云巴官方网站")，点击右上角的“注册”按钮创建账号。  

## 详细步骤

###1. 在云巴 Portal 上创建新应用
请参考 [运行 Yunba Android Demo](https://github.com/yunba/docs/blob/master/quickstart/demo/Demo_Android.md) 
一文中的该步骤的做法，获得一个 AppKey。

###2. 打开工程
本文以 [Yunba C# SDK](https://github.com/yunba/yunba-csharp-sdk) 的 Sample 工程为例，演示 C# SDK 的使用。
<br>
解压下载的 Zip 文件，在 Visual Studio 2015 中打开"MqttDotNet.sln"工程。如图：
<br>
<br>
![csharp_MqttDotNet.png](https://raw.githubusercontent.com/yunba/docs/master/image/for_quickstart/csharp_MqttDotNet.png)


右击 Sample 工程，选择“设为启动项目”。


![csharp_set_the_ project_to_launch.png](https://raw.githubusercontent.com/yunba/docs/master/image/for_quickstart/csharp_set_the_%20project_to_launch.png)


###3. 运行程序
运行程序，会生成 "\yunba-csharp-sdk-master\Sample\bin\Debug" 路径下的 "Sample.exe"。<br>
在控制台进入该路径，启动 "Sample.exe" ，同时输入 AppKey，如图: <br>
<br>
![csharp_run_the_ project.png](https://raw.githubusercontent.com/yunba/docs/master/image/for_quickstart/csharp_run_the_%20project.png)

连接成功之后会显示 "Client connected",可以按任意键继续往下执行。如图:
<br><br>
![csharp_client_connected.png](https://raw.githubusercontent.com/yunba/docs/master/image/for_quickstart/csharp_client_connected.png)
<br><br>
按任意键之后将出现如下提示:
<br><br>
![csharp_enter _common_id.png](https://raw.githubusercontent.com/yunba/docs/master/image/for_quickstart/csharp_enter%20_common_id.png)
<br><br>
可以先订阅话题 `subscribe()` :
<br><br>
![csharp_subscribe.png](https://raw.githubusercontent.com/yunba/docs/master/image/for_quickstart/csharp_subscribe.png)
<br><br>
再发布消息 `publish()`。发布成功后，使用同一 AppKey 并且已订阅该 Topic 的其它客户端将收到消息。使用同一个 AppKey 的客户端在该 Topic 下发布的消息，C# 端也将收到。
<br><br>
![csharp_publishMessage.png](https://raw.githubusercontent.com/yunba/docs/master/image/for_quickstart/csharp_publishMessage.png)


详细的程序逻辑，请参考项目源程序。
