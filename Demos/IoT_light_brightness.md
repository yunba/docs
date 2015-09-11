###**灯泡遥控器**
---


这是一个物联网的 Demo。
灯泡的亮度由灭到亮共有10个等级。用户通过移动设备上相应的 App，可以远程查看灯泡的状态并实时地做出调节。

下面是将灯泡调为中等亮度（Level 5）的发布/订阅代码示例。



- **Android**
``` 
YunBaManager.publish(getApplicationContext(),"light_brightness","level_5",callback);
``` 
``` 
YunBaManager.subscribe(getApplicationContext(),"light_brightness",callback);
``` 

- **iOS**
``` 
[YunBaService publish:light_brightness data:@"level_5"];
``` 
```
[YunBaService subscribe:light_brightness];
```

- **C**
```
char buf[100] = "level_5";
int data_len = strlen(buf);
rc = MQTTClient_publish(hClient, "light_brightness", data_len, buf);
``` 
``` 
rc = MQTTClient_subscribe(hClient, "light_brightness");
```

- **Javascript**
``` 
yunba.publish({'topic': 'light_brightness', 'msg': 'level_5','messageId': 199900724, 'qos': 1}, callback);
```
```
yunba.subscribe({'topic': 'light_brightness'}, callback);
```

- **Socket.io**
```
socketIO.emit('publish', {'topic': 'light_brightness', 'msg': 'level_5', 'qos': 1})
``` 
``` 
socketIO.emit('subscribe', {'topic': 'light_brightness'})
```

更多内容，详见[云巴开发文档](http://yunba.io/docs2 "云巴开发文档")。
