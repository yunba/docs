这篇文档对应 Apple 2016-03-21 更新的 [APNs Provider API](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/APNsProviderAPI.html)。
请以官方开发文档为准。文档如有任何谬误不妥之处，欢迎指正。

## APNs Provider API

借助苹果推送通知服务的 APNs Provider API 可以向你的 iOS、tvOS、OS X、苹果手表（通过 iOS）发送远程通知。这个 API 基于 HTTP/2 网络协议。每次的数据来往都始于一个 POST 请求，带有一个 JSON 负载，由你从你的 Provider 的服务器发往 APNs。之后，APNs 会把通知发给你的某个特定用户机器的 App。

在 [App Distribution Guide](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40012582) 里的 [Creating a Universal Push Notification Client SSL Certificate](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW11) 章节，介绍过如何获取 APNs 证书。
APNs 证书让你可以连接到 APNs 的生产或开发环境。

你可以用你的 APNs 证书向你的主应用发通知，通过 bundle ID 来定位你的应用，也可以向附属的苹果手表应用、或该应用后台的 VoIP 服务发通知。使用证书的（1.2.840.113635.100.6.3.6）扩展来确定你的推送的 topic。
例如，你的应用的 bundle ID 是 com.yourcompany.yourexampleapp，你可以在证书中指定如下的 topics：
```
Extension ( 1.2.840.113635.100.6.3.6 )
Critical NO
Data com.yourcompany.yourexampleapp
Data app
Data com.yourcompany.yourexampleapp.voip
Data voip
Data com.yourcompany.yourexampleapp.complication
Data complication
```

### Connections

发推送通知的第一步是与相应的 APNs 服务器建立连接：
* 开发服务器：api.development.push.apple.com:443
* 生产服务器：api.push.apple.com:443

> 
注意：与 APNs 通信时，你也可以选择 2197 端口。例如，如果你希望 APNs 的信息能跨越防火墙进来，而其他 HTTPS 的信息进不来的话，就可以这么用。

在与 APNs 服务器建立连接时，服务提供商必须支持 TLS 1.2 或更高的版本。此外，连接必须通过你的服务提供商的客户端证书进行授权，这个证书是你从开发者中心获取到的，在 [Creating a Universal Push Notification Client SSL Certificate](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW11) 章节里有介绍。

在每个与 APNs 服务器建立的连接上，可以同时有多个“流”（每个多路复用的请求响应称为一个“流”）。“流”的数量取决于服务器的负载能力，所以没有一个确切的数字。
不要在你的“流”中发送 HTTP/2 PRIORITY，因为会被 APNs 服务器忽略掉。

### Best Practices for Managing Connections

确保在发送多个推送通知时，始终保持与 APNs 的连接；不要不停地打开、关闭连接。APNs 会把短时间内的频繁断连视为拒绝服务的攻击。除非你能确定一个连接在很长一段时间内都空闲，否则应尽量保持连接的打开状态。假如你每天只发一个通知，那么每天都新建连接也是可以的。

你可以与 APNs 服务器建立多个连接，以提升性能。在发送大量的远程通知时，可以把他们分发到若干个服务器终端的连接上。这样一来，你可以更快地发送远程通知，APNs 也会传得更快，比起只用单一的连接，这种做法提高了效能。

你可以用 HTTP/2 PING 来检测连接是否畅通。


### Termination

APNs 通过发送一条 GOAWAY，来终结一个已经建立的 HTTP/2 连接。GOAWAY 帧的负载 JSON 数据中，含有一个原因代码，指明连接终止的原因。表 6-6 列出了原因代码可能的取值。
正常的请求失败不会导致连接的终止。

### Notification API

APNs Provider API 包含一个请求和一个回应，配置好后通过 HTTP/2 POST 命令发送。通过 Request，向 APNs 服务器发送一个推送通知，用 Response 来判断请求的处理结果。

### Request

用请求，来向某个特定用户设备发送一条通知。

**表 6-1 HTTP/2 Request**

| 名称       | 值  |
| :-------- | :-----|
| :method | POST|
|:path|/3/device/\<device-token\>|

\<device-token\>参数，是十六进制字节形式的目标设备的 device token。

为了避免重复的头部的键和值，APNs 要求使用 HPACK（HTTP/2 头部压缩）。
APNs 维护了一个 HPACK 的小型动态表。为了避免出现 HPACK 表填满了必须丢弃数据的情况，头部的编码请采用下述方式。在发送大量的“流”时更要这么做：

1. `:path ` 头部应为 literal header field without indexing 类型的；

2. `apns-id`、`apns-expiration`头部必须根据初始或后续的 POST 操作编码为不同的值。如下：

* 当你首次发送这些头部时，用 incremental indexing 来编码，从而让头部名称可以加进动态表里。

* 后续当你发送这些头部时，用 literal header fields without indexing 形式的头部区域来编码。

其他所有的头部都用 literal header fields without indexing 形式来编码。详见 https://tools.ietf.org/html/rfc7541#section-6.2.1 及 https://tools.ietf.org/html/rfc7541#section-6.2.2。

APNs 会忽略掉不在表格 6-2 中的头部。

**表 6-2 Request header**

| Header      | 描述  |
| :--------: | :-----|
|apns-id| 通知的全球唯一标识符。如果发送通知时出错了，APNs 会通过这个值来查找发往你服务器的这条通知。<br>形式是 32 位小写的十六进制数值，分五组显示，每组之间用中杠隔开，形式为 8-4-4-4-12。例如，下面是一个 UUID 的例子：<br>123e4567-e89b-12d3-a456-42665544000 <br>如果你没写这个头信息的话，APNs 会创建一个新的 UUID 并在回应中返回该值。|
|apns-expiration|UNIX 时代的日期，以秒计算（UTC）。这条头部信息是通知失效可被删除的日期。<br>如果这个值是非零的，APNs 会保存通知，尝试至少发送一次；如果无法第一次无法发送的话，会重复进行尝试。如果值为 0，APNs 会当做这条通知瞬间过期，并且不会保存或重传这条通知。|
|apns-priority|通知的优先级。指定下列其中一个值：<br>10：立刻发送推送通知。这种优先级的通知会触发目标设备上的通知、声音或角标。如果一个通知只有 content-available 键的话，用这个优先级就错了。<br>5：在考虑到设备电量的情况下发送推送通知。这种优先级的通知可能会被分组到一起，然后连续发出。他们会被掐掉，某些情况下甚至会不发。<br>如果没有指定，APNs 会默认设置优先级为 10。|
|apns-topic|远程通知的 topic，通常是你应用的 bundle ID。在开发者中心创建的证书必须包含了能够容纳这个 topic 的能力。<br>如果你的证书包含多个 topics，你必须为这个头部指定一个值。<br>如果没有指定这个头部，并且你的 APNs 证书没有指定多个 topics 的话，APNs 服务器会使用证书的 Subject 作为默认 topic。|

你的消息的 body 内容是 JSON 字典对象，包含有通知的数据。body 数据不可以被压缩，最大 4KB（4096 字节）。body 内容中所包含的键值的信息，详见《The Remote Notification Payload》。

### Response

格式如表 6-3 所示。

**表 6-3 Response headers**

| 名称       | 值  |
| :-------- | :----- |
|apns-id|Request 里的 apns-id。如果 Request 里没有这项，服务器会创建一个新的 UUID 并该值。|
|:status|HTTP 状态码。可能的状态码，见表 6-4。|

表 6-4 列出了 Request 可能的状态码。这些值都包含在 Response 的 `:status` 头部里。

**表 6-4 Response 的状态码**

| 状态码       | 描述  |
| :-------- | :----- |
|200|Success|
|400|Bad Request|
|403|There was an error with the certificate.|
|405|The Request used a bad :method value. Only POST Requests are supported.|
|410|The device token is no longer active for the topic.|
|413|The notification payload was too large.|
|429|The server received too many Requests for the same device token.|
|500|Internal server error|
|503|The server is shutting down and unavailable.|


请求成功时，回应的 body 为空。失败时，回应的 body 包含一个 JSON 的字典，键值如表 6-5 所示。连接终止时的 GOAWAY 帧里，也可能出现该 JSON 数据。

**表 6-5 JSON 数据键**

| 键       | 描述  |
| :-------- | :----- |
|reason|表明失败原因的错误代码。string 类型的错误代码。表 6-6 列出了可能的错误值。|
|timestamp|如果 :status 头部的值为 410，则该键对应的值表示最近一次 APNs 确认该 topic 的 device token 无效的时间。在设备向你的 provider 注册一个新一点的 timestamp 的 token 之前，都不要进行推送。|

表 6-6 列出了 Response 的 JSON 负载的`reason`键的可能的错误码。

**表 6-6 `reason`键的值**

| 原因      | 描述  |
| :-------- | :----- |
|PayloadEmpty|The message payload was empty.<br>Expected HTTP/2 status code is 400; see Table 6-4.|
|PayloadTooLarge|The message payload was too large. The maximum payload size is 4096 bytes.|
|BadTopic|The apns-topic was invalid.|
|TopicDisallowed|Pushing to this topic is not allowed.|
|BadMessageId|The apns-id value is bad.|
|BadExpirationDate|The apns-expiration value is bad.|
|BadPriority|The apns-priority value is bad.|
|MissingDeviceToken|The device token is not specified in the Request :path. Verify that the :path header contains the device token.|
|BadDeviceToken|The specified device token was bad. Verify that the Request contains a valid token and that the token matches the environment.|
|DeviceTokenNotForTopic|The device token does not match the specified topic.|
|Unregistered|The device token is inactive for the specified topic.<br>Expected HTTP/2 status code is 410; see Table 6-4.|
|DuplicateHeaders|One or more headers were repeated.|
|BadCertificateEnvironment|The client certificate was for the wrong environment.|
|BadCertificate|The certificate was bad.|
|Forbidden|The specified action is not allowed.|
|BadPath|The Request contained a bad :path value.|
|MethodNotAllowed|The specified :method was not POST.|
|TooManyRequests|Too many Requests were made consecutively to the same device token.|
|IdleTimeout|Idle time out.|
|Shutdown|The server is shutting down.|
|InternalServerError|An internal server error occurred.|
|ServiceUnavailable|The service is unavailable.|
|MissingTopic|The apns-topic header of the Request was not specified and was required. The apns-topic header is mandatory when the client is connected using a certificate that supports multiple topics.|

### Examples

**表单 6-1 单一 topic 的证书的 Request 示例**
```
HEADERS
  - END_STREAM
  + END_HEADERS
  :method = POST
  :scheme = https
  :path = /3/device/00fc13adff785122b4ad28809a3420982341241421348097878e577c991de8f0
  host = api.development.push.apple.com
  apns-id = eabeae54-14a8-11e5-b60b-1697f925ec7b
  apns-expiration = 0
  apns-priority = 10
DATA
  + END_STREAM
    { "aps" : { "alert" : "Hello" } }
```

**表单 6-2 多 topic 的证书的 Request 示例**
```
HEADERS
  - END_STREAM
  + END_HEADERS
  :method = POST
  :scheme = https
  :path = /3/device/00fc13adff785122b4ad28809a3420982341241421348097878e577c991de8f0
  host = api.development.push.apple.com
  apns-id = eabeae54-14a8-11e5-b60b-1697f925ec7b
  apns-expiration = 0
  apns-priority = 10
  apns-topic = <MyAppTopic> 
DATA
  + END_STREAM
    { "aps" : { "alert" : "Hello" } }
```

**表单 6-3 推送 Request 成功时的 Response 示例**
```
HEADERS
  + END_STREAM
  + END_HEADERS
  :status = 200
```

**表单 6-4 Request 出错时的 Response 示例**
```
HEADERS
  - END_STREAM
  + END_HEADERS
  :status = 400
  content-type = application/json
    apns-id: <a_UUID>
DATA
  + END_STREAM
  { "reason" : "BadDeviceToken" }
```
