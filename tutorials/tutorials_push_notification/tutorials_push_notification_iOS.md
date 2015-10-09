##**消息推送开发指南之 iOS 篇**
---



###1. 注册云巴开发者账号
打开[云巴官方网站](http://yunba.io "云巴官方网站")，点击“注册”按钮创建账号。  

![register_account.png](https://raw.githubusercontent.com/yunba/docs/master/image/register_account.png)

###2. 创建应用
注册账号成功跳转到我的应用界面，点击我的应用 --> 创建新应用，输入应用名称  

![create_app.png](https://raw.githubusercontent.com/yunba/docs/master/image/create_app.png)

###3. 下载 iOS SDK

打开[云巴开发者页面](http://yunba.io/developers "云巴开发者页面")，下载 iOS SDK，iOS SDK 包含 DEMO 程序和开发者所需嵌入的 lib 库以及头文件。  

###4. 导入 iOS SDK

将下载的 YunBa-iOS-sdk 包添加到项目中，并添加依赖库 SystemConfiguration.framework。  

![add_sdk_iOS.png](https://raw.githubusercontent.com/yunba/docs/master/image/add_sdk_iOS.png)

###5. 添加使用代码

添加头文件，引入`YunBaService.h`:  

```objective_c
#import "YunBaService.h"
```

从 Portal 获取 AppKey:  

![copy_app_key.png](https://raw.githubusercontent.com/yunba/docs/master/image/copy_app_key.png)

**注**: 请使用从 Portal 获取到的 AppKey 替换代码中的 AppKey。

初始化 SDK 并订阅 Topic，在`- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions` 中添加初始化和订阅代码:  

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

###6. 添加监听消息及处理代码
在默认消息中心添加的监听代码:  

```objective_c
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(onMessageReceived:) name:kYBDidReceiveMessageNotification object:nil];
```

消息处理代码:  

```objective_c
- (void)onMessageReceived:(NSNotification *)notification {
    YBMessage *message = [notification object];
    NSString *payloadString = [[NSString alloc] initWithData:[message data] encoding:NSUTF8StringEncoding];
    NSLog(@"[Message] %@ => %@", [message topic], payloadString);
}
```

###7. 在 Portal 上传 APNs 证书以激活 APN 推送功能

打开编辑应用页面，点击上传 iOS 开发/生产环境证书并输入相应的证书密码后可以激活 APN 推送功能，如图所示:  

![upload_ios_cert.png](https://raw.githubusercontent.com/yunba/docs/master/image/upload_ios_cert.png)

**注**: 可参考[生成 APNs 证书的步骤](https://github.com/yunba/docs/blob/master/support/knowledge_base/create_APNs_certificate.md "生成 APNs 证书的步骤")
一文来生成 APNs 证书。
