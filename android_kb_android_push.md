# 云巴 Android 消息推送

如下图所示，客户端集成了云巴的 Android SDK，服务端可通过云巴的 SDK 或使用 RESTful API，向 Android 客户端发消息。

![androidpng_kb_push_message.png](https://raw.githubusercontent.com/yunba/docs/master/image/androidpng_kb_push_message.png)

* 后台保持长连接

Android SDK 会启动一个后台的 Service，创建并保持到云巴服务器的长连接，从而保证了消息推送的实时性。

* 确保消息的送达

云巴 SDK 支持 [离线消息](product_kb_offline_message.md) 的功能，可保证消息送达客户端。

在推送消息时，如果客户端当前不在线，消息将暂存在云巴服务器上（多达 50 条，长达 15 天）。
当客户端上线并成功连接到云巴的服务器后，服务器会把离线消息推送给该客户端。客户端成功接收后，服务器才会删除保存的离线消息。
