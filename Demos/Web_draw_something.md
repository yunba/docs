##**云巴版你画我猜**
---

你画我猜是由 OMG POP 推出的一款多人社交游戏，在移动、Web 平台上都很受欢迎。
<br>游戏的规则是：在给定的时间内，一个玩家根据命题快速绘图（简称作图端），其他玩家实时观看其绘图，并根据绘图猜测命题（简称猜题端）。
<br><br>云巴版你画我猜 Demo 基于 Web 平台，无需安装任何程序，打开网页就能玩。Demo 重点演示的是如何利用云巴的网页实时通信服务实现浏览器之间的绘图数据的实时收发，
因此仅实现了作图端和猜题端的绘图交互部分。

![Web_draw_something](https://raw.githubusercontent.com/yunba/docs/master/image/for_demo/Web_draw_something.gif)



###**开发步骤**

####**绘图模块**
- 使用 html5 canvas 创建绘图程序；
- 集成 Yunba SDK；
- 创建 Yunba 实例；
- 初始化并连接到消息服务器；
- 作图端调用 publish API 实时地发布绘制路径（即一系列的鼠标坐标）到 Yunba 的云端；
- 猜题端调用 subscribe API 实时地从云端接收绘制路径，并原样展现；
- 调用 presense API 实时监测用户的在线状态。


更多内容，详见[Socket.io API 手册](http://yunba.io/docs2/socket.io_API/ "Socket.io API 手册")
及[Javascript SDK 使用文档](http://yunba.io/docs2/Javascript_SDK/ "Javascript SDK 使用文档")。
