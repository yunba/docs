# Yunba iOS SDK 快速入门
## 注册开发者账号
打开 [云巴官方网站](https://yunba.io), 点击注册创建账号。  

![productpng_portal_register_account.png](https://raw.githubusercontent.com/yunba/docs/master/image/productpng_portal_register_account.png)

## 创建应用
注册账号成功跳转到我的应用界面，点击“创建应用”，输入应用名称  

![productpng_portal_creat_app.png](https://raw.githubusercontent.com/yunba/docs/master/image/productpng_portal_creat_app.png)

## 下载 iOS SDK

打开 [开发者资源页面](https://yunba.io/developers/>) 下载 iOS SDK， iOS SDK 包含 DEMO 程序和开发者所需嵌入的 lib 库以及头文件。  

## 导入 iOS SDK

下载的 YunBa-iOS-sdk 包并添加到项目中，并且添加依赖库SystemConfiguration.framework。  

![iospng_sdk_add_in_xcode.png](https://raw.githubusercontent.com/yunba/docs/master/image/iospng_sdk_add_in_xcode.png)

## 添加使用代码

添加头文件，引入`YunBaService.h`:  

```objective_c
#import "YunBaService.h"
```

从portal获取AppKey:  

![productpng_portal_copy_app_key.png](https://raw.githubusercontent.com/yunba/docs/master/image/productpng_portal_copy_app_key.png)

>**注**: 请使用从 Portal 获取到的 AppKey 替换代码中的 AppKey。

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

![productpng_portal_publish_to_topic.png](https://raw.githubusercontent.com/yunba/docs/master/image/productpng_portal_publish_to_topic.png)

## 在 Portal 查看消息发布实时报表

打开应用详情页面，点击发布上报统计可以查看消息发布实时送达比，如图所示:  

![productpng_portal_publish_statistic.png](https://raw.githubusercontent.com/yunba/docs/master/image/productpng_portal_publish_statistic.png)

## 在 Portal 查看用户在线信息实时报表

打开应用详情页面，点击在线用户统计可以查看当前在线用户数，用户活跃数等信息，如图所示:  

![productpng_portal_online_statistic.png](https://raw.githubusercontent.com/yunba/docs/master/image/productpng_portal_online_statistic.png)

## 在 Portal 上传APNs证书以激活APN推送功能

打开编辑应用页面，点击 “选择文件” 按钮，上传证书文件(*.p12)。如果证书导出时设置了密码，则需将密码填写在 “证书密码” 一栏。

此外，iOS SDK 还支持 “[多证书](ios_kb_multiple_certificates.md)” 功能。用户可以点击左侧的 “添加更多证书” 的按钮，上传多个证书（暂无数量限制），从而实现对同一应用的不同版本进行推送。

证书添加完成后，请点击 “更新” 按钮，保存设置。

![ios_create_new_app.png](https://raw.githubusercontent.com/yunba/docs/master/image/iospng_portal_add_certificate.png)

### 生成APNs证书的步骤如下：
1. 打开 Apple 开发者网站的 [证书管理界面](https://developer.apple.com/account/ios/certificate/)

2. 点击新建证书  

	![iospng_cert_creat_certificate.png](https://raw.githubusercontent.com/yunba/docs/master/image/iospng_cert_creat_certificate.png)

3. 选择新建证书的类型（开发或者生产推送环境）

	![iospng_cert_choose_type.png](https://raw.githubusercontent.com/yunba/docs/master/image/iospng_cert_choose_type.png)

	注：如果你的系统中没有中间签名证书，则需要下载并安装到你的系统中：  

	![iospng_cert_before_create_certificate.png](https://raw.githubusercontent.com/yunba/docs/master/image/iospng_cert_before_create_certificate.png)

4. 为证书选择对应需要推送功能的APP ID:  

	![iospng_cert_choose_appid.png](https://raw.githubusercontent.com/yunba/docs/master/image/iospng_cert_choose_appid.png)

5. 为制作推送证书，需要有一个CSR文件用以使新生成的推送证书与私钥相匹配：

	![iospng_cert_upload_csr.png](https://raw.githubusercontent.com/yunba/docs/master/image/iospng_cert_upload_csr.png)
	
	注：如果你没有CSR文件，则需要新建一个，步骤如下：  

	a. 打开钥匙串访问  

	![iospng_cert_open_key_chain.png](https://raw.githubusercontent.com/yunba/docs/master/image/iospng_cert_open_key_chain.png)
	
	b. 申请创建CSR文件  

	![iospng_cert_creat_csr.png](https://raw.githubusercontent.com/yunba/docs/master/image/iospng_cert_creat_csr.png)
	
	c. 输入你的邮件等信息后保存CSR文件到本地磁盘  

	![iospng_cert_save_csr.png](https://raw.githubusercontent.com/yunba/docs/master/image/iospng_cert_save_csr.png)

6. 点击继续之后生成APNs证书成功，然后点击下载即可  

	![iospng_cert_download_certifcate.png](https://raw.githubusercontent.com/yunba/docs/master/image/iospng_cert_download_certifcate.png)
7. 导出证书为p12文件

	![iospng_cert_export_certificate.png](https://raw.githubusercontent.com/yunba/docs/master/image/iospng_cert_export_certificate.png)

	导出时可以添加自定义密码以提高安全性:

	![iospng_cert_p12_password.png](https://raw.githubusercontent.com/yunba/docs/master/image/iospng_cert_p12_password.png)

8. 制作完成后，在 Portal 上传APNs证书以激活APN推送功能
	
9. 注意：*如果你使用生产和开发证书两个证书来进行推送：云巴会在使用的过程中根据 SDK 上报的状态自动选择生产或者开发证书，也就是说消息会推送给使用开发或生产证书的所有设备。如果需要仅发送给一个证书下的app的话建议在推送前删除另一个证书。*
