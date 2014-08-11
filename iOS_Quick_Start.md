# Yunba iOS SDK 快速入门
## 注册开发者账号
打开 <http://yunba.io>, 点击注册创建账号。  

![register_account.png](../image/register_account.png)

## 创建应用
注册账号成功跳转到我的应用界面，点击我的应用 --> 创建新应用，输入应用名称  

![create_app.png](../image/create_app.png)

## 下载 iOS SDK

打开 <http://yunba.io/developers/> 下载 iOS SDK， iOS SDK 包含 DEMO 程序和开发者所需嵌入的 lib 库以及头文件。  

## 导入 iOS SDK

下载的 YunBa-iOS-sdk 包并添加到项目中，并且添加依赖库SystemConfiguration.framework。  

![add_sdk_iOS.png](../image/add_sdk_iOS.png)

## 添加使用代码

添加头文件，引入`YunBaService.h`:  

```objective_c
#import "YunBaService.h"
```

从portal获取AppKey:  

![copy_app_key.png](../image/copy_app_key.png)

<aside class="notice">注意使用从portal获取到的AppKey替换代码中的AppKey。</aside>

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

## 添加 监听消息及处理代码
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

## 在 Portal 上发布消息

打开应用详情页面，点击发布消息，如图所示:  

![send_message.png](../image/send_message.png)

## 在 Portal 查看消息发布实时报表

打开应用详情页面，点击发布上报统计可以查看消息发布实时送达比，如图所示:  

![publish_statistic.png](../image/publish_statistic.png)

## 在 Portal 查看用户在线信息实时报表

打开应用详情页面，点击在线用户统计可以查看当前在线用户数，用户活跃数等信息，如图所示:  

![online_statistic.png](../image/online_statistic.png)

## 在 Portal 上传APNs证书以激活APN推送功能

打开编辑应用页面，点击上传iOS开发/生产环境证书并输入相应的证书密码后可以激活APN推送功能，如图所示:  

![upload_APNs_cert_file.png](../image/upload_APNs_cert_file.png)

### 生成APNs证书的步骤如下：
1. 打开apple开发者网站的证书管理界面<https://developer.apple.com/account/ios/certificate/>  

2. 点击新建证书  

	![create_ios_cert.png](../image/create_ios_cert.png)  

3. 选择新建证书的类型（开发或者生产推送环境）

	![create_cert_choose_type.png](../image/create_cert_choose_type.png)  

	注：如果你的系统中没有中间签名证书，则需要下载并安装到你的系统中：  

	![before_create_cert.png](../image/before_create_cert.png)  

4. 为证书选择对应需要推送功能的APP ID:  

	![create_cert_choose_appid.png](../image/create_cert_choose_appid.png)  

5. 为制作推送证书，需要有一个CSR文件用以使新生成的推送证书与私钥相匹配：

	![create_cert_upload_csr.png](../image/create_cert_upload_csr.png)  
	
	注：如果你没有CSR文件，则需要新建一个，步骤如下：  

	a. 打开钥匙串访问  

	![open_key_chain.png](../image/open_key_chain.png)  
	
	b. 申请创建CSR文件  

	![create_csr.png](../image/create_csr.png)  
	
	c. 输入你的邮件等信息后保存CSR文件到本地磁盘  

	![save_csr.png](../image/save_csr.png)  

6. 点击继续之后生成APNs证书成功，然后点击下载即可  

	![download_created_cert.png](../image/download_created_cert.png)  
	