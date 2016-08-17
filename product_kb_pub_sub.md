# Pub/Sub 模型

区别于传统的 Client/Server 模型，在 Pub/Sub 模型中，Publisher（消息的发布者）和 Subscriber（消息的订阅者）之间没有关联，而是借助 Broker 进行通信的。

**云巴就是一个基于 Pub/Sub 模型的实时系统。**

## 原理


在 Pub/Sub 模型中，Broker 是类似于中介作用的第三方，主要作用是存储和转发消息。

每一个 Client 都只能与 Broker 进行连接，连接建立后，通过 Broker 收发消息。每一条消息都会指定一个 [Topic](product_kb_topic_and_alias.md)。收到 Publisher 发来的消息后，Broker 会根据 Topic 进行过滤，将消息分发给对 Topic 感兴趣的 Subscriber。

## 优势

Broker 的引入，实现了 Publisher 和 Subscriber 在空间上、时间上和同步操作方面的解耦：
* Publisher 和 Subscriber 之间无须建立联系
* 在消息收发的过程中，不要求 Publisher 和 Subscriber 同时在线
* Client 以异步的方式收发消息，不会阻碍其他任务的进行
