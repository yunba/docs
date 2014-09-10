# 云巴对MQTT的协议调整

##　1) 消息ID的调整
mqtt 目前定义的 message id 为 16bit，对于一个设计容量及大的系统，很快会面临 mid 重复的问题，对系统设计、数据统计带来很多麻烦。 所以把 mid 扩展为 64bit。

## 2) 0x13 协议定义

* 协议版本为 0x13 时，message id 为 8 bytes

* 协议版本为 0x13 时，CONNECT/CONNACK/DISCONNECT 包格式不变，PUBLISH/PUBACK/PUBREL/PUBCOMP/SUBSCRIBE/SUBACK/UNSUBSCRIBE/UNSUBACK 包格式需相应变化

## 3) 对MQTT命令的扩展

当前有很多业务需要进行查询内容，返回结果的流程，MQTT当前协议的puback并没有payload。通过扩展MQTT协议，可以解决这类问题。MQTT协议的Message Type有两个保留值0和15，扩展15来处理上面的问题。

### 包结构

```
fix header
Byte 1: 
    Message Type: 15
    Dup: x
    Qos: x
    Retain: x
Byte 2:
    remaining length
Variable header
8字节：MessageId
```

### Payload
1字节： 1~254表示命令字  (0，255保留)
2字节： 表示后面的长度
最后部分： 参数 或者 返回结果

### Payload ACK

1字节： 1~254表示命令字  (0，255保留)
1字节： 表示状态
2字节： 表示后面的长度
最后部分 (string)： 参数 或者 返回结果

### cmd 列表

1: get_alias
2: get_alias_ack
返回值

```json
    {command:2, status:0, data:"aliasname"}
    {command:2, status:1, data:"server internal error"}
    {command:2, status:2, data:"alias not find"}
```

3: get_topic
4: get_topic_ack
返回值

```json
    {command:4, status:0, data: "{'topic':[topic1,topic2,...]}"}
    {command:4, status:1, data: "server internal error"}
    {command:4, status:2, data: "no operation permission"}
    {command:4, status:3, data: "alias not find"}
```

5: get_aliaslist
6: get_aliaslist_ack
返回值

```json
    {command:6, status:0, data: "{'alias':[alias1,alias2,alias3], 'occupancy': alias_length}"}
    {command:6, status:1, data: "server internal error"}
    {command:6, status:2, data: "no operation permission"}
```

7: new_publish 至少需要两个参数叠加:topic/payload (platform/apn_json/...)
8: new_puback
返回值

```json
    {command:8, status:0, data: NULL}
```

9: get_status
参数： alias
10: get_status_ack
返回值

```json
    {command:10, status:0, data: "online/offline"}
    {command:10, status:1, data: "server internal error"}
    {command:10, status:2, data: "no operation permission"}
    {command:10, status:3, data: "alias not find"}
    {command:10, status:4, data: "no status"}
    {command:10, status:5, data: "alias is empty"}
```

11: puback. 通知推送者谁是第一个收到该消息的。

```json
{"sub_uid": <uid>, 'mid':<message_Id>, "timestamp": <timestamp>, "alias":<aliasName>}
```

uid: 第一个收到该消息的uid.
message_Id: 该消息的ID.
timestamp:第一个收到该休息client的timestamp.
alias: 第一个收到该消息client的别名.

对new publish tlv的说明。

new_publish tlv 参数
1字节： 1~254表示 type 类型  (0，255保留)
2字节： 参数value的长度
后面的字节： 参数的值

type 类型列表

* 0: topics

* 1: payload

* 2: platform 参数为int 目标用户终端手机的平台类型，如： android, ios, winphone 多个请使用逗号分隔（默认全部推送）

* 3: time_to_live 参数为int 从消息推送时起，保存离线的时长。秒为单位。最多支持15天 (默认永久保留)

* 4: time_delay 定时发送

* 5: location 位置

* 6: qos

* 7: apn_json，包含以下键:

```
      expiration: number (maybe NULL)
      priority: number (maybe NULL)
      alert: string or dictionary (maybe NULL) 
            dictionary: (暂时不做检查)  
      badge: number (maybe NULL)
      sound: string (maybe NULL)
      content-available: number (maybe NULL)
      extra:  dictionary （自定义参数， maybe NULL)
```

## 4)　特殊topic的定义

* topic为”,yta/alias“，表示发送该消息到一个别名为alias的用户。

* topic为”,yali“，表示设置别名。消息内容为别名。

* topic为”,yaliget“，表示获取别名。

