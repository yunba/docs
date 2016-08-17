# AppKey

## 功能
云巴以 AppKey 来标示应用。每个应用有不同的标识符。**使用同一个 AppKey 的客户端，才可以互相通信。**

## 如何获取
AppKey 是云巴的注册用户在云巴 [Portal](product_kb_portal.md) 上创建应用后得到的一串标识符，可以在每个应用的 “应用详情” 页面中查看到。
**用户应妥善保管好自己的 AppKey，不在群聊等公众场合下泄露。**

## 举例

下面通过一个实际的应用场景来解释 AppKey、UID、[频道](product_kb_topic_and_alias.md)（Topic）、[别名](product_kb_topic_and_alias.md)（Alias）的概念：

用户登录云巴  [Portal](product_kb_portal.md) 创建了一个应用，获得了一个 AppKey。
在开发应用的过程中，使用这个申请到的 AppKey 集成 SDK（集成过程中需要使用 AppKey，详见官网的开发文档）。
用户将这个应用装到了 3 台机器上，那么这 3 个客户端将会共享同一个 AppKey。

在连接到云巴的消息系统时，这 3 台机器会获取到不同的 UID。**云巴以 UID 来标识 AppKey 下的不同客户端。**
于是，这个 AppKey 下就有了 3 个 UID。**客户端可以通过设置 [别名](product_kb_topic_and_alias.md) 为 UID 命名，或设置 [别名](product_kb_topic_and_alias.md) 为空字符串来删除绑定。**

假设这 3 个客户端都订阅了一个名为 Topic_1 的 [频道](product_kb_topic_and_alias.md)，那么这个 AppKey 的 Topic_1 下，就有了 3 个 UID。
这时，如果在 [Portal](product_kb_portal.md) 中选择这个应用，对 Topic_1 发布消息的话，这 3 个客户端都可以收到。
用户也可以开发其他的服务端程序，向这几个客户端发布消息。只需注意，在集成云巴的 SDK 时，使用同一个 AppKey。

