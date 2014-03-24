# [](#id_anchor_01)导入 SDK 
下载的 lib文件夹加入到项目工程中。

#[](#id_anchor_02) 添加使用代码
初始化 YunBa SDK。请在您的代码入口函数didFinishLaunchingWithOptions处添加如下代码：

    [YunBaService setupWithAppkey:AppKey LogLevel:kYBLogLevelDebug];


#[](#id_anchor_03) 添加通知监听代码接受 Publish 消息

    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(onMessageReceived:) name:kMbDidReceiveMessageNotificationKey object:nil];


##[](#id_anchor_03_01)  自定义消息处理函数处理 Publish 消息代码示例  

    - (void) onMessageReceived:(NSNotification *)notification {
        mbMessage *message = [notification object];
        NSLog(@"new message, %zu bytes, topic=%@", (unsigned long)[[message data] length], [message topic]);
        NSString *payloadString = [[NSString alloc] initWithData:[message data] encoding:NSUTF8StringEncoding];
        NSLog(@"data: %@ %@", payloadString,[message data]);
        NSString *curMsg = [NSString stringWithFormat:@"%@ => %@", [message topic], payloadString];
        NSLog(@"received message: %@", curMsg);
    }


#[](#id_anchor_04) API - subscribe

###[](#id_anchor_04_01) 功能
App 可以增加订阅一个Topic, 以便可以接收来自 Topic 的 Message。

###[](#id_anchor_04_02) 函数原型

     + (void)subscribeToTopic:(NSString*)topic atLevel:(UInt8)qosLevel resultBlock:(MbResultBlock)resultBlock;

###[](#id_anchor_04_03) 参数说明
* topic: app 订阅的的主题数，topic 只支持英文数字下划线，长度不超过50个字符。
* qosLevel: 服务质量等级，具体参考服务质量等级说明。
* resultBlock: API 回调接口， 可通过返回的BOOL succ判断结果的成功与否, NSError *error获取错误原因。

Code Example

    [YunBaService subscribeToTopic:topic atLevel:qosLevel resultBlock:^(BOOL succ, NSError *error){
        if (succ) {
            [self addMsgToTextView:[NSString stringWithFormat:@"[Demo] subscribe to topic(%@) succeed", topic]];
        } else {
            [self addMsgToTextView:[NSString stringWithFormat:@"[Demo] subscribe to topic(%@) failed: %@, recovery suggestion: %@", topic, error, [error localizedRecoverySuggestion]]];
        }
    }];

#[](#id_anchor_05) API - unsubscribe

###[](#id_anchor_05_01) 功能
App 可以取消订阅一个 Topic, 以便取消接收来自 Topic 的 Message.

###[](#id_anchor_05_02) 函数原型


     + (void)unsubscribeTopic:(NSString*)theTopic resultBlock:(MbResultBlock)resultBlock;


###[](#id_anchor_06_03) 参数说明
* topics: app 订阅的的主题数组，topic 只支持英文数字下划线，长度不超过50个字符。
* resultBlock: API 回调接口， 可通过返回的BOOL succ判断结果的成功与否, NSError *error获取错误原因。

Code Example

    [YunBaService unsubscribeTopic:topic resultBlock:^(BOOL succ, NSError *error){
        if (succ) {
            [self addMsgToTextView:[NSString stringWithFormat:@"[Demo
unsubscribe to topic(%@) succeed", topic]];
        } else {
            [self addMsgToTextView:[NSString stringWithFormat:@"[Demo
unsubscribe to topic(%@) failed: %@, recovery suggestion: %@", topic, error, [error localizedRecoverySuggestion]]];
        }
    }];



#[](#id_anchor_06) API - Publish

###[](#id_anchor_06_01) 功能
App 可以向 Topic 发送消息, 那么任何订阅此 Topic 的 Client 都会接受到消息。

###[](#id_anchor_06_02) 函数原型

     + (void)publishData:(NSData*)data ToTopic:(NSString*)topic atLevel:(UInt8)qosLevel retain:(BOOL)retainFlag resultBlock:(MbResultBlock)resultBlock;

###[](#id_anchor_06_03) 参数说明
* topic: app 待发布消息的主题，只支持英文数字下划线，长度不超过50个字符。
* message: 向对应 topic 的订阅者发布的消息。
* resultBlock: API 回调接口， 可通过返回的BOOL succ判断结果的成功与否, NSError *error获取错误原因。

Code Example


    [YunBaService publishData:data ToTopic:topic atLevel:qosLevel retain:isRetained
                  resultBlock:^(BOOL succ, NSError *error){
        if (succ) {
            [self addMsgToTextView:[NSString stringWithFormat:@"[Demo] publish data(%@) to topic(%@) succeed", [_pubContentText text], topic]];
        } else {
            [self addMsgToTextView:[NSString stringWithFormat:@"[Demo] publish data(%@) to topic(%@) failed: %@, recovery suggestion: %@", [_pubContentText text], topic, error,  [error localizedRecoverySuggestion]]];
        }
    }];


#[](#id_anchor_07) API - Store DeviceToken

###[](#id_anchor_07_01) 功能
App 将DeviceToken 存储在YunBa的云端， 那么可以通过YunBa发送APNs通知。

###[](#id_anchor_07_02) 函数原型

     + (void)storeDeviceToken:(NSData *)token resultBlock:(YBResultBlock)resultBlock;

###[](#id_anchor_07_03) 参数说明
* token: 通过注册APNs而获取到的对应设备的device token。
* resultBlock: API 回调接口， 可通过返回的BOOL succ判断结果的成功与否, NSError *error获取错误原因。

Code Example

    [[UIApplication sharedApplication] registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];     //注册APNs，申请获取device token

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
