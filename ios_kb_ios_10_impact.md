# iOS 10 更新的影响

Apple 发布 iOS 10，带来了一系列的 [新特性](https://developer.apple.com/library/content/releasenotes/General/WhatsNewIniOS/Articles/iOS10.html)。
在消息推送方面，引入了 User Notifications 框架进行本地和远程通知的传送和处理。

本文阐述这次更新对云巴 iOS 产品的影响。

## 云巴 iOS SDK Demo 出现 Warning

由于 iOS 10 舍弃了部分原有的 API，用户在运行云巴 iOS Demo（1.7.3）时，会出现 Warning。

例如，授权接收通知的逻辑（authorization API）被舍弃，需要使用 UNUserNotificationCenter。

我们针对 iOS 的不同版本对云巴 iOS Demo 中的`registerRemoteNotification`作了修改（详见下方代码）。为了兼容旧的版本，Demo 中的 Warning 暂时无法全部消除。

另：经测试，在 iOS 升级为 10 以后，云巴 iOS Demo 不进行任何修改，使用原有 API 也可以正常编译运行、正常接收推送。**部分设备升级后收不到 APNs，可能是 Device Token 改变引起的。请参考 [iOS FAQ 第 12 条](https://yunba.io/docs/ios_faq#12)**。

```
@import UserNotifications;
```


```
- (void)registerRemoteNotification {
    // register for remote notification(APNs)     注册APNs，申请获取device token
    if ([[[UIDevice currentDevice] systemVersion] floatValue] >= 10.0) 
    {
        UNUserNotificationCenter* center = [UNUserNotificationCenter currentNotificationCenter];
        [center requestAuthorizationWithOptions:(UNAuthorizationOptionAlert + UNAuthorizationOptionSound + 
         UNAuthorizationOptionBadge  completionHandler:^(BOOL granted, NSError * _Nullable error) 
         {
            granted ? NSLog(@"author success!") : NSLog(@"author failed!");
        }];
        [[UIApplication sharedApplication] registerForRemoteNotifications];
    }
    else if ([[[UIDevice currentDevice] systemVersion] floatValue] < 10.0 &&
             [[[UIDevice currentDevice] systemVersion] floatValue] >= 8.0)
    {
        [[UIApplication sharedApplication] registerUserNotificationSettings:[UIUserNotificationSettings
                                                                             settingsForTypes:(UIUserNotificationTypeSound | 
                                                                             UIUserNotificationTypeAlert | 
                                                                             UIUserNotificationTypeBadge) categories:nil]];
        [[UIApplication sharedApplication] registerForRemoteNotifications];
    }
    else
    {
        [[UIApplication sharedApplication] registerForRemoteNotificationTypes:( UIRemoteNotificationTypeBadge | 
                                                                                UIRemoteNotificationTypeSound |
                                                                                UIRemoteNotificationTypeAlert)];
    }
}
```


