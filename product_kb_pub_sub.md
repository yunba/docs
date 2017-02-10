# 发布订阅模型


[如前所述](https://yunba.io/docs/product_kb_whats_yunba)，云巴是一个跨平台的双向实时通信系统。云巴兼容标准的 [MQTT](http://public.dhe.ibm.com/software/dw/webservices/ws-mqtt/mqtt-v3r1.html) 3.1 协议，并做了部分[扩展](https://yunba.io/docs/product_kb_mqtt_porting)。

本文简单介绍一下 MQTT 协议中的发布/订阅模型。

# 概念

![productpng_kb_pub_sub_1.png](https://raw.githubusercontent.com/yunba/docs/master/image/productpng_kb_pub_sub_1.png)

发布/订阅（Publish/Subscribe 或 Pub/Sub）是一种消息范式。

区别于传统的 Client/Server 模型，在 Pub/Sub 模型中，Publisher（消息的发布者）和 Subscriber（消息的订阅者）互相不知道对方的存在，而是借助 Broker 进行通信。

Broker 是类似于中介作用的第三方，主要作用是存储和转发消息。每一个 Client 都只能与 Broker 进行连接，连接建立后，通过 Broker 收发消息。每一条消息都会指定一个 [Topic](product_kb_topic_and_alias.md)。收到 Publisher 发来的消息后，Broker 会根据 Topic 进行过滤，将消息分发给对 Topic 感兴趣的 Subscriber。

## 特点

Broker 的引入，实现了 Publisher 和 Subscriber 在空间上、时间上和同步操作方面的解耦，带来了更好的可扩展性和更为动态的网络拓扑。

* Publisher 和 Subscriber 之间无须建立联系
* 在消息收发的过程中，不要求 Publisher 和 Subscriber 同时在线
* Client 以异步的方式收发消息，不会阻碍其他任务的进行

## 举例

下面给出两个实际的应用场景。


**图一**

下图中，两个智能灯泡订阅了名为 Light 的 Topic，智能手机向 Light 发消息，云巴 MQTT Broker 将消息转发给了灯泡。

![productpng_kb_pub_sub_2.png](https://raw.githubusercontent.com/yunba/docs/master/image/productpng_kb_pub_sub_2.png)

**图二**

下图中，智能温度计向名为 Temperature 的频道发消息，云巴 MQTT Broker 将消息转发给了该频道的订阅者。未订阅的客户端不会收到消息。

![productpng_kb_pub_sub_3.png](https://raw.githubusercontent.com/yunba/docs/master/image/productpng_kb_pub_sub_3.png)

注意，上方的示意图仅演示了单向的消息传输，云巴支持 [双向的消息通信](https://yunba.io/docs/product_kb_whats_yunba#%E5%8F%8C%E5%90%91)。基于发布/订阅原理，在图一中，灯泡可以通过订阅频道的方式接收来自智能手机的消息，智能手机也可以通过订阅频道的方式接收灯泡端上报的消息。
