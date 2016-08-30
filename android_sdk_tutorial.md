## Android SDK 使用指南
本文介绍了 YunBa Android SDK 的使用和故障排除。
### 嵌入 Yunba Android SDK
如果未下载 Android SDK，请先阅读 [Yunba Android SDK 快速入门](android_sdk_quick_start.md) ，嵌入 Android SDK。确保完成了 AndroidManifest.xml 的添加权限、配置 AppKey、修改应用包名、添加 Service 和添加 Receiver 几个步骤。


### 初始化 SDK
进行订阅等操作之前，请先初始化 SDK，在您的 Application 子类的 OnCreate 方法中加入如下代码：
```java

	public class YunBaApplication extends Application {
	
	    public void onCreate() {
	        super.onCreate();
	        YunBaManager.start(getApplicationContext());
	    }
	}
```
YunBaManager.start API 详细说明参考 [Android SDK API 手册](android_sdk_api_manual.md#start) 。



### 如何群聊（广播）
**订阅话题 [`subscribe()`](android_sdk_api_manual.md#subscribe)**

> Code Example

```java

	YunBaManager.subscribe(getApplicationContext(),topic,
	   new IMqttActionListener() {
	        @Override
	        public void onSuccess(IMqttToken asyncActionToken) {
	          DemoUtil.showToast( "Subscribe succeed : " + topic, getApplicationContext());
	        }
	
	        @Override
	        public void onFailure(IMqttToken asyncActionToken, Throwable exception) {
	           if (exception instanceof MqttException) {
	               MqttException ex = (MqttException)exception;
	                String msg =  "Subscribe failed with error code : " + ex.getReasonCode();
	                DemoUtil.showToast(msg, getApplicationContext());
	           }
	        }
	   }
	);
```

**发布消息 [`publish()`](android_sdk_api_manual.md#publish)或 [`publish2()`](android_sdk_api_manual.md#publish2)**

> 以 `publish2()` 为例

```java
	JSONObject opts = new JSONObject();
	JSONObject apn_json = new JSONObject();
	JSONObject aps = new JSONObject();
	aps.put("sound", "bingbong.aiff");
	aps.put("badge", 9);
	aps.put("alert", "msg from android中文");
	apn_json.put("aps", aps);
	opts.put("apn_json", apn_json);
		
	YunBaManager.publish2(getApplicationContext(), topic, msg, opts,
	    new IMqttActionListener() {
	        @Override
	        public void onSuccess(IMqttToken asyncActionToken) {
	            String msgLog = "Publish2 succeed : " + topic;
	            DemoUtil.showToast(msgLog, getApplicationContext());
	        }
	
	        @Override
	        public void onFailure(IMqttToken asyncActionToken, Throwable exception) {
	             if (exception instanceof MqttException) {
	               MqttException ex = (MqttException)exception;
	                String msg =  "publish2 failed with error code : " + ex.getReasonCode();
	                DemoUtil.showToast(msg, getApplicationContext());
	           }
	        }
	    }
	);
```

**自定义 Receiver 接收 Publish 消息**

在 AndroidManifest.xml 自定义 Receiver ，确保添加 `<action android:name="io.yunba.android.MESSAGE_RECEIVED_ACTION" />`；在主程序中进行接收消息广播的处理。具体参考 [Android SDK 快速入门](android_sdk_quick_start.md#自定义Receiver在AndroidManifest.xml的配置) 和 Android Demo 的示例。

>处理接收消息广播的示例

```java
     public class MessageReceiver extends BroadcastReceiver {
		@Override
		public void onReceive(Context context, Intent intent) {
		    Log.i(TAG, "Action - " + intent.getAction());
			if (YunBaManager.MESSAGE_RECEIVED_ACTION.equals(intent.getAction())) {
				
				String topic = intent.getStringExtra(YunBaManager.MQTT_TOPIC);
				String msg = intent.getStringExtra(YunBaManager.MQTT_MSG);
				StringBuilder showMsg = new StringBuilder();
				showMsg.append("[Message] ").append(YunBaManager.MQTT_TOPIC)
						.append(" = ").append(topic).append(" ,")
						.append(YunBaManager.MQTT_MSG).append(" = ").append(msg);
				setCostomMsg(showMsg.toString());
			}
		}
     }
```


**`publish()` 和 `publish2()` 的区别**

`publish2()` 的参数比 `publish()` 多了 opts (JSONObject) 参数，可用于封装 
[QoS](product_kb_qos.md) (服务质量)、[time_to_live](product_kb_offline_message.md) (离线消息保留时间)、[aps_json](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/TheNotificationPayload.html#//apple_ref/doc/uid/TP40008194-CH107-SW1) (设置 APNs 消息的通知方式) 。



**如何停止接收订阅消息**

调用 [`unsubscribe()`](android_sdk_api_manual.md#unsubscribe) ，传入 Topic 参数，将不再接收到该 Topic 下的消息。

### 如何实现单聊（点对点通讯）

**设置别名、发送**

先在发送方和接收方设置别名： [setAlias()](android_sdk_api_manual.md#setAlias) ；
再调用 [`publishToAlias()`](android_sdk_api_manual.md#publishToAlias) 或 [`publish2ToAlias()`](android_sdk_api_manual.md#publish2ToAlias) 发送消息到指定的 Alias。

同一 AppKey 下别名唯一存在；同一台设备，设置的新别名将替换旧别名。详见 [频道与别名](product_kb_topic_and_alias.md) 。

可通过调用 [`getAlias()`](android_sdk_api_manual.md#getAlias) 获取当前用户的别名。

**注：**
确保自定义了 [接收 Message 的Receiver](android_sdk_quick_start.md#自定义Receiver在AndroidManifest.xml的配置) ，才能接收到消息，可参考上文“自定义 Receiver 接收 Publish 消息”示例。


**如何获取当前用户(Alias)订阅的频道列表**

获取当前用户 (Alias) 所订阅的频道列表，可以调用 [`getTopicList()`](android_sdk_api_manual.md#getTopicList)，从回调函数获得。

**`publishToAlias()` 和 `publish2ToAlias()` 的区别**

` publish2ToAlias ()` 的参数比 ` publishToAlias ()` 多了 opts(JSONObject) 参数，可用于封装 
[QoS](product_kb_qos.md) (服务质量)、[time_to_live](product_kb_offline_message.md)(离线消息保留时间)、[aps_json](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/TheNotificationPayload.html#//apple_ref/doc/uid/TP40008194-CH107-SW1) (设置 APNs 消息的通知方式) 。

### 如何停止和恢复接收消息

可调用 [`stop()`](android_sdk_api_manual.md#stop) 停止接收消息，使所有的 API 都失效（包括 start API）；

```java
	YunBaManager.stop(getApplicationContext());
```
当需要重新使用服务时，必须要调用 [`resume()`](android_sdk_api_manual.md#resume) 。

```java

	YunBaManager.resume(getApplicationContext());
```
如需检查推送服务是否被停止了，可调用 `isStopped()` 。

>**注**：当调用了 `stop()` ，设备处于离线状态，只有调用 `resume()` 才能恢复服务；当恢复服务时，在保留时间 (time_to_live) 以内的离线消息可以送达。

### 获取消息的发送者
如果需要在接收消息时显示消息发送者的用户名 Alias，需要在发送时把 Msg 和 Alias 封装到 Message 进行发送；在接收消息广播的 `onReceive()` 进行解析。

>**注**： 如果有其它平台的客户端，建议一并进行 Message 的封装。

> 发送消息代码示例：

```java

	JSONObject js = new JSONObject();
	try {
		js.put("user_name", userName);
		js.put("msg", msg);
	} catch (JSONException e) {
	}
	YunBaManager.publishToAlias(getApplicationContext(),userName,  js.toString(), 
	    new IMqttActionListener() {
		public void onSuccess(IMqttToken asyncActionToken) {
			
		}
			
		@Override
		public void onFailure(IMqttToken asyncActionToken, Throwable exception) {
	
		}
	    } 
	);
```
> 接收消息代码示例：

```java
	public class MessageReceiver extends BroadcastReceiver {	
		@Override
	    public void onReceive(Context context, Intent intent) {
		if (YunBaManager.MESSAGE_RECEIVED_ACTION.equals(intent.getAction())) {
	
	            String topic = intent.getStringExtra(YunBaManager.MQTT_TOPIC);
	            String msgJson = intent.getStringExtra(YunBaManager.MQTT_MSG);
	            try {
	                JSONObject js = new JSONObject(msgJson);
	                String message = js.optString("msg");
	                String user_name = js.optString("user_name");
	    			showMsg.append(user_name+" said, ").append(message);
	            }
	        }
	    }
	}
```

### 如何发送离线消息
发送消息使用 [`publish2()`](android_sdk_api_manual.md#publish2) 或 [`publish2ToAlias()`](android_sdk_api_manual.md#publish2ToAlias) 进行发送，需设置 opts（JSONObject） 封装的参数：
qos 设置为 1 或 2，保证离线消息的送达，默认为 1；设置 time_to_live，控制离线消息在云巴服务器上保留的时间（以秒为单位）。详见： [云巴的离线消息](product_kb_offline_message.md) 。

> Code Example

```java

	JSONObject opts=new JSONObject();
	try {
		opts.put("qos", 1);
		opts.put("time_to_live", 2*24*3600); //保存两天
	} catch (JSONException e) {
		e.printStackTrace();
	}
		
	YunBaManager.publish2(getApplicationContext(), topic, msg, opts, 
	   new IMqttActionListener() {
		public void onSuccess(IMqttToken asyncActionToken) {

			String msgLog = "Publish succeed : " + topic;
			StringBuilder showMsg = new StringBuilder();
			showMsg.append("[Demo] publish msg")
					.append(" = ").append(msg).append(" to ")
					.append(YunBaManager.MQTT_TOPIC).append(" = ").append(topic).append(" succeed");
			setCostomMsg(showMsg.toString());
			DemoUtil.showToast(msgLog, getApplicationContext());
		}
			
		@Override
		public void onFailure(IMqttToken asyncActionToken, Throwable exception) {
			String msg = "[Demo] Publish topic = " + topic + " failed : " + exception.getMessage();
			setCostomMsg(msg);
			DemoUtil.showToast(msg, getApplicationContext());
				
		}
	   }
	);
```


### 如何发送 APNs 消息

使用 [`publish2()`](android_sdk_api_manual.md#publish2) 或 [`publish2ToAlias()`](android_sdk_api_manual.md#publish2ToAlias) 进行消息发送，必须设置 opts（JSONObject） 参数封装的 apn_json 参数，该参数的作用和设置方法详见 [iOS 官方文档](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/TheNotificationPayload.html#//apple_ref/doc/uid/TP40008194-CH107-SW1) 。




> Code Example

```java

	JSONObject opts = new JSONObject();
	JSONObject apn_json = new JSONObject();
	JSONObject aps = new JSONObject();
	aps.put("sound", "bingbong.aiff");
	aps.put("badge", 9);
	aps.put("alert", "msg from android中文");
	apn_json.put("aps", aps);
	opts.put("apn_json", apn_json);
		
	YunBaManager.publish2(getApplicationContext(), topic, msg, opts,
	    new IMqttActionListener() {
	        @Override
	        public void onSuccess(IMqttToken asyncActionToken) {
	            String topic = DemoUtil.join(asyncActionToken.getTopics(), ", ");
	            String msgLog = "Publish2 succeed : " + topic;
	            DemoUtil.showToast(msgLog, getApplicationContext());
	        }
	
	        @Override
	        public void onFailure(IMqttToken asyncActionToken, Throwable exception) {
	             if (exception instanceof MqttException) {
	               MqttException ex = (MqttException)exception;
	                String msg =  "publish2 failed with error code : " + ex.getReasonCode();
	                DemoUtil.showToast(msg, getApplicationContext());
	           }
	        }
	    }
	);
```

**注：** iOS 端需 [注册 APNs 证书](ios_sdk_quick_start.md#在Portal上传APNs证书以激活APN推送功能)。APNs(Apple Push Notification Service) 消息的意义可参考 [iOS 官方文档](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9) 。


### 如何监听用户上下线

调用 [`subscribePresence()`](android_sdk_api_manual.md#subscribePresence) ，传入监听的 Topic，将会监听 Topic 下所有用户别名的上下线状态变化。任何用户的状态变化时都会对 App 发起一个 `<action android:name="io.yunba.android.PRESENCE_RECEIVED_ACTION" />` 的广播，需要在 AndroidManifest [自定义 Receiver](android_sdk_quick_start.md#自定义Receiver接受Publish消息) 接收该上下线广播，在主程序中进行广播的处理。

> Code Example

```java

	YunBaManager.subscribePresence(getApplicationContext(), "t1",
	    new IMqttActionListener() {
	        @Override
	        public void onSuccess(IMqttToken mqttToken) {
	            DemoUtil.showToast("subscribePresence to topic succeed", getApplicationContext());
	        }
	        @Override
	        public void onFailure(IMqttToken asyncActionToken, Throwable exception) {
	            if (exception instanceof MqttException) {
	               MqttException ex = (MqttException)exception;
	               String msg =  "subscribePresence failed with error code : " + ex.getReasonCode();
	               DemoUtil.showToast(msg, getApplicationContext());
	             }
	        }
	    }
	);
```


**取消用户上下线通知的监听**

调用 [`unsubscribePresence()`](android_sdk_api_manual.md#unsubscribePresence) ，传入取消监听的 Topic 即可。将取消监听该 Topic 下用户的状态变化，不再接收到`<action android:name="io.yunba.android.PRESENCE_RECEIVED_ACTION"/>` 的广播。

> Code Example


```java

	YunBaManager.unsubscribePresence(getApplicationContext(), "t1",
	    new IMqttActionListener() {
	        @Override
	        public void onSuccess(IMqttToken mqttToken) {
	            String msg = "unsubscribePresence to topic succeed ";
	            DemoUtil.showToast(msg, getApplicationContext());
	        }
	
	
	        @Override
	        public void onFailure(IMqttToken asyncActionToken, Throwable exception) {
	              if (exception instanceof MqttException) {
	               MqttException ex = (MqttException)exception;
	               String msg =  "unsubscribePresence failed with error code : " + ex.getReasonCode();
	               DemoUtil.showToast(msg, getApplicationContext());
	             }
	        }
	    }
	);
```

### 如何获取订阅人数和用户在线状态

**获取订阅人数**

获取某 Topic下的所有用户，可调用 [`getAliasList()`](android_sdk_api_manual.md#getAliasList) ，传入 Topic，从回调函数获得。


> Code Example

```java

	YunBaManager.getAliasList(getApplicationContext(), "t1",
	    new IMqttActionListener() {
	        @Override
	        public void onSuccess(IMqttToken mqttToken) {
	            JSONObject result = mqttToken.getResult();
	                    try {
	                         JSONArray topics = result.getJSONArray("alias");
	                         System.out.println(topics.toString());
	                    } catch (JSONException e) {
	                        
	                    }
	        }
	
	
	        @Override
	        public void onFailure(IMqttToken asyncActionToken, Throwable exception) {
	             if (exception instanceof MqttException) {
	               MqttException ex = (MqttException)exception;
	               String msg =  "getAliasList failed with error code : " + ex.getReasonCode();
	               DemoUtil.showToast(msg, getApplicationContext());
	             }
	        }
	    }
	);
```

**获取某用户的在线状态**

获取某用户 Alias 的在线状态，可调用 [`getState()`](android_sdk_api_manual.md#getState) ，传入 Alias ，从回调函数获得。


> Code Example

```java

	YunBaManager.getState(getApplicationContext(), "a1",
	    new IMqttActionListener() {
	       @Override
	       public void onSuccess(IMqttToken mqttToken) {
	            JSONObject result = mqttToken.getResult();
	                    try {
	                        String status = result.getString("status");
	                        System.out.println("status = " + status);
	                    } catch (JSONException e) {
	                        
	                    }
	        }
	
	
	        @Override
	        public void onFailure(IMqttToken asyncActionToken, Throwable exception) {
	            if (exception instanceof MqttException) {
	               MqttException ex = (MqttException)exception;
	               String msg =  "getState failed with error code : " + ex.getReasonCode();
	               DemoUtil.showToast(msg, getApplicationContext());
	             }
	        }
	    }
	);
```



##故障排除
### 按照步骤操作但接收不到消息
1. 如果从未成功接收消息，一般为代码集成错误，请参考 [Yunba Android SDK 快速入门](android_sdk_quick_start.md)，检查集成步骤。
**提示：**确保完成了 AndroidManifest.xml 的添加权限、配置 AppKey、修改应用包名、添加 Service 、添加 Receiver 和初始化 SDK 几个步骤；检查自定义 Receiver （接收消息）部分的代码,可参考 YunBa Android Demo 的 AndroidManifest.xml 和 DemoReceiver.java。
2. 检查网络问题，在网络正常情况下为秒内延迟。网络不稳定可能导致客户端与云巴服务端的长连接断开。支持 2G，3G，4G，Wi-Fi 网络环境。
3. 检查是否有保留后台进程，详见下文的后台进程部分。

### 显示“等待来自服务器的响应时超时”
如果 `subscribe()` 和 `publish()` 等操作未成功过，显示“等待来自服务器的响应时超时”，则一般为代码集成错误；如果是近期的故障，请检查本地网络环境。

### 应用退出后收不到消息
在后台进程驻留的情况下，应用可以接收到消息。

当后台进程被系统杀死，则长连接断开，客户端收不到消息。解决方法：可以通过后台进程守护和进程相互拉起使 App 退出后仍能收到消息。请参考 [特殊版本的 SDK](https://raw.githubusercontent.com/yunba/yunba-sdk-releases/master/Android/YunBa-Android-sdk-1.6.3.zip)（该版本仅供测试）。
