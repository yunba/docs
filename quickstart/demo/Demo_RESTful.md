# 运行 Yunba RESTful API Demo

本文演示云巴的 RESTful API 的使用。

本文涉及的运行环境如下：

* Mac OS X 10.11.1
* Safari 9.0.1
* Bash 2.6

## 准备工作

### 注册云巴开发者账号
打开 [云巴官方网站](http://yunba.io "云巴官方网站")，点击右上角的“注册”按钮创建账号。  

## 详细步骤

###1. 申请 AppKey

在演示 RESTful API 的范例之前，我们先准备好一个集成了云巴 SDK 的客户端程序，用来收消息。

例如，就使用 Yunba Android SDK 中提供的 Demo 程序，
其创建方法可参考 [运行 Yunba Android Demo](http://yunba.io/docs2/android_demo) 一文。
创建完成后，可在 Portal 的应用详情页面，获得 AppKey 和 Secret Key。

鉴于后文的描述中，会向 Android SDK Demo 发消息，这里可以提前做一些设置。
如，订阅“news”，将 Android 客户端的别名（alias）设置为 Jack。

###2. RESTful API 用法示例

下面给出 RESTful API 的用法示例。注意，示例中的“appkey”和“seckey”均需要替换为步骤 1 中获得的 AppKey 和 Secret Key。

####2.1 HTTP GET 示例


* `publish`方法：

在 Mac 的 Terminal 下，输入下面的 curl 命令，将会向名为“news”的 topic 发送一条消息，消息内容是“good_news”。
由于 Android SDK Demo 程序在步骤 1 中曾订阅过“news”，它将收到这条消息。

```bash
$ curl  --request GET "http://rest.yunba.io:8080?method=publish&appkey=XXXXXXXXXXXXXXXXXXXXXXX&seckey=sec-XXXXXXXXXXXXXXXXXXXXXXXXXXXXX&topic=news&msg="good_news""

```
发送后，会看到如下格式的返回值以及 Message ID，详见文末的状态参数说明。
```bash
{"status":0,"messageId":512632202199044096}
```

* `publish_to_alias`方法：

这个例子，向别名为“bulb001”的客户端发送了一条 JSON 的消息。可以在浏览器地址栏中打开。

```html
http://rest.yunba.io:8080?method=publish_to_alias&appkey=XXXXXXXXXXXXXXXXXXXXXXX&seckey=sec-XXXXXXXXXXXXXXXXXXXXXXXXXXXXX&alias=bulb001&msg={"p":999,"r":1111,"g":0,"b":0}
```
同样地，发送后会在浏览器中看到如下格式的返回信息，详见文末的状态参数说明。
```bash
{"status":0,"messageId":512633115034783744}
```

####2.2 HTTP POST 示例
#####2.2.1 请求 JSON 的格式

首先介绍 请求 JSON 的格式。

* 向 topic 广播消息：

```json
{"method":<method>, "appkey":<app-key>, "seckey":<secret-key>, "topic":<topic>, "msg":<message>}
```

* 向 alias 发一对一消息：

```json
{"method":<method>, "appkey":<app-key>, "seckey":<secret-key>, "alias":<alias> , "msg":<message>}
```

* 可选项：

在 JSON 中，可选部分如下:

```json
"opts":{"time_to_live":<number>,"platform":<number>,"time_delay":<number>,"location":<string>,"qos":<number>,"apn_json":{"aps":{"alert":<string>,"badge":<number>,"sound":<string>,"priority":<number>,"expiration":<number>,"content-available":<number>}}}
```

* APN 

APN 部分，请参考苹果官方说明——[Apple Push Notification Service](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW12 "A")。

如果在 opts 中没有出现 apn_json 项时，就不会发送 APN。

#####2.2.2 API 示例

下面开始介绍目前支持的几种 RESTful 方法，包括`publish`，`publish_to_alias`，`publish_to_alias_batch`，`publish_async` 和 `publish_check`。

* `publish` 

用于向指定的 topic 发送广播消息。

```bash
$ curl -l -H "Content-type: application/json" -X POST -d '{"method":"publish", "appkey":"XXXXXXXXXXXXXXXXXXXXXXX", "seckey":"sec-XXXXXXXXXXXXXXXXXXXXXXXXXXXXX", "topic":"news", "msg":"good news"}' http://rest.yunba.io:8080
```

运行后，会收到返回信息，详见文末的状态参数说明。


* `publish_to_alias` 

用于向指定的别名发送一对一的消息。


```bash
$ curl -l -H "Content-type: application/json" -X POST -d '{"method":"publish_to_alias", "appkey": "XXXXXXXXXXXXXXXXXXXXXXX", "seckey":"sec-XXXXXXXXXXXXXXXXXXXXXXXXXXXXX", "alias”:”Jack”, "msg":"message from RESTful API", "opts":{"time_to_live":20000}}' http://rest.yunba.io:8080
```

运行后，会收到返回信息，详见文末的状态参数说明。


* `publish_to_alias_batch` 

该方法可以同时向多个 alias 发消息。例如，我们向 Android SDK Demo 中曾设置的别名 Jack 和 Rose 发送消息。

```bash
$ curl -l -H "Content-type: application/json" -X POST -d '{"method":"publish_to_alias_batch", "appkey":"XXXXXXXXXXXXXXXXXXXXXXX", "seckey":"sec-XXXXXXXXXXXXXXXXXXXXXXXXXXXXX", "aliases":["Jack","Rose"], "msg":"good news", "opts":{"time_to_live": 20}}' http://rest.yunba.io:8080
```

发送后，返回信息格式如下。从这个信息中可以看出，发送给 Jack 的消息成功了。但由于没有找到叫 Rose 的客户端，发给 Rose 的消息没有成功。

**注**：别名设置的逻辑是，新设置的别名会取代之前设置的别名。

```bash
{"status":0,"results":{"Jack":{"status":0,"messageId":512625795122860032},"Rose":{"status":5,"alias":"56251969be17bc415cfbf2a1-Rose","error":"alias not found"}}}
```

* `publish_async`

下面这个例子演示了 `publish_async` 的使用。`publish_async` 和 `publish` 的不同之处在于前者是异步的，会立即返回，而后者则要等操作完成后才能返回。

```bash
$ curl -l -H "Content-type: application/json" -X POST -d '{"method":"publish_async", "appkey":"XXXXXXXXXXXXXXXXXXXXXXX", "seckey":"sec-XXXXXXXXXXXXXXXXXXXXXXXXXXXXX", "topic":"news", "msg":"good news"}' http://rest.yunba.io:8080
```

调用成功后，会收到如下格式的返回信息，详见文末的状态参数说明：
```bash
{"status":0,"messageId":512619373110763520}
```

* `publish_check`

当使用 `publish_async` 方法发送后，可以使用 `publish_check` 进行检查。以上面的返回值为例，将 Message ID “512619373110763520”作为消息的内容，填写到 `publish_check` 的“msg”处即可。如下：

```bash
$ curl -l -H "Content-type: application/json" -X POST -d '{"method":"publish_check", "appkey":"XXXXXXXXXXXXXXXXXXXXXXX", "seckey":"sec-XXXXXXXXXXXXXXXXXXXXXXXXXXXXX", "topic”:”news”, "msg":"512619373110763520"}' http://rest.yunba.io:8080
```

发送后，会收到返回信息，详见下文。


####2.3 发送状态回复

下面列出返回的状态参数的具体含义。

* 发送成功

```json
{"status":0, "messageId": "<message-id>"}
```

* 参数错误

```json
{"status":1, "error": "invalid parameters"}
```

* 内部服务错误

```json
{"status":2, "error": "internal server"}
```

* 没有应用

```json
{"status":3}
```

* 发布超时

```json
{"status":4, "error": "timeout"}
```

 * alias 没有找到
 
```json
{"status":5, "alias":"XXXXbd7179b6570f2ca6XXXX-mytest", "error": "alias not found"}
```
