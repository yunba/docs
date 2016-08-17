# Message ID

在 MQTT 协议中，QoS > 0 的情况下，下面这些消息中都会带有 Message ID。

* PUBLISH
* PUBACK
* PUBREC
* PUBREL
* PUBCOMP
* SUBSCRIBE
* SUBACK
* UNSUBSCRIBE
* UNSUBACK

在一个特定方向（服务器发往客户端为一个方向，客户端发送到服务器端为另一个方向）的通信消息中，Message ID 是唯一的。

在云巴 SDK 中，Message ID 是自动生成的。

MQTT 协议规定的 Message ID 是 16 bit，云巴将 Message ID 扩展为 64 bit，以防止 Message ID 重复带来的系统设计和数据统计方面的麻烦。

