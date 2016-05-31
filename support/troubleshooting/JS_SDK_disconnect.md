## 问题

###JS SDK 连接失败怎么办？

###官网后台的管理页面（Portal）显示连接断开了怎么办？

![js_portal_disconnect_1.png](https://raw.githubusercontent.com/yunba/docs/master/image/for_support/for_troubleshooting/js_portal_disconnect_1.png)
![js_portal_disconnect_2.png](https://raw.githubusercontent.com/yunba/docs/master/image/for_support/for_troubleshooting/js_portal_disconnect_2.png)

## 解决方案

以上两个问题均可能是网络或浏览器兼容性的问题。

首先，请 [Ping](https://en.wikipedia.org/wiki/Ping_(networking_utility)) 一下 sock.yunba.io，如果网络异常，请自行排查网络故障；

其次，如果网络正常，可能是浏览器兼容性的问题，可以换其他的浏览器试试（如，Chrome，Firefox）；

如果问题还没得到解决，可以打开浏览器的 Debug 窗口（如，Chrome 可以按 F12 打开 Debug 窗口），看看错误信息是什么，并反馈给我们。

例如，下图是出错时的 Debug 窗口截图，可以看出错误原因是：reg failed

![browser_debug.png](https://raw.githubusercontent.com/yunba/docs/master/image/for_support/for_troubleshooting/browser_debug.png)

最后，还有一种可能是您当前使用的 [Appkey](https://github.com/yunba/kb/blob/master/AppKey.md) 被我们的系统限制了，请将您的 Appkey 用私聊或邮件（support@yunba.io）的方式发给我们，我们会及时处理。
