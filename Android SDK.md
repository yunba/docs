# Yunba Android SDK 使用文档

## 导入 SDK
下载的 yunba-sdk-release.jar 包放到项目的 libs 目录下。

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

### 配置应用的 AppKey (AppKey 来自 Portal,与包名对应）
```xml
<meta-data android:name="YUNBA_APPKEY" android:value="XXXXXXXXXXXXXX" />
```
### 添加 Service
```xml
<service android:name="io.yunba.android.core.YunBaService"> </service>
```
### 添加 Receiver
```xml
<receiver android:name="io.yunba.android.core.YunBaReceiver">
    <intent-filter>
        <action android:name="android.intent.action.USER_PRESENT" />
        <action android:name="android.net.conn.CONNECTIVITY_CHANGE" />
    </intent-filter>
</receiver>
```

## 添加使用代码
初始化 SDK。请在您的 Application 子类的 OnCreate 方法中加入如下代码
```Java
public class YourApp extends Application {

    public void onCreate() {

        super.onCreate();
        YunBaManager.start(getApplicationContext());

    }
}
```


## 自定义 Receiver 接受 Publish 消息

### 自定义 Receiver 在 AndroidManifest.xml 的配置

	<receiver android:name="Your Receiver">
		<intent-filter>
		<action android:name="io.yunba.android.MESSAGE_RECEIVED_ACTION" />
		<category android:name="Package Name" />
		</intent-filter>
	</receiver>

### 自定义 Receiver 处理 Publish 消息代码示例

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



## API - subscribe

### 功能
App 可以订阅一个或者多个 Topics, 以便可以接收来自 Topic 的 Message.

### 函数原型

	public static void subscribe(
	    Context context,
	    String[] topics,
        IMqttActionListener mqttAction
    );


### 参数说明
* context: Android 应用上下文环境。
* topics: app 订阅的的频道数组列表，topic 只支持英文数字下划线，长度不超过50个字符,数组的长度不超过100.
* mqttAction: API 回调接口， 成功会回调 onSuccess， 失败回调 onFailure.

### Code Example

	YunBaManager.subscribe(getApplicationContext(),topic,
	  new IMqttActionListener() {
        @Override
        public void onSuccess(IMqttToken asyncActionToken) {
          String topic = DemoUtil.join(asyncActionToken.getTopics(), ",");
          DemoUtil.showToast( "Subscribe succeed : " + topic,
            getApplicationContext());
        }

        @Override
        public void onFailure(IMqttToken asyncActionToken, Throwable exception) {
          String msg =  "Subscribe failed : " + exception.getMessage();
          DemoUtil.showToast(msg, getApplicationContext());
        }
      }
    );



## API - unsubscribe

### 功能
App 可以取消订阅一个或者多个 Topics, 以便取消接收来自 Topic 的 Message.

### 函数原型


	public static void unsubscribe(
	    Context context,
        String[] topics,
        IMqttActionListener mqttAction
    )


### 参数说明
* context: Android 应用上下文环境。
* topics:  app 订阅的的频道数组列表，topic 只支持英文数字下划线，长度不超过50个字符,数组的长度不超过100.
* mqttAction: API 回调接口， 成功会回调 onSuccess， 失败回调 onFailure.

### Code Example

```Java
YunBaManager.unsubscribe(getApplicationContext(), topic,
  new IMqttActionListener() {

    @Override
    public void onSuccess(IMqttToken asyncActionToken) {
      String topic = DemoUtil.join(asyncActionToken.getTopics(), ",");
      DemoUtil.showToast( "UnSubscribe succeed : " + topic,
        getApplicationContext());
    }

    @Override
    public void onFailure(IMqttToken asyncActionToken,Throwable exception) {
      String msg =  "UnSubscribe failed : " + exception.getMessage();
      DemoUtil.showToast(msg, getApplicationContext());
    }
  }
);
```

## API - Publish

### 功能
App 可以向 Topic 发送消息, 那么任何订阅此 Topic 的 Client 都会接受到消息。

### 函数原型

	public static void publish(
	    Context context,
	    String topic,
	    String message,
	    IMqttActionListener mqttAction
    );

### 参数说明
* context: Android 应用上下文环境。
* topic: app 待发布消息的频道，只支持英文数字下划线，长度不超过50个字符.
* message: 向对应 topic 的订阅者发布的消息.
* mqttAction: API 回调接口， 成功会回调 onSuccess， 失败回调 onFailure.

### Code Example
```Java
YunBaManager.publish(getApplicationContext(), topic, msg,
    new IMqttActionListener() {

        public void onSuccess(IMqttToken asyncActionToken) {
            String topic = DemoUtil.join(asyncActionToken.getTopics(), ", ");
            String msgLog = "Publish succeed : " + topic;
            DemoUtil.showToast(msgLog, getApplicationContext());
        }

        @Override
        public void onFailure(IMqttToken asyncActionToken, Throwable exception) {
            String msg = "Publish failed : " + exception.getMessage();
            DemoUtil.showToast(msg, getApplicationContext());
        }
    }
);
```


## API - stop
#### 功能
App 可以调用此函数来停止推送服务，当需要使用推送服务时，则必须要调用 resume API
### 函数原型
```Java
public static void stop(
	    Context context,
    );
```

### 参数说明
* context: Android 应用上下文环境。

### Code Example

```Java
YunBaManager.stop(getApplicationContext());
```


## API - resume
#### 功能
App 可以调用此函数来恢复推送服务，与 stop API 相对应。
### 函数原型
```Java
public static void resume(
	    Context context,
    );
```

### 参数说明
* context: Android 应用上下文环境。

### Code Example

```Java
YunBaManager.resume(getApplicationContext());
```


## API - isStopped
#### 功能
App 可以调用此函数来查看推送服务是否被停止。
### 函数原型
```Java
public static void isStopped(
	    Context context,
    );
```

### 参数说明
* context: Android 应用上下文环境。

### Code Example

```Java
YunBaManager.isStopped(getApplicationContext());
```


## API - Report
### 功能
App  可以调用此函数来上报客户端的行为，如打开通知栏次数，按钮点击次数，资源下载成功等等行为。

### 函数原型
```Java
public static void report(
	    Context context,
	    String actiton,
	    String data
    );
```

### 参数说明
* context: Android 应用上下文环境。
* action: app 需要统计的行为，如打开通知栏，下载资源成功等等。
* data: 想对应 action 的附加数据，以满足统计相关的其他业务需求。

### Code Example

```Java
YunBaManager.report(getApplicationContext(), "notifaction_opened", null,);
```
