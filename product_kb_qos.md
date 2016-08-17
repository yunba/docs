# QoS

云巴兼容标准的 MQTT 3.1 协议，本文介绍一下协议中的 [QoS](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718099)。

## QoS 定义

QoS，全称为 Quality of Service，即服务质量。在 MQTT 协议中，QoS 可分为以下三个等级：

* “QoS = 0”: At most once. Fire and Forget. （至多一次，发完即删，不保证送达。）
* “QoS = 1”: At least once. Acknowledged delivery. （保证至少送达一次。）
* “QoS = 2”: Exactly once. Assured delivery. （保证送达，且仅送达一次。）

## 不同 QoS 等级下的消息流

### QoS = 0

QoS 为 0 时，消息发送后，无论服务端有没有收到这条消息，都不会重发，也不会等任何回应。
这种即发即删的传输方式无疑是最高效的，网络通信的压力也比较小。

|客户端|消息以及消息流向|服务器|
|:----:|:----:|:----:|
| QoS = 0| -- PUBLISH --> | 向订阅者发布消息 |

### QoS = 1

QoS 为 1 时，服务器收到消息后会回应一个 PUBACK。
如果发送方或者通信链路出了问题，或是在既定的时间内没收到 PUBACK 的话，
发送方会把 DUP 置位，并重发消息。服务器收到重发来的消息，会重新发布给订阅者，并再次回复 PUBACK 给发送方。
因而消息至少送达一次。
在 MQTT 协议中，SUBSCRIBE 和 UNSUBSCRIBE 消息的 QoS 都为 1 级。

QoS 为 1 时，消息会带 Message ID。

|客户端|消息以及消息流向|服务器|
|:----:|:----:|:----:|
| QoS = 0<br>DUP = 0<br>Message ID = x| -- PUBLISH --> | 存储消息<br>向订阅者发布消息<br>删除消息 |
|丢弃消息| <-- PUBACK -- | |

### QoS = 2
QoS 2 级是 QoS 1 级的升级版，也是消息传输的最高等级。
它不仅能保证消息送达，且保证只送达一次。

与 QoS 1 级一样，QoS 2 级的消息也带有 Message ID。

服务端接收到消息后，有两种处理流程，这两种流程效果相同。

|客户端|消息以及消息流向|服务器|
|:----:|:----:|:----:|
| QoS = 2<br>DUP = 0<br>Message ID = x| -- PUBLISH --> |存储消息 |
|丢弃消息| <-- PUBACK -- | Message ID = x|
|Message ID = x | --PUBREL--> |向订阅者发布消息<br>删除消息|
|丢弃消息| <--PUBCOMP--|Message ID = x|


|客户端|消息以及消息流向|服务器|
|:----:|:----:|:----:|
| QoS = 2<br>DUP = 0<br>Message ID = x| -- PUBLISH --> | 存储 Message ID<br>向订阅者发布消息 |
|丢弃消息| <-- PUBACK -- | Message ID = x|
|Message ID = x | --PUBREL--> |删除消息|
|丢弃消息| <--PUBCOMP--|Message ID = x|

如果传输过程中检测到错误，或者既定时间内没收到期待的回应，则会从没收到确认消息的出错处开始尝试重发。
即，重发 PUBLISH 或重发 PUBREL。







