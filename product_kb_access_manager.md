# 权限管理

**注：本文中所提及的 RESTFUL API 所用的地址，如无特别提及，均为`https://abj-yam-1.yunba.io`，所用 http request method 均为 `POST`**

目前，市面上的大多数应用都或多或少地涉及到对用户账号的权限控制，应用开发者们希望能够用一种简易而安全的方式管理用户，控制他们对不同内容的访问权和发布权。

针对这一需求，我们推出了权限管理。

**应用开发者需要建立自己的权限管理服务器**，一面向云巴请求权限，一面将不同的权限授予其不同的用户。开启权限管理后，用户的所有 Pub/Sub 等操作都会需要经过云巴权限管理模块的核查，认证成功后才会执行。


权限管理的特点：
- 云巴的权限管理采用白名单模式。**用户在开启权限管理功能后，用户客户端的所有的订阅（读）、发布（写）操作都默认被禁止，只有被用户的服务器授权才能进行操作。**
- 读写权限被禁止后，用户进行发布相关操作时，会超时。而进行订阅相关的操作时，服务器会返回 QoS 为 0 的 ACK，但订阅操作本身也是失败的。
- **调用下述[yam_set](#打开关闭权限管理)开、关权限管理后，需要等待 10 分钟才能在服务器端生效。**
- 关闭权限管理开关后，之前申请的权限会保存在云巴服务器，在重新打开后可以继续使用。

我们提供了如下三个层级的权限管理，通过 RESTful 请求的方式进行使用。其中，Topic 与 Alias 属于同一个层级，但为了方便表述，下文会对二者分别进行介绍。

- App 层级
- Topic 与 Alias 层级
- Token 层级

所控制的权限包括：
- (必选)读权限 "r"，即 `subscribe`
- (必选)写权限 "w"，包括 `publish`、`publish_to_alias`、`publish2`、`publish2_to_alias`、`set_alias`
- (可选)读权限 "p"，即、`subscribe_presence`

注意：`unsubscribe`和`unsubscribe_presence`不受权限管理的影响。

![productpng_access_manager_process.png](https://raw.githubusercontent.com/yunba/docs/master/image/productpng_access_manager_process.png)

图示备注：
- Customer's Server：应用开发者的权限管理服务器
- Customer's Client：应用开发者的终端用户的客户端
- Yunba's Access Manager：云巴的权限管理模块
- Yunba's Server：云巴实时系统的服务器

如上图所示。开启权限管理后，大致的通信流程如下（其中，用户的服务器和客户端之间的通信逻辑由用户自己决定）：

1. 用户在客户端注册并登录账号；
2. 开发者的权限管理服务器根据自身账号管理的逻辑，决定分配怎样的权限给该用户，并通过向云巴权限管理模块请求 App/Topic/Token 权限来获得相应层级的权限；
3. 云巴权限管理模块处理请求并返回结果（如果请求的是 Token 权限，会将 Token 返回给开发者的服务器）；
4. 开发者的服务器将收到的 Token 发给客户端（如果申请的是 App 或 Topic 层级的权限，则不存在这一步骤）；
5. 此时，用户的客户端可以进行发布/订阅操作了（如果涉及 Token，需要带上 Token）；
6. 云巴服务器收到用户的发布/订阅消息后，会请求权限管理模块进行审核；
7. 权限管理模块将审核结果返回给消息服务器；
8. 消息服务器根据结果来决定是否用户的发布/订阅请求是否可以执行：是，则返回成功，并执行用户请求的发布/订阅操作；否，则返回错误。

下面分别介绍不同层级的权限管理。

## 打开／关闭权限管理

### 打开权限管理

- 所需字段：`appkey`、`seckey`、`method`、`trigger`
- method：yam\_set
- 字段含义和返回值说明参见文末

下面的例子为，发起以下请求以为 `appkey` 为 `593e027013c4f1713e47694d` 的应用打开权限管理：

`POST https://abj-yam-1.yunba.io`
```
{
"method":"yam_set",
"appkey":"593e027013c4f1713e47694d",
"trigger":1,
"seckey":"sec-key"
}
```

请求成功会返回 0，错误返回值参见文末。

```json
{
    "status":0
}
```

### 关闭权限管理

- 所需字段：`appkey`、`seckey`、`method`、`trigger`
- method：yam\_set
- 字段含义和返回值说明参见文末

下面的例子为，发起以下请求以为 `appkey` 为 `593e027013c4f1713e47694d` 的应用关闭权限管理：

`POST https://abj-yam-1.yunba.io`
```
{
"method":"yam_set",
"appkey":"593e027013c4f1713e47694d",
"trigger":0,
"seckey":"sec-key"
}
```

请求成功会返回 0，错误返回值参见文末。

```json
{
    "status":0
}
```

## App 层级

### 申请权限

App 层级的权限控制的是整个 App 内所有 Topic 的订阅和发布权限。

- 所需字段：`appkey`、`seckey`、`method`、`r`、`w`、`ttl`
- method：yam_grant
- 字段含义和返回值说明参见文末

例如，下面的请求，会将 AppKey 为 `567a4a754407a3cd028aaf6b` 的 App 的所有 Topic 都设置为可读可写，持续 1000 秒。相当于暂时废除了权限管理。

```json
{
    "appkey": "567a4a754407a3cd028aaf6b",
    "seckey": "sec-mj64xlu0ob1Xs1wWuZzmGZOYZqrpFmFxp5jHULr13eUZCVpS",
    "method":"yam_grant",
    "r":1,
    "w":1,
    "ttl":1000
}
```

请求成功会返回 0，错误返回值参见文末。

```json
{
    "status":0
}
```

### 查看权限

- 所需字段：`appkey`、`seckey`、`method`
- method: yam_audit
- 字段含义和返回值说明参见文末

通过`yam_audit`方法，可以查看某个 App 当前的读写权限情况。

下面是一个例子。

```json
{
    "appkey": "567a4a754407a3cd028aaf6b",
    "seckey": "sec-mj64xlu0ob1Xs1wWuZzmGZOYZqrpFmFxp5jHULr13eUZCVpS",
    "method":"yam_audit"
}
```

请求成功会返回 0，错误返回值参见文末。

```json
{
	"status": 0,
	"r" :1,
	"w" :1,
	"p": 0

}
```


## Topic 层级

### 申请权限

Topic 层级比 App 层级更细，可以申请对 App 内的某一个或多个 Topic 进行读写权限的限制。申请权限时，需要带 `appkey`、`seckey`和`topic`参数。

- 所需字段：`appkey`、`seckey`、`method`、`topic`、`r`、`w`、`p`(可选)、`ttl`
- method：yam_grant
- 字段含义和返回值说明参见文末

例如，下面的请求会将 Appkey 为 `567a4a754407a3cd028aaf6b` 的 App 下的 `news` 频道设置为所有人可读。但并不是所有人可写。

```json
{
    "appkey": "567a4a754407a3cd028aaf6b",
    "seckey": "sec-mj64xlu0ob1Xs1wWuZzmGZOYZqrpFmFxp5jHULr13eUZCVpS",
    "method":"yam_grant",
    "topic":"news",
    "r":1,
    "w":0,
    "p":1,
    "ttl":0
}
```
请求成功会返回 0，错误返回值参见文末。

```json
{
    "status":0
}
```

### 查看权限

- 所需字段：`appkey`、`seckey`、`method`、`topic`
- method: yam_audit
- 字段含义和返回值说明参见文末

通过`yam_audit`方法，也可以查看某个 Topic 当前的读写权限情况。

下面是一个例子。

```json
{
    "appkey": "567a4a754407a3cd028aaf6b",
    "seckey": "sec-mj64xlu0ob1Xs1wWuZzmGZOYZqrpFmFxp5jHULr13eUZCVpS",
    "method": "yam_audit",
    "topic":  "news"
}
```

请求成功会返回 0，错误返回值参见文末。

```json
{
	"status": 0,
	"r" :1,
	"w" :0,
    "p" :1
}
```

## Alias 层级

### 申请权限

Alias 层级的权限控制可以管理某个别名的读写权限，即是否允许设置为某别名、是否允许对某别名发消息。

**注意：`alias`和`topic`是同一个层级的概念，两者不可以同时出现。**

- 所需字段：`appkey`、`seckey`、`method`、`alias`、`r`、`w`、`ttl`
- method：yam_grant
- 字段含义和返回值说明参见文末

例如，下面的请求，会将`jack`这个`alias`设置为可读不可写。任何人都可以将自己的别名设置为`jack`；但不允许任何人给`jack`发消息。

```json
{
    "appkey": "567a4a754407a3cd028aaf6b",
    "seckey": "sec-mj64xlu0ob1Xs1wWuZzmGZOYZqrpFmFxp5jHULr13eUZCVpS",
    "method":"yam_grant",
    "alias":"jack",
    "r":1,
    "w":0,
    "ttl":0
}
```
请求成功会返回 0，错误返回值参见文末。

```json
{
    "status":0
}
```
### 查看权限

- 所需字段：`appkey`、`seckey`、`method`、`alias`
- method: yam_audit
- 字段含义和返回值说明参见文末

通过这个`yam_audit`方法，可以获取某个 `alias` 的权限情况。

例如：

```json
{
    "appkey": "567a4a754407a3cd028aaf6b",
    "seckey": "sec-mj64xlu0ob1Xs1wWuZzmGZOYZqrpFmFxp5jHULr13eUZCVpS",
    "method":"yam_audit",
    "alias":"jack"
}
```

返回内容：

```json
{
    "status": 0,
    "r" :1,
    "w" :0
}
```

**注意：各个层级的权限控制是“或”的关系，使用 yam_audit 只会显示当前层级的读写权限情况，只有综合了 App、Topic、Alias 等各个层级的权限设置情况才能确定某个 Topic 或 Alias 实际的读写权限。**

## Token 层级


相较前面几种，Token 层级的权限控制更加灵活。
Token 用来控制指定 Topic 或 Alias 的读写权限。

### 申请 Token

用户的服务器向云巴申请 Token 时，需要指定 `appkey`、`seckey`、`topic`或`alias`（二选一），**并带上 value 为空的 `token` 字段**。

- 所需字段：`appkey`、`seckey`、`method`、`topic`或`alias`（二选一）、`token`、`r`、`w`、`ttl`
- method：yam_grant
- 字段含义和返回值说明参见文末

**例一**

下面是为某个 Topic 申请 Token 例子。

```json
{
    "appkey": "567a4a754407a3cd028aaf6b",
    "seckey": "sec-mj64xlu0ob1Xs1wWuZzmGZOYZqrpFmFxp5jHULr13eUZCVpS",
    "method":"yam_grant",
    "topic":"news",
    "token":"",
    "r":1,
    "w":0,
    "ttl":0
}
```

上述请求会将返回一个 Token，该 Token 可以在 AppKey 为 `567a4a754407a3cd028aaf6b` 的 App 下对名为 news 的 Topic 进行读操作。成功时返回如下：

```json
{
	"status":0,
	"token":"342251e5c3f547f24c91e9a97356ae1f"
}
```

**例二**
下面是为某个 Alias 申请 Token 例子。

```json
{
    "appkey": "567a4a754407a3cd028aaf6b",
    "seckey": "sec-mj64xlu0ob1Xs1wWuZzmGZOYZqrpFmFxp5jHULr13eUZCVpS",
    "method":"yam_grant",
    "alias":"jack",
    "token":"",
    "r":1,
    "w":1,
    "ttl":0
}
```

上述请求会将返回一个 Token，该 Token 可以在 AppKey 为 `567a4a754407a3cd028aaf6b` 的 App 下对名为 jack 的 Alias 进行读写操作。即，带有该 Token，就可以将别名设置为 jack，或向别名 jack 发布消息。成功时返回如下：

```json
{
    "status":0,
    "token":"89ce08e0158d729f570a972bb22c5439"
}
```

### 使用 Token

申请成功后，在进行相关 Topic 或 Alias 的`subscribe`、`publish`以及`set_alias`操作时，必须使用带有`token`的`topic`或`alias`，否则，操作会被禁止。

格式如下：

- `,yam` + `<your-token>` + `_` + `<your-topic>`

- `,yam` + `<your-token>` + `_` + `,yta/` + `<your-alias>`


例如，名为 news 的`topic`，携带`token`为`683dc38987d3acc5a5684979f42943be`进行读写，则：

- 原先的 Topic：`news`
- 携带 Token 后的 Topic：`,yam683dc38987d3acc5a5684979f42943be_news`

再如，名为 jack 的`alias`，携带`token`为`89ce08e0158d729f570a972bb22c5439`进行读写，则：

- 原先的 Alias：`jack`
- 携带 Token 后的 Alias：`,yam89ce08e0158d729f570a972bb22c5439_,yta/jack`

>2017.2.6 后续会更新各个版本的 SDK，提供带有 Token 的 Pub/Sub 接口。

**注意：各个层级的权限控制是“或”的关系，如果之前设置过 Topic、Alias 或 App 级别的权限控制，则无需再携带 Token，使用原有的 Topic 即可。**

### 为 Token 新增权限

- 所需字段：`appkey`、`seckey`、`method`、`topic`或`alias`（二选一）、`token`、`r`、`w`、`ttl`
- method: yam_grant
- 字段含义和返回值说明参见文末

已申请到的 Token 还可以附加其他 Topic 的读写权限。

例如，下面的请求，会为`342251e5c3f547f24c91e9a97356ae1f`这个 Token 新增 `another_topic` 这个 Topic 的读权限。将`topic`字段替换为`alias`，就可以为 Token 新增某个 Alias 的读写权限。不再赘述。


```json
{
    "appkey": "567a4a754407a3cd028aaf6b",
    "seckey": "sec-mj64xlu0ob1Xs1wWuZzmGZOYZqrpFmFxp5jHULr13eUZCVpS",
    "method":"yam_grant",
    "topic":"another_topic",
    "token":"342251e5c3f547f24c91e9a97356ae1f",
    "r":1,
    "w":0,
    "ttl":0
}
```

请求成功会返回 0，错误返回值参见文末。

```json
{
    "status":0
}
```



### 查看权限

- 所需字段：`appkey`、`seckey`、`method`、`topic`或`alias`（二选一）、`token`
- method: yam_audit
- 字段含义和返回值说明参见文末

通过这个`yam_audit`方法，可以获取某个 Token 对某个 Topic 或 Alias 所具有的读写权限情况。

参见下方的示例。同理，将`topic`字段替换为`alias`，就可以获取某个 Token 对某个 Alias 所具有的读写权限情况。不再赘述。


```json
{
    "appkey": "567a4a754407a3cd028aaf6b",
    "seckey": "sec-mj64xlu0ob1Xs1wWuZzmGZOYZqrpFmFxp5jHULr13eUZCVpS",
    "method":"yam_audit",
    "topic":"news",
    "token":"342251e5c3f547f24c91e9a97356ae1f"
}
```

返回内容：

```json
{
	"status": 0,
	"r" :1,
	"w" :0,
	"p" :0
}
```

---

- **字段含义**

字段名 | 字段含义
------- | -------
appkey | 应用的 AppKey，可以在 Portal 中查看。
seckey | 应用的 Secret Key，可以在 Portal 中查看。
method | 请求的方法
r | 读权限，即 `subscribe`。取值 1 表示允许，0 表示禁止
w | 写权限，包括 `publish`、`publish_to_alias`、`publish2`、`publish2_to_alias`、`setAlias`。取值 1 表示允许，0 表示禁止
p | 读 Presence 的权限，即 `subscribe_presence`。取值 1 表示允许，0 表示禁止
ttl | 即 time to live，权限的有效期限，单位为秒。0 表示永久有效。**注意：权限的有效期始终按照最后一次请求时的 ttl 来计算计算。**

- **返回值说明**

返回值 | 含义|解释
------- | ------- | -------
0| success|成功
1| invalidate parameter|参数错误
2| internal error | 内部错误
3| not permitted| 未开启权限管理，或正在开启权限管理，或请求超时
