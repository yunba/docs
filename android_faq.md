# Android SDK 常见问题解答


<a name="1"></a>1. **当 App 退出，进程被杀死时，能接收到 Message 吗？**

答： Android 端需要在后台保留进程才能接收消息；iOS 端的 APNs 在 App 退出后仍可接收消息。

Android 端的解决方法：增加相互拉起功能和后台守护进程，使 App 退出后仍能接收到推送消息。可下载并参考 [特殊版本的 SDK](https://raw.githubusercontent.com/yunba/yunba-sdk-releases/master/Android/YunBa-Android-sdk-1.6.3.zip)（该版本仅供测试）。

对于 Android 5.0 以上版本的系统，请使用 Android 最新的 SDK，参考我们的[第三方集成指南](android_sdk_third_part_push.md)，集成小米、华为推送。

---
<a name="2"></a>2. **Android 端如何设置 qos 等级？**

答：`publish2()`、`publish2ToAlias()`的 opts(JSONObject) 参数可以设置 qos。

附：qos 为服务质量等级。有三种取值：0 表示最多送达一次；1 表示最少送达一次；2 表示保证送达且仅送达一次。默认为 1。详见 [QoS](product_kb_qos.md) 的说明。

---
<a name="3"></a>3. **Android 端怎么设置离线消息时间？**

答： 设置`publish2()`、`publish2ToAlias()`的 opts（JSONObject） 参数；
qos 设置为 1 或 2，就能够保证离线消息的送达；设置 time_to_live，可以控制离线消息在云巴服务器上保留的时间（以秒为单位）。详见：[云巴的离线消息](product_kb_offline_message.md) 。

---
<a name="4"></a>4. **YunBa Android SDK 有没有设置通知栏的 API？**

答： 关于设置消息通知栏，YunBa Android SDK 没提供相关的 API。设置方法可参考 [Android 官方文档](http://developer.android.com/guide/topics/ui/notifiers/notifications.html)。


---
<a name="5"></a>5. **怎么获取 Message 的 Message ID？**

答： YunBa Android SDK 暂时没有提供获取接收消息的 Message ID 的API。
如果需要 Message ID 等自定义内容，可以封装自定义内容到 Message 进行发送，在接收时进行解析。

---
<a name="6"></a>6. **Android 端如何断开连接，不接收消息？**

答： 可以调用[`stop()`](android_sdk_api_manual.md#stop)停止推送服务，使所有的 API 都失效（包括 start API）；当需要重新使用推送服务时，必须要调用[`resume ()`](android_sdk_api_manual.md#resume)。


<a name="7"></a>7. **云巴的第三方推送，小米可以和华为一样只有透传没有通知栏么？**

答： 我们目前的第三方推送主要目的是：在自己应用被杀死的情况下提供一种拉起应用的方式。基于这个目的：
- 小米官方要求用通知栏消息才可以接受消息（详见 [小米文档](http://dev.xiaomi.com/doc/p=7674/index.html) 问题 8）；
- 华为则是透传和通知栏就可以拉起应用；

<a name="8"></a>8. **我集成了云巴推送，需要设置混淆吗？**

答：Android SDK 已经混淆过了。

