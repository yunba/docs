# Yunba Android SDK 快速入门
## 注册开发者账号
打开 <http://yunba.io>, 点击注册创建账号。

![create_accout.jpg](../image/register_account.png)

## 创建应用
注册账号成功跳转到我的应用界面，点击我的应用 --> 创建新应用，输入应用名称和包名（包名为 Java 标准包名规范）

![create_application.jpg](../image/create_app.png)

## 下载 Android SDK

打开 <http://yunba.io/developers/> 下载 Android SDK， Android SDK 包含 DEMO 程序和开发者所需嵌入的 jar 包。

## 导入 Android SDK

下载的 yunba-sdk-release.jar 包放到项目的 libs 目录下。

![libs_android.jpeg](../image/libs_android.jpeg)

## 配置 AndroidManifest.xml
### 添加权限

> 添加权限

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

### 配置应用的 AppKey (AppKey 来自 Portal,与包名对应）
AppKey 来自 YunBa 注册的应用，与包名对应. 

![appkey-pkg.jpg](../image/copy_app_key.png)

> 添加 Appkey

```xml

<meta-data android:name="YUNBA_APPKEY" android:value="Your Appkey" />

```
### 添加 Service
添加 YunBaService ，YunBa SDK 会启动一个后台的 service.

> 添加 YunBaService

```xml

<service android:name="io.yunba.android.core.YunBaService"> </service>
```

### 添加 Receiver
添加 YunBaReceiver, 用来监听网络变化等事件，确保网络切换时能重新建立长连接.

> 添加 YunBaReceiver

```xml

<receiver android:name="io.yunba.android.core.YunBaReceiver">
    <intent-filter>
        <action android:name="android.intent.action.USER_PRESENT" />
        <action android:name="android.net.conn.CONNECTIVITY_CHANGE" />
    </intent-filter>
</receiver>
```

## 添加使用代码
初始化 SDK 并订阅 Topic，请在您的 Application 子类的 OnCreate 方法中加入如下代码：

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


## 自定义 Receiver 接受 Publish 消息
YunBa 系统 Publish 的消息会通过广播的形式传递给 App, App 通过监听相关的 Action 接受消息并处理。

### 自定义 Receiver 在 AndroidManifest.xml 的配置


 > 自定义 Receiver 接受 Publish 消息, Package Name 为当前应用程序的包名。
 
```xml
 
	<receiver android:name="Your Receiver">
		<intent-filter>
		<action android:name="io.yunba.android.MESSAGE_RECEIVED_ACTION" />
		<category android:name="Package Name" />
		</intent-filter>
	</receiver>
```

### 自定义 Receiver 处理 Publish 消息代码示例

> 自定义 Receiver 处理 Publish 消息

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
                .append(" = ")
                .append(msg);
		DemoUtil.showNotifation(context, topic, msg);
	}
```
## 在 Portal 上发布消息

客服端集成 YunBa SDK 后，打开 Portal 上应用详情页面，点击发布消息，客户端即可收到消息，如图所示:

![publish.png](../image/send_message.png)

## 在 Portal 查看消息发布实时报表

打开应用详情页面，点击发布上报统计可以查看消息发布实时送达比，如图所示:

![report.jpeg](../image/publish_statistic.png)

## 在 Portal 查看用户在线信息实时报表

打开应用详情页面，点击在线用户统计可以查看当前在线用户数，用户活跃数等信息，如图所示:

![online.jpeg](../image/online_statistic.png)
