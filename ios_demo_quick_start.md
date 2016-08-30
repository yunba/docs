# 运行 Yunba iOS Demo

本文介绍如何运行 Yunba iOS SDK 中的 Demo 示例程序。
本文涉及的运行环境如下：

* Mac OS X 10.11.1
* Xcode 7.0.1
* YunBa iOS SDK 1.6.0

## 准备工作

###1. 注册云巴开发者账号
打开 [云巴官方网站](https://yunba.io)，点击右上角的“注册”按钮创建账号。  

###2. 下载云巴 iOS SDK
打开 [云巴开发者页面](https://yunba.io/developers)，下载最新版本的 iOS SDK。iOS SDK 包含 Demo 程序和开发者所需嵌入的 lib 库以及头文件。

## 详细步骤

###1. 生成 APNs 证书
iOS Demo 程序会用到 APNs，因此，在运行之前，请先参考 [生成 APNs 证书的步骤](ios_kb_create_apns_certificate.md) 一文，生成 APNs 证书。

###2. 在云巴 Portal 上创建新应用

打开 [云巴官方网站](https://yunba.io)，登录，点击 “创建应用”。

![productpng_portal_creat_app.png](https://raw.githubusercontent.com/yunba/docs/master/image/productpng_portal_creat_app.png)

输入应用的名称及 Bundle ID。

![productpng_portal_creat_new_app.png](https://raw.githubusercontent.com/yunba/docs/master/image/productpng_portal_creat_new_app.png)

同时，上传上一个步骤中生成的 iOS 开发或生产证书，并输入导出证书时所设置的证书密码（如证书导出时未设置密码，则留空即可）。如果您的同一个应用分为不同的版本，您也可以添加 “[多个证书](ios_kb_multiple_certificates.md)”。

![ios_create_new_app.png](https://raw.githubusercontent.com/yunba/docs/master/image/iospng_portal_add_certificate.png)

应用信息填好以后，点击页面下方的“创建应用”按钮，会来到“应用详情”页面。这里可以看到从 [Portal](product_kb_portal.md) 申请到的 [AppKey](product_kb_app_key.md)、Secret Key 等。

**注**：请妥善保管好您的 AppKey、Secret Key 等，不要泄露给他人。

![productpng_portal_copy_app_key.png](https://raw.githubusercontent.com/yunba/docs/master/image/productpng_portal_copy_app_key.png)

###3. 生成 Provisioning Profile
请参考 [生成 Provisioning Profile](ios_kb_create_provisioning_profile.md) 一文，生成 Provisioning Profile，并导入到 Xcode 中。

###4. 运行 Yunba iOS Demo 工程

**4.1. 打开 YunBaDemo 工程** 

在 Xcode 中打开 Yunba iOS SDK 中的 Demo 工程“YunBaDemo”。

![iospng_demo_file_list.png](https://raw.githubusercontent.com/yunba/docs/master/image/iospng_demo_file_list.png)

**4.2. 替换代码中的 AppKey**

iOS SDK Demo 的代码已经实现了订阅、接收等逻辑，您无需添加任何代码，只需使用您从 Portal 获取到的 Appkey 替换代码中的 Appkey 即可。
```iOS
[YunBaService setupWithAppkey:AppKey];  // 请替换此处的 AppKey。
```

**4.3. 配置 Bundle ID**

在 Xcode 的 General --> Identity 页面，修改 Bundle Identifier 的值，如图所示：

![iospng_demo_bundle_identity.png](https://raw.githubusercontent.com/yunba/docs/master/image/iospng_demo_bundle_identity.png)

**4.4. 配置 Code Signing**

在 Xcode 的 Build Setting --> Code Signing 页面，Code Signing Identity 选择 Automatic，Provisioning Profile 选择之前在步骤 3 中双击导入的 Provisioning Profile。

![iospng_demo_code_sign.png](https://raw.githubusercontent.com/yunba/docs/master/image/iospng_demo_code_sign.png)

**4.5. 编译**

至此，编译运行即可。

**4.6. 运行**

App 在真机上运行起来后，可以通过左右拨动，切换到不同的功能展示页面。

**其中，在 Demo App 内，向右拨动至最后一页，打开 Enable APNs 的开关，才能收到 APNs 的消息。**

![iospng_demo_enable_apns.png](https://raw.githubusercontent.com/yunba/docs/master/image/iospng_demo_enable_apns.png)


###5. 订阅和发布

在 App 的首页，最上方如果显示 “Connected”，则表示连上了云巴服务器。

此时，填写 Topic 的名称，点击 Subscribe 按钮，即可成功订阅。

在官网的 Portal 上，选择对应的应用，点击“发布消息”，即可向特定的 Topic 发消息，您的 iOS App 会收到相应的消息。

在 Portal 上发送消息：

![productpng_portal_publish_to_topic.png](https://raw.githubusercontent.com/yunba/docs/master/image/productpng_portal_publish_to_topic.png)


App 在前台运行时会收到内部消息：

![iospng_demo_new_message.png](https://raw.githubusercontent.com/yunba/docs/master/image/iospng_demo_new_message.png)


App 在后台运行时会收到推送通知：

![iospng_demo_bar_message.png](https://raw.githubusercontent.com/yunba/docs/master/image/iospng_demo_bar_message.png)
