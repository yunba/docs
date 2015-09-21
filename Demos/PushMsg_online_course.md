##**云巴课程提醒**
---
这是一个消息推送的 Demo。<br>
演示的是使用云巴的消息推送服务为网络课程 App 提供课程开课提醒的功能。<br>
用户通过移动设备上的网络课程 App 订阅在线课程，在每门课程开课前，
App 会提前十分钟向该课程的订阅用户发送通知，告知用户课程即将开始。推送逻辑如下：<br><br>

**Android 版**：支持后台保持长链接。<br>
- 正在运行 App 或正在后台运行 App 的联网客户，会收到一条推送的开课提醒；<br>
- 当前未联网的用户或者 App 完全退出的用户，会在下次联网运行 App 时收到推送提醒。<br>

**iOS 版**：集成了 [Apple Push Notification service (APNs)](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9
 "iOS APNs")，即苹果推送通知服务。<br>
- 对于当前联网的用户，会收到一条推送的开课提醒；<br>
- 对于当前未联网的用户，会在下次联网时收到推送提醒。<br>


![PushMsg_online_course_1](https://raw.githubusercontent.com/yunba/docs/master/image/for_demo/PushMsg_online_course_1.gif)

###**开发步骤**

####**客户端**

- 集成 Yunba SDK；
- 实现网络课程 App，包括课程订阅、在线课堂等的功能；
- 在用户选课时，调用 subscribe API 进行课程订阅。

####**服务端**
- 任意选择一个平台作为消息推送的服务端；
- 集成 Yunba SDK；
- 在课程即将开始前，调用 Publish API 向该门课程的订阅用户推送开课提醒。

具体步骤可参考[Android SDK 快速入门](http://yunba.io/docs2/Android_Quick_Start/ "Android SDK 快速入门")，
以及[iOS SDK 快速入门](http://yunba.io/docs2/iOS_Quick_Start/ "iOS SDK 快速入门")。

###**代码示例**
假设 Topic 名称为 psychology，是心理学的在线课程。<br>
下面列出开课提醒的 Publish 示例代码。

<table border="0">

<tr>
  <td align="center"><h4>课程提醒系统</h4></td>
</tr>
<tr>
  <td align="center"><h5>Publish</h5></td>
</tr>

<tr>
  <td align="center"><i><b>Javascript</b></i></td>
</tr>

<tr>
  <td>
    <code>yunba.publish2({</code>
    <br><code>&nbsp&nbsp&nbsp&nbsp'topic': 'psychology', </code>
    <br><code>&nbsp&nbsp&nbsp&nbsp'msg': 'course_info',</code>
    <br><code>&nbsp&nbsp&nbsp&nbsp'opts': {</code>
    <br><code>&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp'qos': 1,</code>
    <br><code>&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp'time_to_live': 36000,</code>
    <br><code>&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp'apn_json': {</code>
    <br><code>&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp'aps': {</code>
    <br><code>&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp'sound': 'bingbong.aiff', </code>
    <br><code>&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp'badge': 3, </code>
    <br><code>&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp'alert': 'Psychology class will begin in ten minutes!'</code>
    <br><code>&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp}</code>
    <br><code>&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp},</code>
    <br><code>&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp'messageId': '11833652203486491113'</code>
    <br><code>&nbsp&nbsp&nbsp&nbsp}</code>
<br><code>},  callback);</code>
  </td>
  

</table>
