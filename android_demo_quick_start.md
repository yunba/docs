# 运行 Yunba Android Demo


本文介绍如何在 Android Studio 中运行 Yunba Android SDK 中的 Demo 示例程序。

另，云巴 Android SDK 自 v1.8.0-beta 版本起，开始支持小米、华为推送，通过在这两类机型上的小米、华为推送进程来拉起云巴进程，从而实现应用被杀掉也收到推送的效果。本文将以小米推送为例，对这一功能作出演示。

本文涉及的运行环境如下：

* Mac OS X 10.11.6
* Android Studio 2.2
* Android SDK 4.0.3 (API 15)
* YunBa Android SDK 1.8.0-beta
* 小米手机：MI MAX - Android 6.0.1

## 详细步骤

### 1. 在云巴 Portal 上创建新应用

打开云巴官方网站，注册并登录。

点击界面右上角“创建应用”。

对于 Android 应用来说，只需填写应用名称、应用包名两项即可。

假设我们创建了一个应用包名（PackageName）为：io.yunba.test 的应用。

创建完成后，即可得到对应的 AppKey。如图所示：

![productpng_portal_info.png](https://raw.githubusercontent.com/yunba/docs/master/image/productpng_portal_info.png)

### 2. 引入 Android Demo 工程

云巴官网提供的 Android Demo 是 Eclipse 下的工程，引入 Android Studio 时，需在其 Welcome 界面选择 Import project (Eclipse ADT, Gradle, etc.) 选项。

如图所示。

![androidpng_demo_import_project.png](https://raw.githubusercontent.com/yunba/docs/master/image/androidpng_demo_import_project.png)

在弹出的文件树中，找到下载的 SDK 的 yunba-demo 文件夹，点击 OK 确认，并输入目标工程的路径 (Import Destination Directory)，点击 Next、Finish 即可成功导入。

![androidpng_demo_select_project.png](https://raw.githubusercontent.com/yunba/docs/master/image/androidpng_demo_select_project.png)


引入后，如遇到错误提示："failed to find target with hash string android-15"，
则说明您没有安装 Android 4.0.3 (API 15) 的 SDK，打开 Tools --> Android --> SDK Manager，按需要进行安装即可。


### 3. 替换包名

由于 Android SDK Demo 使用的应用包名是 io.yunba.example，而本文创建的是 io.yunba.test。因此，需要把工程中所有的 example 重命名为 test。注意，这里不可以仅仅使用文本查找和替换的方式，具体的重命名方法可以参考下文。（在修改过程中，如出现 sync now 的黄色横幅提示，请点击确认）

- 打开 app - manifests - AndroidManifest.xml，将该文件中的 example 字符批量重命名为 test（笔者的测试环境下，批量重命名的快捷键是 Fn + Shift + F6），并保存；

- 打开 Gradle Scripts 下的 build.gradle (Module.app)，将 `applicationId "io.yunba.example"` 改为 `applicationId "io.yunba.test"`；

- 右击 app - java - io.yunba.example，选择 Refactor，将 example 改为 test，点击 Do refactor 按钮确认修改；

- 打开菜单栏的 Edit - Find - Replace in Path，将其他出现 example 的地方替换为 test；

至此，包名修改完成

### 4. 替换 AppKey

使用第一步中从 Portal 获取到的 AppKey 替换 AndroidManifest.xml 中的 AppKey。

### 5. 小米推送相关内容

有关小米、华为推送集成的详细内容，可查看 [第三方推送集成指南](android_sdk_third_part_push.md)。

#### 5.1 创建小米应用

- 登录小米开发者账号，在 Portal 上新建应用，填写在本例中的应用包名：io.yunba.test；
- 进入小米开放平台下的消息推送模块；
- 点击创建应用 - 创建手机/平板应用；
- 填写应用名称、应用包名后，点击创建，创建成功后，可以查看应用的 AppID、AppKey、AppSecret；
- 点击同意条款后，应用会成功启用；

#### 5.2 修改 Demo 工程

在 Demo 的 YunBaApplication.java 文件中添加 `YunBaManager.setXMRegister(String appid,String appkey)`。
使用上一步中创建小米应用时获得的 AppID、AppKey 作为参数。添加后如下：

```java
YunBaManager.setThirdPartyEnable(getApplicationContext(), true);
YunBaManager.setXMRegister(<Your_Xiaomi_AppID>, <Your_Xiaomi_AppKey>);
YunBaManager.start(getApplicationContext());
```

### 6. 在云巴 Portal 填写第三方推送信息

登录云巴 Portal，进入应用列表，找到之前创建的应用，并点击其右侧的第三方推送选项。选择小米标签页后，在启用一栏选择“是”，并填上小米应用的包名（io.yunba.test）和 AppSecret 即可。

### 7. 编译运行

这里以小米 Max 手机为例，连接小米手机并打开开发者模式。在 Android Studio 中，点击 Run 按钮，选择小米设备即可。

成功运行后，在小米手机上选择允许安装，**并在 设置 - 授权管理 - 自启动管理 中将 YunBaDemo 的自启动打开**。

打开应用，订阅一个名为 news 的 Topic。订阅成功后，将该应用杀死。然后在云巴 Portal 上向 news 发送一条内容为 good news 的消息：

![productpng_portal_publish_to_topic.png](https://raw.githubusercontent.com/yunba/docs/master/image/productpng_portal_publish_to_topic.png)

发送后，可在小米手机端收到两条通知：

- 来自小米通知：标题为 yunba_push，内容为 good news；
- 来自云巴通道：标题为 news，内容为 good news；

![androidpng_demo_xiaomi_push.png](https://raw.githubusercontent.com/yunba/docs/master/image/androidpng_demo_xiaomi_push.png)

