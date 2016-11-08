配置远程通知服务

本文结合云巴 iOS SDK 的使用，翻译了 iOS 官方开发文档的 Configuring Remote Notification Support 章节的部分内容。

由于专业术语的翻译可能会引起理解上的偏差，请以 iOS 官方开发文档为准。文档如有任何谬误不妥之处，欢迎指正。

借助远程通知服务，可以向用户即时推送最新的消息，且无需担心用户是否正在使用该应用。远程通知功能的大部分程序都由云巴服务器进行处理，应用方面需要做如下配置：

- 打开应用的推送通知（Push Notification）功能；
- 在应用的启动代码中注册 APNs；
- 实现远程通知的处理代码；

打开推送通知功能

要处理远程通知，应用必须先取得与 APNs 通讯的权限。在 Xcode 工程的 Capability 面板中添加这一功能权限。

流程

- 在工程导航中选择你的工程；
- 在编辑器的 TARGETS 中选择你的应用；
- 点击右侧的 Capability 标签页；
- 打开推送通知（Push Notification）功能权限；

按上面的做法，Xcode 就会把所需的权限加进你的工程中。

如果未添加相应的功能权限，应用会在应用商店审核阶段被驳回。测试期间，如果没有相应的权限，注册 APNs 时会返回错误。



通过注册接受远程通知

每次你的 app 启动的时候，都必须注册 APNs。大致上这分为两步：

1. 获取 app 的 device token
2. 将 device token 通过 API 发送到云巴的服务器

每个设备每个 app 的 device token 都是唯一的。云巴的 SDK 通过获得 Device token 来与 app 进行 APNs 通信，每一个 APNs data 都包含了 device token 作为识别。

永远不要储存 device token ，需要的时候再将他从系统中获取，通过云巴 SDK 进行上传。虽然 device token 对每个设备每个 app 都是唯一的，但是它是会随时改变的。也许是用户恢复备份时，也许用户安装你的 app 到一个新的设备时，也许用户重新安装了系统时，它有可能在任何时候发生变化，但是唯一性是能够被保障的。从系统获取 token （而不是从储存的 device token 获取）才能保证 APNs 的正常使用。另外，如果 device token 没有变化，获取它是很快的，并不会导致任何明显的耽误。

重要：

当 device token 发生变化，用户需要启动你的 app 一次 APNs 通知才能再次被获取到



获取 Device Token

你需要通过调用 UIApplication registerForRemoteNotifications 函数来获取 Device Token。推荐的做法是将调用这个函数加入你正常启动 app 需要的程序之中。第一次当你的 app 调用这个函数，app 会向 APNs 请求 device token 。当初始化完成，你的 app 只在 device token 发生变化的情况才会与 APNs 连接。其他时候，获取 token 都是非常快速的。

当 app 获取 device token （不管是成功还是失败），Appdelegate  的对应代理方法都会被同步调用。你使用这些代理方法回调来处理 device token 或者处理 errors。你需要实现下面的代理方法来进行处理，不管是成功还是失败：

- 使用 application:didRegisterForRemoteNotificationsWithDeviceToken: 来获得 device token 并将他们上传到云巴服务器。
- 使用 application:didFailToRegisterForRemoteNotificationsWithError: 来对错误进行相应。

note：

如果 device token 发生了变化且你的 app 正在运行，app 会再次调用相对应的代理方法来告诉你 device token 发生了变化。

下面的代码展现了如何获取 device token， app 代理调用 registerForRemoteNotifications 方法作为初始化调用。当获取到了 device token ，调用 application:didRegisterForRemoteNotificationsWithDeviceToken: 方法让你有机会将它上传到云巴的服务器。当在此期间发生了任何错误，app 会暂时禁用所有远程推送相关的特性直到正确的 device token 被收到为止。

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

如果蜂窝数据或 wifi 无法连接，不管是 application:didRegisterForRemoteNotificationsWithDeviceToken: 还是 application:didFailToRegisterForRemoteNotificationsWithError: 都不会呗调用。当 wifi 连接上时，也经常会发生因端口配置而无法连接到 APNs 的情况。这种情况下，用户可以移动到其他不会阻拦请求端口 wifi 网络环境下。而蜂窝数据的情况，用户同样可以等到蜂窝数据服务能被使用为止。



处理远程通知

User Notification 框架把所有涉及到本地和远程通知相关的操作代码都集中到了一个地方。

具体来说，可以用同样的方法处理下面的事情：

- 如果应用在前台运行，可以直接收通知，并静默该通知；
- 如果应用在后台，且未运行：
  - 可以在用户选择了一个与通知有关的自定义操作时做出响应；
  - 可以在用户忽略掉通知，或者登录了应用时做出响应；

除了借助 User Notification 框架提供的方法来处理通知以外，应用还可以通过它的 app delegate 接收整个远程通知的负载。当一个远程通知到达时，如果应用在后台，系统会正常处理用户的交互。系统也会把通知的负载发给 iOS 的 app delegate 的 application:didReceiveRemoteNotification:fetchCompletionHandler:方法。你可以用这个方法检测负载，并执行相应的操作。例如，收到一个静默的远程通知后，可以执行应用更新的下载。

有关如何使用 User Notifications 框架的方法处理通知，详见 Responding to the Delivery of Notifications。

有关如何在 app delegate 里处理通知，详见UIApplicationDelegate Protocol Reference 或 NSApplicationDelegate Protocol Reference。


