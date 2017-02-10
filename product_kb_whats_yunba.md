# 云巴是什么

云巴（Cloud Bus）是一个跨平台的双向实时通信系统，为物联网、App 和 Web 提供实时通信服务。

云巴基于 [MQTT](http://public.dhe.ibm.com/software/dw/webservices/ws-mqtt/mqtt-v3r1.html)，支持 [Socket.IO](socketio_api_api_manual.md) 协议，支持 [RESTful API](restful_api_api_manual.md)。

**更多详情：[《云巴产品白皮书》](https://yunba.github.io/docs/white_paper.pdf)**

## 跨平台

云巴支持 C、Embeded C、iOS、Android 等多种 SDK，支持 RESTful API，支持 Socket.IO 协议。无论是智能硬件、移动 App，还是 Web 应用的开发者们，只需集成云巴的 SDK，就可以实现不同平台之间的实时通信。

## 大规模

云巴的产品定位，是提供大规模的实时通信服务，现有的付费用户也大多拥有极其庞大的客户群。云巴的通信系统能够在大规模并发通信的同时依然保证其高实时性。

## 双向

云巴是一个 Pub/Sub 模型的双向实时系统，任何集成了云巴 SDK 的客户端在连上了云巴服务器（Broker）后，都兼具两种角色——发布者（Publisher）和订阅者（Subscriber）。

云巴支持 [频道](product_kb_topic_and_alias.md)（Topic）和 [别名](product_kb_topic_and_alias.md)（Alias）两种发布消息的方式：
* 一对多：客户端可以向某个频道发布（Publish）消息，也可以通过订阅（Subscribe）某个频道来接收消息；
* 一对一：客户端还可以通过设置自己的 “别名” 来实现一对一的通信。

## 实时通信

实时性是云巴的核心。

简单说来，通信耗时主要有两部分，云巴与客户端之间消息收发的耗时和云巴内部数据处理的耗时。

* 对于前者，云巴维护了与客户端之间的长连接，让客户端一直 “在线”，避免了反复建立 TCP 连接的时间和资源开销；
* 对于内部耗时，云巴的系统目前已经能够做到在巨大的压力下作出快速响应。而最大限度地缩短这一耗时，是云巴长期以来努力的重点，也正是云巴的核心竞争力。
