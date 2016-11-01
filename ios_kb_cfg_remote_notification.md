# 配置远程通知服务

本文翻译了 iOS 官方开发文档的 [Configuring Remote Notification Support](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/HandlingRemoteNotifications.html#//apple_ref/doc/uid/TP40008194-CH6-SW1) 章节的部分内容。
由于专业术语的翻译可能会引起理解上的偏差，请以 iOS 官方开发文档为准。文档如有任何谬误不妥之处，欢迎指正。

借助远程通知服务，可以向用户即时推送最新的消息，且无需担心用户是否正在使用该应用。远程通知功能的大部分程序都运行在你公司的服务器上，应用方面也需要做如下配置：

* 打开应用的推送通知（Push Notification）功能；
* 在应用的启动代码中注册 APNs；
* 实现远程通知的处理代码；

关于远程通知在服务器方面的配置方法，请参考 [APNs 概述](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1)。

## 打开推送通知功能

要处理远程通知，应用必须先取得与 APNs 通讯的权限。在 Xcode 工程的 Capability 面板中添加这一功能权限。

**任务**

* 在工程导航中选择你的工程；
* 在编辑器的 TARGETS 中选择你的应用；
* 点击右侧的 Capability 标签页；
* 打开推送通知（Push Notification）功能权限；

按上面的做法，Xcode 就会把所需的权限加进你的工程中。

如果未添加相应的功能权限，应用会在应用商店审核阶段被驳回。可以测试下，如果没有相应的权限，注册 APNs 时会返回错误。



## 处理远程通知

User Notification 框架把所有涉及到本地和远程通知相关的操作代码都集中到了一个地方。

具体来说，可以用同样的方法处理下面的事情：

* 如果应用在前台运行，可以直接收通知，并静默该通知；
* 如果应用在后台，且未运行：
    * 可以在用户选择了一个与通知有关的自定义操作时做出响应；
    * 可以在用户忽略掉通知，或者登录了应用时做出响应；

除了借助 User Notification 框架提供的方法来处理通知以外，应用还可以通过它的 app delegate 接收整个远程通知的负载。当一个远程通知到达时，如果应用在后台，系统会正常处理用户的交互。系统也会把通知的负载发给 iOS 的 app delegate 的 `application:didReceiveRemoteNotification:fetchCompletionHandler:`方法。你可以用这个方法检测负载，并执行相应的操作。例如，收到一个静默的远程通知后，可以执行应用更新的下载。

有关如何使用 User Notifications 框架的方法处理通知，详见 [Responding to the Delivery of Notifications](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/SchedulingandHandlingLocalNotifications.html#//apple_ref/doc/uid/TP40008194-CH5-SW14)。

有关如何在 app delegate 里处理通知，详见[UIApplicationDelegate Protocol Reference](https://developer.apple.com/reference/uikit/uiapplicationdelegate) 或 [NSApplicationDelegate Protocol Reference](https://developer.apple.com/reference/appkit/nsapplicationdelegate)。


