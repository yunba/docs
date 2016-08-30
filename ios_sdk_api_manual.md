# YunBa iOS SDK API 手册 

## setup

### 功能
初始化 YunBa SDK。

### 函数原型
`+ (BOOL)setupWithAppkey:(NSString *)appkey;`

`+ (BOOL)setupWithAppkey:(NSString *)appkey option:(YBSetupOption *)option;`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
appkey | NSString* | 在云巴 Portal 申请到的应用的 [AppKey](product_kb_app_key.md)。
option | YBSetupOption* | 选项。可包含用于获取订阅权限的密钥 subscribeKey，用于获取发布权限的密钥 publishKey，用于获取管理权限的密钥 secretKey（切勿外泄）和用于 Access Manager 模块中权限管理的动态密钥）authorizeKey。

### 返回值
- YES：初始化成功，开始尝试连接。
- NO：初始化失败，参数错误。

### 代码示例
```objectivec
BOOL succ = [YunBaService setupWithAppkey:appkey option:option];
if (succ) {
    NSLog(@"setup succ %@:%@", appkey, option);
} else {
    NSLog(@"setup failed %@:%@", appkey, option);
}
```

## subscribe

### 功能
增加订阅一个 [频道](product_kb_topic_and_alias.md)。成功订阅后，App 可以收到来自该频道的消息。新增订阅不会影响已有的订阅。

### 函数原型
`+ (void)subscribe:(NSString *)topic resultBlock:(YBResultBlock)resultBlock;`

`+ (void)subscribe:(NSString *)topic qos:(UInt8)qosLevel resultBlock:(YBResultBlock)resultBlock;`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | ----
topic | NSString* | App 订阅的频道。topic 只支持英文数字下划线，长度不超过 50 个字符。
qosLevel | NSString* | 服务质量等级。具体参考 [QoS](product_kb_qos.md) 说明。
resultBlock | YBResultBlock | API 回调接口。可通过返回的 BOOL succ 判断结果成功与否；通过 NSError *error 获取错误信息。

### 返回值
None

### 代码示例
```objectivec
[YunBaService subscribe:topic qos:qosLevel resultBlock:^(BOOL succ, NSError *error) {
    if (succ) {
        NSLog(@"subscribe to topic succ: %@", topic);
    } else {
        NSLog(@"subscribe to topic failed due to : %@, recovery suggestion: %@", error, [error localizedRecoverySuggestion]);
    }
}];
```

## unsubscribe

### 功能
取消对某个 [频道](product_kb_topic_and_alias.md) 的订阅。成功取消后，就不会再收到来自该频道的消息。

### 函数原型
`+ (void)unsubscribe:(NSString *)topic resultBlock:(YBResultBlock)resultBlock;`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | ----
topic | NSString* | App 取消订阅的频道，topic 只支持英文数字下划线，长度不超过 50 个字符。
resultBlock | YBResultBlock | API 回调接口。可通过返回的 BOOL succ 判断结果成功与否；通过 NSError *error 获取错误信息。

### 返回值
None

### 代码示例
```objectivec
[YunBaService unsubscribe:topic resultBlock:^(BOOL succ, NSError *error) {
    if (succ) {
        NSLog(@"unsubscribe to topic succ: %@", topic);
    } else {
        NSLog(@"unsubscribe to topic failed due to: %@, recovery suggestion: %@", error, [error localizedRecoverySuggestion]);
    }
}];
```

## publish

### 功能
向某个 [频道](product_kb_topic_and_alias.md) 发布消息。成功发布后，所有订阅此频道的客户端都会收到消息。

### 函数原型
`+ (void)publish:(NSString *)topic data:(NSData *)data resultBlock:(YBResultBlock)resultBlock;`

`+ (void)publish:(NSString *)topic data:(NSData *)data option:(YBPublishOption *)option resultBlock:(YBResultBlock)resultBlock;`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | ----
topic | NSString* | App 发布消息的频道。只支持英文数字下划线，长度不超过 50 个字符。
data | NSData* | 待发布的消息。
option | YBPublishOption* | 选项。可包含 [QoS](product_kb_qos.md) 和 retained（暂未支持）属性。
resultBlock | YBResultBlock | API 回调接口。可通过返回的 BOOL succ 判断结果成功与否；通过 NSError *error 获取错误信息。

### 返回值
None

### 代码示例
```objectivec
[YunBaService publish:topic data:data option:option resultBlock:^(BOOL succ, NSError *error) {
    if (succ) {
        NSLog(@"publish to topic: %@ data: %@ succ", topic, data);
    } else {
        NSLog(@"publish to topic failed due to: %@, recovery suggestion: %@", error, [error localizedRecoverySuggestion]);
    }
}];
```

## publish2

### 功能
`publish2`是`publish`的升级版，二者都用于向某个 [频道](product_kb_topic_and_alias.md) 发布消息，`publish2`可以带有其他参数，如 APN 选项。发布成功后，所有订阅此频道的客户端都会收到消息。

### 函数原型
`+ (void)publish2:(NSString *)topic data:(NSData *)data resultBlock:(YBResultBlock)resultBlock;`

`+ (void)publish2:(NSString *)topic data:(NSData *)data option:(YBPublish2Option *)option resultBlock:(YBResultBlock)resultBlock;`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | ----
topic | NSString* | App 发布消息的频道。只支持英文数字下划线，长度不超过 50 个字符。
data | NSData* | 待发布的消息。
option | YBPublish2Option* | 选项。可包含 YBApnOption 和 timeToLive 属性。
resultBlock | YBResultBlock | API 回调接口。可通过返回的 BOOL succ 判断结果成功与否；通过 NSError *error 获取错误信息。

### 返回值
None

### 代码示例
```objectivec
[YunBaService publish2:topic data:data option:option resultBlock:^(BOOL succ, NSError *error) {
    if (succ) {
        NSLog(@"publish2 to topic: %@ data: %@ succ", topic, data);
    } else {
        NSLog(@"publish2 to topic failed due to: %@, recovery suggestion: %@", error, [error localizedRecoverySuggestion]);
    }
}];
```


## publishToAlias

### 功能
向某个 [别名](product_kb_topic_and_alias.md) 发布消息。发布成功后，该别名的客户端会收到消息。

### 函数原型
`+ (void)publishToAlias:(NSString *)alias data:(NSData *)data resultBlock:(YBResultBlock)resultBlock;`

`+ (void)publishToAlias:(NSString *)alias data:(NSData *)data option:(YBPublishOption *)option resultBlock:(YBResultBlock)resultBlock;`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | ----
alias | NSString* | 目标用户的别名。只支持英文数字下划线，长度不超过 50 个字符。
data | NSData* | 待发布的消息。
option | YBPublishOption* | 选项，可包含 [QoS](product_kb_qos.md) 和 retained （暂未支持）属性。
resultBlock | YBResultBlock | API 回调接口。可通过返回的 BOOL succ 判断结果成功与否；通过 NSError *error 获取错误信息。

### 返回值
None

### 代码示例
```objectivec
[YunBaService publishToAlias:alias data:data option:option resultBlock:^(BOOL succ, NSError *error) {
    if (succ) {
        NSLog(@"publish to alias: %@ data: %@ succ", alias, data);
    } else {
        NSLog(@"publish to alias failed due to: %@, recovery suggestion: %@", error, [error localizedRecoverySuggestion]);
    }
}];
```

## publish2ToAlias

### 功能
`publish2ToAlias`是`publishToAlias`的升级版，二者都用于向 [别名](product_kb_topic_and_alias.md) 发布一对一的消息，`publish2ToAlias`可以带有其他参数，如 APN 选项。发布成功后，该别名的客户端会收到消息。

### 函数原型
`+ (void)publish2ToAlias:(NSString *)alias data:(NSData *)data option:(YBPublish2Option *)option resultBlock:(YBResultBlock)resultBlock;`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | ----
alias | NSString* | 目标用户的别名。只支持英文数字下划线，长度不超过 50 个字符。
data | NSData* | 待发布的消息。
option | YBPublish2Option* | 选项。可包含 YBApnOption 和 timeToLive 属性。
resultBlock | YBResultBlock | API 回调接口。可通过返回的 BOOL succ 判断结果成功与否；通过 NSError *error 获取错误信息。

### 返回值
None

### 代码示例
```objectivec
[YunBaService publish2ToAlias:alias data:data option:option resultBlock:^(BOOL succ, NSError *error) {
    if (succ) {
        NSLog(@"publish2 to alias: %@ data: %@ succ", alias, data);
    } else {
        NSLog(@"publish2 to alias failed due to: %@, recovery suggestion: %@", error, [error localizedRecoverySuggestion]);
    }
}];
```

## subscribePresence

### 功能
订阅某 [频道](product_kb_topic_and_alias.md) 下用户行为的事件通知，事件包括上线、下线、订阅频道和取消订阅频道。

### 函数原型
`+ (void)subscribePresence:(NSString *)topic resultBlock:(YBResultBlock)resultBlock;`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | ----
topic | NSString* | App 订阅的目标用户所在的频道。只支持英文数字下划线，长度不超过 50 个字符。
resultBlock | YBResultBlock | API 回调接口。可通过返回的 BOOL succ 判断结果成功与否；通过 NSError *error 获取错误信息。

### 返回值
None

### 代码示例
```objectivec
[YunBaService subscribePresence:topic resultBlock:^(BOOL succ, NSError *error) {
    if (succ) {
        NSLog(@"subscribe presence to topic:%@ succ", topic);
    } else {
        NSLog(@"subscribe presence failed due to : %@, recovery suggestion: %@", error, [error localizedRecoverySuggestion]);
    }
}];
```

## unsubscribePresence

### 功能
取消订阅某 [频道](product_kb_topic_and_alias.md) 下用户行为的事件通知，事件包括上线、下线、订阅频道、取消订阅频道。

### 函数原型
`+ (void)unsubscribePresence:(NSString *)topic resultBlock:(YBResultBlock)resultBlock;`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | ----
topic | NSString* | App 希望取消订阅的对象所在的频道。只支持英文数字下划线，长度不超过 50 个字符。
resultBlock | YBResultBlock | API 回调接口。可通过返回的 BOOL succ 判断结果的成功与否；通过 NSError *error 获取错误信息。

### 返回值
None

### 代码示例
```objectivec
[YunBaService unsubscribePresenceToTopic:topic resultBlock:^(BOOL succ, NSError *error) {
    if (succ) {
        NSLog(@"unsubscribe presence to topic:%@ succ", topic);
    } else {
        NSLog(@"unsubscribe presence failed due to : %@, recovery suggestion: %@", error, [error localizedRecoverySuggestion]);
    }
}];
```

## getAliasList

### 功能
获取某 [频道](product_kb_topic_and_alias.md) 下的所有订阅者的 [别名](product_kb_topic_and_alias.md) 列表，别名个数及状态。

### 函数原型
`+ (void)getAliasList:(NSString *)topic resultBlock:(YBArrayCountResultBlock)arrayCountResultBlock;`

`+ (void)getAliasList:(NSString *)topic disableState:(BOOL)disableState disableAlias:(BOOL)disableAlias resultBlock:(YBArrayCountResultBlock)arrayCountResultBlock;`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | ----
topic | NSString* | 目标频道。
disableState | BOOL | 结果是否排除别名状态信息。
disableAlias | BOOL | 结果是否排除别名列表。
arrayCountResultBlock | YBArrayCountResultBlock | API 回调接口。可通过 NSError *error 判断结果成功与否，并获取错误信息；通过 NSArray *resArray 获取别名及状态列表；通过 size_t resCount 获取别名数量。

### 返回值
None

### 代码示例
```objectivec
[YunBaService getAliasList:testTopic disableState:NO disableAlias:NO resultBlock:^(NSArray *resArray, size_t resCount, NSError *error) {
    if (error.code == kYBErrorNoError) {
        NSLog(@"get alias list[%@] of topic[%@] succ", res, testTopic);
    } else {
        NSLog(@"get alias list failed due to: %@, recovery suggestion: %@", error, [error localizedRecoverySuggestion]);
    }
}];
```

## getTopicList

### 功能
获取某个用户所订阅的 [频道](product_kb_topic_and_alias.md) 列表。

### 函数原型
`+ (void)getTopicList:(YBArrayResultBlock)stringResultBlock;`

`+ (void)getTopicList:(NSString *)alias resultBlock:(YBArrayResultBlock)arrayResultBlock;`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | ----
alias | NSString* | 目标用户的 [别名](product_kb_topic_and_alias.md) 。
arrayResultBlock | YBArrayResultBlock | API 回调接口。可通过 NSError *error 判断结果成功与否，并获取错误信息；通过 NSArray *res 获取频道列表。

### 返回值
None

### 代码示例
```objectivec
[YunBaService getTopicList:alias resultBlock:^(NSArray *res, NSError *error) {
    if (error.code == kYBErrorNoError) {
        NSLog(@"get topic list[%@] of alias[%@] succ", res, alias);
    } else {
        NSLog(@"get topic list failed due to: %@, recovery suggestion: %@", error, [error localizedRecoverySuggestion]);
    }
}];
```

## report

### 功能
**暂未支持。** 上报客户端的行为。如，打开通知栏次数、按钮点击次数、资源下载成功次数等行为。

### 函数原型
`+ (void)report:(NSString *)action withData:(NSData *)data;`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | ----
action | NSString* | App 需要统计的行为，如打开通知栏，资源下载成功等。
data | NSData * | 对应行为的附加数据，以满足统计相关的其他业务需求。

### 返回值
None

### 代码示例
```objectivec
[YunBaService report:action withData:data];
```

## getState

### 功能
获取用户的在线状态。

### 函数原型
`+ (void)getState:(NSString *)alias resultBlock:(YBStringResultBlock)stringResultBlock;`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | ----
alias | NSString* | 目标用户的 [别名](product_kb_topic_and_alias.md) 。
stringResultBlock | YBStringResultBlock | API 回调接口。可通过 NSError *error 判断结果成功与否，并获取错误信息。

### 返回值
None

### 代码示例
```objectivec
[YunBaService getState:alias resultBlock:^(NSString *res, NSError *error) {
    if (error.code == kYBErrorNoError) {
        NSLog(@"get state[%@] of alias[%@] succ", res, alias);
    } else {
        NSLog(@"get state of alias failed due to: %@, recovery suggestion: %@", error, [error localizedRecoverySuggestion]);
    }
}];
```

## setAlias

### 功能
用来设置用户名，即绑定账号。每个用户只能指定一个 [别名](product_kb_topic_and_alias.md)。

### 函数原型
`+ (void)setAlias:(NSString *)alias resultBlock:(YBResultBlock)resultBlock;`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | ----
alias | NSString* | 用户设置的别名信息。只支持英文数字下划线，长度不超过 50 个字符。
resultBlock | YBResultBlock | API 回调接口。可通过返回的 BOOL succ 判断结果成功与否；通过 NSError *error 获取错误信息。

### 返回值
None

### 代码示例
```objectivec
[YunBaService setAlias:alias resultBlock:^(BOOL succ, NSError *error) {
    if (succ) {
        NSLog(@"set alias[%@] succ", alias);
    } else {
        NSLog(@"set alias failed due to : %@, recovery suggestion: %@", error, [error localizedRecoverySuggestion]);
    }
}];
```

## getAlias

### 功能
获取当前用户的 [别名](product_kb_topic_and_alias.md)。

### 函数原型
`+ (void)getAlias:(YBStringResultBlock)stringResultBlock;`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | ----
stringResultBlock | YBStringResultBlock | API 回调接口。可通过返回的 NSError *error 获取错误信息。

### 返回值
None

### 代码示例
```objectivec
[YunBaService getAlias:^(NSString *res, NSError *error) {
    if (error.code == kYBErrorNoError) {
        NSLog(@"get alias succ:%@", res);
    } else {
        NSLog(@"get alias failed due to : %@, recovery suggestion: %@", error, [error localizedRecoverySuggestion]);
    }
}];
```

## storeDeviceToken

### 功能
App 将 Device Token 存储在 YunBa 的云端，即可[通过 YunBa 发送 APNs 通知](ios_kb_apns_implementation.md)。


### 函数原型
`+ (void)storeDeviceToken:(NSData *)token resultBlock:(YBResultBlock)resultBlock;`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | ----
token | NSData* | 向 APNs 注册所获取到的设备的 Device Token。
resultBlock | YBResultBlock | API 回调接口。可通过返回的 BOOL succ 判断结果成功与否；通过 NSError *error 获取错误信息。

### 返回值
None

### 代码示例

* 向 APNs 注册，申请获取 Device Token:

```objectivec
[[UIApplication sharedApplication] registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
```

* 上传 Device Token:

```objectivec
- (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {
    NSLog(@"get Device Token: %@", [NSString stringWithFormat:@"Device Token: %@", deviceToken]);
    [YunBaService storeDeviceToken:deviceToken resultBlock:^(BOOL succ, NSError *error) {
        if (succ) {
            NSLog(@"store device token to YunBa succ");
        } else {
            NSLog(@"store device token to YunBa failed due to : %@", error);
        }
    }];
}
```

## 附录

### 错误信息

名称 | 说明
---- | ----
kYBErrorNoError | 成功
kYBErrorInternalError | 云巴服务器内部错误
kYBErrorTimeoutError | 操作超时
kYBErrorServiceNotSetup | 云巴服务尚未初始化
kYBErrorArgumentIllegal | 非法参数
kYBErrorNetworkError | 网络错误
kYBErrorOperationInterrupted |调用被中断了，可能由于云巴服务在启用时关闭了或是上传了两次 Token 等原因

