##**云巴版你画我猜**
---

你画我猜是由 OMG POP 推出的一款多人社交游戏，在移动、Web 平台上都很受欢迎。
<br>游戏的规则是：在给定的时间内，一个玩家根据命题快速绘图（简称作图端），其他玩家实时观看其绘图，并根据绘图猜测命题（简称猜题端）。
<br><br>云巴版你画我猜 Demo 基于 Web 平台，无需安装任何程序，打开网页就能玩。Demo 重点演示的是如何利用云巴的网页实时通信服务实现浏览器之间的绘图数据的实时收发，
因此仅实现了作图端和猜题端的绘图交互部分。

![Web_draw_something](https://raw.githubusercontent.com/yunba/docs/master/image/for_demo/Web_draw_something.gif)



###**开发步骤**

####**绘图模块**
- 使用 HTML5 canvas 创建绘图程序；
- 集成 Yunba SDK；
- 创建 Yunba 实例；
- 初始化并连接到消息服务器；
- 作图端调用 publish API 实时地发布绘制路径（即一系列的鼠标坐标）到 Yunba 的云端；
- 猜题端调用 subscribe API 实时地从云端接收绘制路径，并原样展现；
- 调用 presence API 实时监测用户的在线状态。

###**代码示例**
下面分别列出作图端发送 (Publish) 绘图数据、猜题端接收 (Subscribe) 绘图数据，以及实时监测在线状态 (Presence) 的示例代码。

<table border="0">

<tr>
  <td colspan="2" align="center"><i><b>JavaScript</b></i></td>

</tr>

<tr>
  <td align="center"><b>作图端</b></i></td>
      <td align="center"><b>猜题端</b></i></td>

</tr>

<tr>
  <td align="center"><b>Publish</b></td>
    <td align="center"><b>Subscribe</b></td>

</tr>


<tr>  
<td>
<code>yunba.publish({
</code><br>
<code>&nbsp&nbsp&nbsp&nbsp'topic’: 'draw_room_047', 
</code><br>
<code>&nbsp&nbsp&nbsp&nbsp'msg': msg, //coordinates and tools info
</code><br>
<code>&nbsp&nbsp&nbsp&nbsp'messageId': 199900724, 
</code><br>
<code>&nbsp&nbsp&nbsp&nbsp'qos': 1
</code><br>
<code>&nbsp&nbsp&nbsp&nbsp}, 
</code><br>
<code>&nbsp&nbsp&nbsp&nbspcallback
</code><br>
<code>);
</code>
</td>
  
  
<td>
<code>yunba.subscribe({
</code><br>
<code>
&nbsp&nbsp&nbsp&nbsp'topic': 'draw_room_047'
</code><br>
<code>
&nbsp&nbsp&nbsp&nbsp}, 
</code><br>
<code>
&nbsp&nbsp&nbsp&nbspfunction (success, msg) {
</code><br>
<code>&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbspif (!success) {//draw on the canvas}
</code><br>
<code>&nbsp&nbsp&nbsp&nbsp&nbsp}
</code><br>
<code>
);
</code>
</td>
  
</tr>

<tr>
  <td colspan="2" align="center"><b>Presence</b></td>
</tr>


<tr>  
  <td colspan="2">
    <code> yunba.subscribe_presence({'topic': 'draw_room_047'}, callback);
    </code>
  </td>
</tr>
</table>


更多内容，详见 [Socket.io API 手册](http://yunba.io/docs2/socket.io_API/ "Socket.io API 手册")
 及 [Javascript SDK 使用文档](http://yunba.io/docs2/Javascript_SDK/ "Javascript SDK 使用文档")。
