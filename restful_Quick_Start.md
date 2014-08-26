#Yunba restful api 快速入门
## 注册开发者账号
打开 <http://yunba.io>, 点击注册创建账号。

![create_accout.jpg](../image/register_account.png)

## 创建应用
注册账号成功跳转到我的应用界面，点击我的应用 --> 创建新应用，输入应用名称和包名

![create_application.jpg](image/create_app.png)

##方法
### publish

http GET请求格式如下:

```url
http://abj-tfs-2:8080/
publish/
app-key/
secret-key/
topic/
your_mesage
```

http POST请求JSON格式如下:

```json
{'method':"publish",'appkey':"app-key",'seckey':"secret-key,'topic':"your-topic",'msg':"your-message"}
```

##发送状态回复

*发送成功

```json
{'status':0,'msg_id':message-id}
```

*参数错误

```json
{'status':1}
```

*内部服务错误

```json
{'status':2}
```

*没有应用

```json
{'status':3}
```

*推送超时

```json
{'status':4}
```
