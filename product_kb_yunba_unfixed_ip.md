# 云巴服务的 IP 是不固定的

客户端在与 MQTT broker 进行长连接之前会先请求云巴 ticket 服务，
ticket 服务会返回一个当前可用的 broker IP，
然后客户端会跟这个当前可用的 broker 进行长连接，
最后才开始进行 MQTT 通信。


为了保障 MQTT 数据包的分布式处理，
ticket 服务返回的 broker IP 会随着用户的使用情况和当前系统负载进行相应的调整，并非一个固定的接入 IP。


如果想要申请独立的 broker 服务，可以考虑云巴的付费服务。
