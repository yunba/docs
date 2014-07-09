#Yunba C 快速入门

##Where do I get the code?
你可以使用下面命令去获得sdk:
```c
git clone https://github.com/alexbank/c_sdk.git
```
##How do I get started?
###1.添加lib以及header到Makefile：
```c
INCLUDEPATH = -I/home/yunba/test/cmqtt-sdk/install/include
LIBPATH = -L/home/yunba/test/cmqtt-sdk/install/lib
```
其中/home/yunba/test/cmqtt-sdk/install是你的yun SDK目录。
###2.在应用中添加yunba服务。
在你的代码中应用
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
##how to get demo code?
使用下面命令获得demo代码
```c
git clone https://github.com/alexbank/yunba_demo.git
```
