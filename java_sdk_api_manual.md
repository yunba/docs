# YunBa Java SDK API 手册

## createMqttClient

### 功能
MqttAsyncClient 的静态方法， 根据 AppKey (来自portal) 实例化 MqttAsyncClient

### 函数原型
`   public static MqttAsyncClient createMqttClient(String appkey) throws MqttException, JSONException  `

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
appKey | String | YunBa Portal 中注册的 App Key

### Code Example
```java
   final MqttAsyncClient mqttAsyncClient = MqttAsyncClient.createMqttClient("52fcc04c4dc903d66d6f8f92");
   //mqttAsyncClient.setCallback(new MqttCallback())
```


## setCallback

### 功能
MqttAsyncClient 对象的回调函数，用来接受消息，处理连接断开等事件。

### 函数原型
`   public void setCallback(MqttCallback callback)  `

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
callback | MqttCallback | 处理消息到达，服务器状态变化等事件

### Code Example
```java
   mqttAsyncClient.setCallback(new MqttCallback() {

				@Override
				public void messageArrived(String topic, MqttMessage message) throws Exception {
					System.out.println("mqtt receive topic = " + topic + " msg = " + new String(message.getPayload())) ;//reciver msg from yunba server
				}

				@Override
				public void deliveryComplete(IMqttDeliveryToken token) {
				}

				@Override
				public void connectionLost(Throwable cause) {
					System.out.println("mqtt connectionLost");
				}
```

## connect

### 功能
MqttAsyncClient 连接到 YunBa Server, 只有连接成功后才能调用 subscribe, publish 等 API。

### 函数原型
`   public void connect(IMqttActionListener mqttAction) `


### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
mqttAction | IMqttActionListener | 成功会回调 onSuccess， 失败回调 onFailure

### Code Example

```java

	mqttAsyncClient.connect(getApplicationContext(),topic,
	  new IMqttActionListener() {
        @Override
        public void onSuccess(IMqttToken asyncActionToken) {
          // do subscibe, publish....
        }

        @Override
        public void onFailure(IMqttToken asyncActionToken, Throwable exception) {
          if (exception instanceof MqttException) {
               MqttException ex = (MqttException)exception;
               System.err.println("connect to server failed with the error code = " + ex.getReasonCode());
           }
        }
      }
    );
```
## subscribe

### 功能
App 可以订阅一个或者多个 Topics, 以便可以接收来自 Topic 的 Message.

### 函数原型
`   public void subscribe(String topic, IMqttActionListener mqttAction) `

`	public void subscribe(String[] topics, IMqttActionListener mqttAction) `

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
topic | String | app 订阅的的频道，topic 只支持英文数字下划线，长度不超过50个字符,数组的长度不超过100
topics | String[] | app 订阅的的频道数组列表，topic 只支持英文数字下划线，长度不超过50个字符,数组的长度不超过100
mqttAction | IMqttActionListener | 成功会回调 onSuccess， 失败回调 onFailure

### Code Example

```java

	mqttAsyncClient.subscribe(topic, new IMqttActionListener() {
        @Override
        public void onSuccess(IMqttToken asyncActionToken) {
           // do publish...
        }

        @Override
        public void onFailure(IMqttToken asyncActionToken, Throwable exception) {
           if (exception instanceof MqttException) {
               MqttException ex = (MqttException)exception;
               System.err.println("subscribe failed with the error code = " + ex.getReasonCode());
           }
        }
      }
    );
```


## unsubscribe

### 功能
App 可以取消订阅一个或者多个 Topics, 以便取消接收来自 Topic 的 Message.

### 函数原型

`    public void unsubscribe(String topic, IMqttActionListener mqttAction) `

`    public void unsubscribe(Context context, String[] topics, IMqttActionListener mqttAction) `

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
topic | String | app 订阅的的频道，topic 只支持英文数字下划线，长度不超过50个字符,数组的长度不超过100
topics | String[] | app 订阅的的频道数组列表，topic 只支持英文数字下划线，长度不超过50个字符,数组的长度不超过100
mqttAction | IMqttActionListener | 成功会回调 onSuccess， 失败回调 onFailure

### Code Example

```java

 mqttAsyncClient.unsubscribe(topic,new IMqttActionListener() {

    @Override
    public void onSuccess(IMqttToken asyncActionToken) {
      //...
    }

    @Override
    public void onFailure(IMqttToken asyncActionToken,Throwable exception) {
       if (exception instanceof MqttException) {
               MqttException ex = (MqttException)exception;
               System.err.println("connect to server failed with the error code = " + ex.getReasonCode());
          }
    }
  }
);
```

## publish

### 功能
App 可以向 Topic 发送消息, 那么任何订阅此 Topic 的 Client 都会接受到消息。

### 函数原型


`	public void publish(String topic, String message,IMqttActionListener mqttAction)) `

`   public void publish(String topic, String message, Map opts, IMqttActionListener mqttAction) `

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
topic | String | app 订阅的的频道，topic 只支持英文数字下划线，长度不超过50个字符,数组的长度不超过100
message | String | 向目标 topic 的订阅者发布的消息
opts | Map | 向目标 topic 的订阅者发布的消息的选项：如消息有效时间，目标平台等等
mqttAction | IMqttActionListener | 成功会回调 onSuccess， 失败回调 onFailure

### Code Example

```java

mqttAsyncClient.publish(topic, msg, new IMqttActionListener() {
        @Override
        public void onSuccess(IMqttToken asyncActionToken) {
            String[] topic = asyncActionToken.getTopics();
            //....
        }

        @Override
        public void onFailure(IMqttToken asyncActionToken, Throwable exception) {
             if (exception instanceof MqttException) {
               MqttException ex = (MqttException)exception;
               System.err.println("publish failed with the error code = " + ex.getReasonCode());
          }
        }
    }
);
```

## publishToAlias

### 功能
向用户别名发送消息, 用于实现点对点的消息发送。

### 函数原型

`	public void publishToAlias(String alias, String message,IMqttActionListener mqttAction) `

`   public void publishToAlias(String alias, String message, Map opts, IMqttActionListener mqttAction) `

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
alias| String | 用户设置的别名信息，只支持英文数字下划线，长度不超过50个字符
message | String | 向目标别名的订阅者发布的消息
opts | Map | 向目标别名的订阅者发布的消息的选项：如消息有效时间，目标平台等等
mqttAction | IMqttActionListener | 成功会回调 onSuccess， 失败回调 onFailure

### Code Example

```java

mqttAsyncClient.publishToAlias(topic, msg,
    new IMqttActionListener() {
        @Override
        public void onSuccess(IMqttToken asyncActionToken) {
             //....
        }

        @Override
        public void onFailure(IMqttToken asyncActionToken, Throwable exception) {
            if (exception instanceof MqttException) {
               MqttException ex = (MqttException)exception;
               System.err.println("publishToAlias failed with the error code = " + ex.getReasonCode());
            }
        }
    }
);
```




## report

### 功能
App  可以调用此函数来上报客户端的行为，如打开通知栏次数，按钮点击次数，资源下载成功等等行为。

### 函数原型

`
    public void report(String actiton, String data)
`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
action | String | 需要统计的行为，如打开通知栏，下载资源成功等等
data | String | 想对应 action 的附加数据，以满足统计相关的其他业务需求


### Code Example

```java

    mqttAsyncClient.report("notifaction_opened", null,);
```

## setAlias

### 功能
App  可以调用此函数来绑定账号，用户名，每个用户只能指定一个别名。

### 函数原型

`
    public void setAlias(String alias, IMqttActionListener mqttAction)
`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
alias | String | 用户设置的别名信息，只支持英文数字下划线，长度不超过50个字符
mqttAction | IMqttActionListener | 成功会回调 onSuccess， 失败回调 onFailure

### Code Example

```java

mqttAsyncClient.setAlias(alias, 
    new IMqttActionListener() {
        @Override
        public void onSuccess(IMqttToken asyncActionToken) {
            String alias = asyncActionToken.getAlias();
            //...
        }

        @Override
        public void onFailure(IMqttToken asyncActionToken, Throwable exception) {
             if (exception instanceof MqttException) {
               MqttException ex = (MqttException)exception;
               System.err.println("setAlias failed with the error code = " + ex.getReasonCode());
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
    public void getAlias(IMqttActionListener mqttAction)
`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
mqttAction | IMqttActionListener | 成功会回调 onSuccess， 失败回调 onFailure

### Code Example

```java

mqttAsyncClient.getAlias(new IMqttActionListener() {
        @Override
        public void onSuccess(IMqttToken mqttToken) {
             String alias = mqttToken.getAlias();
             //...
        }

        @Override
        public void onFailure(IMqttToken asyncActionToken, Throwable exception) {
             if (exception instanceof MqttException) {
               MqttException ex = (MqttException)exception;
               System.err.println("getAlias failed with the error code = " + ex.getReasonCode());
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
    public void getTopicList(IMqttActionListener mqttAction)
`

`    
    public static void getTopicList(String alias, IMqttActionListener mqttAction)
`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
alias | String | 用户设置的别名信息，只支持英文数字下划线，长度不超过50个字符
mqttAction | IMqttActionListener | 成功会回调 onSuccess， 失败回调 onFailure

### Code Example

```java

mqttAsyncClient.getTopicList(new IMqttActionListener() {
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
               System.err.println("getTopicList failed with the error code = " + ex.getReasonCode());
            }
        }
    }
);
```


## getAliasList

### 功能
App  可以调用此函数来获取订阅输入 Topic 下面所有的用户的别名。


### 函数原型


  ` public void getAliasList(String topic, IMqttActionListener mqttAction) `
  
  ` public void getAliasList(String topic, boolean disableState, boolean disableAlias, IMqttActionListener mqttAction) `


### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
topic | String | app 订阅的的频道，topic 只支持英文数字下划线，长度不超过50个字符,数组的长度不超过100
disableState | boolean | 结果是否排除别名状态信息
disableAlias | boolean | 结果是否排除别名列表
mqttAction | IMqttActionListener | 成功会回调 onSuccess， 失败回调 onFailure


### Code Example

```java

mqttAsyncClient.getAliasList("t1",
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
               System.err.println("getAliasList failed with the error code = " + ex.getReasonCode());
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
    public void  getState(String alias, IMqttActionListener mqttAction)
`


### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
alias | String | 用户设置的别名信息，只支持英文数字下划线，长度不超过50个字符
mqttAction | IMqttActionListener | 成功会回调 onSuccess， 失败回调 onFailure


### Code Example

```java

mqttAsyncClient.getState("t1", new IMqttActionListener() {
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
               System.err.println("get state failed with the error code = " + ex.getReasonCode());
            }
        }
    }
);
```



    
