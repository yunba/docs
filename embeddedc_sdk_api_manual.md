# Yunba Embedded-C API 手册


## MQTTClient_setup_with_appkey

### 功能
用 [AppKey](product_kb_app_key.md) 获取注册信息。使用该函数时，每次用户都会获得新的注册信息，包括 Client ID、Username、Password 和 Device ID。

### 函数原型
`int MQTTClient_setup_with_appkey(char* appkey, REG_info *info)`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
appkey | char* | 在云巴 [Portal](product_kb_portal.md) 申请到的应用的 AppKey。
info | REG_info* | 保存获取到的注册信息。

### 返回值
* (int): SUCCESS 说明操作成功。详细请查看 MQTTClient.h 中定义的返回码。

### 代码示例
```c
int res = MQTTClient_setup_with_appkey(appkey, &my_reg_info);
printf("Get reg info: client_id:%s,username:%s,password:%s, devide_id:%s\n", my_reg_info.client_id, my_reg_info.username, my_reg_info.password, my_reg_info.device_id);
```

## MQTTClient_setup_with_appkey_and_deviceid

### 功能
用 [AppKey](product_kb_app_key.md) 和 Device ID 获取注册信息。

当 deviceid 参数为 NULL 时，云巴会返回一个 deviceid 给用户，用户需自行保存该 deviceid，供下次调用该函数时使用。
用户也可以使用自己的 deviceid，但必须保证其唯一性。

###函数原型
`int MQTTClient_setup_with_appkey_and_deviceid(char* appkey, char *deviceid, REG_info *info)`

### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
appkey | char* | 在云巴 [Portal](product_kb_portal.md) 申请到的应用的 AppKey。
deviceid | char* | 用户自定义的 Device ID。
info | REG_info* | 保存获取到的注册信息。

### 返回值
* (int): SUCCESS 说明操作成功。详细请查看 MQTTClient.h 中定义的返回码。

### 代码示例
```c
int res = MQTTClient_setup_with_appkey_and_deviceid(appkey, device_id, &my_reg_info);
printf("Get reg info: client_id:%s,username:%s,password:%s, devide_id:%s\n", my_reg_info.client_id, my_reg_info.username, my_reg_info.password, my_reg_info.device_id);
```

## MQTTSubscribe
### 功能

增加订阅一个 [频道](product_kb_topic_and_alias.md)。成功订阅后，App 可以收到来自该频道的消息。新增订阅不会影响已有的订阅。

### 函数原型
`int MQTTSubscribe(Client*, const char*, enum QoS);`

### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
handle | MQTTClient| 客户端句柄。
topic | char* | App 订阅的频道。取值范围详见 [参数说明](product_kb_param.md#topic)。
qos | enum | 参看 MQTTClient.h 定义。

### 返回值
* (int): SUCCESS 说明操作成功。详细请查看 MQTTClient.h 中定义的返回码。

### 代码示例
```c
rc = MQTTSubscribe(client, "rocket", QOS1);
```

## MQTTUnsubscribe

### 功能
取消对某个 [频道](product_kb_topic_and_alias.md) 的订阅。成功取消后，就不会再收到来自该频道的消息。

### 函数原型
`int MQTTUnsubscribe(Client*, const char*);`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
handle | Client* | 客户端句柄。
topic | const char* | App 取消订阅的频道。取值范围详见 [参数说明](product_kb_param.md#topic)。

### 返回值
* (int): SUCCESS 说明操作成功。详细请查看 MQTTClient.h 中定义的返回码。

### 代码示例
```c
rc = MQTTUnsubscribe(client, "rocket");
```

## MQTTPublish

### 功能
向某个 [频道](product_kb_topic_and_alias.md) 发布消息。成功发布后，所有订阅此频道的客户端都会收到消息。

### 函数原型
`int MQTTPublish (Client*, const char*, MQTTMessage*)`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
handle | Client* | 客户端句柄。
msg | MQTTMessage* | 发送消息的结构体。

### 返回值
* (int): SUCCESS 说明操作成功。详细请查看 MQTTClient.h 中定义的返回码。

### 代码示例
```c
MQTTMessage M;
M.qos = QOS1;
M.payload = buf;
M.payloadlen = strlen(buf);
rc = MQTTPublish(&c, topic, &M);
```

## MQTTPublishToAlias

### 功能
向某个 [别名](product_kb_topic_and_alias.md) 发布消息。发布成功后，该别名的客户端会收到消息。

### 函数原型
`int MQTTPublishToAlias(Client* c, const char* alias, void *payload, int payloadlen)`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
handle | Client* | 客户端句柄。
alias | char* | 目标别名。取值范围详见 [参数说明](product_kb_param.md#alias)。
payload | void* | 消息指针。
payloadlen | int | 消息内容的长度。


### 返回值
* (int): SUCCESS 说明操作成功。详细请查看 MQTTClient.h 中定义的返回码。

### 代码示例
```c
rc = MQTTPublishToAlias(&c, "alias", "test", strlen("test"));
```

## MQTTSetAlias

### 功能
用来设置用户名，即绑定账号。每个用户只能指定一个 [别名](product_kb_topic_and_alias.md)。

### 函数原型
`int MQTTSetAlias(Client*, const char*);`

### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
handle | MQTTClient | 客户端句柄。
alias  | char* | 用户设置的别名信息，取值范围详见 [参数说明](product_kb_param.md#alias)。

### 返回值
* (int): SUCCESS 说明操作成功。详细请查看 MQTTClient.h 中定义的返回码。

### 代码示例
```c
int ret = MQTTSetAlias(client, "000000018302");
```

## MQTTGetAlias

### 功能
获取当前用户的 [别名](product_kb_topic_and_alias.md)。

通过设置`MQTTSetCallBack`的参数——回调函数`extendedmessageHandler`——来获取相关信息。

### 函数原型
`int MQTTGetAlias(Client* c, const char *param)`

### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
handle | Client* | 客户端句柄。
parameter | char* | 目标别名。

### 返回值
* (int): SUCCESS 说明操作成功。详细请查看 MQTTClient.h 中定义的返回码。

### 代码示例
```c
int ret = MQTTGetAlias(client, "0");
```

## MQTTGetStatus
### 功能
查询目标用户（[别名](product_kb_topic_and_alias.md)）的在线状态。

通过设置`MQTTSetCallBack`的参数——回调函数`extendedmessageHandler`——来获取相关信息。

### 函数原型
`int MQTTGetStatus(Client* c, const char *parameter);`

### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
handle | Client* | 客户端句柄。
parameter | char* | 目标别名。

### 返回值
* (int): SUCCESS 说明操作成功。详细请查看 MQTTClient.h 中定义的返回码。

### 代码示例
```c
int ret = MQTTGetStatus(client, "000000018302");
```

## MQTTGetAliasList

### 功能
获取某 [频道](product_kb_topic_and_alias.md) 下的所有订阅者的 [别名](product_kb_topic_and_alias.md) 列表。

通过设置`MQTTSetCallBack`的参数——回调函数`extendedmessageHandler`——来获取相关信息。

### 函数原型
`int MQTTGetAliasList(Client* c, const char *parameter);`

### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
handle | Client* | 客户端句柄。
parameter | char* | 目标频道。[参数说明](product_kb_param.md#topic)。

### 返回值
* (int): SUCCESS 说明操作成功。详细请查看 MQTTClient.h 中定义的返回码。

### 代码示例
```c
int ret = MQTTGetAliasList(client, "rocket");
```

## MQTTGetTopic

### 功能
获取某个用户所订阅的 [频道](product_kb_topic_and_alias.md)。

通过设置`MQTTSetCallBack`的参数——回调函数`extendedmessageHandler`——来获取相关信息。

### 函数原型
`int MQTTGetTopic(Client* c, const char *parameter);`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
handle | Client* | 客户端句柄。
parameter | char* | 目标用户的别名。取值范围详见 [参数说明](product_kb_param.md#alias)。

### 返回值
* (int): SUCCESS 说明操作成功。详细请查看 MQTTClient.h 中定义的返回码。

### 代码示例
```c
int ret = MQTTGetTopic(client, "000000018302");
```

## MQTTReport

### 功能
**暂未支持。** 上报客户端的行为。如，打开通知栏次数、按钮点击次数、资源下载成功次数等行为。

### 函数原型
`int MQTTReport(Client* c, const char* action, const char *data);`

### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
handle | Client* | 客户端句柄.
action | char* | App 需要统计的行为，如打开通知栏，资源下载成功等。
data | char* | 对应行为的附加数据，以满足统计相关的其他业务需求。

### 返回值
* (int): SUCCESS 说明操作成功。详细请查看 MQTTClient.h 中定义的返回码。

### 代码示例
```c
int ret = MQTTReport(client, "action", "data");
```

## MQTTSetCallBack

### 功能
用户通过设置回调函数，用来接收 MQTT 消息。

### 函数原型
`int MQTTSetCallBack(Client *c, messageHandler, extendedmessageHandler);`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
handle | Client* | 客户端句柄。
msgHandler | messageHandler | 接收 MQTT 消息的回调函数。
extmsgHandler | extendedmessageHandler | 接收云巴扩展命令字 MQTT 消息的回调函数。

### 返回值
* (int): SUCCESS 说明操作成功。详细请查看 MQTTClient.h 中定义的返回码。

### 代码示例
```c
MQTTSetCallBack(&c, messageArrived, extMessageArrive);
```

## MQTTConnect

### 功能
建立客户端与 Broker 之间的 MQTT 连接。

### 函数原型
`int MQTTConnect (Client*, MQTTPacket_connectData*);`

### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
handle | Client* | 客户端句柄。
connData | MQTTPacket_connectData* | MQTT 连接需传递数据结构体指针。

### 返回值
* (int): SUCCESS 说明操作成功。详细请查看 MQTTClient.h 中定义的返回码。

### 代码示例
```c
MQTTPacket_connectData data = MQTTPacket_connectData_initializer;
data.willFlag = 0;
data.MQTTVersion = 19;
data.clientID.cstring = reg.client_id;
data.username.cstring = reg.username;
data.password.cstring = reg.password;

data.keepAliveInterval = 200;
data.cleansession = 0;

rc = MQTTConnect(&c, &data);
```

## MQTTDisconnect

### 功能
断开 MQTT 连接。

### 函数原型
`int MQTTDisconnect (Client*);`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
handle | Client* | 客户端句柄指针。

### 返回值
* (int): SUCCESS 说明操作成功。详细请查看 MQTTClient.h 中定义的返回码。

### 代码示例
```c
MQTTDisconnect(&c);
```
