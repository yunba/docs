# 消息推送开发指南之 RESTful API 篇

不同于其他 SDK，使用 RESTful API 只能发送消息，无法接收消息。本文介绍如何使用云巴 RESTful API 发送广播消息推送。
<br>
本文涉及的运行环境如下：

* Mac OS X 10.11.1
* Safari 9.0.1
* Bash 2.6

## 准备工作

### 注册云巴开发者账号
打开 [云巴官方网站](http://yunba.io "云巴官方网站")，点击右上角的“注册”按钮创建账号。  

## 详细步骤

### 1. 在云巴 Portal 上创建新应用
请参考 [运行 Yunba Android Demo](https://github.com/yunba/docs/blob/master/quickstart/demo/Demo_Android.md) 
一文中的该步骤的做法，获得 AppKey 和 Secret Key。

###2. 使用 RESTful API 实现消息推送

####2.1 API 用法示例

下面给出通过 RESTful API 实现广播消息推送的用法示例，涉及的 API 包括：`publish`、`publish_async` 及 `publish_check`。
其中，POST 请求中的 JSON 的格式请参考下一节。
注意，示例中的“appkey”和“seckey”均需要替换为步骤 1 中获得的 AppKey 和 Secret Key。


* `publish`

用于向指定的 topic 发送广播消息。下面的例子，通过 `publish` 方法，向名为“news”的 topic 发送一条消息：“good news”。

```bash
$ curl -l -H "Content-type: application/json" -X POST -d '{"method":"publish", "appkey":"XXXXXXXXXXXXXXXXXXXXXXX", "seckey":"sec-XXXXXXXXXXXXXXXXXXXXXXXXXXXXX", "topic":"news", "msg":"good news"}' http://rest.yunba.io:8080
```

运行后，会收到返回的状态信息，详见文末的状态参数说明。
成功发送后，所有订阅了 news 的客户端都会收到这条消息。

* `publish_async`

`publish_async` 同样用于发送广播消息，它和 `publish` 的不同之处在于前者是异步的，会立即返回，而后者则需要等到操作完成后才能返回。

```bash
$ curl -l -H "Content-type: application/json" -X POST -d '{"method":"publish_async", "appkey":"XXXXXXXXXXXXXXXXXXXXXXX", "seckey":"sec-XXXXXXXXXXXXXXXXXXXXXXXXXXXXX", "topic":"news", "msg":"good news"}' http://rest.yunba.io:8080
```

调用成功后，会收到如下格式的返回信息，详见文末的状态参数说明：
```bash
{"status":0,"messageId":512619373110763520}
```

* `publish_check`

当使用 `publish_async` 方法发送消息后，可以使用 `publish_check` 进行检查。
以上面的返回信息为例，将 Message ID “512619373110763520”作为消息的内容，填写到 `publish_check` 的“msg”处即可。如下：

```bash
$ curl -l -H "Content-type: application/json" -X POST -d '{"method":"publish_check", "appkey":"XXXXXXXXXXXXXXXXXXXXXXX", "seckey":"sec-XXXXXXXXXXXXXXXXXXXXXXXXXXXXX", "topic":"news", "msg":"512619373110763520"}' http://rest.yunba.io:8080
```

运行后，会收到如下格式的返回信息，详见文末的状态参数说明。
```bash
{"status":0,"results":"done"}
```

####2.2 JSON 格式说明


下面介绍请求的 JSON 格式。其中，“opts”为可选项，如果在“opts”中没有出现“apn_json”项时，就不会发送 APN。

```json
{
	"method":<method>, 
	"appkey":<app-key>, 
	"seckey":<secret-key>, 
	"topic":<topic>, 
	"msg":<message>,

	"opts":	{
				"time_to_live":<number>,
				"platform":<number>,
				"time_delay":<number>,
				"location":<string>,
				"qos":<number>,
				"apn_json":	{
								"aps":	{
											"alert":<string>,
											"badge":<number>,
											"sound":<string>,
											"priority":<number>,
											"expiration":<number>,
											"content-available":<number>
								}
				}
	}
}
```


**参数说明：**
* time_to_live：用来设置离线消息保留多久。单位为秒(例如，“3600”代表1小时)，默认值为 5 天，最大不超过 15 天。
* platform：该参数暂未实现。
* time_delay：该参数暂未实现。
* location：该参数暂未实现。
* qos：用来设置服务质量等级。有三种取值：“0”表示最多送达一次；“1”表示最少送达一次；“2”表示保证送达且仅送达一次。详见 [官方文档](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718099)。
* apn_json：APN 部分，请参考苹果官方说明——[Apple Push Notification Service](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW12 "A")。



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
