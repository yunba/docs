# 运行 Yunba Android Demo


本文介绍如何在 Android Studio 中运行 Yunba Android SDK 中的 Demo 示例程序。

本文涉及的运行环境如下：

* Mac OS X 10.10.5
* Android Studio 1.3.2
* Android SDK 4.0.3 (API 15)
* YunBa Android SDK 1.4.5

## 详细步骤

### 1. 在云巴 Portal 上创建新应用
打开云巴官方网站，注册并登录。

点击界面右上角“我的应用 --> 创建新应用”。

对于 Android 应用来说，只需填写应用名称、应用包名两项即可。

Yunba Android Demo 的应用包名（PackageName）为："io.yunba.example"。

创建完成后，即可得到对应的 AppKey。如图所示：

![android_portal_info.png](https://raw.githubusercontent.com/yunba/docs/master/image/for_quickstart/android_portal_info.png)

### 2. 修改 AndroidManifest.xml 文件
用文本编辑器打开 YunBa-Android-sdk 中的 AndroidManifest.xml 文件，进行如下修改：

将 line 3 和 line 78 "Your PackageName" 修改为："io.yunba.example"；

将 line 83 的 "Your AppKey" 修改为之前从 Portal 中获取到的 AppKey。

### 3. 引入 Android Demo 工程
云巴官网提供的 Android Demo 是 Eclipse 下的工程，
引入 Android Studio 时，需在其 Welcome 界面选择 Import project (Eclipse ADT, Gradle, etc.) 选项。

如图所示。

![android_import_prj.png](https://raw.githubusercontent.com/yunba/docs/master/image/for_quickstart/android_import_prj.png)

在弹出的文件树中，找到刚才做过修改的那份 YunBa-Android-sdk，展开后，
选择 yunba-demo 文件夹，点击 OK 确认，
并输入目标工程的路径 (Import Destination Directory)，点击 Next、Finish 即可。

![android_select_prj.png](https://raw.githubusercontent.com/yunba/docs/master/image/for_quickstart/android_select_prj.png)


引入后，如遇到错误提示："failed to find target with hash string android-15"，
则说明您没有安装 Android 4.0.3 (API 15) 的 SDK，打开 Tools --> Android --> SDK Manager，按需要进行安装即可。


在 Android Studio 中，可以查看 AndroidManifest 文件，再次确认 PackageName 和 AppKey 已经正确修改了：

![android_manifest_pkg.png](https://raw.githubusercontent.com/yunba/docs/master/image/for_quickstart/android_manifest_pkg.png)

![android_manifest_pkg_appkey.png](https://raw.githubusercontent.com/yunba/docs/master/image/for_quickstart/android_manifest_pkg_appkey.png)

**注**：之所以先修改 Package Name，再引入 Android Studio 进行编译，
是因为 Android Studio 不像 Eclipse 那样，只在 Manifest 里面定义 Package Name 等内容。
如果先引入再修改的话，还会涉及其他地方的修改。这里不再赘述。

### 4. 编译运行
Android Demo 中已经包含了相关的处理代码，您无需添加代码即可测试。
连接 Android 手机并设置开发者模式。在 Android Studio 中，把 Run Target 设为 USB device，然后 Rebuild 工程，Run 即可。

在 Android 手机端订阅“news”，然后在 Portal 上发送消息：

![tutorials_push_notification_iOS_publish.png](https://raw.githubusercontent.com/yunba/docs/master/image/for_tutorials/tutorials_push_notification_iOS_publish.png)


即可在 Android 手机端订阅收到消息：

![android_emu_appmsg.png](https://raw.githubusercontent.com/yunba/docs/master/image/for_quickstart/android_emu_appmsg.png)

![android_emu_pushmsg.png](https://raw.githubusercontent.com/yunba/docs/master/image/for_quickstart/android_emu_pushmsg.png)

