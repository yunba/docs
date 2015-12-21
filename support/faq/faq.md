## 常见问题及解答

### 商务合作

---
* 云巴是如何收费的？
* 请发邮件到：support@yunba.io 咨询。

---

### 业务范围

---
* [云巴是什么](https://github.com/yunba/kb/blob/master/云巴是什么.md)

---
* 云巴是做推送的吗？
* 云巴，即云消息总线，是一个跨平台实时消息系统。消息推送只是云巴其中一个产品。

---
* 云巴可以用于智能家居、物联网吗？
* 云巴兼容标准的 MQTT 3.1 协议，即物联网协议，因而天然支持智能家居、物联网。

---
* 云巴对 MQTT 协议做了哪些扩展？
* 请参考我们的 [Porting 文档](https://github.com/yunba/docs/blob/master/yunba_MQTT_porting.md)。

---
* 云巴有哪些产品？
* Android、iOS 消息推送（集成 APNs），跨平台双向实时通信，实时查看统计信息，实时获取在线状态。

---
* 云巴能用在 Web 上吗？
* 可以。云巴支持 socket.io 协议。请参考官网上的 [socket.io API](http://yunba.io/docs2/socket.io_API/)、[RESTful API](http://yunba.io/docs2/restful_Quick_Start/) 及 [JavaScript SDK](http://yunba.io/docs2/Javascript_SDK/) 文档。

---
* 云巴系统可以用来发短信吗？
* 不可以。

---
* 云巴可以根据地域进行推送吗？
* 暂不支持。

---
* 请问从国外访问云巴的服务会有问题吗？
* 没问题。

---
* 云巴有完整的聊天系统可直接用吗？
* 云巴只做最底层的消息系统，发送的具体内容不做处理。


### 消息推送、实时消息

---
* 和其他公司相比，云巴的消息推送有什么不同？
* 云巴支持双向推送，一个客户端既可以 Publish 也可以 Subscribe；而其他家的单向推送，只能 Subscribe，Publish 还需要提供新的接口。

---
* [云巴支持的“频道”和“别名”两种发布方式，具体是怎样的？](https://github.com/yunba/kb/blob/master/频道和别名.md)

---
* [云巴的离线消息是怎样的？](https://github.com/yunba/kb/blob/master/云巴的离线消息是怎样的.md)

---
* 云巴的消息送达率是多少？
* 理论上送达率是百分之百。所有消息都会跟踪状态，只要终端能上线，消息一定能送达。

---
* 云巴的实时消息系统依赖什么网络环境？
* 2G，3G，4G，Wi-Fi 均可。

---
* 发送消息出现超时是什么原因？
* 请先检查网络连接是否正常。

---
* 发送的消息大小有什么限制吗？
* 建议不要超过 2K。

---
* 支持发送语音、图片、视频吗？
* 大数据建议使用第三方存储。可先将资源保存到服务器，然后推送地址。

---
* 发出的消息有历史记录吗？
* 目前只有在 Portal 发的消息，才可以查看历史记录。

---
* 订阅一个频道后，可以收到在订阅之前频道内推送的消息吗？
* 不可以。

---
* 发送方怎样知道接收方收到了？
* 接收方收到消息后，服务器会给发送方再发一个 recvack。

---
* 发布的消息可以撤回吗？
* 目前不支持撤回。

---
* 是否保证消息按顺序送达？
* 不保证。

---
* 发布消息前必须先订阅频道吗？
* 发布端可以不用订阅，但接收端必须订阅了频道，才能收到该频道发布的消息。

---
* 为什么我可以收到自己发布的消息？
* 只要订阅了就会收到。

---
* 是不是订阅一次就可以永久有效？
* 是。

---
* 频道的数量有限制吗？
* 没有。

---
* 离线消息的数量有限制吗？
* 最多保留 50 条。

---
* 可以同时给多个频道推送消息吗？
* 不可以。一次只能向一个频道发布消息。

---
* 云巴可以定点推送给某个设备吗？
* 可以，请参考 [别名（alias）的相关文档](https://github.com/yunba/kb/blob/master/频道和别名.md)。

---
* 推送的消息出现乱码是什么原因？
* 云巴采用二进制透传，不对消息做任何处理。如果出现乱码，请自行检查应用的编码解码程序。

---
* 使用云巴 SDK，怎样实现不同平台之间的通讯呢？
* 在云巴官网 Portal 创建新应用，创建后得到一个 AppKey，在不同平台上使用同一个 AppKey，即可互相通讯。

---
* 不同 AppKey 之间可以互相通信吗？
* 不可以。一个应用包名对应一个 AppKey，使用同一个 AppKey 的客户端才可以相互通信。

---
* Android SDK 不同包名可以互相通信吗？
* 使用云巴 Android SDK，如果需要同一个 Appkey 不同包名的客户端之间能够互相通信，请把 Appkey 对应的包名发到 support@yunba.io，我们会在内部做些处理来支持。


###iOS SDK

---
* 1. 如何实现 iOS 应用退出或者处于后台时可以收到推送消息？
* 需要 [生成APNS证书](http://yunba.io/docs2/iOS_Quick_Start/#在Portal上传APNs证书以激活APN推送功能)；在 App 注册 remoteNotifacation 通知，获取 device token，并通过[`storeDeviceToken（）`]( http://yunba.io/docs2/iOS_API_Reference/#storeDeviceToken) 函数保存 device token 到云巴服务端；
通过带有 ApnOption 的 `publish2()`或者默认的 `publish()`进行发送 APNs 消息，该参数设置详见 [iOS 官方文档](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/TheNotificationPayload.html#//apple_ref/doc/uid/TP40008194-CH107-SW1)。
<br>
**注**：完成 APNs 注册后，`publish2()` 需要带有 ApnOption 参数才能成功发送；而 `publish()` 会发送默认的 APNs 消息。

---
* 2. 接收的消息，除了基本的内容（Topic 和 Message）还可以传递一些参数信息吗？
* 开发者可以封装多个数据到 data.msg。

---
* 3. ApnOption 的 sound 和 badge 有什么作用？
* 可在`publish2ToAlias()` 、 `publish2()` 的 ApnOption 参数设置消息通知的方式。
alert 设置消息通知栏的内容；badge 设置角标；sound 设置通知的铃声。
具体参考 [iOS 官方文档](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/TheNotificationPayload.html#//apple_ref/doc/uid/TP40008194-CH107-SW1) 和下载 [iOS demo]( http://yunba.io/developers/) 参考 ApnOption 的设置方法。

---
* 4. iOS SDK `subscribe()` 的 qosLevel 参数，和 YBPublish2Option 的 qos 这两个参数有什么区别？
* `subscribe()` 的 qos Level 限制该话题下接收到 message 的最大 qos 等级。 例如：当设置 `subscribe()` 的 qosLevel 为 0，则 qos 为 1 的接收消息会降级到 qos 为 0。详见 [MQTT V3.1 Protocol Specification
]( http://public.dhe.ibm.com/software/dw/webservices/ws-mqtt/mqtt-v3r1.html#subscribe) 和 [QoS 的说明](https://github.com/yunba/kb/blob/master/QoS.md)。


---
* 5. iOS 端怎么设置不接收任何消息？
* 设置别名为空和 `unsubscribe()` 所有 Topic。

---
* 6. 当同时定义了 `publish()` 的 data 和 ApnOption 参数中 alert 的 message，消息内容将以哪个为准？
* 以 alert 的 message 为准。当没定义 alert 时，则默认显示 `publish()` 的 data。

---
* 7. iOS 端如何设置通知方式？
* [上传 APNs 证书](http://yunba.io/docs2/iOS_Quick_Start/#在Portal上传APNs证书以激活APN推送功能) ；
通过 YBPublish2Option 参数的 alert 设置通知栏内容、角标和声音等，具体参考 sdk 中关于 [`pushlish2()`](http://yunba.io/docs2/iOS_API_Reference/#publish2) 的介绍 和下载 [iOS demo]( http://yunba.io/developers/) 参考 YBPublish2Option 的设置。
完整的设置方法参考 [iOS官方文档](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/TheNotificationPayload.html#//apple_ref/doc/uid/TP40008194-CH107-SW1)。


###Android SDK



---
* 1. 当 App 退出，进程被杀死时，能接收到 Message 吗？
* Android 端需要在后台保留进程才能接收消息；iOS 端的 APNs 在 App 退出后仍可接收消息。
<br>
Android 端的解决方法：增加相互拉起功能和后台守护进程，使 App 退出后仍能接收到推送消息。

---
* 2. Android 端如何设置 qos 等级？
* `publish2()`、`publish2ToAlias()` 的 opts(JSONObject) 参数可以设置 qos。
<br>
附：qos 为服务质量等级。有三种取值：“0” 表示最多送达一次；“1” 表示最少送达一次；“2” 表示保证送达且仅送达一次。默认为 “1”。详见 [QoS 的说明](https://github.com/yunba/kb/blob/master/QoS.md)。

---
* 3. Android 端怎么设置离线消息时间？
* 设置 `publish2()`、`publish2ToAlias()`的 opts（JSONObject） 参数；
qos 设置为 1 或 2，就能够保证离线消息的送达；设置 time_to_live，可以控制离线消息在云巴服务器上保留的时间（以秒为单位）。详见： [云巴的离线消息是怎样的](https://github.com/yunba/kb/blob/master/%E4%BA%91%E5%B7%B4%E7%9A%84%E7%A6%BB%E7%BA%BF%E6%B6%88%E6%81%AF.md) 。

---
* 4. Yunba-Android-SDK 有没有设置通知栏的 API？
* 关于设置消息通知栏，Yunba-Android-SDK 没提供相关的 API。设置方法可参考 [Android官方文档](http://developer.android.com/guide/topics/ui/notifiers/notifications.html)。


---
* 5. 怎么获取 Message 的 Message ID？
* Yunba-Android-SDK 暂时没有提供获取接收消息的 Message ID 的API。
如果需要 Message ID 等自定义内容，可以封装自定义内容到 Message 进行发送，在接收时进行解析。



---
* 6. Android 端如何断开连接，不接收消息？
* 可以调用 [`stop()`](http://yunba.io/docs2/Android_API_Reference/#stop) 停止推送服务，使所有的 API 都失效（包括 start API）；当需要重新使用推送服务时，必须要调用 [`resume ()`](http://yunba.io/docs2/Android_API_Reference/#resume)。


