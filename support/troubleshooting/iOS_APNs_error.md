## 问题

### 使用 iOS SDK，上传了 APNs 证书，但应用在后台时却收不到 APNs 通知？

## 解决方案

1. 请确认一下是否在云巴 Portal 的该应用页面下正确地上传了 APNs 证书；
2. 请确认应用是否开启了接收 APNs；
3. 请通过查看日志打印或 store Device Token 回调的返回值的方式来确认 Device Token 是否上传成功了；
4. 集成 SDK 所使用的 AppKey 是否是 Portal 中该应用的 AppKey；
5. 请确认 Xcode 里填入的 bundle id 与在 Apple Portal 上的创建的证书的 bundle id 是否一致；
6. 另外还需注意：`publish()` 或 `publish_to_alias()` 会发送默认的 APNs 通知，
而 `publish2()` 或 `publish2_to_alias()`，则**必须在 opts 里填写 apn_json，才会发送 APNs 通知**。

最后，如果问题依然没有得到解决，请将您的 Appkey 用私聊或邮件（support@yunba.io）的方式发给我们，我们会尽快协助您找出原因。

===
###相关参考文档：
- [API 手册](https://github.com/yunba/docs/blob/master/iOS_API_Reference.md)
- [iOS SDK 快速入门](https://github.com/yunba/docs/blob/master/iOS_Quick_Start.md)
- [运行 Yunba iOS Demo](https://github.com/yunba/docs/blob/master/quickstart/demo/Demo_iOS.md)
- [云巴 iOS 消息推送](https://github.com/yunba/kb/blob/master/云巴%20iOS%20消息推送.md)
- [如何通过云巴实现 APNs 推送](https://github.com/yunba/kb/blob/master/如何通过云巴实现%20APNs%20推送.md)
- [APNs 推送的 Payload 介绍](https://github.com/yunba/kb/blob/master/APNs/Payload.md)
- [FAQ](https://github.com/yunba/docs/blob/master/support/faq/faq.md#ios-sdk)
