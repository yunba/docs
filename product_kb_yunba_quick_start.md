# 云巴快速入门教程

![productpng_kb_quick_start_flow.png](https://raw.githubusercontent.com/yunba/docs/master/image/productpng_kb_quick_start_flow.png)

## 第一步 注册账号

打开 [云巴官方网站](https://yunba.io)，注册并登录。

## 第二步 创建新应用

登录后，点击界面右上角 “创建应用”，填写应用名称、应用包名等内容。详见 [Portal 的说明文档](product_kb_portal.md)。

获得应用的 [AppKey](product_kb_app_key.md)。

## 第三步 下载 SDK

在云巴官网的 [开发者页面](https://yunba.io/developers/) 或云巴的 [Github](https://github.com/yunba) 上找到所需平台的 SDK，并下载。

每种 SDK 的文件包内都包含一个 Demo，用来演示主要 API 的用法和功能。
建议您从运行 Demo 开始熟悉云巴的 SDK，详见各个 SDK 的 Demo 运行的文档。

## 第四步 集成 SDK

将云巴 SDK 集成到您的应用中（此时会用到第二步中的 [AppKey](product_kb_app_key.md)）。详细内容可参考各个 SDK 的快速入门文档。

## 第五步 开始通信

集成 SDK 后，您可以将该应用安装在 A、B、C 三个设备上（当然，您也可以集成到不同平台的应用中，进行跨平台通信），进行实时通信了：

- A 和 B 订阅一个 [频道](product_kb_topic_and_alias.md)，C 向该 频道 发一条消息，那么 A 和 B 都会收到这条消息。

- 给 A 设置 [别名](product_kb_topic_and_alias.md) “Alex”，就可以用 B 或 C 向 Alex 发一对一的消息。

- B 和 C 还可以订阅 Alex 的 [在线](product_kb_presence.md) 状态，实时查看 Alex 的上下线和订阅状态。

- 发消息时，您还可以指定消息送达的 [QoS](product_kb_qos.md) 等级（至少一次/至多一次/有且仅有一次）。如果客户端当前不在线，云巴还可以为您保存 [离线消息](product_kb_offline_message.md)。

