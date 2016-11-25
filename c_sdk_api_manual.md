# Yunba C API 手册 

## MQTTClient_getVersionInfo

### 功能 
获取 SDK 的版本号。

### 函数原型
`MQTTClient_nameValue* MQTTClient_getVersionInfo();`

### 参数说明
无。

### 返回值
* MQTTClient_nameValue* : 保存 SDK 版本号的结构体指针。

### 代码示例
```c
MQTTClient_nameValue* version = MQTTClient_getVersionInfo();
printf("used:%s, %s\n", version->name, version->value);
```

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
* int : 返回 0 时，表示成功。小于 0，表示失败。

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

### 函数原型
`int MQTTClient_setup_with_appkey_and_deviceid(char* appkey, char *deviceid, REG_info *info)`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
appkey | char* | 在云巴 [Portal](product_kb_portal.md) 申请到的应用的 AppKey。
deviceid | char* | 用户自定义的 Device ID。
info | REG_info* | 保存获取到的注册信息。

### 返回值
* int : 为 0 时，表示成功。小于 0，表示失败。

### 代码示例
```c
int res = MQTTClient_setup_with_appkey_and_deviceid(appkey, device_id, &my_reg_info);
printf("Get reg info: client_id:%s,username:%s,password:%s, devide_id:%s\n", my_reg_info.client_id, my_reg_info.username, my_reg_info.password, my_reg_info.device_id);
```

## MQTTClient_get_host

### 功能
通过 [AppKey](product_kb_app_key.md) 获取 host 的信息。

### 函数原型
`int MQTTClient_get_host(char *appkey, char* url)`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
appkey | char* | 在云巴 [Portal](product_kb_portal.md) 申请到的应用的 AppKey。
url | char* | 保存获取到的 host 信息。

### 返回值
* int : 为 0 时，表示成功。小于 0，表示失败。

### 代码示例
```c
char url[200];
int res = MQTTClient_get_host(appkey, url);
```

## MQTTClient_subscribe

### 功能
增加订阅一个 [频道](product_kb_topic_and_alias.md)。成功订阅后，App 可以收到来自该频道的消息。新增订阅不会影响已有的订阅。

### 函数原型
`int MQTTClient_subscribe(MQTTClient handle, char* topic);`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
handle | MQTTClient| 客户端句柄。
topic | char* | App 订阅的频道。取值范围详见 [参数说明](product_kb_param.md#topic)。

### 返回值
* (int): MQTTCLIENT_SUCCESS 说明操作成功。详细请查看 [yunba.h](https://github.com/yunba/yunba-c-sdk/blob/master/src/yunba.h) 中定义的返回码。

### 代码示例
```c
rc = MQTTClient_subscribe(client, "rocket");
```

## MQTTClient_unsubscribe

### 功能
取消对某个 [频道](product_kb_topic_and_alias.md) 的订阅。成功取消后，就不会再收到来自该频道的消息。

### 函数原型
`int MQTTClient_unsubscribe(MQTTClient handle, char* topic);`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
handle | MQTTClient | 客户端句柄。
topic | char* | App 取消订阅的频道。取值范围详见 [参数说明](product_kb_param.md#topic)。

### 返回值
* (int): MQTTCLIENT_SUCCESS 说明操作成功。详细请查看 [yunba.h](https://github.com/yunba/yunba-c-sdk/blob/master/src/yunba.h) 中定义的返回码。

### 代码示例
```c
rc = MQTTClient_unsubscribe(client, "rocket");
```

## MQTTClient_presence

### 功能
监听某 [频道](product_kb_topic_and_alias.md) 下用户行为的事件通知，事件包括上线、下线、订阅频道和取消订阅频道。

参见 [云巴的实时在线](product_kb_presence.md)。

### 函数原型
`int MQTTClient_presence(MQTTClient handle, char* topic);`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
handle | MQTTClient | 客户端句柄。
topic | char* | 目标用户所在的频道。取值范围详见 [参数说明](product_kb_param.md#topic)。

### 返回值
* (int): MQTTCLIENT_SUCCESS 说明操作成功。详细请查看 [yunba.h](https://github.com/yunba/yunba-c-sdk/blob/master/src/yunba.h) 中定义的返回码。

### 代码示例
```c
rc = MQTTClient_presence(client, "rocket");
```

## MQTTClient_unpresence

### 功能
取消监听某 [频道](product_kb_topic_and_alias.md) 下用户行为的事件通知，事件包括上线、下线、订阅频道和取消订阅频道。

参见 [云巴的实时在线](product_kb_presence.md)。

### 函数原型
`int MQTTClient_unpresence(MQTTClient handle, char* topic);`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
handle | MQTTClient | 客户端句柄。
topic | char* | App 希望取消订阅的对象所在的频道。取值范围详见 [参数说明](product_kb_param.md#topic)。

### 返回值
* (int): MQTTCLIENT_SUCCESS 说明操作成功。详细请查看 [yunba.h](https://github.com/yunba/yunba-c-sdk/blob/master/src/yunba.h) 中定义的返回码。

### 代码示例
```c
rc = MQTTClient_unpresence(client, "rocket");
```

## MQTTClient_publish

### 功能
向某个 [频道](product_kb_topic_and_alias.md) 发布消息。成功发布后，所有订阅此频道的客户端都会收到消息。

### 函数原型
`int MQTTCient_publish(MQTTClient handle, char* topicName, int data_len, void *data)`

### 参数说明:
名称 | 类型 | 说明
--------- | ------- | -----------
handle | MQTTClient | 客户端句柄。
topic | char* | 订阅的主题，取值范围详见 [参数说明](product_kb_param.md#topic)。
data_len | int | 消息内容的长度。
data | void* | 消息指针。

### 返回值
* (int): MQTTCLIENT_SUCCESS 说明操作成功。详细请查看 [yunba.h](https://github.com/yunba/yunba-c-sdk/blob/master/src/yunba.h) 中定义的返回码。

### 代码示例
```c
char buf[100] = "Just test";
int data_len = strlen(buf);
rc = MQTTClient_publish(client, topic, data_len, buf);
```

## MQTTClient_publish2

### 功能
`MQTTClient_publish()`的升级版本，支持更多参数。

### 函数原型
`int MQTTClient_publish2(MQTTClient handle, const char* topicName, int payloadlen, void* payload, cJSON *opt);`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
handle | MQTTClient | 客户端句柄。
topic | char* | 订阅的主题，取值范围详见 [参数说明](product_kb_param.md#topic)。
payloadlen | int | payload 的长度。
payload | void* | payload 的内容。
opt | CJSON * | [扩展参数](#扩展参数说明)，可以带 apn_json，time_to_live 等参数。

### 扩展参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
qos | Number | 服务质量等级。有三种取值：“0”表示最多送达一次；“1”表示最少送达一次；“2”表示保证送达且仅送达一次。默认为“1”。详见 [QoS](product_kb_qos.md) 的说明。
apn_json | Dict | 如果不填，则不会发送 APN。 APN 参考：[Apple 官方文档](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/PayloadKeyReference.html#//apple_ref/doc/uid/TP40008194-CH17-SW1) 及云巴 [相关文档](ios_kb_payload.md)。
messageId | String | 消息 ID，64 位整型数转化成 String。发布消息时可以指定，如果不填，则由系统自动生成。
time_to_live | Number | 用来设置 [离线消息](product_kb_offline_message.md) 保留多久。单位为秒（例如，3600 代表 1 小时），默认值为 5 天，最大不超过 15 天。


### 返回值
* (int): MQTTCLIENT_SUCCESS 说明操作成功。详细请查看 [yunba.h](https://github.com/yunba/yunba-c-sdk/blob/master/src/yunba.h) 中定义的返回码。

### 代码示例
```c
cJSON *apn_json, *aps;
cJSON *Opt = cJSON_CreateObject();
cJSON_AddStringToObject(Opt,"time_to_live",  "120");
cJSON_AddStringToObject(Opt,"time_delay",  "1100");
cJSON_AddStringToObject(Opt,"apn_json",  "{\"aps\":{\"alert\":\"FENCE alarm\", \"sound\":\"alarm.mp3\"}}");
ret = MQTTClient_publish2(client, topic, strlen("test"), "test", Opt);
cJSON_Delete(Opt);
printf("publish2 status:%i\n", ret);
```

## MQTTClient_publish_to_alias

### 功能
向某个 [别名](product_kb_topic_and_alias.md) 发布消息。发布成功后，该别名的客户端会收到消息。

### 函数原型
`int MQTTClient_publish_to_alias(MQTTClient handle, char* alias, int data_len, void* data);`

### 参数说明:
名称 | 类型 | 说明
--------- | ------- | -----------
handle | MQTTClient | 客户端句柄。
alias | char* | 目标别名。取值范围详见 [参数说明](product_kb_param.md#alias)。
data_len | int | 消息内容的长度。
data | void* | 消息指针。

### 返回值
* (int): MQTTCLIENT_SUCCESS 说明操作成功。详细请查看 [yunba.h](https://github.com/yunba/yunba-c-sdk/blob/master/src/yunba.h) 中定义的返回码。

### 代码示例
```c
rc = MQTTClient_publish_to_alias(client, "Hello_alias", strlen("test"), "test");
```


## MQTTClient_publish2_to_alias

### 功能
`MQTTClient_publish2_to_alias()`的升级版本，支持更多参数。

### 函数原型
`int MQTTClient_publish2_to_alias(MQTTClient handle, const char* alias, int payloadlen, void* payload, cJSON *opt);`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
handle | MQTTClient | 客户端句柄。
alias | const char* | 目标别名。取值范围详见 [参数说明](product_kb_param.md#alias)。
payloadlen | int | payload 的长度。
payload | void* | payload 内容。
opt | CJSON * | [扩展参数](#扩展参数说明)，可以带 apn_json，time_to_live 等参数。

### 返回值
* (int): MQTTCLIENT_SUCCESS 说明操作成功。详细请查看 [yunba.h](https://github.com/yunba/yunba-c-sdk/blob/master/src/yunba.h) 中定义的返回码。

### 代码示例
```c
cJSON *apn_json, *aps;
cJSON *Opt = cJSON_CreateObject();
cJSON_AddStringToObject(Opt,"time_to_live",  "120");
cJSON_AddStringToObject(Opt,"time_delay",  "1100");
cJSON_AddStringToObject(Opt,"apn_json",  "{\"aps\":{\"alert\":\"FENCE alarm\", \"sound\":\"alarm.mp3\"}}");
ret = MQTTClient_publish2_to_alias(client, "Alex", strlen("test"), "test", Opt);
cJSON_Delete(Opt);
printf("publish2_alas status:%i\n", ret);
```


## MQTTClient_publish_json

### 功能
向某个 [频道](product_kb_topic_and_alias.md) 发布 JSON 格式的消息。成功发布后，所有订阅此频道的客户端都会收到消息。

### 函数原型
`int MQTTClient_publish_json(MQTTClient handle, char* topicName, cJSON* data)`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
handle | MQTTClient | 客户端句柄。
topic | char* | 目标频道。取值范围详见 [参数说明](product_kb_param.md#topic)。
data | cJSON* | JSON 包。

### 返回值
* (int): MQTTCLIENT_SUCCESS 说明操作成功。详细请查看 [yunba.h](https://github.com/yunba/yunba-c-sdk/blob/master/src/yunba.h) 中定义的返回码。

### 代码示例
```c
char buf[100] = "{\"num_name\":2}";
cJSON *data = cJSON_Parse(buf);
rc = MQTTClient_publish_json(client, topic, data);
cJSON_Delete(data);
```


## MQTTClient_set_alias

### 功能
用来设置用户名，即绑定账号。每个用户只能指定一个 [别名](product_kb_topic_and_alias.md)。

**将`MQTTClient_set_alias()`的`alias`参数设置为空字符串即可取消别名。**

### 函数原型
`int MQTTClient_set_alias(MQTTClient handle, char* alias);`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
handle | MQTTClient | 客户端句柄。
alias  | char* | 用户设置的别名信息，取值范围详见 [参数说明](product_kb_param.md#alias)。

### 返回值
* (int): MQTTCLIENT_SUCCESS 说明操作成功。详细请查看 [yunba.h](https://github.com/yunba/yunba-c-sdk/blob/master/src/yunba.h) 中定义的返回码。

### 代码示例
```c
int ret = MQTTClient_set_alias(client, "000000018302");
```

## MQTTClient_get_alias

### 功能
获取当前用户的 [别名](product_kb_topic_and_alias.md)。

通过设置`MQTTClient_setCallbacks()`的 [最后一个参数](https://github.com/yunba/yunba-c-sdk/blob/master/src/MQTTClient.c#L639) ——回调函数`MQTTClient_extendedCmdArrive`——来获得相关信息，可能的信息包括：
```json
{command:2, status:0, data:"aliasname"}
{command:2, status:1, data:"server internal error"}
{command:2, status:2, data:"alias not find"}
```

### 函数原型
`int MQTTClient_get_alias(MQTTClient handle, char* parameter);`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
handle | MQTTClient | 客户端句柄。
parameter | char* | 目标别名。

### 返回值
* (int): MQTTCLIENT_SUCCESS 说明操作成功。详细请查看 [yunba.h](https://github.com/yunba/yunba-c-sdk/blob/master/src/yunba.h) 中定义的返回码。

### 代码示例
```c
int ret = MQTTClient_get_alias(client, "0");
```

## MQTTClient_get_status

### 功能
查询目标用户（[别名](product_kb_topic_and_alias.md)）的在线状态。

通过设置`MQTTClient_setCallbacks()`的 [最后一个参数](https://github.com/yunba/yunba-c-sdk/blob/master/src/MQTTClient.c#L639) ——回调函数`MQTTClient_extendedCmdArrive`——来获得相关信息，可能的信息包括：

```json
{command:10, status:0, data: "online/offline"}
{command:10, status:1, data: "server internal error"}
{command:10, status:2, data: "no operation permission"}
{command:10, status:3, data: "alias not find"}
{command:10, status:4, data: "no status"}
{command:10, status:5, data: "alias is empty"}
```

### 函数原型
`int MQTTClient_get_status(MQTTClient handle, char* parameter);`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
handle | MQTTClient | 客户端句柄。
parameter | char* | 目标别名。

### 返回值
* (int): MQTTCLIENT_SUCCESS 说明操作成功。详细请查看 [yunba.h](https://github.com/yunba/yunba-c-sdk/blob/master/src/yunba.h) 中定义的返回码。

### 代码示例
```c
int ret = MQTTClient_get_status(client, "000000018302");
```

## MQTTClient_get_status2

### 功能
查询目标用户（[别名](product_kb_topic_and_alias.md)）的在线状态。是`MQTTClient_get_status()`的升级版本，其返回的状态信息中，会包含目标别名的名称。 

通过设置`MQTTClient_setCallbacks()`的 [最后一个参数](https://github.com/yunba/yunba-c-sdk/blob/master/src/MQTTClient.c#L639) ——回调函数`MQTTClient_extendedCmdArrive`——来获得相关信息，可能的信息包括：
```json
{command:20, status:0, data: "{'status': 'online/offline', 'alias': aliasname}"}  #aliasname: string
{command:20, status:1, data: "{'msg': 'server internal error'}"}
{command:20, status:2, data: "{'msg': 'no operation permission'}"}
{command:20, status:3, data: "{'msg': 'alias not find'}"}
{command:20, status:4, data: "{'msg': 'no status'}"}
{command:20, status:5, data: "{'msg': 'alias is empty'}"}
```

### 函数原型
`int MQTTClient_get_status2(MQTTClient handle, char* parameter);`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
handle | MQTTClient | 客户端句柄。
parameter | char* | 目标别名。

### 返回值
* (int): MQTTCLIENT_SUCCESS 说明操作成功。详细请查看 [yunba.h](https://github.com/yunba/yunba-c-sdk/blob/master/src/yunba.h) 中定义的返回码。

### 代码示例
```c
int ret = MQTTClient_get_status2(client, "000000018302");
```

## MQTTClient_get_aliaslist

### 功能
获取某 [频道](product_kb_topic_and_alias.md) 下的所有订阅者的 [别名](product_kb_topic_and_alias.md) 列表。

通过设置`MQTTClient_setCallbacks()`的 [最后一个参数](https://github.com/yunba/yunba-c-sdk/blob/master/src/MQTTClient.c#L639) ——回调函数`MQTTClient_extendedCmdArrive`——来获得相关信息，可能的信息包括：
```json
{command:6, status:0, data: "{'alias':[alias1,alias2,alias3], 'occupancy': alias_length}"}
{command:6, status:1, data: "server internal error"}
{command:6, status:2, data: "no operation permission"}
```

### 函数原型
`int MQTTClient_get_aliaslist(MQTTClient handle, char* parameter);`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
handle | MQTTClient | 客户端句柄。
parameter | char* | 目标频道。

### 返回值
* (int): MQTTCLIENT_SUCCESS 说明操作成功。详细请查看 [yunba.h](https://github.com/yunba/yunba-c-sdk/blob/master/src/yunba.h) 中定义的返回码。

### 代码示例
```c
int ret = MQTTClient_get_aliaslist(client, "rocket");
```

## MQTTClient_get_aliaslist2

### 功能
获取某 [频道](product_kb_topic_and_alias.md) 下的所有订阅者的 [别名](product_kb_topic_and_alias.md) 列表。是`MQTTClient_get_aliaslist()`的升级版本，其返回的状态信息中，会包含目标频道的名称。 

通过设置`MQTTClient_setCallbacks()`的 [最后一个参数](https://github.com/yunba/yunba-c-sdk/blob/master/src/MQTTClient.c#L639) ——回调函数`MQTTClient_extendedCmdArrive`——来获得相关信息，可能的信息包括：
```json
{command:16, status:0, data: "{'alias':[alias1,alias2,alias3], 'occupancy': alias_length, 'topic': topicname}"}  # topicnam2/alias1/alias2...: string, alias_length: int
{command:16, status:1, data: "{'msg': 'server internal error'}"}
{command:16, status:2, data: "{'msg': 'no operation permission'}"}
```

### 函数原型
`int MQTTClient_get_aliaslist2(MQTTClient handle, char* parameter);`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
handle | MQTTClient | 客户端句柄。
parameter | char* | 目标频道。

### 返回值
* (int): MQTTCLIENT_SUCCESS 说明操作成功。详细请查看 [yunba.h](https://github.com/yunba/yunba-c-sdk/blob/master/src/yunba.h) 中定义的返回码。

### 代码示例
```c
int ret = MQTTClient_get_aliaslist2(client, "mytopic");
```

## MQTTClient_get_topic

### 功能
获取某个用户所订阅的 [频道](product_kb_topic_and_alias.md)。

通过设置`MQTTClient_setCallbacks()`的 [最后一个参数](https://github.com/yunba/yunba-c-sdk/blob/master/src/MQTTClient.c#L639) ——回调函数`MQTTClient_extendedCmdArrive`——来获得相关信息，可能的信息包括：
```json
{command:4, status:0, data: "{'topic':[topic1,topic2,...]}"}
{command:4, status:1, data: "server internal error"}
{command:4, status:2, data: "no operation permission"}
{command:4, status:3, data: "alias not find"}
```
### 函数原型
`int MQTTClient_get_topic(MQTTClient handle, char* parameter);`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
handle | MQTTClient | 客户端句柄。
parameter | char* | 目标用户的别名。

### 返回值
* (int): MQTTCLIENT_SUCCESS 说明操作成功。详细请查看 [yunba.h](https://github.com/yunba/yunba-c-sdk/blob/master/src/yunba.h) 中定义的返回码。

### 代码示例
```c
int ret = MQTTClient_get_topic(client, "000000018302");

```

## MQTTClient_get_topiclist2

### 功能
获取某个用户所订阅的 [频道](product_kb_topic_and_alias.md)。
是`MQTTClient_get_topic()`的升级版本，其返回的状态信息中，会包含目标 
[别名](product_kb_topic_and_alias.md) 的名称。

### 函数原型
`int MQTTClient_get_topiclist2(MQTTClient handle, char* parameter);`

通过设置`MQTTClient_setCallbacks()`的 [最后一个参数](https://github.com/yunba/yunba-c-sdk/blob/master/src/MQTTClient.c#L639) ——回调函数`MQTTClient_extendedCmdArrive`——来获得相关信息，可能的信息包括：
```json
{command:14, status:0, data: "{'topic':[topic1,topic2,...], 'alias': aliasname}"} 
{command:14, status:1, data: "{'msg': 'server internal error'}"}
{command:14, status:2, data: "{'msg': 'no operation permission'}"}
{command:14, status:3, data: "{'msg': 'alias not find'}"}
```
### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
handle | MQTTClient | 客户端句柄。
parameter | char* | 目标用户的别名。取值范围详见 [参数说明](product_kb_param.md#alias)。

### 返回值
* (int): MQTTCLIENT_SUCCESS 说明操作成功。详细请查看 [yunba.h](https://github.com/yunba/yunba-c-sdk/blob/master/src/yunba.h) 中定义的返回码。

### 代码示例
```c
int ret = MQTTClient_get_topiclist2(client, "000000018302");
```

## MQTTClient_report

### 功能
**暂未支持。** 上报客户端的行为。如，打开通知栏次数、按钮点击次数、资源下载成功次数等行为。

### 函数原型
`int MQTTClient_report(MQTTClient handle, char* action, char* data);`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
handle | MQTTClient | 客户端句柄。
action | char* | App 需要统计的行为，如打开通知栏，资源下载成功等。
data | char* | 对应行为的附加数据，以满足统计相关的其他业务需求。

### 返回值
* (int): MQTTCLIENT_SUCCESS 说明操作成功。详细请查看 [yunba.h](https://github.com/yunba/yunba-c-sdk/blob/master/src/yunba.h) 中定义的返回码。

### 代码示例
```c
int ret = MQTTClient_report(client, "action", "data");
```

## MQTTClient_set_broker

### 功能
设置 Broker。

### 函数原型
`int MQTTClient_set_broker(MQTTClient* handle, char* broker);`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
handle | MQTTClient* | 客户端句柄的指针。
broker | char* | Broker 域名或者 IP 地址。

### 返回值
* (int): MQTTCLIENT_SUCCESS 说明操作成功。详细请查看 [yunba.h](https://github.com/yunba/yunba-c-sdk/blob/master/src/yunba.h) 中定义的返回码。

### 代码示例
```c
int ret = MQTTClient_set_broker(client, "192.168.1.100");
```

## MQTTClient_get_broker

### 功能
获取当前的 Broker。

### 函数原型
`int MQTTClient_get_broker(MQTTClient* handle, char* broker);`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
handle | MQTTClient* | 客户端句柄的指针。
broker | char* | 存放 Broker 的指针。

### 返回值
* (int): MQTTCLIENT_SUCCESS 说明操作成功。详细请查看 [yunba.h](https://github.com/yunba/yunba-c-sdk/blob/master/src/yunba.h) 中定义的返回码。

### 代码示例
```c
char buf[100];
int ret = MQTTClient_set_broker(client, buf);
```

## MQTTClient_freeMessage

### 功能
释放 message 资源。

### 函数原型
`void MQTTClient_freeMessage(MQTTClient_message** msg);`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
msg | MQTTClient_message** | 指向 message 指针的指针。

### 返回值
无。

### 代码示例
```c
int messageArrived(void* context, char* topicName, int topicLen, MQTTClient_message* m) {
	MQTTClient_freeMessage(&m);
	MQTTClient_free(topicName);
}
```

## MQTTClient_destroy

### 功能
释放客户端资源。

### 函数原型
`void MQTTClient_destroy(MQTTClient* handle);`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
handle | MQTTClient* | 客户端句柄的指针。

### 返回值
无。

### 代码示例
```c
MQTTClient_destroy(&client);
```


