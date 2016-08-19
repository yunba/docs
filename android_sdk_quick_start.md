# Yunba Android SDK 快速入门
## 注册开发者账号

打开 [云巴官方网站](https://yunba.io)，点击注册创建账号。


![create_accout.png](https://raw.githubusercontent.com/yunba/docs/master/image/productpng_portal_register_account.png)


## 创建应用

注册账号成功跳转到我的应用界面，点击“创建应用”，输入应用名称和包名（包名为 Java 标准包名规范）。


![productpng_portal_creat_application.png](https://raw.githubusercontent.com/yunba/docs/master/image/productpng_portal_creat_app.png)


## 下载 Android SDK

打开 [云巴开发者页面](https://yunba.io/developers/) 下载 Android SDK， Android SDK 包含 DEMO 程序和开发者所需嵌入的 jar 包。


## 导入 Android SDK

下载的 yunba-sdk-release.jar 包放到项目的 libs 目录下。


![androidpng_sdk_include_lib.png](https://raw.githubusercontent.com/yunba/docs/master/image/androidpng_sdk_include_lib.png)


开发工具为 Android Studio 的 Android SDK 导入步骤可参考 [Demo 运行文档](android_demo_quick_start.md)。


## 配置 AndroidManifest.xml
### 添加权限

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


### 配置应用的 AppKey（AppKey 来自 Portal，与包名对应）
[AppKey](product_kb_app_key.md) 来自 YunBa 注册的应用，与包名对应。


![appkey-pkg.png](https://raw.githubusercontent.com/yunba/docs/master/image/productpng_portal_copy_app_key.png)


**添加 Appkey**

```xml

<meta-data android:name="YUNBA_APPKEY" android:value="Your Appkey" />

```


### 修改应用包名称

在 AndroidManifest.xml 中根据 YunBa Portal 注册应用的包名替换 “Your PackageName”， 友情提示：一共有两处需要修改。


### 添加 Service

添加 YunBaService，YunBa SDK 会启动一个后台的 service。


**添加 YunBaService**

```xml

<service android:name="io.yunba.android.core.YunBaService"> </service>
```


### 添加 Receiver

添加 YunBaReceiver， 用来监听网络变化等事件，确保网络切换时能重新建立长连接。

```xml

<receiver android:name="io.yunba.android.core.YunBaReceiver">
    <intent-filter>
        <action android:name="android.intent.action.USER_PRESENT" />
        <action android:name="android.net.conn.CONNECTIVITY_CHANGE" />
    </intent-filter>
</receiver>
```


## 添加使用代码

初始化 SDK 并订阅 [Topic](product_kb_topic_and_alias.md)，请在您的 Application 子类的 OnCreate 方法中加入如下代码：

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


## 自定义 Receiver 接收 Publish 消息

YunBa 系统 Publish 的消息会通过广播的形式传递给 App， App 通过监听相关的 Action 接收消息并处理。


### 自定义 Receiver 在 AndroidManifest.xml 的配置

自定义 Receiver 接收 Publish 消息， Package Name 为当前应用程序的包名。

```xml

	<receiver android:name="Your Receiver">
		<intent-filter>
		<action android:name="io.yunba.android.MESSAGE_RECEIVED_ACTION" />
		<category android:name="Package Name" />
		</intent-filter>
	</receiver>
```


### 自定义 Receiver 处理 Publish 消息代码示例

自定义 Receiver 处理 Publish 消息

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


## 重新编译文件

在 Eclipse 中重新编译项目生成新的 R 文件，在MainActivity，DemoUtil，APIActivity，YunBaTabActivity 重新导入 R 文件。


![此处输入图片的描述](https://github.com/yunba/docs/blob/master/image/androidpng_sdk_genr.png?raw=true)


![此处输入图片的描述](https://raw.githubusercontent.com/yunba/docs/master/image/androidpng_sdk_rfi.png)


## 运行程序

运行 yunba-demo 程序（Run as Android application）， 如果 yunba-demo 程序出现 Connected 的日志表示连接成功。


可能遇到的问题：
运行程序的过程中可能会出现 **requires API level 10 (current min is 8)** 的问题。只需要修改 AndroidManifest.xml 中的标签 `<uses-sdk>` 如下：

```
<uses-sdk
        android:minSdkVersion="10"
        android:targetSdkVersion="14" />
  ```


### 程序运行主界面


![此处输入图片的描述](https://raw.githubusercontent.com/yunba/docs/master/image/androidpng_sdk_main_window.png)


### API 接口界面展示


![此处输入图片的描述](https://raw.githubusercontent.com/yunba/docs/master/image/androidpng_demo_api.png)


## 在 Portal 上发布消息

客户端集成 YunBa SDK 后，打开 Portal 上的应用详情页面，输入频道名称和消息内容，点击 “发送”，订阅该频道的客户端即可收到消息，如图所示:


![productpng_portal_publish_to_topic.png](https://raw.githubusercontent.com/yunba/docs/master/image/productpng_portal_publish_to_topic.png)


## 在 Portal 查看消息发布实时报表

打开应用详情页面，点击 “发布上报统计” 可以查看消息发布 **实时送达比**，如图所示:


![report.png](https://raw.githubusercontent.com/yunba/docs/master/image/productpng_portal_publish_statistic.png)


## 在 Portal 查看用户在线信息实时报表

打开应用详情页面，点击 “在线用户统计” 可以查看当前在线用户数，用户活跃数等信息，如图所示:


![online.png](https://raw.githubusercontent.com/yunba/docs/master/image/productpng_portal_online_statistic.png)


**注**：

Portal 的详细使用可参考 [Portal 文档](product_kb_portal.md)。


