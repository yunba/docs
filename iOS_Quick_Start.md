# Yunba iOS SDK 快速入门
## 注册开发者账号
打开 <http://yunba.io>, 点击注册创建账号。

![register_account.png](../image/register_account.png)

## 创建应用
注册账号成功跳转到我的应用界面，点击我的应用 --> 创建新应用，输入应用名称

![create_app.png](../image/create_app.png)

## 下载 iOS SDK

打开 <http://yunba.io/developers/> 下载 iOS SDK， iOS SDK 包含 DEMO 程序和开发者所需嵌入的 jar 包。

## 导入 iOS SDK

下载的 YunBa-iOS-sdk 包并添加到项目中，并且添加依赖库SystemConfiguration.framework。

![add_sdk_iOS.png](../image/add_sdk_iOS.png)

## 添加使用代码

从portal获取AppKey:
![copy_app_key.png](../image/copy_app_key.png)

> 添加头文件，引入`YunBaService.h`:

```objective_c
#import "YunBaService.h"
```

<aside class="warning">注意使用从portal获取到的AppKey替换代码中的AppKey。</aside>

> 初始化 SDK 并订阅 Topic，在`- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions` 中添加初始化和订阅代码:

```objective_c
    [YunBaService setupWithAppkey:AppKey];

    [YunBaService subscribe:topic resultBlock:^(BOOL succ, NSError *error){
        if (succ) {
            NSLog(@"[result] subscribe to topic(%@) succeed", topic);
        } else {
            NSLog(@"[result] subscribe to topic(%@) failed: %@, recovery suggestion: %@", topic, error, [error localizedRecoverySuggestion]);
        }
    }];
```

## 添加 监听消息及处理代码
> 在默认消息中心添加的监听代码 :

```objective_c
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(onMessageReceived:) name:kYBDidReceiveMessageNotification object:nil];
```

> 消息处理代码 :

```objective_c
- (void)onMessageReceived:(NSNotification *)notification {
    YBMessage *message = [notification object];
    NSString *payloadString = [[NSString alloc] initWithData:[message data] encoding:NSUTF8StringEncoding];
    NSLog(@"[Message] %@ => %@", [message topic], payloadString);
}
```

## 在 Portal 上发布消息

打开应用详情页面，点击发布消息，如图所示:

![send_message.png](../image/send_message.png)

## 在 Portal 查看消息发布实时报表

打开应用详情页面，点击发布上报统计可以查看消息发布实时送达比，如图所示:

![publish_statistic.png](../image/publish_statistic.png)

## 在 Portal 查看用户在线信息实时报表

打开应用详情页面，点击在线用户统计可以查看当前在线用户数，用户活跃数等信息，如图所示:

![online_statistic.png](../image/online_statistic.png)
