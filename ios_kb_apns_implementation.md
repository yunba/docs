# 如何通过云巴实现 APNs 推送

## 概述
熟悉 iOS 开发的朋友们都知道，实现 APNs 推送的前提是：告诉苹果的 Push 服务器推送消息的去向——即，发往哪个应用（即应用证书）、哪台机器（即 Device Token）。

因而，[云巴的 iOS 消息推送](ios_kb_ios_push.md) 也有同样必不可少的前提步骤：上传 APNs 推送证书到云巴 Portal 相应的应用界面、获取 Device Token 并保存到云巴服务器。

## 详细步骤
如果在集成云巴 iOS SDK 同时，希望激活 APNs 推送功能，具体应该怎么做呢？

- [生成 APNs 证书](ios_kb_create_apns_certificate.md)。
- 打开 [云巴官方网站](https://yunba.io)，点击右上角的“注册”按钮创建云巴开发者账号。
- 在 [Portal 上创建新应用](product_kb_portal.md#如何在云巴-portal-上创建新应用)，并**上传开通了 Push Notification 功能的 iOS 开发或生产证书**。
- 打开 [云巴开发者页面](https://yunba.io/developers)，下载最新版本的 iOS SDK，并导入到 App 的 Xcode 工程中。
- [生成 Provisioning Profile](ios_kb_create_provisioning_profile.md)，并导入到 Xcode 中。
- 配置 Xcode 中的 Bundle ID 和 Code Signing。
- 代码处理（详见 iOS SDK 中的 YunBaDemo）：
  - 添加头文件，引入YunBaService.h。
  - 通过[`setupWithAppkey`](ios_sdk_api_manual.md#setup)初始化。使用您从 Portal 获取到的 Appkey 替换初始化函数  中的 Appkey。
  - 通过[`subscribe`](ios_sdk_api_manual.md#subscribe)订阅 Topic。
  - 在默认消息中心添加监听消息及处理代码。
  - 通过 registerForRemoteNotificationTypes 注册并获取 Device Token。如果注册成功，再通过 didRegisterForRemoteNotificationsWithDeviceToken **将 Device Token 保存到云巴服务器**；如果注册失败，didFailToRegisterForRemoteNotificationsWithError 会被调用，可根据 NSError 的描述排查出错原因。
  - 处理收到消息后的回调。
- 处理好 App 的其他代码逻辑后，便可以通过 [Portal 发送消息](product_kb_portal.md#利用云巴-portal-发布消息)，进行测试了。

如下图所示，详细步骤可参考 [运行 Yunba iOS Demo](ios_demo_quick_start.md) 一文。


![productpng_kb_apns.png](https://raw.githubusercontent.com/yunba/docs/master/image/productpng_kb_apns.png)
