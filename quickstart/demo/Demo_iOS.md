# 运行 Yunba iOS Demo

本文介绍如何运行 Yunba iOS SDK 中的 Demo 示例程序。
<br>
本文涉及的运行环境如下：

* Mac OS X 10.10.5
* Xcode 7.0.1
* YunBa iOS SDK 1.5.2

## 准备工作

###1. 生成 APNs 证书
请参考 [生成 APNs 证书的步骤](https://github.com/yunba/docs/blob/master/support/knowledge_base/create_APNs_certificate.md "生成 APNs 证书的步骤") 一文，先生成 APNs 证书。
<br>
###2. 下载云巴 iOS SDK
打开 [云巴开发者页面](http://yunba.io/developers "云巴开发者页面")，下载最新版本的 iOS SDK。iOS SDK 包含 DEMO 程序和开发者所需嵌入的 lib 库以及头文件。
<br>
###3. 注册云巴开发者账号
打开 [云巴官方网站](http://yunba.io "云巴官方网站")，点击右上角的“注册”按钮创建账号。  


## 详细步骤

###1. 在云巴 Portal 上创建新应用
注册成功后，页面会跳转到“我的应用”界面，点击 我的应用 --> 创建新应用。

![create_app.png](https://raw.githubusercontent.com/yunba/docs/master/image/create_app.png)


输入应用的名称及 Bundle ID。同时，上传 iOS 开发或生产证书并输入相应的证书密码，以激活苹果推送通知服务。

![tutorials_push_notification_iOS_create_new_app.png](https://raw.githubusercontent.com/yunba/docs/master/image/for_tutorials/tutorials_push_notification_iOS_create_new_app.png)

应用信息填好以后，点击页面下方的“创建应用”按钮，会来到“应用详情”页面。这里可以看到从 Portal 申请到的 AppKey。 

![copy_app_key.png](https://raw.githubusercontent.com/yunba/docs/master/image/copy_app_key.png)


###2. 运行 Yunba iOS Demo 工程

####2.1 打开 YunBaDemo 工程

在 Xcode 中打开 Yunba iOS SDK 中的 Demo 工程“YunBaDemo”。

![ios_xcode.png](https://raw.githubusercontent.com/yunba/docs/master/image/for_quickstart/ios_xcode.png)

####2.2 替换代码中的 AppKey

iOS SDK Demo 里已经包含了类似的处理代码，您无需添加代码，只需使用您从 Portal 获取到的 Appkey 替换代码中的 Appkey 即可。

####2.3 添加 Bundle ID

在 Xcode 中添加 Bundle ID，如图所示：

![tutorials_push_notification_iOS_BundleID.png](https://raw.githubusercontent.com/yunba/docs/master/image/for_tutorials/tutorials_push_notification_iOS_BundleID.png)

至此，编译运行即可。

**注**：iOS Demo 真机调试时可能遇到“不包含 Bitcode”的错误，请参考 [相关文章](https://github.com/yunba/docs/blob/master/support/troubleshooting/iOS_YunbaDemo_bitcode_error.md "相关文章") 解决。
<br>
**注**：云巴 SDK 1.5.2 以及 1.5.2 之前的版本在 iOS 9 上无法正常注册，请参考 [相关文章](https://github.com/yunba/docs/blob/master/support/troubleshooting/SDK_registration_problem_on_iOS9.md "相关文章") 解决。

###4. 发布消息

选择您在 Portal 上创建的应用，点击“发布消息”，即可向特定的 Topic 发消息，则您的 iOS App 会收到相应的消息。
<br>
在 Portal 上发送消息：

![tutorials_push_notification_iOS_publish.png](https://raw.githubusercontent.com/yunba/docs/master/image/for_tutorials/tutorials_push_notification_iOS_publish.png)

<br>
App 运行时会收到内部消息：

![tutorials_push_notification_iOS_recvmsg.png](https://raw.githubusercontent.com/yunba/docs/master/image/for_tutorials/tutorials_push_notification_iOS_recvmsg.png)

<br>
App 未运行时会收到推送通知：

![tutorials_push_notification_iOS_push.png](https://raw.githubusercontent.com/yunba/docs/master/image/for_tutorials/tutorials_push_notification_iOS_push.png)
