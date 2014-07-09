# Yunba iOS SDK 快速入门
## 注册开发者账号
打开 <http://yunba.io>, 点击注册创建账号。

![register_account.png](register_account.png)

## 创建应用
注册账号成功跳转到我的应用界面，点击我的应用 --> 创建新应用，输入应用名称

![create_app.png](create_app.png)

## 下载 iOS SDK

打开 <http://yunba.io/developers/> 下载 iOS SDK， iOS SDK 包含 DEMO 程序和开发者所需嵌入的 jar 包。

## 导入 iOS SDK

下载的 YunBa-iOS-sdk 包并添加到项目中。

![add_sdk_iOS.png](add_sdk_iOS.png)

## 添加 头文件
引入`YunBaService.h`

```objective_c
#import "YunBaService.h"
```

## 添加 监听消息及处理代码
在默认消息中心添加message received的监听代码。

```objective_c
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(onMessageReceived:) name:kYBDidReceiveMessageNotificationKey object:nil];
```

### 添加 消息处理代码
```objective_c
- (void)onMessageReceived:(NSNotification *)notification {
    YBMessage *message = [notification object];
    NSString *payloadString = [[NSString alloc] initWithData:[message data] encoding:NSUTF8StringEncoding];
    NSLog(@"[Message] %@ => %@", [message topic], payloadString);
}
```

## 添加使用代码
初始化 SDK 并订阅 Topic，请在您的 Application 子类的 OnCreate 方法中加入如下代码

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

### 自定义 Receiver 在 iOSManifest.xml 的配置
```xml
	<receiver iOS:name="Your Receiver">
		<intent-filter>
		<action iOS:name="io.yunba.iOS.MESSAGE_RECEIVED_ACTION" />
		<category iOS:name="Package Name" />
		</intent-filter>
	</receiver>
```

### 自定义 Receiver 处理 Publish 消息代码示例

```Java
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
### 在 Portal 上发布消息

打开应用详情页面，点击发布消息，如图所示:

![publish.png](https://bitbucket.org/yunba/public_docs/downloads/publish.png)

### 在 Portal 查看消息发布实时报表

打开应用详情页面，点击发布上报统计可以查看消息发布实时送达比，如图所示:

![report.jpeg](https://bitbucket.org/yunba/public_docs/downloads/report.jpeg)

### 在 Portal 查看用户在线信息实时报表

打开应用详情页面，点击在线用户统计可以查看当前在线用户数，用户活跃数等信息，如图所示:

![online.jpeg](https://bitbucket.org/yunba/public_docs/downloads/online.jpeg)
