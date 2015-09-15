##**云巴 Wi-Fi 灯泡**
---



这是一个物联网的 Demo。<br>
演示的是用户使用客户端程序（如移动设备上的 App），通过 Yunba 的服务来远程查看 Wi-Fi 灯泡的状态并实时调节其亮度。

###**开发步骤**

####**Wi-Fi 灯泡端**
- 集成 Yunba SDK；
- 实现灯泡亮度的控制逻辑；
- 配置灯泡的 Wi-Fi，使其接入到网络；
- 调用 subscribe API 订阅灯泡亮度的 Topic；
- 初始化灯泡的状态；

####**控制端**
- 集成 Yunba SDK；
- 连 Wi-Fi，接入到网络；
- 调用 subscribe API 订阅该 Topic；
- 通过 presence API 监听灯泡的在线状态；
- 调用 publish API 发布有关亮度的消息，从而实时控制灯泡的亮度。

假设 Topic 名称为 light_brightness，灯泡的亮度由灭到亮共有10个等级。<br>
下面列出将灯泡调为中等亮度 (Level 5) 代码示例，包括控制端的 Publish 代码以及灯泡端的 Subscribe 代码。

###**代码示例**

<table border="0">

<tr>
  <td align="center"><h4>控制端</h4></td>
  <td align="center"><h4>Wi-Fi 灯泡端</h4></td>
</tr>
<tr>
  <td align="center"><h5>Publish</h5></td>
  <td align="center"><h5>Subscribe</h5></td>
</tr>
<tr>
  <td colspan = "2" align="center"><i><b>Android</b></i></td>
</tr>

<tr>
  <td>
    <code>YunBaManager.publish(getApplicationContext(), "light_brightness", "level_5", callback);</code>
  </td>
  <td>
    <code>YunBaManager.subscribe(getApplicationContext(), "light_brightness", callback);</code>
  </td>
</tr>

<tr>
  <td colspan = "2" align="center"><i><b>iOS</b></i></td>
</tr>

<tr>
  <td>
    <code>[YunBaService publish:light_brightness data:@"level_5"];</code>
  </td>
  <td>
    <code>[YunBaService subscribe:light_brightness];</code>
  </td>
</tr>

<tr>
  <td colspan = "2" align="center"><i><b>C</b></i></td>
</tr>

<tr>
  <td>
    <code>char buf[100] = "level_5";</code><br>
    <code>int data_len = strlen(buf);</code><br>
    <code>rc = MQTTClient_publish(hClient, "light_brightness", data_len, buf);</code>
  </td>
  
  <td>
    <code>rc = MQTTClient_subscribe(hClient, "light_brightness");
    </code>
  </td>
</tr>


<tr>
  <td colspan = "2" align="center"><i><b>Javascript</b></i></td>
</tr>

<tr>
  <td>
    <code>yunba.publish({'topic': 'light_brightness', 'msg': 'level_5','messageId': 199900724, 'qos': 1}, callback);</code>
  </td>
  <td>
    <code>yunba.subscribe({'topic': 'light_brightness'}, callback);</code>
  </td>
  
  
<tr>
  <td colspan = "2" align="center"><i><b>Socket.io</b></i></td>
</tr>

<tr>
  <td>
    <code>socketIO.emit('publish', {'topic': 'light_brightness', 'msg': 'level_5', 'qos': 1})</code>
  </td>
  <td>
    <code>socketIO.emit('subscribe', {'topic': 'light_brightness'})</code>
  </td>

</table>


更多内容，详见[云巴开发文档](http://yunba.io/docs2 "云巴开发文档")。
