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
* MQTTClient_nameValue* : 保存SDK版本号的结构体指针

### Code Example
```c
MQTTClient_nameValue* version = MQTTClient_getVersionInfo();
printf("used:%s, %s\n", version->name, version->value);
```

## MQTTClient_subscribe
### 功能

App 可以增加订阅一个Topic, 以便可以接收来自 Topic 的 Message。

### 函数原型
` int MQTTClient_subscribe(MQTTClient handle, char* topic); `

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
handle | MQTTClient|  客户端句柄
topic | char* | 订阅的的主题，topic 只支持英文数字下划线，长度不超过50个字符

### 返回值
* (int): MQTTCLIENT_SUCCESS说明操作成功。详细请查看yunba.h中定义的返回码

### Code Example
```c
rc = MQTTClient_subscribe(client, “rocket”);
```

## MQTTClient_unsubscribe
### 功能

App 可以取消订阅一个Topic。

### 函数原型
`  int MQTTClient_unsubscribe(MQTTClient handle, char* topic); `

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
handle | MQTTClient | 客户端句柄
topic | char* | 取消订阅的的主题，topic 只支持英文数字下划线，长度不超过50个字符

### 返回值
* (int): MQTTCLIENT_SUCCESS说明操作成功。详细请查看yunba.h中定义的返回码

### Code Example
```c
rc = MQTTClient_unsubscribe(client, “rocket”);
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

## MQTTClient_publish_json
### 功能

App 可以向 Topic 发送json包, 那么任何订阅此 Topic 的 Client 都会接受到消息。

### 函数原型
` int MQTTClient_publish_json(MQTTClient handle, char* topicName, cJSON* data) `

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
handle | MQTTClient | 客户端句柄
topic | char* | 订阅的主题，topic 只支持英文数字下划线，长度不超过50个字符
data | cJSON* | json包

### 返回值
* (int): MQTTCLIENT_SUCCESS说明操作成功。详细请查看yunba.h中定义的返回码

### Code Example
```c
char buf[100] = "{"num_name":2}";
cJSON *data = cJSON_Parse(buf);
rc = MQTTClient_publish_json(client, topic, data);
cJSON_Delete(data);
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
* (int): MQTTCLIENT_SUCCESS说明操作成功。详细请查看yunba.h中定义的返回码

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
* (int): MQTTCLIENT_SUCCESS说明操作成功。详细请查看yunba.h中定义的返回码

### Code Example
```c
int ret = MQTTClient_get_alias(client, "0");
```

在回调函数extendedCmdArrive中获得该用户的alias.

## MQTTClient_get_status
### 功能

App 可以调用此函数来获得某个alias的用户状态。

### 函数原型
` int MQTTClient_get_status(MQTTClient handle, char* parameter); `

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
handle | MQTTClient | 客户端句柄
parameter | char* | alias名字

### 返回值
* (int): MQTTCLIENT_SUCCESS说明操作成功。详细请查看yunba.h中定义的返回码

### Code Example
```c
int ret = MQTTClient_get_alias(client, "000000018302");
```

在回调函数extendedCmdArrive中获得该用户的状态.

## MQTTClient_get_aliaslist
### 功能

App 可以调用此函数来某个topic的别名列表

### 函数原型
`  int MQTTClient_get_aliaslist(MQTTClient handle, char* parameter); `

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
handle | MQTTClient | 客户端句柄
parameter | char* | topic名字

### 返回值
* (int): MQTTCLIENT_SUCCESS说明操作成功。详细请查看yunba.h中定义的返回码

### Code Example
```c
int ret = MQTTClient_get_aliaslist(client, "rocket");
```

在回调函数extendedCmdArrive中获得该用户的状态.

## MQTTClient_get_topic
### 功能

App 可以调用此函数来某个alias的所订阅的topic

### 函数原型
` int MQTTClient_get_topic(MQTTClient handle, char* parameter); `

### 参数说明
名称 | 类型 | 说明
--------- | ------- | -----------
handle | MQTTClient | 客户端句柄
parameter | char* | 用户别名

### 返回值
* (int): MQTTCLIENT_SUCCESS说明操作成功。详细请查看yunba.h中定义的返回码

### Code Example
```c
int ret = MQTTClient_get_topic(client, "000000018302");
```

在回调函数extendedCmdArrive中获得该用户的状态.


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
* (int): MQTTCLIENT_SUCCESS说明操作成功。详细请查看yunba.h中定义的返回码

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
broker | char* | broker域名或者ip地址

### 返回值
* (int): MQTTCLIENT_SUCCESS说明操作成功。详细请查看yunba.h中定义的返回码

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
broker | char* |存放broker的指针

### 返回值
* (int): MQTTCLIENT_SUCCESS说明操作成功。详细请查看yunba.h中定义的返回码

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
msg | MQTTClient_message** | 指向message指针的指针

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
