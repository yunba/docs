##**消息推送开发指南之 Android 篇**
---
###**准备工作**

####1. 下载云巴 Android SDK
打开 [云巴开发者页面](http://yunba.io/developers "云巴开发者页面")，下载最新版本的 Android SDK。
Android SDK 包含 DEMO 程序和开发者所需嵌入的 jar 包。
####2. 注册云巴开发者账号
打开 [云巴官方网站](http://yunba.io "云巴官方网站")，点击右上角的“注册”按钮创建账号。  


###**详细步骤**

####1. 在云巴 Portal 上创建新应用
注册成功后，页面会跳转到“我的应用”界面，点击 我的应用 --> 创建新应用。输入应用名称和应用包名。

![create_app.png](https://raw.githubusercontent.com/yunba/docs/master/image/create_app.png)

注意，这里填写的应用包名需要与您的项目中的应用包名完全一致。创建完成后，即可得到对应的 AppKey。 如图所示：

![android_portal_info.png](https://raw.githubusercontent.com/yunba/docs/master/image/for_quickstart/android_portal_info.png)

####2. 导入 Android SDK

解压下载的 YunBa-Android-sdk.zip ，将 yunba-sdk-release.jar 包放到您的项目的 libs 目录下。

![libs_android.jpeg](https://raw.githubusercontent.com/yunba/docs/master/image/libs_android.jpeg)


####3. 修改 AndroidManifest.xml 文件

#####3.1 添加权限

```xml

<uses-permission android:name="android.permission.RECEIVE_USER_PRESENT" />
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.WAKE_LOCK" />
<uses-permission android:name="android.permission.READ_PHONE_STATE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.VIBRATE" />
<uses-permission android:name="android.permission.MOUNT_UNMOUNT_FILESYSTEMS" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.WRITE_SETTINGS"/>

<!-- 位置相关权限，不是必须添加 -->
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
```
#####3.2 配置 AppKey
使用之前在 Portal 上创建新应用得到的 AppKey 修改 AndroidManifest.xml 上相应的参数。

```xml

<meta-data android:name="YUNBA_APPKEY" android:value="Your Appkey" />

```
#####3.3 修改应用包名

确保在 AndroidManifest.xml  中的两处 “Your PackageName” 所填写的应用包名，
与之前在 Portal 上创建新应用时填写的应用包名完全一致。包名需遵守 Java 标准包名规范。

#####3.4 添加 Service
添加 YunBaService，则 YunBa SDK 会启动一个后台的 service。

```xml

<service android:name="io.yunba.android.core.YunBaService"> </service>
```

#####3.5 添加 Receiver
添加 YunBaReceiver，用来监听网络变化等事件，以确保网络切换时能重新建立长连接。

```xml

<receiver android:name="io.yunba.android.core.YunBaReceiver">
    <intent-filter>
        <action android:name="android.intent.action.USER_PRESENT" />
        <action android:name="android.net.conn.CONNECTIVITY_CHANGE" />
    </intent-filter>
</receiver>
```

####4. 添加使用代码
初始化 SDK 并订阅 Topic。请在您的 Application 子类的 OnCreate 方法中加入如下代码：

```java

public class YourApp extends Application {

    public void onCreate() {

        super.onCreate();
        YunBaManager.start(getApplicationContext());

        YunBaManager.subscribe(getApplicationContext(), new String[]{"t1"}, new IMqttActionListener() {

			@Override
			public void onSuccess(IMqttToken arg0) {
				Log.d(TAG, "Subscribe topic succeed");
			}

			@Override
			public void onFailure(IMqttToken arg0, Throwable arg1) {
				Log.d(TAG, "Subscribe topic failed" ;
			}
		});

    }
}
```

####5. 自定义 Receiver 接受 Publish 消息
Yunba 系统 Publish 的消息会通过广播的形式传递给 App，App 通过监听相关的 Action 接受消息并进行处理。

#####5.1 自定义 Receiver 在 AndroidManifest.xml 的配置

自定义 Receiver 接受 Publish 消息，Package Name 为当前应用程序的包名。

```xml

	<receiver android:name="Your Receiver">
		<intent-filter>
		<action android:name="io.yunba.android.MESSAGE_RECEIVED_ACTION" />
		<category android:name="Package Name" />
		</intent-filter>
	</receiver>
```

#####5.2 自定义 Receiver 处理 Publish 消息代码示例


```java

	if (YunBaManager.MESSAGE_RECEIVED_ACTION.equals(intent.getAction())) {

		String topic = intent.getStringExtra(YunBaManager.MQTT_TOPIC);
		String msg = intent.getStringExtra(YunBaManager.MQTT_MSG);

		//在这里处理从服务器发布下来的消息， 比如显示通知栏， 打开 Activity 等等
		StringBuilder showMsg = new StringBuilder();
		showMsg.append("Received message from server: ")
                .append(YunBaManager.MQTT_TOPIC)
                .append(" = ")
                .append(topic)
                .append(" ")
                .append(YunBaManager.MQTT_MSG)
                .append(" = ")\

                .append(msg);
		DemoUtil.showNotifation(context, topic, msg);
	}
```
####6. 编译运行
在 Eclipse 中重新编译项目生成新的 R 文件。
以 Yunba Demo 为例，运行程序（Run as Android application），如果程序显示 Connected 的日志表示连接成功。

**注**：运行程序的过程中可能会出现 requires API level 10 (current min is 8)的问题。
只需要修改 AndroidManifest.xml 中的标签 <uses-sdk> 即可。
```
<uses-sdk
        android:minSdkVersion="10"
        android:targetSdkVersion="14" />
```
        
        
####7. 在 Portal 上发布消息
  

在 Android App 端订阅“news”，然后在 Portal 上发送消息：

![tutorials_push_notification_iOS_publish.png](https://raw.githubusercontent.com/yunba/docs/master/image/for_tutorials/tutorials_push_notification_iOS_publish.png)

<br>
即可在 App 端订阅收到消息：

![android_emu_appmsg.png](https://raw.githubusercontent.com/yunba/docs/master/image/for_quickstart/android_emu_appmsg.png)

![android_emu_pushmsg.png](https://raw.githubusercontent.com/yunba/docs/master/image/for_quickstart/android_emu_pushmsg.png)

