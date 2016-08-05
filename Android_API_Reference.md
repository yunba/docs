# YunBa Android SDK API 手册

如果未导入 YunBa Android SDK 请参考 [YunBa Android SDK 快速入门](http://yunba.io/docs2/Android_Quick_Start/)。


了解 Android SDK 的具体使用方法可参考 [YunBa Android SDK 使用指南](http://yunba.io/docs2/android_tutorial)。

## start

### 功能

App 初始化 YunBa SDK。

### 函数原型

`public static void start(Context context)`
  
`public static void start(Context context, String appkey)`
  
`public static void start(Context context, String appkey, Map opts)`


### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
context | Context | Android 应用上下文环境
appkey | String | YunBa 中注册的 [AppKey][3]，如果用户已经在 AndroidManifest.xml 定义了 YUNBA_APPKEY，此处的设置是无效的。
opts | Map | 选项，可包含 sub_key （用于获取订阅权限的密钥），pub_key （用于获取发布权限的密钥），sec_key （用于获取管理权限的密钥，切勿外泄），auth_key （用于 access manager 模块中权限管理的动态密钥）


### Code Example

```java
YunBaManager.start(getApplicationContext());
```


## subscribe

### 功能

App 可以`订阅`一个或者多个 [频道][5](Topic)，以接收来自频道的消息。


### 函数原型

`public static void subscribe(Context context, String topic, IMqttActionListener mqttAction)`

`public static void subscribe(Context context, String[] topics, IMqttActionListener mqttAction)`


### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
context | Context | Android 应用上下文环境
topic | String | App 订阅的 [频道][5]，topic 只支持英文数字下划线，长度不超过 50 个字符
topics | String[] | App 订阅的频道数组列表，topic 只支持英文数字下划线，长度不超过 50 个字符，数组的长度不超过 100
mqttAction | IMqttActionListener | 成功会回调 onSuccess，失败回调 onFailure


### Code Example

```java

YunBaManager.subscribe(getApplicationContext(),topic,
  new IMqttActionListener() {
    @Override
    public void onSuccess(IMqttToken asyncActionToken) {
      String topic = DemoUtil.join(asyncActionToken.getTopics(), ",");
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


## unsubscribe

### 功能

App 可以`取消订阅`一个或者多个 [频道][5](Topic)，以取消接收来自频道的消息。


### 函数原型

`public static void unsubscribe(Context context, String topic, IMqttActionListener mqttAction)`

`public static void unsubscribe(Context context, String[] topics, IMqttActionListener mqttAction)`


### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
context | Context | Android 应用上下文环境
topic | String | App 订阅的 [频道][5]，topic 只支持英文数字下划线，长度不超过 50 个字符，数组的长度不超过 100
topics | String[] | App 订阅的频道数组列表，topic 只支持英文数字下划线，长度不超过 50 个字符，数组的长度不超过 100
mqttAction | IMqttActionListener | 成功会回调 onSuccess，失败回调 onFailure


### Code Example

```java

YunBaManager.unsubscribe(getApplicationContext(), topic,
  new IMqttActionListener() {

    @Override
    public void onSuccess(IMqttToken asyncActionToken) {
      String topic = DemoUtil.join(asyncActionToken.getTopics(), ",");
      DemoUtil.showToast( "UnSubscribe succeed : " + topic, getApplicationContext());
    }

    @Override
    public void onFailure(IMqttToken asyncActionToken,Throwable exception) {
       if (exception instanceof MqttException) {
               MqttException ex = (MqttException)exception;
                String msg =  "unSubscribe failed with error code : " + ex.getReasonCode();
                DemoUtil.showToast(msg, getApplicationContext());
        }
    }
  }
);
```


## publish

### 功能

App 可以向 Topic 发送消息，那么同一应用（AppKey）下任何`subscribe`（订阅）此 Topic 的 Client 都会接收到消息。


**注**：需自定义 Receiver 接收 Publish 消息，可参考 [Android SDK 快速入门](http://yunba.io/docs2/Android_Quick_Start.md#%E8%87%AA%E5%AE%9A%E4%B9%89-receiver-%E6%8E%A5%E6%94%B6-publish-%E6%B6%88%E6%81%AF)。


### 函数原型


`public static void publish(Context context, String topic, String message,IMqttActionListener mqttAction)`


### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
context | Context | Android 应用上下文环境
topic | String | App 订阅的 [频道][5]，topic 只支持英文数字下划线，长度不超过 50 个字符，数组的长度不超过 100
message | String | 向目标 topic 的订阅者发布的消息
mqttAction | IMqttActionListener | 成功会回调 onSuccess，失败回调 onFailure


### Code Example

```java

YunBaManager.publish(getApplicationContext(), topic, msg,
    new IMqttActionListener() {
        @Override
        public void onSuccess(IMqttToken asyncActionToken) {
            String topic = DemoUtil.join(asyncActionToken.getTopics(), ", ");
            String msgLog = "Publish succeed : " + topic;
            DemoUtil.showToast(msgLog, getApplicationContext());
        }

        @Override
        public void onFailure(IMqttToken asyncActionToken, Throwable exception) {
             if (exception instanceof MqttException) {
               MqttException ex = (MqttException)exception;
                String msg =  "publish failed with error code : " + ex.getReasonCode();
                DemoUtil.showToast(msg, getApplicationContext());
           }
        }
    }
);
```


**注**：如果需要在接收消息时获取发送者 Alias，可在发送时将 Alias 封装到 Message。可参考 YunBa Android SDK 使用指南中的 [获取消息发送者](http://yunba.io/docs2/android_tutorial#%E8%8E%B7%E5%8F%96%E6%B6%88%E6%81%AF%E7%9A%84%E5%8F%91%E9%80%81%E8%80%85)。


## publish2

### 功能

App 可以向 Topic 发送消息，那么同一应用（AppKey）下任何`subscribe`（订阅）此 Topic 的 Client 都会接收到消息，此 API 可以带有其他参数，如 APN 选项等。


**注**：需自定义 Receiver 接收 Publish 消息，可参考  [Android SDK 快速入门](http://yunba.io/docs2/Android_Quick_Start.md#%E8%87%AA%E5%AE%9A%E4%B9%89-receiver-%E6%8E%A5%E6%94%B6-publish-%E6%B6%88%E6%81%AF)


### 函数原型

`public static void publish2(Context context, String topic, String message, JSONObject opts, IMqttActionListener mqttAction)`


### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
context | Context | Android 应用上下文环境
topic | String | App 订阅的 [频道][5]，topic 只支持英文数字下划线，长度不超过 50 个字符，数组的长度不超过 100
message | String | 向目标 topic 的订阅者发布的消息
opts | JSONObject | 向目标 topic 的订阅者发布的消息的选项：如消息有效时间，目标平台，APNs 等等
mqttAction | IMqttActionListener | 成功会回调 onSuccess，失败回调 onFailure


### Code Example

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


### 扩展参数说明

`publish2`扩展参数 opts 是可选项，如果不填写参数，`publish2`的行为与`publish`相似（除了 apn_json 参数）。

名称 | 类型 | 说明
--------- | ------- | -----------
qos | number | 如果不填，默认为 1，参数设置请参考 [QoS][11]
apn_json | dict | 如果不填，则不会发送 iOS 端的 APNs 消息；而`publish`会发送默认的 APNs 消息。APNs 具体可参考：[Apple 官方文档](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html)
time_to_live | number | [离线消息](http://yunba.io/docs2/yunba_offline_message) 保留时间值，单位是秒（例如 2 天 2\*24\*3600），当前默认值为 5 天


## publishToAlias

### 功能

向用户 [别名][6] 对象发送消息，用于实现点对点的消息发送。


**注**：需要先`setAlias`进行别名设置


### 函数原型

`	public static void publishToAlias(Context context, String alias, String message,IMqttActionListener mqttAction)`


### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
context | Context | Android 应用上下文环境
alias| String | 用户设置的别名信息，同一 AppKey 下唯一，只支持英文数字下划线，长度不超过 50 个字符
message | String | 向设置该目标别名的对象发布的消息
mqttAction | IMqttActionListener | 成功会回调 onSuccess，失败回调 onFailure


### Code Example

```java

YunBaManager.publishToAlias(getApplicationContext(), alias, msg,
    new IMqttActionListener() {
        @Override
        public void onSuccess(IMqttToken asyncActionToken) {
            String topic = DemoUtil.join(asyncActionToken.getTopics(), ", ");
            String msgLog = "publish to alias succeed : " + topic;
            DemoUtil.showToast(msgLog, getApplicationContext());
        }

        @Override
        public void onFailure(IMqttToken asyncActionToken, Throwable exception) {
             if (exception instanceof MqttException) {
               MqttException ex = (MqttException)exception;
               String msg =  "publishToAlias failed with error code : " + ex.getReasonCode();
               DemoUtil.showToast(msg, getApplicationContext());
             }
        }
    }
);
```


**注**：如果需要在接收消息时获取发送者 Alias，可在发送时将 Alias 封装到 Message。可参考 YunBa Android SDK 使用指南中的 [获取消息发送者]( http://yunba.io/docs2/android_tutorial#%E8%8E%B7%E5%8F%96%E6%B6%88%E6%81%AF%E7%9A%84%E5%8F%91%E9%80%81%E8%80%85)。


## publish2ToAlias

### 功能

向用户别名对象发送消息，用于实现点对点的消息发送，此 API 可以带有其他参数，如 APN 选项等。


**注**：需要先`setAlias`进行别名设置


### 函数原型

`public static void publish2ToAlias(Context context, String alias, String message, JSONObject opts, IMqttActionListener mqttAction)`


### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
context | Context | Android 应用上下文环境
alias| String | 用户设置的 [别名][6] 信息，同一 AppKey 下唯一，只支持英文数字下划线，长度不超过 50 个字符
message | String | 向设置该目标别名的对象发布的消息
opts | JSONObject | 向设置该目标别名的对象发布的消息的选项：如消息有效时间，目标平台，APNs 参数等等
mqttAction | IMqttActionListener | 成功会回调 onSuccess，失败回调 onFailure


### Code Example

```java

JSONObject opts = new JSONObject();
JSONObject apn_json = new JSONObject();
JSONObject aps = new JSONObject();
aps.put("sound", "bingbong.aiff");
aps.put("badge", 9);
aps.put("alert", "msg from android中文");
apn_json.put("aps", aps);
opts.put("apn_json", apn_json);
YunBaManager.publish2ToAlias(getApplicationContext(), alias, msg, opts,
    new IMqttActionListener() {
        @Override
        public void onSuccess(IMqttToken asyncActionToken) {
            String topic = DemoUtil.join(asyncActionToken.getTopics(), ", ");
            String msgLog = "publish2 to alias succeed : " + topic;
            DemoUtil.showToast(msgLog, getApplicationContext());
        }

        @Override
        public void onFailure(IMqttToken asyncActionToken, Throwable exception) {
             if (exception instanceof MqttException) {
               MqttException ex = (MqttException)exception;
               String msg =  "publish2ToAlias failed with error code : " + ex.getReasonCode();
               DemoUtil.showToast(msg, getApplicationContext());
             }
        }
    }
);
```


### 扩展参数说明

`publish2ToAlias`扩展参数 opts 是可选项，如果不填写参数，`publish2`的行为与`publish`相似（除了 apn_json 参数）。

名称 | 类型 | 说明
--------- | ------- | -----------
qos | number | 如果不填，默认为 1 ，参数设置请参考 [QoS][11]
apn_json | dict | 如果不填，则不会发送 iOS 端的 APNs 消息；而`publish`会发送默认的 APNs 消息。APNs 具体可参考：[Apple 官方文档](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html)
time_to_live | number | [离线消息](http://yunba.io/docs2/yunba_offline_message) 保留时间值，单位是秒（例如 2 天 2\*24\*3600），当前默认值为 5 天


## stop

### 功能

App 可以调用此函数来停止云巴服务，当服务被停止后，长连接断开，所有的 API 都会失效（包括 start API），该 API 可用于 [停止接收任何消息](http://yunba.io/docs2/android_tutorial#%E5%A6%82%E4%BD%95%E5%81%9C%E6%AD%A2%E5%92%8C%E6%81%A2%E5%A4%8D%E6%8E%A5%E6%94%B6%E6%B6%88%E6%81%AF)；当需要重新连接服务时，必须调用`resume`。


### 函数原型

`public static void stop(Context context)`


### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
context | Context | Android 应用上下文环境


### Code Example

```java
YunBaManager.stop(getApplicationContext());
```


## resume

### 功能

App 可以调用此函数来恢复云巴服务，与`stop()`相对应。


### 函数原型

`public static void resume(Context context)`


### 参数说明

* context: Android 应用上下文环境。


### Code Example

```java

YunBaManager.resume(getApplicationContext());
```


## isStopped

### 功能

App 可以调用此函数来查看云巴服务是否被停止。


### 函数原型

`
    public static void isStopped(Context context)
`


### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
context | Context | Android 应用上下文环境


### Code Example

```java

YunBaManager.isStopped(getApplicationContext());
```


## report

### 功能

App 可以调用此函数来上报客户端的行为，如打开通知栏次数，按钮点击次数，资源下载成功等行为。


### 函数原型

`public static void report(Context context, String actiton, String data)`


### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
context | Context | Android 应用上下文环境
action | String | 需要统计的行为，如打开通知栏，下载资源成功等等
data | String | 相对应 action 的附加数据，以满足统计相关的其他业务需求


### Code Example

```java

YunBaManager.report(getApplicationContext(), "notifaction_opened", null);
```


## setAlias

### 功能

App 可以调用此函数来绑定账号，用户名，同一应用（AppKey）下每个用户只能指定一个 [别名][6]。


### 函数原型

`public static void setAlias(Context context, String alias, IMqttActionListener mqttAction)`


### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
context | Context | Android 应用上下文环境
alias | String | 用户设置的别名信息，只支持英文数字下划线，长度不超过 50 个字符
mqttAction | IMqttActionListener | 成功会回调 onSuccess，失败回调 onFailure


### Code Example

```java

YunBaManager.setAlias(getApplicationContext(), alias, 
    new IMqttActionListener() {
        @Override
        public void onSuccess(IMqttToken asyncActionToken) {
            DemoUtil.showToast("success", getApplicationContext());
        }

        @Override
        public void onFailure(IMqttToken asyncActionToken, Throwable exception) {
             if (exception instanceof MqttException) {
               MqttException ex = (MqttException)exception;
               String msg =  "setAlias failed with error code : " + ex.getReasonCode();
               DemoUtil.showToast(msg, getApplicationContext());
             }
        }
    }
);
```


## getAlias

### 功能

App 可以调用此函数来获取当前用户的 [别名][6]。


### 函数原型

`public static void getAlias(Context context, IMqttActionListener mqttAction)`


### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
context | Context | Android 应用上下文环境
mqttAction | IMqttActionListener | 成功会回调 onSuccess，失败回调 onFailure


### Code Example

```java

YunBaManager.getAlias(getApplicationContext(), 
    new IMqttActionListener() {
        @Override
        public void onSuccess(IMqttToken mqttToken) {
            DemoUtil.showToast("get alias success " + mqttToken.getAlias(), getApplicationContext());
        }

        @Override
        public void onFailure(IMqttToken asyncActionToken, Throwable exception) {
             if (exception instanceof MqttException) {
               MqttException ex = (MqttException)exception;
               String msg =  "getAlias failed with error code : " + ex.getReasonCode();
               DemoUtil.showToast(msg, getApplicationContext());
             }
        }
    }
);
```


## getTopicList

### 功能

App 可以查询用户`订阅`的 [频道][5] 列表，如果不传入参数 [alias][6]，则是获取当前用户的频道列表，如果输入参数 alias，则是获取目标 alias 的频道列表。


### 函数原型

`public static void getTopicList(Context context, IMqttActionListener mqttAction)`

`public static void getTopicList(Context context, String alias, IMqttActionListener mqttAction)`


### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
context | Context | Android 应用上下文环境
alias | String | 用户设置的别名信息，只支持英文数字下划线，长度不超过 50 个字符
mqttAction | IMqttActionListener | 成功会回调 onSuccess，失败回调 onFailure


### Code Example

```java

YunBaManager.getTopicList(getApplicationContext(), 
    new IMqttActionListener() {
        @Override
        public void onSuccess(IMqttToken mqttToken) {
            JSONObject result = mqttToken.getResult();
				try {
					JSONArray topics = result.getJSONArray("topics");
					System.out.println(topics.toString());
				} catch (JSONException e) {
					
				}
        }

        @Override
        public void onFailure(IMqttToken asyncActionToken, Throwable exception) {
             if (exception instanceof MqttException) {
               MqttException ex = (MqttException)exception;
               String msg =  "getTopicList failed with error code : " + ex.getReasonCode();
               DemoUtil.showToast(msg, getApplicationContext());
             }
        }
    }
);
```


## getAliasList

### 功能

App 可以调用此函数来获取输入 Topic 下面所有`订阅`用户的 [别名][6]。


### 函数原型

`public static void getAliasList(Context context, String topic, IMqttActionListener mqttAction)`
  
`public static void getAliasList(Context context, String topic, boolean disableState, boolean disableAlias, IMqttActionListener mqttAction)`


### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
context | Context | Android 应用上下文环境
topic | String | App 订阅的 [频道][5]，topic 只支持英文数字下划线，长度不超过 50 个字符，数组的长度不超过 100
disableState | boolean | 结果是否排除别名状态信息
disableAlias | boolean | 结果是否排除别名列表
mqttAction | IMqttActionListener | 成功会回调 onSuccess，失败回调 onFailure


### Code Example

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


## getState

### 功能

根据 [别名][6] 来获取用户的状态，如是否在线等信息。


### 函数原型

`public static void getState(Context context, String alias, IMqttActionListener mqttAction)`


### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
context | Context | Android 应用上下文环境
alias | String | 用户设置的别名信息，只支持英文数字下划线，长度不超过 50 个字符
mqttAction | IMqttActionListener | 成功会回调 onSuccess，失败回调 onFailure


### Code Example

```java

YunBaManager.getState(getApplicationContext(), "t1",
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


## subscribePresence

### 功能

App 可以订阅某个频道上的用户的上、下线 及 订阅（或取消订阅）该频道的事件通知。所有用户的状态变化时都发起一个`<action android:name="io.yunba.android.PRESENCE_RECEIVED_ACTION" />`的广播，用户 App 的程序监听此 action 的广播就能收到相应状态的变化通知。


### 函数原型

`public static void subscribePresence(Context context, String topic, IMqttActionListener mqttAction)`


### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
context | Context | Android 应用上下文环境
topic | String | App 订阅的 [频道][5]，topic 只支持英文数字下划线，长度不超过 50 个字符，数组的长度不超过 100
mqttAction | IMqttActionListener | 成功会回调 onSuccess，失败回调 onFailure


### Code Example

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


### 自定义 Receiver 监听状态变化的 AndroidManifest.xml 配置

```xml
<receiver android:name="Your Receiver">
        <intent-filter>
            <action android:name="io.yunba.android.MESSAGE_RECEIVED_ACTION" />
            <action android:name="io.yunba.android.PRESENCE_RECEIVED_ACTION" /> 
            <category android:name="Package Name" />
        </intent-filter>
</receiver>

```


### 自定义 Receiver 监听状态变化代码片段

```java

else if(YunBaManager.PRESENCE_RECEIVED_ACTION.equals(intent.getAction())) {
			 //msg from presence.
			 String topic = intent.getStringExtra(YunBaManager.MQTT_TOPIC);		
				String payload = intent.getStringExtra(YunBaManager.MQTT_MSG);
				try {
					JSONObject res = new JSONObject(payload);
					String action = res.optString("action", null);
					String  alias = res.optString("alias", null);
					//process your code
				} catch (JSONException e) {
				
				}
		}

```


## unsubscribePresence

### 功能

与`subscribePresence`相对应，取消监听对应 Topic 下用户上、下线 及 订阅（或取消订阅）该频道的事件通知。


### 函数原型

`public static void unsubscribePresence(Context context, String topic, IMqttActionListener mqttAction)`


### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
context | Context | Android 应用上下文环境
topic | String | App 订阅的 [频道][5]，topic 只支持英文数字下划线，长度不超过 50 个字符，数组的长度不超过 100
mqttAction | IMqttActionListener | 成功会回调 onSuccess，失败回调 onFailure


### Code Example

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


[3]: http://yunba.io/docs2/appkey
[5]: http://yunba.io/docs2/topic_and_alias#%E9%A2%91%E9%81%93topic
[6]: http://yunba.io/docs2/topic_and_alias#%E5%88%AB%E5%90%8Dalias
[11]: http://yunba.io/docs2/qos

