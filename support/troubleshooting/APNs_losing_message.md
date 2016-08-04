#APNs 丢消息问题

## 问题描述
首先是安达发现了我们的 demo 偶尔会出现丢消息的现象；主要的症状为：

1. 原来装上的 demo 都可以正常收消息，新 build 进去的就收不到消息了；

2. 即使是可以正常工作的 demo 有时也会出现大量的、无规律的丢失 APNs 的消息。

## 分析过程
### 信息总结
先总结一下所有的信息：

1. APNs 的收发消息的原理：
	1.1 设备会和 APNs 服务器建立一个 TLS 链接，app 会通过这个链接向 APNs 发送一个 http/2 request 来请求 device token，APNs 服务器在确认了设备的 device certificate 之后才会向其返回 apnsToken。device token 是向 APNs 传输的关键。

	1.2 http/2 的 POST 请求中包含 path、version、container 和 environment 参数；POST 作为一个 JSON dictionary，其中包含一个 apnsEnvironment key 的参数来指明 APNs 的环境，它有两个值，分别为 production 和 development。对应的即为相应的 APNs push notification 的生产和开发证书。这个 POST 请求是由设备发出的。APNs 会在 response 中返回 apsapnsEnvironment，apnsToken 和 webcourierURL。

	1.3 app 获得 device token 之后会把它[打包](https://developer.apple.com/library/ios/documentation/DataManagement/Conceptual/CloutKitWebServicesReference/CreateTokens/CreateTokens.html)，发送给 porvider 去，由porvider 把 token 和 payload 打包向 APNs 发起 http/2 请求，然后由 APNs 进行消息推送。

2. 我在 demo 的输出里留意到有时候会出现没有成功获得 device token。

3. 从 APNs 的原理来看，发送的消息是有 development 和 production 的分别的，而老板在回答一个客户的问题时提到了，我们是根据 SDK 上报的消息来自动选取开发和生产证书的，亦即我们是根据 APNs 返回的 apsEnvironment 来决定是使用哪一个证书。

4. 生产和开发证书都可以用来向 APNs 请求推送，但是在 build 的时候会有区别。

### 问题原因
掌握了以上的信息后我在以下几个方面找了原因：

1. 是否 provider 方面出了问题，才导致消息有时可以发送成功，有时不可以，但是在 APNs 的原理中可以发现 token 都是设备请求的，和 provider 方面关系不大，反而是如果 provider 出问题的话所有消息都会收不到。所以这个原因就排除掉了。

2. 是否是推送证书的问题；针对这一点，我在 Apple developer 里找到了添加 push notification 的步骤，对比我们官网上的步骤发现有以下不同：
	Provisioning Profile 的生成要在设置了打开 push notification 之后再生成。

这个的不同之处在于这样生成的 provisioning profile 里才包含了有效的对应的 push notification 的证书。我按照这个顺序下来选择的 code signing 就可以正常推送消息，而且无论 development 和 production都不会丢消息了。

### 原因分析
* 为什么会出现这样的情况呢，查阅资料以后发现，provisioning profile 里包含了证书、App ID 和设备的信息；app 在使用Xcode build 到手机中去的时候会使用这个 provisoning profile 里的证书信息来做签名。但是这个 provisoning profile 不会自动更新，所以生成它的时候，就要先在 iOS certificate 的页面内创建好对应的 certificates，再生成 provisioning profile。

* 为什么会有大量丢消息的现象呢，原因可能和我们根据 apsEnvironment 自动选择证书的策略有关：当一个 provisioning profile 同时包含生产和开发两个证书时，用户可能会同时在 portal 上传了一份包含在该 provisioning profile 的有效证书和不包含在该 profile 内的无效证书，我们在自动选择的时候可能会选中那份无效的证书，导致消息无法送达。我在验证的时候使用无效证书的时候会全都收不到，所以可能还要继续研究这个问题。

* 还有一点需要注意的事是在 Xcode 中因为没有 provisioning profile 而无法编译时如果选择了 fix issue，Xcode 创建的 provisioning profile 并不时常管用，有时可以正常工作，大部分时间又不可以，所以建议还是自己在 code signing 里选择正确的 provisioning profile。

## reference
* [CreateTokens](https://developer.apple.com/library/ios/documentation/DataManagement/Conceptual/CloutKitWebServicesReference/CreateTokens/CreateTokens.html)

* [Registering for Remote Notifications](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/IPhoneOSClientImp.html#//apple_ref/doc/uid/TP40008194-CH103-SW2)

* [Local and Remote Notification Programming Guide](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html)

* [App Distribution Guide](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html)

* [AddingCapabilitie](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW11)

* [Determine if device token is sandbox or distribution](http://stackoverflow.com/questions/5879715/determine-if-device-token-is-sandbox-or-distribution?rq=1#comment63204372_6004038)

* [关于Certificate、Provisioning Profile、App ID的介绍及其之间的关系](http://blog.csdn.net/joosonmao/article/details/21172835)
