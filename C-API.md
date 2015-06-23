#Yunba C API 手册 

## MQTTClient_getVersionInfo
### 功能 

###函数原型
` MQTTClient_nameValue* MQTTClient_getVersionInfo(); `

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
null | null | null

### 返回值
* MQTTClient_nameValue* : 保存 SDK 版本号的结构体指针

### Code Example
```c
MQTTClient_nameValue* version = MQTTClient_getVersionInfo();
printf("used:%s, %s\n", version->name, version->value);
```


## MQTTClient_setup_with_appkey
### 功能

通过 appkey ，获得注册信息。使用该函数时，每次用户都将会获得新的注册信息，即 client id，username，password，device id。

###函数原型
` int MQTTClient_setup_with_appkey(char* appkey, REG_info *info) `

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
appkey | char* | 用户通过网站注册获得到的 app key。
info | REG_info* | 保存获取到的注册信息。

### 返回值
* int : 为0时，表示成功。小于0，表示失败。

### Code Example
```c
int res = MQTTClient_setup_with_appkey(appkey, &my_reg_info);
printf("Get reg info: client_id:%s,username:%s,password:%s, devide_id:%s\n", my_reg_info.client_id, my_reg_info.username, my_reg_info.password, my_reg_info.device_id);
```

## MQTTClient_setup_with_appkey_and_deviceid
### 功能

通过 appkey ，device_id 获得注册信息。

当 deviceid 为 NULL ，云巴会返回一个 deviceid 给用户，用户需保存改 deviceid 。下次调用该函数时，需使用该 deviceid 。

用户可自使用自己的 deviceid ，但需保证该 deiviceid 的唯一性。

###函数原型
` int MQTTClient_setup_with_appkey_and_deviceid(char* appkey, char *deviceid, REG_info *info) `

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
appkey | char* | 用户通过网站注册获得到的 app key 。
deviceid | char* | 用户自定义 device id 。
info | REG_info* | 保存获取到的注册信息。

### 返回值
* int : 为0时，表示成功。小于0，表示失败。

### Code Example
```c
int res = MQTTClient_setup_with_appkey_and_deviceid(appkey, device_id, &my_reg_info);
printf("Get reg info: client_id:%s,username:%s,password:%s, devide_id:%s\n", my_reg_info.client_id, my_reg_info.username, my_reg_info.password, my_reg_info.device_id);
```

## MQTTClient_get_host
### 功能

通过 appkey ，获得 host 信息。

###函数原型
` int MQTTClient_get_host(char *appkey, char* url) `

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
appkey | char* | 主用注册时或得到的 app key 。
url | char* | 保存获取到的 host 。

### 返回值
* int : 为0时，表示成功。小于0，表示失败。

### Code Example
```c
char url[200];
int res = MQTTClient_get_host(appkey, url);
```

## MQTTClient_subscribe
### 功能

App 可以增加订阅一个 Topic , 以便可以接收来自 Topic 的 Message 。

### 函数原型
` int MQTTClient_subscribe(MQTTClient handle, char* topic); `

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
handle | MQTTClient|  客户端句柄
topic | char* | 订阅的的主题，topic 只支持英文数字下划线，长度不超过50个字符

### 返回值
* (int): MQTTCLIENT_SUCCESS 说明操作成功。详细请查看yunba.h中定义的返回码

### Code Example
```c
rc = MQTTClient_subscribe(client, "rocket");
```

## MQTTClient_unsubscribe
### 功能

App 可以取消订阅一个 Topic。

### 函数原型
`  int MQTTClient_unsubscribe(MQTTClient handle, char* topic); `

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
handle | MQTTClient | 客户端句柄
topic | char* | 取消订阅的的主题，topic 只支持英文数字下划线，长度不超过50个字符

### 返回值
* (int): MQTTCLIENT_SUCCESS 说明操作成功。详细请查看yunba.h中定义的返回码

### Code Example
```c
rc = MQTTClient_unsubscribe(client, "rocket");
```

## MQTTClient_presence
### 功能

App 可以调用此函数来监听 Topic 下面所有的用户的别名状态的变化

### 函数原型
` int MQTTClient_presence(MQTTClient handle, char* topic); `

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
handle | MQTTClient | 客户端句柄
topic | char* | 订阅的的主题，topic 只支持英文数字下划线，长度不超过50个字符

### 返回值
* (int): MQTTCLIENT_SUCCESS 说明操作成功。详细请查看yunba.h中定义的返回码

### Code Example
```c
rc = MQTTClient_presence(client, "rocket");
```

## MQTTClient_unpresence
### 功能

取消监听对应 Topic 下用户状态的变化

### 函数原型
` int MQTTClient_unpresence(MQTTClient handle, char* topic); `

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
handle | MQTTClient | 客户端句柄
topic | char* | 订阅的的主题，topic 只支持英文数字下划线，长度不超过50个字符

### 返回值
* (int): MQTTCLIENT_SUCCESS 说明操作成功。详细请查看yunba.h中定义的返回码

### Code Example
```c
rc = MQTTClient_unpresence(client, "rocket");
```

## MQTTClient_publish
### 功能

App 可以向 Topic 发送消息, 那么任何订阅此 Topic 的 Client 都会接受到消息。

### 函数原型
` int MQTTCient_publish(MQTTClient handle, char* topicName, int data_len, void *data) `

### 参数说明:
名称 | 类型 | 说明
--------- | ------- | -----------
handle | MQTTClient | 客户端句柄
topic | char* | 订阅的主题，topic 只支持英文数字下划线，长度不超过50个字符
data_len | int | 消息内容长度
data | void* | 消息指针

### 返回值
* (int): MQTTCLIENT_SUCCESS说明操作成功。详细请查看yunba.h中定义的返回码

### Code Example
```c
char buf[100] = "Just test";
int data_len = strlen(buf);
rc = MQTTClient_publish(client, topic, data_len, buffer);
```

## MQTTClient_publish_to_alias
### 功能

App 向某个 alias 发送 message。

### 函数原型
` int MQTTClient_publish_to_alias(MQTTClient handle, char* alias, int data_len, void* data); `

### 参数说明:
名称 | 类型 | 说明
--------- | ------- | -----------
handle | MQTTClient | 客户端句柄
alias | char* | alias 名字
data_len | int | 消息内容长度
data | void* | 消息指针

### 返回值
* (int): MQTTCLIENT_SUCCESS 说明操作成功。详细请查看yunba.h中定义的返回码

### Code Example
```c
rc = MQTTClient_publish_to_alias(client, "Hello_alias", strlen("test"), "test");
```

## MQTTClient_publish_json
### 功能

App 可以向 Topic 发送 json 包, 那么任何订阅此 Topic 的 Client 都会接受到消息。

### 函数原型
` int MQTTClient_publish_json(MQTTClient handle, char* topicName, cJSON* data) `

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
handle | MQTTClient | 客户端句柄
topic | char* | 订阅的主题，topic 只支持英文数字下划线，长度不超过50个字符
data | cJSON* | json 包

### 返回值
* (int): MQTTCLIENT_SUCCESS 说明操作成功。详细请查看yunba.h中定义的返回码

### Code Example
```c
char buf[100] = "{\"num_name\":2}";
cJSON *data = cJSON_Parse(buf);
rc = MQTTClient_publish_json(client, topic, data);
cJSON_Delete(data);
```

## MQTTClient_publish2
### 功能

App 可以向 Topic 发送 publish2, 那么任何订阅此 Topic 的 Client 都会接受到消息。

### 函数原型
` int MQTTClient_publish2(MQTTClient handle, const char* topicName, int payloadlen, void* payload, cJSON *data); 
`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
handle | MQTTClient | 客户端句柄
topic | char* | 订阅的主题，topic 只支持英文数字下划线，长度不超过50个字符
payloadlen | int | payload的长度
payload | void* | payload内容
data | CJSON * | Opt选项，可以带apn，ttl等参数内容

### 返回值
* (int): MQTTCLIENT_SUCCESS 说明操作成功。详细请查看yunba.h中定义的返回码

### Code Example
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

## MQTTClient_publish2_to_alias
### 功能

App 可以向 alias 发送 publish2, 那么任何订阅此 Topic 的 Client 都会接受到消息。

### 函数原型
` int MQTTClient_publish2_to_alias(MQTTClient handle, const char* alias, int payloadlen, void* payload, cJSON *data);
`

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
handle | MQTTClient | 客户端句柄
alias | const char* | 发送对方的别名
payloadlen | int | payload的长度
payload | void* | payload内容
data | CJSON * | Opt选项，可以带apn，ttl等参数内容

### 返回值
* (int): MQTTCLIENT_SUCCESS 说明操作成功。详细请查看yunba.h中定义的返回码

### Code Example
```c
cJSON *apn_json, *aps;
cJSON *Opt = cJSON_CreateObject();
cJSON_AddStringToObject(Opt,"time_to_live",  "120");
cJSON_AddStringToObject(Opt,"time_delay",  "1100");
cJSON_AddStringToObject(Opt,"apn_json",  "{\"aps\":{\"alert\":\"FENCE alarm\", \"sound\":\"alarm.mp3\"}}");
ret = MQTTClient_publish2_to_alias(client, "Alex", strlen("test"), "test", Opt);
cJSON_Delete(Opt);
printf("publish2 status:%i\n", ret);
```

## MQTTClient_set_alias
### 功能

App 可以调用此函数来绑定账号，用户名，每个用户只能指定一个别名。

### 函数原型
` int MQTTClient_set_alias(MQTTClient handle, char* alias); `

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
handle | MQTTClient | 客户端句柄
alias  | char* | 用户设置的别名信息，只支持英文数字下划线，长度不超过50个字符

### 返回值
* (int): MQTTCLIENT_SUCCESS 说明操作成功。详细请查看yunba.h中定义的返回码

### Code Example
```c
int ret = MQTTClient_set_alias(client, "000000018302");
```

## MQTTClient_get_alias
### 功能

App 可以调用此函数来获取当前用户的别名

### 函数原型
` int MQTTClient_get_alias(MQTTClient handle, char* parameter); `

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
handle | MQTTClient | 客户端句柄
parameter | char* | 参数

### 返回值
* (int): MQTTCLIENT_SUCCESS 说明操作成功。详细请查看yunba.h中定义的返回码

### Code Example
```c
int ret = MQTTClient_get_alias(client, "0");
```

在回调函数 extendedCmdArrive 中获得该用户的 alias。

## MQTTClient_get_status
### 功能

App 可以调用此函数来获得某个 alias 的用户状态。

### 函数原型
` int MQTTClient_get_status(MQTTClient handle, char* parameter); `

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
handle | MQTTClient | 客户端句柄
parameter | char* | alias名字

### 返回值
* (int): MQTTCLIENT_SUCCESS 说明操作成功。详细请查看yunba.h中定义的返回码

### Code Example
```c
int ret = MQTTClient_get_alias(client, "000000018302");
```

在回调函数 extendedCmdArrive 中获得该用户的状态.

## MQTTClient_get_aliaslist
### 功能

App 可以调用此函数来某个 topic 的别名列表

### 函数原型
`  int MQTTClient_get_aliaslist(MQTTClient handle, char* parameter); `

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
handle | MQTTClient | 客户端句柄
parameter | char* | topic名字

### 返回值
* (int): MQTTCLIENT_SUCCESS 说明操作成功。详细请查看yunba.h中定义的返回码

### Code Example
```c
int ret = MQTTClient_get_aliaslist(client, "rocket");
```

在回调函数 extendedCmdArrive 中获得该用户的状态.

## MQTTClient_get_topic
### 功能

App 可以调用此函数来某个 alias 的所订阅的 topic 。

### 函数原型
` int MQTTClient_get_topic(MQTTClient handle, char* parameter); `

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
handle | MQTTClient | 客户端句柄
parameter | char* | 用户别名

### 返回值
* (int): MQTTCLIENT_SUCCESS 说明操作成功。详细请查看yunba.h中定义的返回码

### Code Example
```c
int ret = MQTTClient_get_topic(client, "000000018302");
```

在回调函数 extendedCmdArrive 中获得该用户的状态.


## MQTTClient_report
### 功能

App 可以调用此函数来上报客户端的行为，如打开通知栏次数，按钮点击次数，资源下载成功等等行为。

### 函数原型
`  int MQTTClient_report(MQTTClient handle, char* action, char* data); `

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
handle | MQTTClient | 客户端句柄
action | char* | app 需要统计的行为，如打开通知栏，下载资源成功等等
data | char* | 想对应 action 的附加数据，以满足统计相关的其他业务需求。

### 返回值
* (int): MQTTCLIENT_SUCCESS 说明操作成功。详细请查看yunba.h中定义的返回码

### Code Example
```c
int ret = MQTTClient_report(client, "action", "data");
```

## MQTTClient_set_broker
### 功能

App 可以设置broker

### 函数原型
` int MQTTClient_set_broker(MQTTClient* handle, char* broker); `

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
handle | MQTTClient* | 客户端句柄指针
broker | char* | broker域名或者 ip 地址

### 返回值
* (int): MQTTCLIENT_SUCCESS 说明操作成功。详细请查看yunba.h中定义的返回码

### Code Example
```c
int ret = MQTTClient_set_broker(client, "192.168.1.100");
```


## MQTTClient_get_broker
### 功能

App 可以获得broker

### 函数原型
` int MQTTClient_get_broker(MQTTClient* handle, char* broker); `


### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
handle | MQTTClient* | 客户端句柄指针
broker | char* |存放 broker 的指针

### 返回值
* (int): MQTTCLIENT_SUCCESS 说明操作成功。详细请查看yunba.h中定义的返回码

### Code Example
```c
char buf[100];
int ret = MQTTClient_set_broker(client, buf);
```

## MQTTClient_freeMessage
### 功能

释放message资源

### 函数原型
` void MQTTClient_freeMessage(MQTTClient_message** msg); `

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
msg | MQTTClient_message** | 指向 message 指针的指针

### 返回值
null

### Code Example
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
` void MQTTClient_destroy(MQTTClient* handle); `


### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
handle | MQTTClient* | 客户端句柄指针

### 返回值
null

### Code Example
```c
MQTTClient_destroy(&client);
```
