# iOS SDK 常见问题解答

<a name="1"></a>1. **如何实现 iOS 应用退出或者处于后台时可以收到推送消息？**

答：
* 需要生成 APNs 证书；
* 在 App 注册 remoteNotification 通知，获取 Device Token，并通过[`storeDeviceToken()`](ios_sdk_api_manual.md#storeDeviceToken)函数保存 Device Token 到云巴服务端；
* 通过带有 ApnOption 的`publish2()`、`publish2ToAlias()`或者默认的`publish()`、`publishToAlias()`发送 APNs 消息，该参数设置详见云巴知识库的 [Payload](ios_kb_payload.md) 一文，以及 [iOS 官方文档](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/TheNotificationPayload.html#//apple_ref/doc/uid/TP40008194-CH107-SW1)。

**注**：`publish2()`需要带有 ApnOption 参数才能成功发送 APNs 消息；而`publish()`会发送默认的 APNs 消息。

---
<a name="2"></a>2. **接收的消息，除了基本的内容（Topic 和 Message）还可以传递一些参数信息吗？**

答： 开发者可以封装多个数据到 data.msg。

---
<a name="3"></a>3. **ApnOption 的 sound 和 badge 有什么作用？**

答： 可在`publish2ToAlias()`、`publish2()`的 ApnOption 参数设置消息通知的方式。
alert 设置消息通知栏的内容；badge 设置角标；sound 设置通知的铃声。

具体参考云巴知识库的 [Payload](ios_kb_payload.md) 一文，以及 [iOS 官方文档](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/TheNotificationPayload.html#//apple_ref/doc/uid/TP40008194-CH107-SW1)，或参考 iOS demo 中有关 ApnOption 的设置方法。

---
<a name="4"></a>4. **如何自定义 iOS 推送的铃声？**

答： 先将自定义的铃声文件加入你的 Xcode 工程里，然后，在推送的时候指定音频的名称即可。注意使用系统支持的音频格式。

```json
{
	"aps": {
		"alert":"yunba",
		"badge":3,
		"sound":"bingbong.aiff"
	}
}
```
 
 如使用默认铃声，则为：
```JSON
 "sound":"default"
```

---
<a name="5"></a>5. **如何处理 badge？**

答： 在推送的时候可通过指定 badge 的值来改变当前的 badge（如上例）；通过`[[UIApplication sharedApplication] setApplicationIconBadgeNumber:0];`可清除 badge 的值。

---
<a name="6"></a>6. **iOS SDK`subscribe()`的 qosLevel 参数，和 YBPublish2Option 的 qos 这两个参数有什么区别？**

答：`subscribe()`的 qos Level 限制该话题下接收到 message 的最大 qos 等级。 例如：当设置`subscribe()`的 qosLevel 为 0，则 qos 为 1 的接收消息会降级到 qos 为 0。详见 [MQTT V3.1 Protocol Specification
]( http://public.dhe.ibm.com/software/dw/webservices/ws-mqtt/mqtt-v3r1.html#subscribe) 和 [QoS](product_kb_qos.md) 的说明。

---
<a name="7"></a>7. **iOS 端怎样设置不接收任何消息？**

答： 设置别名为空，并`unsubscribe()`所有 Topic。

---
<a name="8"></a>8. **当同时定义了`publish()`的 data 和 ApnOption 参数中 alert 的 message，消息内容将以哪个为准？**

答： 以 alert 的 message 为准。未定义 alert 时，则默认显示`publish()`的 data。

---
<a name="9"></a>9. **iOS 端如何设置通知方式？**

答： 上传 APNs 证书；通过 YBPublish2Option 参数的 alert 设置通知栏内容、角标和声音等，具体参考 sdk 中关于[`pushlish2()`](ios_sdk_api_manual.md#publish2)的介绍，也可以下载并参考 iOS demo 中 YBPublish2Option 的设置。

完整的设置方法请参考 [iOS 官方文档](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/TheNotificationPayload.html#//apple_ref/doc/uid/TP40008194-CH107-SW1)。

---
<a name="10"></a>10. **使用苹果电脑来生成 APNs 证书，在双击打开证书文件（*.cer）时，遇到“不能修改“System Roots”钥匙串”的提示**

答：在钥匙串访问的界面左侧，选择“登录”（login），将证书文件（*.cer）拖进钥匙列表中即可。


---
<a name="11"></a>11. **使用 iOS SDK，上传了 APNs 证书，但应用在后台时却收不到 APNs 通知？**

答：
* 请确认一下是否在云巴 Portal 的该应用页面下正确地上传了 APNs 证书；
* 请确认应用是否开启了接收 APNs；
* 请通过查看日志打印或 store Device Token 回调的返回值的方式来确认 Device Token 是否上传成功了；
* 集成 SDK 所使用的 AppKey 是否是 Portal 中该应用的 AppKey；
* 请确认 Xcode 里填入的 bundle id 与在 Apple Portal 上的创建的证书的 bundle id 是否一致；
* 另外还需注意：`publish()` 或 `publish_to_alias()` 会发送默认的 APNs 通知，
而 `publish2()` 或 `publish2_to_alias()`，则**必须在 opts 里填写 apn_json，才会发送 APNs 通知**。

最后，如果问题依然没有得到解决，请将您的 Appkey 用私聊或邮件（support@yunba.io）的方式发给我们，我们会尽快协助您找出原因。




