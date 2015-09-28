##**云巴实时股票报价**
---
这是一个 Web Demo，使用 HTML5 和 JavaScript 实现。<br>
演示的是如何利用云巴的网页实时通信服务来开发实时股票报价的 Web 应用。


![Web_stock_quote_2](https://raw.githubusercontent.com/yunba/docs/master/image/for_demo/Web_stock_quote_2.gif)


###**开发步骤**

####**Server 端**
- 集成 Yunba SDK；
- 起多个 Server 进程，调用 publish API 实时发送多路股价数据到云巴的消息服务器；

####**Web 应用端**
- 集成 Yunba SDK；
- 创建 Yunba 实例；
- 初始化并连接到消息服务器；
- 调用 subscribe API 订阅不同的股票种类，实时地获取股票数据。

###**代码示例**
以苹果 (AAPL) 和谷歌 (GOOG) 的股票为例，下面列出应用端订阅 (Subscribe) 及取消订阅 (Unsubscribe) 示例代码。

<table border="0">

<tr>
  <td colspan="2" align="center"><b>Web 应用端</b></td>
</tr>
<tr>
  <td colspan="2" align="center"><i><b>Javascript</b></i></td>
</tr>
<tr>
  <td align="center"><h5>Subscribe</h5></td>
  <td align="center"><h5>Unsubscribe</h5></td>
</tr>

<tr>
  <td>
    <code>yunba.subscribe({</code>
    <br><code>&nbsp&nbsp&nbsp&nbsp'topic': 'AAPL', </code>
    <br><code>},  callback);</code>
    
    <br><br>
    
    <code>yunba.subscribe({</code>
    <br><code>&nbsp&nbsp&nbsp&nbsp'topic': 'GOOG', </code>
    <br><code>},  callback);</code>
    
  </td>
  
  <td>
    
    <code>yunba.unsubscribe({</code>
    <br><code>&nbsp&nbsp&nbsp&nbsp'topic': 'GOOG', </code>
    <br><code>},  callback);</code>
    
  </td>
</tr>
</table>


更多内容，详见 [Socket.io API 手册](http://yunba.io/docs2/socket.io_API/ "Socket.io API 手册")
 及 [Javascript SDK 使用文档](http://yunba.io/docs2/Javascript_SDK/ "Javascript SDK 使用文档")。
