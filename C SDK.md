#Yunba C quick start
##Where do I get the code?
你可以使用下面命令去获得sdk:
git clone https://github.com/yunba_c_sdk.git
##How do I get started?
###1.添加lib以及header到你的Makefile,比如：
INCLUDEPATH = -I/home/yunba/test/cmqtt-sdk/install/include
LIBPATH = -L/home/yunba/test/cmqtt-sdk/install/lib
其中/home/yunba/test/cmqtt-sdk/install是你的yun SDK目录。
###2.在应用中添加yunba服务。
在你的代码中应用
···c
#include "yunba.h"
```
在入口函数中添加yunba服务初始化：
···c
MQTTClient_connectOptions conn_opts = MQTTClient_connectOptions_initializer;
rc = MQTTClient_create(&client, url, opts.clientid, MQTTCLIENT_PERSISTENCE_NONE, NULL);

rc = MQTTClient_setCallbacks(client, NULL, NULL, messageArrived, NULL, extendedCmdArrive);
```
订阅你的频道
```c
rc = MQTTClient_subscribe（client, "your_channel"）;
```

当你的程序退出时，不要忘记使用：
···c
MQTTClient_destroy(&client)
```
##如何获得demo
使用下面命令获得demo代码。
git clone https://github.com/yunba_c_sdk_demo.git


# Yunba C SDK 使用文档
导入 SDK
根据用户平台芯片下载相应的yunba c sdk。比如arm平台。下载 yunba-c-sdk-release-for-arm.tar.gz包解压到本地。在使用yunba的代码中，加入相应的头文件。在Makefile添加yunba库文件。

## 添加使用代码
初始化 YunBa SDK。请在您的代码入口函数main后处添加类似如下代码：
```c
MQTTClient client;
MQTTClient_connectOptions conn_opts = MQTTClient_connectOptions_initializer;
char url[100];
sprintf(url, "%s:%s", opts.host, opts.port);
rc = MQTTClient_create(&client, url, opts.clientid, MQTTCLIENT_PERSISTENCE_NONE, NULL);
MQTTClient_connect(client, &conn_opts);
```
## 添加 Message Received 代码
```c
rc = MQTTClient_setCallbacks(client, NULL, NULL, messageArrived, NULL, extendedCmdArrive);
```
其中messageArrived， extendedCmdArrive为回调函数。

下面函数处理status, get alias get-topic等扩展命令。
```c
int extendedCmdArrive(void *context, EXTED_CMD cmd, int status, int ret_string_len, char *ret_string) {
  //处理接收到的扩展命令返回。
}

int messageArrived(void* context, char* topicName, int topicLen, MQTTClient_message* m) {
  //处理topic以及推送的消息内容。
}
```

##API - MQTTClient_getVersionInfo
### 功能

获得使用SDK的版本号

###函数原型
```c
MQTTClient_nameValue* MQTTClient_getVersionInfo();
```
### 参数说明
* null

### Code Example
```c
MQTTClient_nameValue* version = MQTTClient_getVersionInfo();
printf("used:%s, %s\n", version->name, version->value);
```

## API - MQTTClient_subscribe
### 功能

App 可以增加订阅一个Topic, 以便可以接收来自 Topic 的 Message。

### 函数原型
```c
int MQTTClient_subscribe(MQTTClient handle, char* topic);
```
### 参数说明
* handle: 客户端句柄
* topic: 订阅的的主题，topic 只支持英文数字下划线，长度不超过50个字符

### Code Example
```c
rc = MQTTClient_subscribe(client, “rocket”);
```

## API - MQTTClient_unsubscribe
### 功能

App 可以取消订阅一个Topic。

### 函数原型
```c
int MQTTClient_unsubscribe(MQTTClient handle, char* topic);
```
### 参数说明
* handle: 客户端句柄
* topic: 取消订阅的的主题，topic 只支持英文数字下划线，长度不超过50个字符

### Code Example
```c
rc = MQTTClient_unsubscribe(client, “rocket”);
```

## API - MQTTClient_publish
### 功能

App 可以向 Topic 发送消息, 那么任何订阅此 Topic 的 Client 都会接受到消息。

### 函数原型
```c
MQTTCient_publish(MQTTClient handle, char* topicName, int data_len, void *data)
```
### 参数说明
* handle: 客户端句柄
* topic: 订阅的主题，topic 只支持英文数字下划线，长度不超过50个字符
* data_len: 消息内容长度
* data: 消息指针

### Code Example
```c
char buf[100] = "Just test";
int data_len = strlen(buf);
rc = MQTTClient_publish(client, topic, data_len, buffer);
```


## API - MQTTClient_publish_json
### 功能

App 可以向 Topic 发送json包, 那么任何订阅此 Topic 的 Client 都会接受到消息。

### 函数原型
```c
int MQTTClient_publish_json(MQTTClient handle, char* topicName, cJSON* data)
```
### 参数说明
* handle: 客户端句柄
* topic: 订阅的主题，topic 只支持英文数字下划线，长度不超过50个字符
* data: json包

### Code Example
```c
char buf[100] = "{"num_name":2}";
cJSON *data = cJSON_Parse(buf);
rc = MQTTClient_publish_json(client, topic, data);
cJSON_Delete(data);
```

## API - SetAlias
### 功能

App 可以调用此函数来绑定账号，用户名，每个用户只能指定一个别名。

### 函数原型
```c
int MQTTClient_set_alias(MQTTClient handle, char* alias);
```
### 参数说明
* handle: 客户端句柄
* alias: 用户设置的别名信息，只支持英文数字下划线，长度不超过50个字符

### Code Example
```c
int ret = MQTTClient_set_alias(client, "000000018302");
```

## API - MQTTClient_get_alias
### 功能

App 可以调用此函数来获取当前用户的别名

### 函数原型
```c
int MQTTClient_get_alias(MQTTClient handle, char* parameter);
```
### 参数说明
* handle: 客户端句柄
* parameter: 参数

### Code Example
```c
int ret = MQTTClient_get_alias(client, "0");
```

在回调函数extendedCmdArrive中获得该用户的alias.

## API - MQTTClient_get_status
### 功能

App 可以调用此函数来获得某个alias的用户状态。

### 函数原型
```c
int MQTTClient_get_status(MQTTClient handle, char* parameter);
```
### 参数说明
* handle: 客户端句柄
* parameter: alias名字

### Code Example
```c
int ret = MQTTClient_get_alias(client, "000000018302");
```

在回调函数extendedCmdArrive中获得该用户的状态.

## API - MQTTClient_get_aliaslist
### 功能

App 可以调用此函数来某个topic的别名列表

### 函数原型
```c
int MQTTClient_get_aliaslist(MQTTClient handle, char* parameter);
```
### 参数说明
* handle: 客户端句柄
* parameter: topic名字

### Code Example
```c
int ret = MQTTClient_get_aliaslist(client, "rocket");
```

在回调函数extendedCmdArrive中获得该用户的状态.

## API - MQTTClient_get_topic
### 功能

App 可以调用此函数来某个alias的所订阅的topic

### 函数原型
```c
int MQTTClient_get_topic(MQTTClient handle, char* parameter);
```
### 参数说明
* handle: 客户端句柄
* parameter: 用户别名。

### Code Example
```c
int ret = MQTTClient_get_topic(client, "000000018302");
```

在回调函数extendedCmdArrive中获得该用户的状态.


## API - MQTTClient_report
### 功能

App 可以调用此函数来上报客户端的行为，如打开通知栏次数，按钮点击次数，资源下载成功等等行为。

### 函数原型
```c
int MQTTClient_report(MQTTClient handle, char* action, char* data);
```
### 参数说明
* handle: 客户端句柄
* action: app 需要统计的行为，如打开通知栏，下载资源成功等等
* data: 想对应 action 的附加数据，以满足统计相关的其他业务需求。

### Code Example
```c
int ret = MQTTClient_report(client, "action", "data");
```

## API - MQTTClient_set_broker
### 功能

App 可以设置broker

### 函数原型
```c
int MQTTClient_set_broker(MQTTClient* handle, char* broker);
```
### 参数说明
* handle: 客户端句柄指针
* broker: broker域名或者ip地址

### Code Example
```c
int ret = MQTTClient_set_broker(client, "192.168.1.100");
```


## API - MQTTClient_get_broker
### 功能

App 可以获得broker

### 函数原型
```c
int MQTTClient_get_broker(MQTTClient* handle, char* broker);
```

### 参数说明
* handle: 客户端句柄指针
* broker: 存放broker的指针

### Code Example
```c
char buf[100];
int ret = MQTTClient_set_broker(client, buf);
```

## API - MQTTClient_freeMessage
### 功能

释放message资源

### 函数原型
```c
void MQTTClient_freeMessage(MQTTClient_message** msg);
```

### 参数说明
* msg: 指向message指针的指针

### Code Example
```c
int messageArrived(void* context, char* topicName, int topicLen, MQTTClient_message* m) {
	MQTTClient_freeMessage(&m);
	MQTTClient_free(topicName);
}
```

## API - MQTTClient_destroy 
### 功能

释放客户端资源。

### 函数原型
```c
void MQTTClient_destroy(MQTTClient* handle);
```

### 参数说明
* handle:  客户端句柄指针

### Code Example
```c
MQTTClient_destroy(&client);
```
