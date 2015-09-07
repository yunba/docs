#Yunba Embedded-C API 手册


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
* (int): SUCCESS 说明操作成功。详细请查看 MQTTClient.h 中定义的返回码

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
* (int): SUCCESS 说明操作成功。详细请查看 MQTTClient.h 中定义的返回码

### Code Example
```c
int res = MQTTClient_setup_with_appkey_and_deviceid(appkey, device_id, &my_reg_info);
printf("Get reg info: client_id:%s,username:%s,password:%s, devide_id:%s\n", my_reg_info.client_id, my_reg_info.username, my_reg_info.password, my_reg_info.device_id);
```

## MQTTSubscribe
### 功能

App 可以增加订阅一个 Topic , 以便可以接收来自 Topic 的 Message 。

### 函数原型
` int MQTTSubscribe(Client*, const char*, enum QoS); `

### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
handle | MQTTClient|  客户端句柄
topic | char* | 订阅的的主题，topic 只支持英文数字下划线，长度不超过50个字符
qos | enum | 参看 MQTTClient.h 定义。

### 返回值
* (int): SUCCESS 说明操作成功。详细请查看 MQTTClient.h 中定义的返回码

### Code Example
```c
rc = MQTTSubscribe(client, "rocket", QOS1);
```

## MQTTUnsubscribe
### 功能

App 可以取消订阅一个 Topic。

### 函数原型
`  int MQTTUnsubscribe(Client*, const char*); `

### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
handle | Client* | 客户端句柄
topic | const char* | 取消订阅的的主题，topic 只支持英文数字下划线，长度不超过50个字符

### 返回值
* (int): SUCCESS 说明操作成功。详细请查看 MQTTClient.h 中定义的返回码

### Code Example
```c
rc = MQTTUnsubscribe(client, "rocket");
```

## MQTTPublish
### 功能

App 可以向 Topic 发送消息, 那么任何订阅此 Topic 的 Client 都会接受到消息。

### 函数原型
` int MQTTPublish (Client*, const char*, MQTTMessage*) `

### 参数说明:

名称 | 类型 | 说明
--------- | ------- | -----------
handle | Client* | 客户端句柄
msg | MQTTMessage* | 发送消息的结构体

### 返回值
* (int): SUCCESS 说明操作成功。详细请查看 MQTTClient.h 中定义的返回码

### Code Example
```c
MQTTMessage M;
M.qos = QOS1;
M.payload = buf;
M.payloadlen = strlen(buf);
rc = MQTTPublish(&c, topic, &M);
```

## MQTTPublishToAlias
### 功能

App 向某个 alias 发送 message。

### 函数原型
` int MQTTPublishToAlias(Client* c, const char* alias, void *payload, int payloadlen) `

### 参数说明:

名称 | 类型 | 说明
--------- | ------- | -----------
handle | Client* | 客户端句柄
alias | char* | alias 名字
payload | void* | 消息指针
payloadlen | int | 消息内容长度


### 返回值
* (int): SUCCESS 说明操作成功。详细请查看 MQTTClient.h 中定义的返回码

### Code Example
```c
rc = MQTTPublishToAlias(&c, "alias", "test", strlen("test"));
```

## MQTTSetAlias
### 功能

App 可以调用此函数来绑定账号，用户名，每个用户只能指定一个别名。

### 函数原型
` int MQTTSetAlias(Client*, const char*); `

### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
handle | Client* | 客户端句柄
alias  | char* | 用户设置的别名信息，只支持英文数字下划线，长度不超过50个字符

### 返回值
* (int): SUCCESS 说明操作成功。详细请查看 MQTTClient.h 中定义的返回码

### Code Example
```c
int ret = MQTTSetAlias(client, "000000018302");
```

## MQTTGetAlias
### 功能

App 可以调用此函数来获取当前用户的别名

### 函数原型
` int MQTTGetAlias(Client* c, const char *param) `

### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
handle | Client* | 客户端句柄
parameter | char* | 别名

### 返回值
* (int): SUCCESS 说明操作成功。详细请查看 MQTTClient.h 中定义的返回码

### Code Example
```c
int ret = MQTTGetAlias(client, "0");
```

在回调函数 extMessageArrive 中获得该用户的 alias。

## MQTTGetStatus
### 功能

App 可以调用此函数来获得某个 alias 的用户状态。

### 函数原型
` int MQTTGetStatus(Client* c, const char *parameter); `

### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
handle | Client* | 客户端句柄
parameter | char* | alias名字

### 返回值
* (int): SUCCESS 说明操作成功。详细请查看 MQTTClient.h 中定义的返回码

### Code Example
```c
int ret = MQTTGetStatus(client, "000000018302");
```

在回调函数 extendedCmdArrive 中获得该用户的状态.

## MQTTGetAliasList
### 功能

App 可以调用此函数来某个 topic 的别名列表

### 函数原型
`  int MQTTGetAliasList(Client* c, const char *parameter); `

### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
handle | Client* | 客户端句柄
parameter | char* | topic 名字

### 返回值
* (int): SUCCESS 说明操作成功。详细请查看 MQTTClient.h 中定义的返回码

### Code Example
```c
int ret = MQTTGetAliasList(client, "rocket");
```

在回调函数 extendedCmdArrive 中获得　alias 列表.

## MQTTGetTopic
### 功能

App 可以调用此函数来某个 alias 的所订阅的 topic 。

### 函数原型
` int MQTTGetTopic(Client* c, const char *parameter); `

### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
handle | Client* | 客户端句柄
parameter | char* | 用户别名

### 返回值
* (int): SUCCESS 说明操作成功。详细请查看 MQTTClient.h 中定义的返回码

### Code Example
```c
int ret = MQTTGetTopic(client, "000000018302");
```

在回调函数 extendedCmdArrive 中获得订阅的　topic 。


## MQTTReport
### 功能

App 可以调用此函数来上报客户端的行为，如打开通知栏次数，按钮点击次数，资源下载成功等等行为。

### 函数原型
`  int MQTTReport(Client* c, const char* action, const char *data); `

### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
handle | Client* | 客户端句柄
action | char* | app 需要统计的行为，如打开通知栏，下载资源成功等等
data | char* | 想对应 action 的附加数据，以满足统计相关的其他业务需求。

### 返回值
* (int): SUCCESS 说明操作成功。详细请查看 MQTTClient.h 中定义的返回码

### Code Example
```c
int ret = MQTTReport(client, "action", "data");
```

## MQTTSetCallBack
### 功能

用户设置回调函数，用来接收 MQTT 消息。

### 函数原型
` int MQTTSetCallBack(Client *c, messageHandler, extendedmessageHandler); `

### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
handle | Client* | 客户端句柄
msgHandler | messageHandler | 接收 MQTT 消息的回调函数 
extmsgHandler | extendedmessageHandler | 接收云巴扩展命令字 MQTT 消息的回调函数。

### 返回值
* (int): SUCCESS 说明操作成功。详细请查看 MQTTClient.h 中定义的返回码

### Code Example
```c
MQTTSetCallBack(&c, messageArrived, extMessageArrive);
```

## MQTTConnect
### 功能

MQTT 连接到　broker 。

### 函数原型
` int MQTTConnect (Client*, MQTTPacket_connectData*); `

### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
handle | Client* | 客户端句柄
connData | MQTTPacket_connectData× | MQTT 连接需传递数据结构体指针

### 返回值
* (int): SUCCESS 说明操作成功。详细请查看 MQTTClient.h 中定义的返回码

### Code Example
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
` int MQTTDisconnect (Client*); `

### 参数说明

名称 | 类型 | 说明
--------- | ------- | -----------
handle | Client* | 客户端句柄指针

### 返回值
* (int): SUCCESS 说明操作成功。详细请查看 MQTTClient.h 中定义的返回码

### Code Example
```c
MQTTDisconnect(&c);
```
