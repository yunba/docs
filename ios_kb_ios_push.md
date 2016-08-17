## 云巴 iOS 消息推送

如下图所示，客户端集成了云巴的 iOS SDK，
服务端通过云巴的 SDK 向 iOS 设备发消息。
<br>
一方面，云巴的服务器会负责向苹果的服务器发送 APNs 的消息；
另一方面，当应用在前台运行时，云巴会通过与 App 建立的长连接，直接推送内部消息。

![iospng_kb_push_message.png](https://raw.githubusercontent.com/yunba/docs/master/image/iospng_kb_push_message.png)


通过云巴的 SDK 向 iOS App 推送消息，App 在不同状态下的消息接收情况如下：

<table border="0">

<tr>
  <td><b>App 状态</b></td>
  <td><b>一直 Online</b></td>
  <td><b>服务端推送了消息后，设备才 Online</b></td>
  <td><b>一直 Offline</b></td>
</tr>

<tr>
  <td>运行在前台</td>
  <td>(1) iOS 系统不会显示 APNs 消息通知，但 App 内会有 APNs 的回调；<br><br>
(2) 云巴服务器与 App 建立了长连接，App 会收到云巴服务器直接发来的消息。</td>

  <td rowspan="2">连网后，情况与左侧一列的描述基本一致。<br><br>唯一区别是：<br>设备不在线时，向 APNs 推送多条通知，在上线后只会收到最后一条。</td>

  <td rowspan="2">收不到消息。</td>
</tr>

<tr>
  <td>运行在后台</td>
  <td>(1) App 会收到并显示 APNs 消息通知；<br><br>
(2) 打开 App 时，如前所述，App 会连上云巴服务器，收到服务器直接发来的消息。</td>
</tr>
</table>

* 集成 APNs

云巴 SDK 集成了 APNs，开发者无需开发与 APNs 对接的模块，也不必自己负责 Device Token 的更新，一切交给云巴。

* 确保消息的送达

众所周知，APNs 并不保证消息的送达。
而云巴 SDK 支持 [离线消息](product_kb_offline_message.md) 的功能，可保证消息送达客户端。
<br>
在推送消息时，如果客户端当前不在线，消息将暂存在云巴服务器上（多达 50 条，长达 15 天）。
当客户端上线并成功连接到云巴的服务器后，服务器会把离线消息推送给该客户端。客户端成功接收后，服务器才会删除保存的离线消息。
<br>
