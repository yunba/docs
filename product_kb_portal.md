# Portal

## 功能
Portal，即 “门户”。云巴的 Portal 是用户应用的管理入口，后端连接着云巴的实时消息系统。

用户在云巴官网注册并登录后，可以通过点击页面右上方的用户名进入 Portal 页面。 
通过云巴的 Portal 页面，可以创建和管理应用、发布消息、查看统计信息，还可以查看通过 Portal 发布过的消息的历史记录。

常有客户疑惑，为什么只有一个设备在线时，Portal 的活跃用户统计数量却是 2 ？原因很简单，Portal 也同时是一个 [JavaScript](https://github.com/yunba/yunba-javascript-sdk) 客户端，可以向指定应用（[AppKey](product_kb_app_key.md)）的指定 Topic/Alias 发布消息，因此会算在统计内。

## 如何在云巴 Portal 上创建新应用

- 打开[云巴官方网站](https://yunba.io)，注册并登录。
- 登录后，会进入 Yunba Portal 界面，点击右上角 “创建应用”。
- 逐一填写应用信息。其中，“应用名称” 和 “应用包名” 是必填项。对于 Android 应用来说，“应用包名” 一项需要填写 “Android” 应用包名。见下图。
- 对于 iOS 应用，在 “iOS 开发/生产证书” 处上传 iOS 开发/生产证书（*.p12）。如果证书导出时有设置密码，需要在 “开发/生产证书密码” 项填上证书的密码。
- 创建完成后，查看 “应用信息” 页面，可以看到应用的 AppKey、Secret Key 等。**请妥善保管好您的 AppKey、Secret Key 等应用信息，不要在群聊等公众场合下泄露。**

![productpng_portal_creat_new_app.png](https://raw.githubusercontent.com/yunba/docs/master/image/productpng_portal_creat_new_app.png)


## 利用云巴 Portal 发布消息

### 通过 Publish 向频道发布消息


客户端集成 YunBa SDK 后，打开 Portal 上应用详情页面，可以向客户端 `subscribe` 的 [频道](product_kb_topic_and_alias.md)（Topic）发布消息，客户端即可收到消息，如图所示:

![productpng_portal_publish_to_topic.png](https://raw.githubusercontent.com/yunba/docs/master/image/productpng_portal_publish_to_topic.png)

在客户端（以 Android 客户端为例）订阅频道：

![androidpng_demo_app_message.png](https://raw.githubusercontent.com/yunba/docs/master/image/androidpng_demo_app_message.png)

客户端收到 Portal 发布的消息：

![androidpng_demo_notification.png](https://raw.githubusercontent.com/yunba/docs/master/image/androidpng_demo_notification.png)

### 通过 Publish2 向频道发布消息

此外，Portal 还提供了通过 Publish2 发布消息的功能。

- 点击 **更多选项** 可设置 [离线消息](product_kb_offline_message.md) 保留时间（Time To Live）、[QoS](product_kb_qos.md) 值 和 Message ID（如果不填则由系统自动生成）
- 可设置 **APN JSON** 发送 APNs 消息，发送 APNs 消息的方法具体可参考 [如何通过云巴实现 APNs 推送](ios_kb_apns_implementation.md) 和 [APNs 参数设置](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/TheNotificationPayload.html#//apple_ref/doc/uid/TP40008194-CH107-SW1)。

![productpng_portal_publish2_to_topic.png](https://raw.githubusercontent.com/yunba/docs/master/image/productpng_portal_publish2_to_topic.png)



### 通过 Publish 向别名发布消息


如果客户端通过 `SetAlias` 设置了[别名](product_kb_topic_and_alias.md)，用户还可以通过 Portal 向客户端的别名发布消息：

![productpng_portal_publish_to_alias.png](https://raw.githubusercontent.com/yunba/docs/master/image/productpng_portal_publish_to_alias.png)

此时，同一 AppKey 下，别名为 Jack 的客户端就会收到该条消息。

### 通过 Publish2 向别名发布消息

类似地，也可以通过 Publish2 发布消息，带更多的参数，如下图。设置了该别名的客户端会收到消息。

- 点击 **更多选项** 可设置 [离线消息](product_kb_offline_message.md) 保留时间（Time To Live）、[QoS](product_kb_qos.md) 值 和 Message ID（如果不填则由系统自动生成）
- 可设置 **APN JSON** 发送 APNs 消息，发送 APNs 消息的方法具体可参考 [如何通过云巴实现 APNs 推送](ios_kb_apns_implementation.md) 和 [APNs 参数设置](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/TheNotificationPayload.html#//apple_ref/doc/uid/TP40008194-CH107-SW1)。

![productpng_portal_publish2_to_alias.png](https://raw.githubusercontent.com/yunba/docs/master/image/productpng_portal_publish2_to_alias.png)


### 查看 Portal 上发布的历史消息

点击 **历史消息** 可查看在 Portal 上发布的历史消息。


## 云巴 Portal 的发布上报统计

![report.png](https://raw.githubusercontent.com/yunba/docs/master/image/productpng_portal_publish_statistic.png)

如图所示，可查看该应用（[AppKey](product_kb_app_key.md)） 下的消息发布和送达情况。

蓝色波形为一定时间（10 秒、分钟、小时、日）内的消息发布数量；黑色波形为一定时间内的消息送达数量。（Portal 也属于一个用户）

## 云巴 Portal 的在线用户统计

![online.png](https://raw.githubusercontent.com/yunba/docs/master/image/productpng_portal_online_statistic.png)

- 一段时间内的在线用户数：该时间段内持续在线（connected）的用户数量。如：“在线用户数/小时” 的单位表示该小时内持续在线，未断开与云巴的长连接的用户数量。
- 一段时间内的活跃用户数：该时间段内进行过上线操作的用户数量（不一定持续在线）。如：“活跃用户数/小时” 的单位表示该小时内进行过上线操作，即连接过云巴服务的用户数量。

>**注**：如果订阅了该 Topic 但未设置用户 [别名](product_kb_topic_and_alias.md)（Alias），则在 “在线用户”和“频道用户列表” 都不进行显示。

### 频道用户列表

点击 **频道用户列表**，可查看应用（[AppKey](product_kb_app_key.md)） 下某个 [频道](product_kb_topic_and_alias.md) 的收听用户别名列表。


## 云巴 Portal 的设备状态查询

![productpng_portal_alias_state.png](https://raw.githubusercontent.com/yunba/docs/master/image/productpng_portal_alias_state.png)

输入设备 [别名](product_kb_topic_and_alias.md) 后，可以查看该设备的 UID、当前的在线/离线状态，以及最近一次上线的时间。
