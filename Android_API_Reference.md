# YunBa Android SDK API 手册

## start

### 功能
App 初始化 YunBa SDK.

### 函数原型
  ` public static void start(Context context) ` 
  
  ` public static void start(Context context, String appkey) `
  
  ` public static void start(Context context, String appkey, Map opts) `

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
context | Context | Android 应用上下文环境
appkey | String | YunBa 中注册的 App Key, 如果用户已经在 AndroidManifest.xml 定义了 YUNBA_APPKEY, 此处的设置是无效的。
opts | Map | 选项，可包含sub_key(用于获取订阅权限的密钥), pub_key(用于获取发布权限的密钥),sec_key(用于获取管理权限的密钥，切勿外泄), auth_key(用于access manager模块中权限管理的动态密钥)

### Code Example

```java
    YunBaManager.start(getApplicationContext());
```

## subscribe

### 功能
App 可以订阅一个或者多个 Topics, 以便可以接收来自 Topic 的 Message.

### 函数原型
`   public static void subscribe(Context context, String topic, IMqttActionListener mqttAction) `

`	public static void subscribe(Context context, String[] topics, IMqttActionListener mqttAction) `

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
context | Context | Android 应用上下文环境
topic | String | app 订阅的的频道，topic 只支持英文数字下划线，长度不超过50个字符,数组的长度不超过100
topics | String[] | app 订阅的的频道数组列表，topic 只支持英文数字下划线，长度不超过50个字符,数组的长度不超过100
mqttAction | IMqttActionListener | 成功会回调 onSuccess， 失败回调 onFailure

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
App 可以取消订阅一个或者多个 Topics, 以便取消接收来自 Topic 的 Message.

### 函数原型

`    public static void unsubscribe(Context context, String topic, IMqttActionListener mqttAction) `

`    public static void unsubscribe(Context context, String[] topics, IMqttActionListener mqttAction) `

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
context | Context | Android 应用上下文环境
topic | String | app 订阅的的频道，topic 只支持英文数字下划线，长度不超过50个字符,数组的长度不超过100
topics | String[] | app 订阅的的频道数组列表，topic 只支持英文数字下划线，长度不超过50个字符,数组的长度不超过100
mqttAction | IMqttActionListener | 成功会回调 onSuccess， 失败回调 onFailure

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
App 可以向 Topic 发送消息, 那么任何订阅此 Topic 的 Client 都会接受到消息。

### 函数原型


`	public static void publish(Context context, String topic, String message,IMqttActionListener mqttAction)) `

`   public static void publish(Context context, String topic, String message, Map opts, IMqttActionListener mqttAction) `

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
context | Context | Android 应用上下文环境
topic | String | app 订阅的的频道，topic 只支持英文数字下划线，长度不超过50个字符,数组的长度不超过100
message | String | 向目标 topic 的订阅者发布的消息
opts | Map | 向目标 topic 的订阅者发布的消息的选项：如消息有效时间，目标平台等等
mqttAction | IMqttActionListener | 成功会回调 onSuccess， 失败回调 onFailure

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

## publishToAlias

### 功能
向用户别名发送消息, 用于实现点对点的消息发送。

### 函数原型

`	public static void publishToAlias(Context context, String alias, String message,IMqttActionListener mqttAction) `

`   public static void publishToAlias(Context context, String alias, String message, Map opts, IMqttActionListener mqttAction) `

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
context | Context | Android 应用上下文环境
alias| String | 用户设置的别名信息，只支持英文数字下划线，长度不超过50个字符
message | String | 向目标别名的订阅者发布的消息
opts | Map | 向目标别名的订阅者发布的消息的选项：如消息有效时间，目标平台等等
mqttAction | IMqttActionListener | 成功会回调 onSuccess， 失败回调 onFailure

### Code Example

```java

YunBaManager.publishToAlias(getApplicationContext(), topic, msg,
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


## stop

#### 功能
App 可以调用此函数来停止推送服务，当推送服务被停止后，所以的 API 都会失效（包括 start API）, 当需要重新使用推送服务时，必须要调用 resume API

### 函数原型

`
    public static void stop(Context context)
`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
context | Context | Android 应用上下文环境

### Code Example

```java

    YunBaManager.stop(getApplicationContext());
```


## resume

#### 功能
App 可以调用此函数来恢复推送服务，与 stop API 相对应。

### 函数原型

`
    public static void resume(Context context)
`

### 参数说明
* context: Android 应用上下文环境。

### Code Example

```java

    YunBaManager.resume(getApplicationContext());
```


## isStopped

#### 功能
App 可以调用此函数来查看推送服务是否被停止。

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
App  可以调用此函数来上报客户端的行为，如打开通知栏次数，按钮点击次数，资源下载成功等等行为。

### 函数原型

`
    public static void report(Context context, String actiton, String data)
`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
context | Context | Android 应用上下文环境
action | String | 需要统计的行为，如打开通知栏，下载资源成功等等
data | String | 想对应 action 的附加数据，以满足统计相关的其他业务需求


### Code Example

```java

    YunBaManager.report(getApplicationContext(), "notifaction_opened", null,);
```

## setAlias

### 功能
App  可以调用此函数来绑定账号，用户名，每个用户只能指定一个别名。

### 函数原型

`
    public static void setAlias(Context context, String alias, IMqttActionListener mqttAction)
`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
context | Context | Android 应用上下文环境
alias | String | 用户设置的别名信息，只支持英文数字下划线，长度不超过50个字符
mqttAction | IMqttActionListener | 成功会回调 onSuccess， 失败回调 onFailure

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
App  可以调用此函数来获取当前用户的别名。

### 函数原型

`
    public static void getAlias(Context context, IMqttActionListener mqttAction)
`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
context | Context | Android 应用上下文环境
mqttAction | IMqttActionListener | 成功会回调 onSuccess， 失败回调 onFailure

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
App 可以查询用户订阅的频道列表，如果不传入参数 alias， 则是获取当前用户的频道列表,如果输入参数 alias，则是获取目标 alias 的频道列表。

### 函数原型

`
    public static void getTopicList(Context context,IMqttActionListener mqttAction)
`

`    
    public static void getTopicList(Context context, String alias, IMqttActionListener mqttAction)
`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
context | Context | Android 应用上下文环境
alias | String | 用户设置的别名信息，只支持英文数字下划线，长度不超过50个字符
mqttAction | IMqttActionListener | 成功会回调 onSuccess， 失败回调 onFailure

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
App  可以调用此函数来获取订阅输入 Topic 下面所有的用户的别名。


### 函数原型


  ` public static void getAliasList(Context context, String topic, IMqttActionListener mqttAction) `
  
  ` public static void getAliasList(Context context, String topic, boolean disableState, boolean disableAlias, IMqttActionListener mqttAction) `


### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
context | Context | Android 应用上下文环境
topic | String | app 订阅的的频道，topic 只支持英文数字下划线，长度不超过50个字符,数组的长度不超过100
disableState | boolean | 结果是否排除别名状态信息
disableAlias | boolean | 结果是否排除别名列表
mqttAction | IMqttActionListener | 成功会回调 onSuccess， 失败回调 onFailure


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
根据别名来获取用用户的状态，如是否在线等信息


### 函数原型

`
    public static void  getState(Context context, String alias, IMqttActionListener mqttAction)
`


### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
context | Context | Android 应用上下文环境
alias | String | 用户设置的别名信息，只支持英文数字下划线，长度不超过50个字符
mqttAction | IMqttActionListener | 成功会回调 onSuccess， 失败回调 onFailure


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
App  可以调用此函数来监听 Topic 下面所有的用户的别名状态的变化。所有用户的状态变化时都发起一个  <action android:name="io.yunba.android.PRESENCE_RECEIVED_ACTION" /> 的广播，用户 App 的程序监听此 action 的广播就能收到相应状态的变化。


### 函数原型

`
    public static void subscribePresence(Context context, String topic, IMqttActionListener mqttAction)
`


### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
context | Context | Android 应用上下文环境
topic | String | app 订阅的的频道，topic 只支持英文数字下划线，长度不超过50个字符,数组的长度不超过100
mqttAction | IMqttActionListener | 成功会回调 onSuccess， 失败回调 onFailure


### Code Example

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


### 自定义 Receive 监听状态变化的 AndroidManifest.xml 配置

> 自定义 Receiver 处理 Publish 消息

```xml
 <receiver android:name="Your Receiver">
              <intent-filter>
                <action android:name="io.yunba.android.MESSAGE_RECEIVED_ACTION" />
                <action android:name="io.yunba.android.PRESENCE_RECEIVED_ACTION" /> 
                <category android:name="Package Name" />
            </intent-filter>
         </receiver>

```


### 自定义 Receive 监听状态变化代码片段

> 自定义 Receive 监听状态变化代码片段

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
与 subscribePresence 想对应， 取消监听对应 Topic 下用户状态的变化。


### 函数原型

`
    public static void unsubscribePresence(Context context, String topic, IMqttActionListener mqttAction)
`


### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
context | Context | Android 应用上下文环境
topic | String | app 订阅的的频道，topic 只支持英文数字下划线，长度不超过50个字符,数组的长度不超过100
mqttAction | IMqttActionListener | 成功会回调 onSuccess， 失败回调 onFailure


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

    
