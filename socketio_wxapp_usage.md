# 在微信小程序中使用云巴实时通信系统

## 准备

云巴在微信小程序中的使用基于 socket io，有关 socket io 的接口详细介绍可以参考我们的[socket io 官方文档](https://yunba.io/docs/socketio_api_api_manual)。

由于微信原生 API 中只支持 WebSocket，没有提供 socket io 的接口。为方便起见，本 demo 使用了 [socket io client](https://github.com/wxsocketio/wxapp-socket-io) 的库来连接云巴。

## 使用

首先，参考该 socket io client 的[使用说明](https://github.com/wxsocketio/wxapp-socket-io)在项目中引入该库。

在本例中即在 app.js 中 import 并初始化一个全局的 io 对象以供调用：

```javascript
import io from './lib/wxsocket.io/index'
...
onLaunch: function () {
  this,globalData.io = io
}
```

然后，在需要连接到云巴的时候，首先连接到我们的 socket io 服务器：

```javascript
const socket = app.globalData.io("wss://abj-rest-fc1.yunba.io:3003/")
```

本例中即在 index.js 中使用全局的 io 对象连接到 `wss://abj-rest-fc1.yunba.io:3003/`，在监听到 `connect` 事件的时候，就说明连接上云巴的 socket io 服务器了。

```javascript
    socket.on('connect', function () {
      self.setData({
        connected: true
      })
```

此时就可以 emit 一个 `connect_v2` 事件，使用 appkey 和 customid 来连接云巴，在监听到 `connack` 事件的时候说明已经连接上云巴的实时通信系统，可以开始使用云巴的实时通信了。（***注：customid 即自定义的会话 id（session id），云巴会保留 session 信息，也就是下次再用此 customid 来连接的话，别名和订阅关系等信息均会保留。同时这也意味着同一时间只允许一个同名的 customid 在线，后上线的会挤掉前面的。***）

```javascript
app.globalData.socket.emit('connect_v2', { 'appkey': this.data.appkey, 'customid': this.data.customid })
```

```javascript
    socket.on('connack', function (msg) {
      if (msg['success'] == true) {
        app.globalData.Online = true
        wx.navigateTo({
          url: '../yunba/yunba'
        })
      }
    })
```

---
reference: [socket io 接口列表](https://yunba.io/docs/socketio_api_api_manual)