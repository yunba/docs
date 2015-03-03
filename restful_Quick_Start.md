# Yunba RESTful API 快速入门

## 注册开发者账号

打开 <http://yunba.io>, 点击注册创建账号。

![create_accout.jpg](../image/register_account.png)

## 创建应用
注册账号成功跳转到我的应用界面，点击我的应用 --> 创建新应用，输入应用名称和包名

![create_application.jpg](../image/create_app.png)

## 方法

### HTTP GET

```url
http://rest.yunba.io:8080/rest/?method=<method>&appkey=<app-key>&seckey=<secret-key>&topic=<topic>&msg="your message"
```

比如

```bash
$curl  --request GET "http://rest.yunba.io:8080/rest/?method=publish&appkey=5316bd7179b6570f2ca6e20b&seckey=sec-qaAQOCmuFL22b0mv78hcOEyc9DzB9q0zesIfBAereaN6FAcb&topic=helllo&msg="Thistest""
```

或直接在浏览器地址栏打开。

在publish_to_alias中，请用 alias=<alias> 替换 topic=<topic> 即可。

### HTTP POST 

请求 JSON 格式如下:

```json
{"method":<method>, "appkey":<app-key>, "seckey":<secret-key>, "topic":<topic>, "msg":<message>}
```
在publish_to_alias中，请用 "alias":<alias> 替换 "topic":<topic> 即可。

在 JSON 中可选部分:

```json
"opts":{"time_to_live":<number>,"platform":<number>,"time_delay":<number>,"location":<string>,"qos":<number>,"apn_json":{"alert":<string>,"badge:<number>,"sound":<string>,"priority":<number>,"expiration":<number>","content-available":<number>}}
```

APN部分，参见苹果官方说明。 [Apple Push Notification Service](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW12 "A")

比如:

> publish

```bash
$ curl -l -H "Content-type: application/json" -X POST -d '{"method":"publish", "appkey":"53ea21cd4e9f46851d5a57b5", "seckey":"sec-QMirTLEpuNC6tIUynXXXXNfrlWDbgDV64iDnjdni4QFyXXXX", "topic":"rocket", "msg":"just test"}' http://rest.yunba.io:8080
```

> publish_to_alias 


```bash
$ curl -l -H "Content-type: application/json" -X POST -d '{"method":"publish_to_alias", "appkey": "XXXXbd7179b6570f2ca6XXXX", "seckey":"sec-XXXXOCmuFL22b0mv78hcOEyc9DzB9q0zesIfBAereaN6XXXX", "alias":"alias_mqttc_sub", "msg":"message from RESTful API", "opts":{"time_to_live":20000}}' http://rest.yunba.io:8080
```

> publish_to_alias_batch


```bash
$ curl -l -H "Content-type: application/json" -X POST -d '{"method":"publish_to_alias_batch", "appkey":"XXXXbd7179b6570f2ca6XXXX", "seckey":"sec-XXXXOCmuFL22b0mv78hcOEyc9DzB9q0zesIfBAereaN6XXXX", "aliases":["mytest", "mytest2"], "msg":"test restful api publish_to_alias_batch 2", "opts":{"time_to_live": 20}}' http://rest.yunba.io:8080
```

>publish_async

```bash
$ curl -l -H "Content-type: application/json" -X POST -d '{"method":"publish_async", "appkey":"53ea21cd4e9f46851d5a57b5", "seckey":"sec-QMirTLEpuNC6tIUynXXXXNfrlWDbgDV64iDnjdni4QFyXXXX", "topic":"rocket", "msg":"just test"}' http://rest.yunba.io:8080
```

>publish_check
当使用方法 publish_async 发送后, 可以使用该方法检查. 注意的是msg中为 publish_async 回复中的 message id.

```bash
$ curl -l -H "Content-type: application/json" -X POST -d '{"method":"publish_async", "appkey":"53ea21cd4e9f46851d5a57b5", "seckey":"sec-QMirTLEpuNC6tIUynXXXXNfrlWDbgDV64iDnjdni4QFyXXXX", "topic":"rocket", "msg":"<message-id>"}' http://rest.yunba.io:8080
```

其中 app-key, secret-key 从应用详情中页面获得，分别对应于页面中 AppKey， Secret Key。

注意:

* <method\>: 目前只支持"publish", "publish_to_alias", "publish_to_alias_batch", "publish_async", "publish_check".

## 发送状态回复

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
 * 没有发现alias
 
```json
{"status":5, "alias":"XXXXbd7179b6570f2ca6XXXX-mytest", "error": "alias not found"}
```