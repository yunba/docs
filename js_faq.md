# JavaScript SDK 常见问题解答（FAQ）


**问： 如何获取`publish2()`的 opts 设置参数？**

答： 可以把 opts 参数封装到 Message，在接收时进行解析。

---
**问： 如何接收离线消息？**

答： 用[`connect_by_customid()`](https://yunba.io/docs2/Javascript_SDK/#connect_by_customid
) 进行连接，连接后的会话状态与上次连接一致（包括离线消息、已订阅的频道和别名）。`connect()`仅用于测试，无法接收离线消息。

---
**问： 如何判断消息来自频道还是用户？**

答： 开发者可传递 Message ID，接收时通过 Message ID 进行判断。设置 Message ID 的方法可以参考 demo 的 [mqtt_publish2 方法](https://github.com/yunba/yunba-javascript-sdk/blob/master/examples/yunba_javascript_demo.html)。

---
**问： 用户订阅的 Topic 和设置的别名 Alias 保存在哪里？**

答： 保存在云巴服务端，与 CustomID 对应。

---
**问： JavaScript SDK 兼容哪些浏览器？**

答： 支持浏览器的版本如下:

   | IE  | Safari | Chrome  | Opera  | Firefox |
   |:-----:|:-----:|:-----:|:-----:|:-----:|
   | 7+  |  ✓   |  ✓  |  ✓   |  ✓ |

IE7 以下版本需 [配置](https://github.com/yunba/yunba-javascript-sdk) 即可。


---
**问： 如何批量订阅频道？**

答： 可以将订阅频道放到数组里，循环订阅。


---
**问：JS SDK 连接失败怎么办？**
**问：官网后台的管理页面（Portal）显示连接断开了怎么办？**

答：<br>

1. 请 [Ping](https://en.wikipedia.org/wiki/Ping_(networking_utility)) 一下 sock.yunba.io，如果网络异常，请自行排查网络故障；

2. 如果网络正常，可能是浏览器兼容性的问题，可以换其他的浏览器试试（如，Chrome，Firefox）；

3. 如果问题还没得到解决，可以打开浏览器的 Debug 窗口（如，Chrome 可以按 F12 打开 Debug 窗口），看看错误信息是什么，并反馈给我们。

例如，下图是出错时的 Debug 窗口截图，可以看出错误原因是：reg failed

![jspng_faq_browser_debug.png](https://raw.githubusercontent.com/yunba/docs/master/image/jspng_faq_browser_debug.png)

4. 最后，还有一种可能是您当前使用的 [AppKey](product_kb_app_key.md) 被我们的系统限制了，请将您的 Appkey 用私聊或邮件（support@yunba.io）的方式发给我们，我们会及时处理。


