#Yunba C SDK 使用文档
导入 SDK
根据用户平台芯片下载相应的yunba c sdk。比如arm平台。下载 yunba-c-sdk-release-for-arm.tar.gz包解压到本地。在使用yunba的代码中，加入相应的头文件。在Makefile添加yunba库文件。

#添加使用代码
初始化 YunBa SDK。请在您的代码入口函数main后处添加如下代码：

MQTTClient client;
MQTTClient_connectOptions conn_opts = MQTTClient_connectOptions_initializer;

char url[100];
sprintf(url, "%s:%s", opts.host, opts.port);
rc = MQTTClient_create(&client, url, opts.clientid, MQTTCLIENT_PERSISTENCE_NONE, NULL);
MQTTClient_connect(client, &conn_opts);

添加 Message Received 代码
rc = MQTTClient_setCallbacks(client, NULL, NULL, messageArrived, NULL, extendedCmdArrive);

其中messageArrived， extendedCmdArrive为回调函数。

下面函数处理status, get alias get-topic等扩展命令。
int extendedCmdArrive(void *context, EXTED_CMD cmd, int status, int ret_string_len, char *ret_string)
{
  //处理接收到的扩展命令返回。
}

int messageArrived(void* context, char* topicName, int topicLen, MQTTClient_message* m)
{
  //处理topic以及推送的消息内容。
}

API - MQTTClient_subscribe
功能

App 可以增加订阅一个Topic, 以便可以接收来自 Topic 的 Message。

函数原型
int MQTTClient_subscribe(MQTTClient handle, char* topic, int qos);
参数说明
handle: 客户端句柄
topic: 订阅的的主题，topic 只支持英文数字下划线，长度不超过50个字符
qos: 订阅服务质量，一般设置为2.

Code Example
rc = MQTTClient_subscribe(client, “rocket”, 2);

API - MQTTClient_unsubscribe
功能

App 可以取消订阅一个Topic。

函数原型
int MQTTClient_unsubscribe(MQTTClient handle, char* topic)
参数说明
handle: 客户端句柄
topic: 取消订阅的的主题，topic 只支持英文数字下划线，长度不超过50个字符

Code Example
rc = MQTTClient_unsubscribe(client, “rocket”);

API - MQTTClient_publish
功能

App 可以向 Topic 发送消息, 那么任何订阅此 Topic 的 Client 都会接受到消息。。

函数原型
int MQTTClient_publish(MQTTClient handle, char* topicName, int payloadlen, void* payload, int qos, int retained,
																 MQTTClient_deliveryToken* dt);
参数说明
handle: 客户端句柄
topic: 订阅的主题，topic 只支持英文数字下划线，长度不超过50个字符
payloadlen: 净荷的长度
qos: 服务质量
retained：该条信息的retained的标示。
dt:指向MQTTClient_deliveryToken的指针。

Code Example
int data_len = 0;
buffer[data_len++] = getchar();
rc = MQTTClient_publish(client, topic, data_len, buffer, 0, 0, NULL);

API - SetAlias
功能

App 可以调用此函数来绑定账号，用户名，每个用户只能指定一个别名。

函数原型
int MQTTClient_set_alias(MQTTClient handle, char* alias);

参数说明
handle: 客户端句柄
alias: 用户设置的别名信息，只支持英文数字下划线，长度不超过50个字符

Code Example
int ret = MQTTClient_set_alias(client, "000000018302");


API - MQTTClient_get_alias

功能

App 可以调用此函数来获取当前用户的别名。

函数原型
int MQTTClient_get_alias(MQTTClient handle, char* parameter);

参数说明
handle: 客户端句柄
parameter: 参数

Code Example
int ret = MQTTClient_get_alias(client, "0“）;
在回调函数extendedCmdArrive中获得该用户的alias.


API - MQTTClient_get_status

功能

App 可以调用此函数来获得alias的用户状态。

函数原型
int MQTTClient_get_status(MQTTClient handle, char* parameter)

参数说明
handle: 客户端句柄
parameter: 该用户的alias

Code Example
int ret = MQTTClient_get_alias(client, "0“）;
在回调函数extendedCmdArrive中获得该用户的状态.


API - MQTTClient_get_aliaslist

功能

App 可以调用此函数来某个topic的别名列表

函数原型
int MQTTClient_get_aliaslist(MQTTClient handle, char* parameter);

参数说明
handle: 客户端句柄
parameter: 某个topic

Code Example
int ret = MQTTClient_get_aliaslist(client, "rocket“）;
在回调函数extendedCmdArrive中获得该用户的状态.



API - MQTTClient_get_topic

功能

App 可以调用此函数来某个alias的所订阅的topic

函数原型
int MQTTClient_get_topic(MQTTClient handle, char* parameter);

参数说明
handle: 客户端句柄
parameter: 用户别名。

Code Example
int ret = MQTTClient_get_topic(client, "000000018302“）;
在回调函数extendedCmdArrive中获得该用户的状态.