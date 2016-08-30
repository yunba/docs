## 实时在线（Presence）

### 1. Presence 的由来

在 MQTT 协议中，并没有提供用户上、下线等行为的消息通知。于是，我们利用 MQTT 现有的一些特性，设计了 Presence。

### 2. 什么是 Presence

实时在线（Presence）是云巴提供的实时获取某个频道下所有用户（[别名](product_kb_topic_and_alias.md)）的上、下线通知以及订阅、取消订阅该频道的通知。

订阅了某个频道的 Presence 后，可以监听到的通知包括：
* Online/Offline：该频道下<u>某个已经设置了别名的用户</u>的“上/下线通知”。
* Join/Leave：某个<u>已经设置了别名的用户</u>订阅该频道的“加入/离开通知”。

从这个定义可以看出，**使用 Presence，只能监听已经设置了 [别名](product_kb_topic_and_alias.md) 的客户端，未设置 [别名](product_kb_topic_and_alias.md) 的客户端不会被监听。**


>Q: 为什么客户端必须设置别名呢？

>A: 由于 Presence 消息针对的是客户端的订阅动态和在线状态，因此，消息必须有一个客户端主体。
在云巴系统内部，会为每一个连接云巴的客户端生成一个唯一的标识符，即 UID。
由于这个 UID 是一串无意义的字符串，不便于对外公开和使用，因而，我们设计了“别名”的概念来取代 UID。
用户可以通过为客户端设置不同的 “[别名](product_kb_topic_and_alias.md)” 来唯一标识客户端。


### 3. Presence 举例



#### 例一

请先看一个最简单的场景，初步了解 “Join/Leave/Online/Offline” 四种消息的含义：

**描述**
* 有一个频道，名为 Room
* 有两个客户端，别名 分别为 Alex 和 Bob
* 头像 **在 Room 内/外** 表示 **订阅/未订阅 Room**
* 头像 **绿色/灰色** 表示 **在线/离线**
* 头像 **有/无 眼睛图标** 表示 **订阅/未订阅 Room 的 Presence 消息**

**Join/Leave 通知：**
![productpng_kb_presence_join_and_leave.png](https://raw.githubusercontent.com/yunba/docs/master/image/productpng_kb_presence_join_and_leave.png)

**Online/Offline 通知：**
![productpng_kb_presence_on_offline.png](https://raw.githubusercontent.com/yunba/docs/master/image/productpng_kb_presence_on_offline.png)


#### 例二

前文有提到，Presence 消息仅对设置了 别名 的客户端有效。

那么，客户端是否设置 别名 对 Presence 消息的收发具体有怎样的影响呢？请看下面两个例子： 

**描述**
* 有一个频道，名为 Room
* 有五个客户端，内部 UID 分别为 1001，1002，1003，1004 和 1005
* UID 1001 和 1002 已经分别设置了 别名 Alex 和 Bob
* UID 1003、1004 和 1005 未设置 别名
* 头像 **在 Room 内/外** 表示 **订阅/未订阅 Room**
* 头像 **绿色/灰色** 表示 **在线/离线**
* 头像 **有/无 眼睛图标** 表示 **订阅/未订阅 Room 的 Presence 消息**

![productpng_kb_presence_b1.gif](https://raw.githubusercontent.com/yunba/docs/master/image/productpng_kb_presence_b1.gif)

![productpng_kb_presence_b2.gif](https://raw.githubusercontent.com/yunba/docs/master/image/productpng_kb_presence_b2.gif)

通过这个例子，可以看出：

* **订阅了 Presence 才能收到 Presence 的消息**
>上例中，UID 1005 虽然订阅了 Room 频道，但未订阅 Room 的 Presence，因而不会收到 Presence 消息；

* **Presence 消息只对设置了别名的客户端有效**
>上例中，UID 1003 和 1004 未设置别名，因而当他们进出房间或上下线时不会有 Presence 消息发出；

* **Presence 消息的订阅者本身并不要求有别名**
>上例中，别名为 Alex 的客户端和未设置别名的 UID 1003 或 1004 都可以收到 Presence 消息；

### 4. Presence 的原理

Presence 的实质是，对 [频道](product_kb_topic_and_alias.md) 下所有用户 [别名](product_kb_topic_and_alias.md) 的状态的订阅。成功订阅后，Topic 下的任何用户别名的状态一旦发生变化，都会向 Topic + '/p' 频道发送消息。

例如，某用户（客户端）通过调用 `subscribe_presence("news")` 来监控 news 频道下的 Presence 消息。
调用成功后，云巴系统会自动为该客户端订阅一个名为 news/p 的频道，并将 news 频道的所有的 Presence 消息发给 news/p，让用户可以实时掌控该频道所有的 Presence 消息。

>**注**：在调用 Presence API 时，系统自动为用户订阅的 Topic + '/p' 是一个特殊的频道，不会出现在用户实际的订阅列表中。

### 5. 相关 API
下面以 [JavaScript SDK](https://github.com/yunba/yunba-javascript-sdk) 为例，介绍一下与频道相关的 API。

* [`subscribe_presence`](js_sdk_api_manual.md#subscribe_presence) 用来监听某个频道下所有用户的别名状态的变化。
* [`unsubscribe_presence`](js_sdk_api_manual.md#unsubscribe_presence) 用来取消对某频道下用户别名状态变化的监听。

以云巴 [JavaScript SDK Demo](java_demo_quick_start.md) 为例。假设频道 news 下有一个名为 defy 的用户，下线后又立刻上线；后有一个名为 cat 的用户订阅了 news 频道，之后又立刻取消订阅。如果通过 `subscribe_presence` 订阅了 news，就可以实时获取到 defy 和 cat 的状态变化。
打印信息如下：

* 来自频道：news/p   消息内容：{"action":"offline","alias":"defy","timestamp":1454321557378}
* 来自频道：news/p   消息内容：{"action":"online","alias":"defy","timestamp":1454321558995}
* 来自频道：news/p   消息内容：{"action":"join","alias":"cat","timestamp":1454321577648}
* 来自频道：news/p   消息内容：{"action":"leave","alias":"cat","timestamp":1454321585416}

