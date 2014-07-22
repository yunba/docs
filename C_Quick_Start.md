#Yunba C 快速入门
## 注册开发者账号
打开 <http://yunba.io>, 点击注册创建账号。

![create_accout.jpg](../image/register_account.png)

## 创建应用
注册账号成功跳转到我的应用界面，点击我的应用 --> 创建新应用，输入应用名称和包名

![create_application.jpg](image/create_app.png)

## 如何获得client-id, 用户名以及密码。

使用下面的命令，其中5335292c44deccab56399e4f为注册时获得到的app key.
```c
curl -X  POST -H "Content-Type: application/json" -d '{"a": "5335292c44deccab56399e4f", "p":2}'  http://reg.yunba.io:8383/device/reg/
```

回复获得
```c
{
  "u": "J3SoTq6t",
  "p": "UFOrH0xkC",
  "c": "0000000034-000000000276"
}
```

json包中，u为用户名，p为密码。c为client-id.


##从哪里获得sdk
你可以使用下面命令去获得sdk:

打开 <http://yunba.io/developers/> 下载 C SDK。

##怎么开始
###1.添加lib以及header到Makefile：

```c
INCLUDEPATH = -I/home/yunba/test/cmqtt-sdk/install/include
LIBPATH = -L/home/yunba/test/cmqtt-sdk/install/lib
```

其中/home/yunba/test/cmqtt-sdk/install是你的yun SDK目录。
###2.在应用中添加yunba服务。
在你的代码中应用包含:

```c
#include "yunba.h"
```

在入口函数中添加yunba服务初始化：

```c
MQTTClient_connectOptions conn_opts = MQTTClient_connectOptions_initializer;
rc = MQTTClient_create(&client, url, opts.clientid, MQTTCLIENT_PERSISTENCE_NONE, NULL);

rc = MQTTClient_setCallbacks(client, NULL, NULL, messageArrived, NULL, extendedCmdArrive);
```

订阅你的频道

```c
rc = MQTTClient_subscribe(client, "your_channel");
```

其中messageArrived， extendedCmdArrive为回调函数。

下面函数处理status, get alias get-topic等扩展命令。

```c
int extendedCmdArrive(void *context, EXTED_CMD cmd, int status, int ret_string_len, char *ret_string)
{
  //处理接收到的扩展命令返回。
}

int messageArrived(void* context, char* topicName, int topicLen, MQTTClient_message* m)
{
  //处理topic以及推送的消息内容。
}
```  

当你的程序退出时，不要忘记使用：

```c
MQTTClient_destroy(&client)
```

##怎样获得demo代码
使用下面命令获得demo代码


打开 <http://yunba.io/developers/> 下载 C demo。

