为了便于大家理解 APNs 的 Payload 的用法，本文翻译了 iOS 官方开发文档的 [The Remote Notification Payload](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/TheNotificationPayload.html#//apple_ref/doc/uid/TP40008194-CH107-SW1) 章节的部分内容。
由于专业术语的翻译可能会引起理解上的偏差，请以 iOS 官方开发文档为准。文档如有任何谬误不妥之处，欢迎指正。

# 远程通知的负载部分

每个远程推送通知都带有一个负载（Payload）。其内容包括系统提醒（alert）用户的方式以及任意的自定义数据。负载大小的上限取决于你使用的 API。如果使用的是 HTTP/2 的服务提供商的 API，最多允许 4096 字节；如使用 Legacy binary interface，则最多 2048 字节。如果内容超出了这个最大值，会被 APNs 服务器拒收。

## 负载的键

开发者需为每一个通知组一个 JSON 的字典对象（遵循 [RFC 4627](http://www.ietf.org/rfc/rfc4627.txt)）。该字典内必须包含另一个键名为 aps 的字典。这个 aps 字典可以包含一个或多个属性，来指定用户通知的类型。如下：

* 向用户显示的提醒消息
* 用于显示在 App 图标右上角的徽标（badge）数字
* 用来播放的铃声

如需实现静默远程通知，需在 Info.plist 文件中，将 remote-notification 值加进 [UIBackgroundModes](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW22) 数组中。

收到通知时，如果目标 App 当前未运行，则提醒消息、铃声或 badge 数值就会生效；如果 App 正在运行，系统则会以 [NSDictionary](https://developer.apple.com/library/ios/documentation/Cocoa/Reference/Foundation/Classes/NSDictionary_Class/index.html#//apple_ref/occ/cl/NSDictionary) 对象的形式把通知传给 App Delegate。其字典包含对应的 Cocoa property-list 对象（外加 [NSNull](https://developer.apple.com/library/ios/documentation/Cocoa/Reference/Foundation/Classes/NSNull_Class/index.html#//apple_ref/occ/cl/NSNull)）。

服务商可以在苹果保留的 aps 字典以外指定自定义的负载值。自定义的值必须遵循 JSON 的结构，并使用原始的类型：字典（对象）、数组、字符串、数字和布尔类型。自定义负载的内容中不应包含客户信息（或其他敏感数据）。相反，应该用它来做些诸如（为用户界面）设置上下文或内部量度（Metrics）的事情。例如，自定义负载的值可能是 IM 客户端应用的一个会话标识符，或是服务商发送通知的时间戳。任何与提醒消息有关的操作都不应该具有破坏性——比如不应该删除设备上的数据。

**重要提醒**：APNs 只会 “尽力” 推送通知，并不保证送达。它只是用来通知用户 App 当前有新的可用内容，而并不应该用来传递数据本身。

表格 5-1 列出了 aps 负载的键和预期的值。

**表格 5-1 aps 字典的键（Key）和值（Value）**

| Key | Value type | 注解 |
|:----|:----|:----|
| alert | string 或 dictionary | 如果包含这一属性，那么系统会根据用户的设置，显示标准形式或是横幅（banner）形式的通知。alert 的值可以是 string 或 dictionary 类型。<br>* 如果指定的是 string，那它就是通知消息的内容，通知带有 Close 和 View 两个按钮。用户按下 View，则会启动 App。<br>* 如果是 dictionary，请参考表格 5-2 查看该字典的键的描述。<br>JSON 的 \U 不支持。请将实际的 UTF-8 的字符放到 alert 文本内。 |
| badge | number | App 图标的徽标上显示的数字。如果不带该属性，则当前徽标值不变。将该属性的值设为 0 可清除徽标。 |
| sound | string | App bundle 或 App data container 的 Library/Sounds 文件夹内的音频文件的名称。这里的音频会在收到通知时播放。如果音频文件不存在或音频的值为 default，则会播放默认的音频。该音频必须是系统兼容的音频格式，详见 [Preparing Custom Alert Sounds](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/IPhoneOSClientImp.html#//apple_ref/doc/uid/TP40008194-CH103-SW6) 章节。 |
| content-available | number | 将该键设为 1 则表示有新的可用内容。带上这个键值，意味着你的 App 在后台启动了或恢复运行了，[application:didReceiveRemoteNotification:fetchCompletionHandler:](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIApplicationDelegate_Protocol/index.html#//apple_ref/occ/intfm/UIApplicationDelegate/application:didReceiveRemoteNotification:fetchCompletionHandler:) 被调用了。 |
| category | string | 为了定义一些自定义的动作，你需要创建 [UIMutableUserNotificationCategory](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIMutableUserNotificationCategory_class/index.html#//apple_ref/occ/cl/UIMutableUserNotificationCategory) 对象。而这个 category 的值即为该对象的 identifier 属性。参考 [Registering Your Actionable Notification Type](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/IPhoneOSClientImp.html#//apple_ref/doc/uid/TP40008194-CH103-SW26) 章节，了解更多自定义动作的用法。|

表格 5-2 列出了 alert 字典的键和预期的值。

**表格 5-2 alert 属性的子属性**

| Key | Value type | 注解 |
|:----|:----|:----|
| title | string | 通知的意图。Apple Watch 会将之显示为通知界面的一部分。这个字符串必须简明扼要，易于快速理解。这个键是在 iOS 8.2 中加入的。|
| body | string | 通知消息的内容。|
| title-loc-key | string 或 null | Localizable.strings 文件中的标题字符串的键，用于描述当前的本地化参数。字符串可以以 %@ 或 %n$@ 的格式来指定 title-loc-args 数组中的变量。参见 [Localized Formatted Strings](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/TheNotificationPayload.html#//apple_ref/doc/uid/TP40008194-CH107-SW7) 章节。这个键是在 iOS 8.2 中加入的。|
| title-loc-args | array of strings 或 null | 待显示于 title-loc-key 中的格式化字符处的字符串变量的值。参见 [Localized Formatted Strings](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/TheNotificationPayload.html#//apple_ref/doc/uid/TP40008194-CH107-SW7) 章节。这个键是在 iOS 8.2 中加入的。 |
| action-loc-key | string 或 null | 如果指定了一个字符串，那么系统会显示一个带有 Close 和 View 按钮的通知。这个字符串是获取本地化字符串的一个索引值，来取代右边按钮上的 View 字眼。参见 [Localized Formatted Strings](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/TheNotificationPayload.html#//apple_ref/doc/uid/TP40008194-CH107-SW7) 章节。|
| loc-key | string | 位于 Localizable.strings 文件中的一个通知消息的字符串的键（由用户设置的语言偏好所决定）。字符串可以以 %@ 或 %n$@ 的格式来指定 loc-args 数组中的变量。参见 [Localized Formatted Strings](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/TheNotificationPayload.html#//apple_ref/doc/uid/TP40008194-CH107-SW7) 章节。|
| loc-args | array of strings | 待显示于 loc-key 中的格式化字符处的字符串变量的值。参见 [Localized Formatted Strings](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/TheNotificationPayload.html#//apple_ref/doc/uid/TP40008194-CH107-SW7) 章节。 |
| launch-image | string | App bundle 中的某张图片的文件名。文件扩展名可有可无。用于用户按下某动作按钮或滑动滑条运行后的启动图片。如未指定，则系统要么使用之前的截图，要么使用 App 的 Info.plist 文件中的 UILaunchImageFile 键指定的图片，亦或是 Default.png。这个属性是在 iOS 4.0 中加入的。|

>**注**：如果想让设备 “原封不动” 地展现一个带有 Close 和 View 的通知消息，那就直接指定通知的内容。如果只有一个 body 属性，就不要把 alert 指定成字典。

## 配置静默通知

aps 字典也可以包含 content-available 属性。其值为 1 时，远程通知会变为静默通知。收到静默通知时，iOS 会在后台唤醒你的 App。之后，App 便可以从你的服务器获取新数据或进行一些后台的信息处理。
用户不会立刻察觉到静默通知所做的改动，下次打开 App 时才会发现。

使用静默通知时，需确保 aps 字典中不存在通知、声音或徽标的负载。否则，错误配置的通知可能会导致通知无法送达 App 的后台，或者是没有做到静默。

## 本地化格式字符串
略。

## JSON 负载的例子

下面给出几个负载的例子，来解释表格 5-1 中的属性的实际应用。键名带有 acme 的属性是自定义负载数据的例子。

>**注**：为了便于阅读，示例中加入了空格和换行。在实际使用时，去掉这些空格和换行符，可以减小负载体积、提高网络性能。

### 例 1
下面的负载是一个 aps 字典，带有一个简单的推荐样式的通知消息，有默认的通知按钮（Close 和 View）。通知的值使用的是字符串，而不是字典。该负载还包含一个自定义的数组的属性。
```JSON
{
    "aps" : { "alert" : "Message received from Bob" },
    "acme2" : [ "bang",  "whiz" ]
}
```

### 例 2
示例中的负载使用如下的 aps 字典来让设备显示出一个左边是 Close 按钮、右边是一个用本地化语言描述的某个 “动作” 按钮的通知。在本例中，PLAY 作为一个索引，用于从 Localizable.strings 文件中取出当前语言对应的描述词语。该 aps 字典还会将 App 的徽标值设为 5。在 Apple Watch 中，title 键用于通知用户当前有新的请求。
```JSON
{
    "aps" : {
        "alert" : {
            "title" : "Game Request",
            "body" : "Bob wants to play poker",
            "action-loc-key" : "PLAY"
        },
        "badge" : 5
    },
    "acme1" : "bar",
    "acme2" : [ "bang",  "whiz" ]
}
```

### 例 3
本例中的负载描述的通知消息带有 Close 和 View 按钮。App 图标的徽标值会被设为 9。另外，在收到通知时，会播放指定的预置的音频。
```JSON
{
    "aps" : {
        "alert" : "You got your emails.",
        "badge" : 9,
        "sound" : "bingbong.aiff"
    },
    "acme1" : "bar",
    "acme2" : 42
}
```

### 例 4
下面的例子使用 alert 字典的子属性 loc-key 和 loc-args 来从 App bundle 中获取格式化的本地字符串来替代相应位置的变量字符串的值（loc-args）。该负载还指定了一个自定义的音频以及一个自定义的属性。
```JSON
{
    "aps" : {
        "alert" : {
            "loc-key" : "GAME_PLAY_REQUEST_FORMAT",
            "loc-args" : [ "Jenna", "Frank"]
        },
        "sound" : "chime.aiff"
    },
    "acme" : "foo"
}
```

### 例 5
本例的负载使用 category 键来指定一组通知的动作（[Registering Your Actionable Notification Types](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/IPhoneOSClientImp.html#//apple_ref/doc/uid/TP40008194-CH103-SW26) 章节中有更多有关 categories 的介绍）。
```JSON
{
   "aps" : {
      "category" : "NEW_MESSAGE_CATEGORY",
      "alert" : {
         "body" : "Acme message received from Johnny Appleseed",
         "action-loc-key" : "VIEW"
      },
      "badge" : 3,
      "sound" : "chime.aiff"
   },
   "acme-account" : "jane.appleseed@apple.com",
   "acme-message" : "message123456"
}
```
