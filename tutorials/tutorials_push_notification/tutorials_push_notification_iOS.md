##**消息推送开发指南之 iOS 篇**
---
###**准备工作**

####1. 生成 APNs 证书
请参考 [生成 APNs 证书的步骤](https://github.com/yunba/docs/blob/master/support/knowledge_base/create_APNs_certificate.md "生成 APNs 证书的步骤") 一文，先生成 APNs 证书。
<br><br>
####2. 下载云巴 iOS SDK
打开 [云巴开发者页面](http://yunba.io/developers "云巴开发者页面")，下载最新版本的 iOS SDK。iOS SDK 包含 DEMO 程序和开发者所需嵌入的 lib 库以及头文件。
<br><br>
####3. 注册云巴开发者账号
打开 [云巴官方网站](http://yunba.io "云巴官方网站")，点击右上角的“注册”按钮创建账号。  


###**详细步骤**

####1. 创建新应用
注册成功后，页面会跳转到“我的应用”界面，点击 我的应用 --> 创建新应用。

![create_app.png](https://raw.githubusercontent.com/yunba/docs/master/image/create_app.png)


输入应用的名称及 Bundle ID。同时，上传 iOS 开发或生产证书并输入相应的证书密码，以激活苹果推送通知服务。

![tutorials_push_notification_iOS_create_new_app.png](https://raw.githubusercontent.com/yunba/docs/master/image/for_tutorials/tutorials_push_notification_iOS_create_new_app.png)

应用信息填好以后，点击页面下方的“创建应用”按钮，会来到“应用详情”页面。这里可以看到从 Portal 申请到的 AppKey。 

![copy_app_key.png](https://raw.githubusercontent.com/yunba/docs/master/image/copy_app_key.png)


####2. 导入 iOS SDK

将下载的 YunBa-iOS-sdk 包添加到项目中，并添加依赖库 SystemConfiguration.framework。  

![add_sdk_iOS.png](https://raw.githubusercontent.com/yunba/docs/master/image/add_sdk_iOS.png)

#####2.1 添加使用代码

添加头文件，引入`YunBaService.h`:  

```objective_c
#import "YunBaService.h"
```

初始化 SDK 并订阅 Topic，在`- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions` 中添加初始化和订阅代码，并使用从 Portal 获取到的 AppKey 替换代码中的 AppKey:  

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

#####2.2 添加监听消息及处理代码
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

**注**: iOS SDK Demo 里已经包含了类似的处理代码。如果您想运行 Demo 程序，无需添加上述代码，只需使用您从 Portal 获取到的 Appkey 替换代码中的 Appkey 即可。另外，iOS Demo 真机调试时可能遇到“不包含 Bitcode”的错误，请参考 [相关文章](https://github.com/yunba/docs/blob/master/support/troubleshooting/iOS_YunbaDemo_bitcode_error.md "相关文章") 解决。

2.3 编译运行即可。需要注意的是，云巴 SDK 1.5.2 之前的版本在 iOS 9 上无法正常注册，请参考 [相关文章](https://github.com/yunba/docs/blob/master/support/troubleshooting/SDK_registration_problem_on_iOS9.md "相关文章") 解决。

####3. 发布消息

这里仅演示在 Portal 上发布消息。如您集成了其他 SDK，或通过 RESTful API，请查阅相应的文档。
<br>
选择您在 Portal 上创建的应用，点击“发布消息”，即可向特定的 Topic 发消息。如图所示:  

![tutorials_push_notification_iOS_publish.png](https://raw.githubusercontent.com/yunba/docs/master/image/for_tutorials/tutorials_push_notification_iOS_publish.png)

以 iOS SDK Demo 为例，订阅该 Topic，则会收到 Portal 发布的消息。如图所示：

![tutorials_push_notification_iOS_recvmsg.png](https://raw.githubusercontent.com/yunba/docs/master/image/for_tutorials/tutorials_push_notification_iOS_recvmsg.png)


