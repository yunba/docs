# YAM (Yunba Access Manager) API

  默认情况下 YAM 是关闭的，用户有publish或者subscribe的权限，当打开YAM,默认是没有任何权限的，必须要设置 YAM 相关权限
## YAM级别的说明

YAM 分为三个级别：

* app level 控制所有用户对此应用的所有topic有publish或者subscribe的权限
* channel level 控制所有用户对此应用的某一topic 有publish或者subscribe的权限
* auth_key level/user level 控制某一或者某一组用户对此应用的某一channel有publish或者subscribe的权限

上面三个level优先级从高到低，优先获取优先级高的权限控制

## API使用说明

外部访问通过HTTP POST即可。

URL为：

```url
http://abj-tfs-2:8181
```

### YAM授权

* app level

```json
{"cmd":"grant","level":"subkey","app_key":"<your-app-key>","secret_key":"<your-sec-key>","r":<access>,"w":<access>,"ttl":"<time-to-live>"}
```

* channel level

```json
{"cmd":"grant","level":"subkey","app_key":"<your-app-key>","secret_key":"<your-sec-key>","r":<access>,"w":<access>,"ttl":"<time-to-live>"}
```

* user level

```json
{"cmd":"grant","level":"user","app_key":"<your-app-key>","secret_key":"<your-sec-key>","r":<access>,"w":<access>,"ttl":"<time-to-live>","channels":"<your-topic>","auth_key":"<your-auth-key>"}
```

其中<access>为 1 或 0．<time-to-live>单位为分钟，为 0 表示永不失效。

### YAM撤销授权

* app level

```json
{"cmd":"revoke","level":"subkey","app_key":"<your-app-key>","secret_key":"<your-sec-key>"}
```

* channel level

```json
{"cmd":"revoke","level":"channel","app_key":"<your-app-key>","secret_key":"<your-sec-key>","channels":"<your-topic>"}
```

* user level

```json
{"cmd":"revoke","level":"user","app_key":"<your-app-key>","secret_key":"<your-sec-key>","channels":"<your-topic>","auth_key":"<your-auth-key>"}
```

### YAM查看授权

* app level

request:

```json
{"cmd":"audit","level":"subkey","app_key":"<your-app-key>","secret_key":"your-sec-key"}
```

response:

```json
{"status":0,"data":{"app_key":"your-app-key","r":<access>,"w":<access>,"ttl":<ttl>}}
```

* channel level

request:

```json
{"cmd":"audit","level":"channel","app_key":"<your-app-key>","secret_key":"your-sec-key", "channels":"<topic>"}
```

response:

```json
{"status":0,"data":{"app_key":"<your-app-key>","r":<access>,"w":<access>,"channels":"<topic>","ttl":<ttl>}}
```

* use level

request:

```json
{"cmd":"audit","level":"user","app_key":"<your-app-key>","secret_key":"<your-sec-key>", "channels":"<topic>","auth_key":"<your-auth-key>"}
```

response:

```json
{"status":0,"data":{"app_key":"<your-app-key>","r":<access>,"w":<access>,"channels":"<topic>","ttl":"<ttl>","auth_key":"<your-auth-key>"}}
```


其中<access>为 1 或 0

### auth key的设置/取消/查看

* 设置

request:

```json
{"cmd":"auth_key_set","client_id":"<your-uid>","auth_key":"<your-auth-key>","app_key":"<your-app-key>"}
```

response:

```json
{"status":0}
```

* 查看

request:

```json
{"cmd":"auth_key_get","client_id":"<your-client-id>", "app_key":"<your-app-key>"}
```

response:

```json
{"status":0,"auth_key":"<your-auth-key>"}
```

* 取消

request:

```json
{"cmd":"auth_key_unset","client_id":"<your-client-id>","app_key":"<your-app-key>"}
```

response:

```json
{"status":0}
```

## response错误说明

* 错误的参数

```json
{"status":1}
```

* 服务内部错误

```json
{"status":2}
```

* 没有应用

```json
{"status":3}
```

* 错误的auth key

```json
{"status":4}
```

* YAM没有使能

```json
{"status":5}
```

* 不存在

```json
{"status":6}
```
