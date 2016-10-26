# 如何用云巴实现弹幕功能

在云巴官网的[《视频直播弹幕互动》][1]案例中，介绍了为什么云巴非常适合应用于视频直播的弹幕、点赞等场景。

本文主要介绍如何用云巴来实现弹幕功能。


## 第一步 注册账号

打开 [云巴官方网站][8]，注册并登录。

## 第二步 创建新应用

登录后，点击界面右上角 “我的应用 --> 创建新应用”，填写您的视频直播应用的名称、应用包名等内容。详见 [Portal 的说明文档][4]。

创建后，您会在应用详情页面看到该应用的 [AppKey][3]。

## 第三步 下载 SDK

在云巴官网的 [开发者页面][2] 或云巴的 [Github][7] 上找到所需平台的 SDK，并下载。

每种 SDK 的文件包内都包含一个 Demo，用来演示主要 API 的用法和功能。
建议您从运行 Demo 开始熟悉云巴的 SDK，详见各个 SDK 的 Demo 运行的文档。

## 第四步 集成 SDK

将云巴 SDK 集成到您的应用中（此时会用到第二步中的 [AppKey][3]）。

我们以 [云巴视频直播案例][5] 所使用的 [JavaScript SDK][6] 为例，介绍一下集成 SDK 的步骤：

### 1. 引入 SDK

Yunba JavaScript SDK 依赖于 Socket.IO，所以要先引入 Socket.IO。 
请参考（SDK 中的）Demo 分别引入 Socket.IO 和 yunba-js-sdk。

### 2. 创建 Yunba 实例

用 “第二步” 中获取到的 [AppKey][3] 创建 Yunba 实例。

```javascript
window.yunba = new Yunba({
  server: 'sock.yunba.io',
  port: 3000,
  appkey: APPKEY // 这里是您在 “第二步” 中获取到的 AppKey。
});
```

### 3. 初始化、连接消息服务器、订阅 “弹幕” 频道

假设弹幕的频道名称为 `TOPIC_BULLET`：

```javascript

yunba.init(function(success) {
  if (success) {
    var cid = Math.random().toString().substr(2);

    // 连接云巴服务器
    yunba.connect_by_customid(cid,
      function(success, msg, sessionid) {
        if (success) {
          console.log('sessionid：' + sessionid);

          // 设置收到信息回调函数
          yunba.set_message_cb(yunba_msg_cb);
      
            // 订阅弹幕 TOPIC
            yunba.subscribe({
                'topic': TOPIC_BULLET
              },
              function(success, msg) {
                if (success) {
                  console.log('subscribed');
                } else {
                  console.log(msg);
                }
            });
        } else {
          console.log(msg);
        }
      });
  } else {
    console.log('yunba init failed');
  }
});
```

### 4. 发布 “弹幕”

用下面的几行代码就可以发布弹幕：

```javascript

var bullet = {
  "mode": mode,
  "text": text,
  "color": color,
  "dur": dur
};
  
yunba.publish({
    topic: TOPIC_BULLET,
    msg: JSON.stringify(bullet)
  },
  function(success, msg) {
    if (!success) {
      console.log(msg);
    }
  }
);

```

更详细的代码，请参考案例的 [Github][5] 页。

[1]: http://yunba.io/usercases/livevideo/
[2]: http://yunba.io/developers/
[3]: product_kb_app_key.md
[4]: product_kb_portal.md
[5]: https://github.com/yunbademo/yunba-live-video
[6]: https://github.com/yunba/yunba-javascript-sdk
[7]: https://github.com/yunba
[8]: http://yunba.io

