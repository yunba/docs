# 配置远程通知服务

本文结合云巴 iOS SDK 的使用，翻译了 iOS 官方开发文档的 [Configuring Remote Notification Support](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/HandlingRemoteNotifications.html#//apple_ref/doc/uid/TP40008194-CH6-SW1) 章节的部分内容。

由于专业术语的翻译可能会引起理解上的偏差，请以 iOS 官方开发文档为准。文档如有任何谬误不妥之处，欢迎指正。

借助远程通知服务，可以向用户即时推送最新的消息，且无需担心用户是否正在使用该应用。远程通知功能的大部分程序都由云巴服务器进行处理，应用方面需要做如下配置：

- 打开应用的推送通知（Push Notification）功能；
- 在应用的启动代码中注册 APNs；
- 实现远程通知的处理代码；

## 打开推送通知功能

要处理远程通知，应用必须先取得与 APNs 通讯的权限。在 Xcode 工程的 Capability 面板中添加这一功能权限。

**流程**

- 在工程导航中选择你的工程；
- 在编辑器的 TARGETS 中选择你的应用；
- 点击右侧的 Capability 标签页；
- 打开推送通知（Push Notification）功能权限；

按上面的做法，Xcode 就会把所需的权限加进你的工程中。

如果未添加相应的功能权限，应用会在应用商店审核阶段被驳回。测试期间，如果没有相应的权限，注册 APNs 时会返回错误。


## 通过注册接受远程通知

App 每次启动的时候，都必须注册 APNs。大致上这分为两步：

1. 获取 App 的 Device Token
2. 将 Device Token 通过 API 发送到云巴的服务器

不同设备不同 App 的 Device Token 都是唯一的。云巴的 SDK 通过获得 Device token 来与 App 进行 APNs 通信，每一个 APNs data 都包含了 Device Token 作为识别。

永远不要储存 Device Token，需要的时候再将它从系统中获取，通过云巴 SDK 进行上传。虽然 Device Token 对每个设备每个 App 都是唯一的，但是它随时可能改变。它有可能在任何时候发生变化：也许是用户恢复备份时、用户把 App 安装到一个新的设备时、用户重装系统时等等，但是能够保障其唯一性。从系统获取 Device Token（而不是从储存的 Device Token 获取）才能保证 APNs 的正常使用。另外，如果 Device Token 没有变化，获取过程是很快的，并不会导致任何明显的耽误。

**重要：当 Device Token 发生变化时，用户只有启动一次应用才能再次接收到 APNs 通知。**


## 获取 Device Token

你需要通过调用 `UIApplication registerForRemoteNotifications` 函数来获取 Device Token。推荐的做法是将调用这个函数加入你正常启动 App 需要的程序之中。第一次调用这个函数时，App 会向 APNs 请求 Device Token。初始化完成后，App 只在 Device Token 发生变化时才会与 APNs 连接。其他时候，获取 Device Token 都非常快。


The app object notifies its delegate asynchronously upon the successful or unsuccessful retrieval of the device token. You use these delegate callbacks to process the device token or to handle any errors that arose. You must implement the following delegate methods to track whether registration was successful:


当 App 获取 Device Token （不管是成功还是失败），app delegate 的对应代理方法都会被同步调用。需使用这些代理方法回调来处理 Device Token 或者处理 Errors。你需要实现下面的代理方法来追溯注册的成败：

- 使用 `application:didRegisterForRemoteNotificationsWithDeviceToken:` 来获得 Device Token 并上传到云巴服务器。
- 使用 `application:didFailToRegisterForRemoteNotificationsWithError:` 对错误进行回应。

**注意：如果 Device Token 发生了变化且你的 App 正在运行，App 会再次调用相对应的代理方法来告诉你 Device Token 发生了变化。**

下面的代码演示了如何获取 Device Token， App 代理调用 `registerForRemoteNotifications` 方法作为初始化调用。当获取到 Device Token 后，调用 `application:didRegisterForRemoteNotificationsWithDeviceToken:` 方法让你有机会将它上传到云巴的服务器。如果在此期间发生了任何错误，App 会暂时禁用所有远程推送相关的特性，直到收到正确的 Device Token 为止。

    - (void)applicationDidFinishLaunching:(UIApplication *)app {
        // Configure the user interactions first.
        [self configureUserInteractions];
     
       // Register for remote notifications.
        [[UIApplication sharedApplication] registerForRemoteNotifications];
    }
     
    // Handle remote notification registration.
    - (void)application:(UIApplication *)app
            didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)devToken {
        // Forward the token to your server.
        [self enableRemoteNotificationFeatures];
        [self forwardTokenToServer:devTokenBytes];
    }
     
    - (void)application:(UIApplication *)app
            didFailToRegisterForRemoteNotificationsWithError:(NSError *)err {
        // The token is not currently available.
        NSLog(@"Remote notification support is unavailable due to error: %@", err);
        [self disableRemoteNotificationFeatures];
    }

    func application(_ application: UIApplication,
                     didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool {
        // Configure the user interactions first.
        self.configureUserInteractions()
        
        // Register with APNs
        UIApplication.shared.registerForRemoteNotifications()
    }
     
    // Handle remote notification registration.
    func application(_ application: UIApplication,
                     didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data){
        // Forward the token to your server.
        self.enableRemoteNotificationFeatures()
        self.forwardTokenToServer(token: deviceToken)
    }
     
    func application(_ application: UIApplication,
                     didFailToRegisterForRemoteNotificationsWithError error: Error) {
        // The token is not currently available.
        print("Remote notification support is unavailable due to error: \(error.localizedDescription)")
        self.disableRemoteNotificationFeatures()
    }

如果蜂窝数据或 Wi-Fi 无法连接，不管是 `application:didRegisterForRemoteNotificationsWithDeviceToken:` 还是 `application:didFailToRegisterForRemoteNotificationsWithError:` 都不会被调用。当 Wi-Fi 连接上时，也经常会发生因端口配置而无法连接到 APNs 的情况。这种情况下，用户可以转移到其他不会阻拦请求端口 Wi-Fi 网络环境下。而蜂窝数据的情况，用户同样可以等到蜂窝数据服务能使用为止。



## 处理远程通知

User Notification 框架把所有涉及到本地和远程通知相关的操作代码都集中到了一个地方。

具体来说，可以用同样的方法处理下面的事情：

- 如果应用在前台运行，可以直接收通知，并静默该通知；
- 如果应用在后台，且未运行：
  - 可以在用户选择了一个与通知有关的自定义操作时做出响应；
  - 可以在用户忽略掉通知，或者登录了应用时做出响应；

除了借助 User Notification 框架提供的方法来处理通知以外，应用还可以通过它的 app delegate 接收整个远程通知的负载。当一个远程通知到达时，如果应用在后台，系统会正常处理用户的交互。系统也会把通知的负载发给 iOS 的 app delegate 的 `application:didReceiveRemoteNotification:fetchCompletionHandler:` 方法。你可以用这个方法检测负载，并执行相应的操作。例如，收到一个静默的远程通知后，可以执行应用更新的下载。

有关如何使用 User Notifications 框架的方法处理通知，详见 [Responding to the Delivery of Notifications](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/SchedulingandHandlingLocalNotifications.html#//apple_ref/doc/uid/TP40008194-CH5-SW14)。

有关如何在 app delegate 里处理通知，详见 [UIApplicationDelegate Protocol Reference](https://developer.apple.com/reference/uikit/uiapplicationdelegate) 或 [NSApplicationDelegate Protocol Reference](https://developer.apple.com/reference/appkit/nsapplicationdelegate)。



