# 运行 Yunba JavaScript Demo

本文介绍如何运行 Yunba JavaScript SDK 中的 Demo 示例程序。

本文涉及的运行环境如下：

* Mac OS X 10.11.1
* YunBa JavaScript SDK 2.0.3

## 准备工作

###1. 下载云巴 JavaScript SDK
打开 [云巴开发者页面](https://yunba.io/downloads/)，下载最新版本的 JavaScript SDK。

###2. 注册云巴开发者账号
打开 [云巴官方网站](https://yunba.io)，点击右上角的“注册”按钮创建账号。  

## 详细步骤

###1. 在云巴 Portal 上创建新应用
请参考 [运行 Yunba Android Demo](android_demo_quick_start.md) 
一文中的该步骤的做法，获得一个 AppKey。

###2. 修改 AppKey
请打开需要用到的 yunba_javascript_demo_customid.html 文件，在开头处找到 AppKey 所在的位置，替换为步骤 1 中在 Portal 创建新应用时获取到的 AppKey。
```
    var yunba = new Yunba({appkey: 'xxxxxxxxxxxxxxxxx'});
```
###3. 运行 Demo

examples 目录下有两个html文件：yunba_javascript_demo.html 和 yunba_javascript_demo_customid.html，差别在于连接消息服务器时，使用的是“connect”，还是“connect_by_customid”。推荐使用后者，使用同样的会话ID进行连接，可确保登录信息与上次连接一致（包括离线消息、之前已订阅的频道和别名）。

双击打开 yunba_javascript_demo_customid.html 后，浏览器会先弹出一个消息框，提示输入自定义 ID。如下图所示，在界面右上方输入 ID，点击“Connect”即可。订阅“news”后，即可收到“news”频道下的消息。见下图标记处的第一行内容。

![jspng_demo_receive_message.png](https://raw.githubusercontent.com/yunba/docs/master/image/jspng_demo_receive_message.png)

可通过 Set Alias 设置自己的别名。例如，设置别名为“Jack”，见下图。设置成功后，即可收到发给该别名的消息。


图中靠下方的位置展示的是查询某个用户的订阅列表和在线状态，以及某个频道下所有的用户别名。其返回的结果可参考上图中标记处的后三行。


![jspng_demo_set_alias.png](https://raw.githubusercontent.com/yunba/docs/master/image/jspng_demo_set_alias.png)

此外，还可以通过 subscribe_presence 来监听收听了“news”的所有用户的别名状态的变化。见下图。
![jspng_demo_subscribe_presence.png](https://raw.githubusercontent.com/yunba/docs/master/image/jspng_demo_subscribe_presence.png)


发布消息的界面如下，可按频道发布，或按别名发布：

![jspng_demo_publish.png](https://raw.githubusercontent.com/yunba/docs/master/image/jspng_demo_publish.png)
