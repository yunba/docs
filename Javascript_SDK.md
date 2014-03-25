# Yunba Javascript SDK 使用文档

通过利用 Yunba Javascript SDK 提供的接口API，你可以很方便的在你的智能手机、平板电脑、网站等终端应用上使用 Yunba 的各种消息服务。

## 获取 SDK

你可以通过以下几种方式获得最新 Yunba Javascript SDK.

* [下载最新SDK](http://yunba.io)

## 新手上路

你只须在你的个人电脑上安装一个带有JavaScript Console功能的网页浏览器（例如：Google Chrome）就可以开始使用我们的SDK了。Chrome本身自带JavaScript Console,其它浏览器可能需要安装相应插件。例如FireFox需要安装FireBug。

### 第一步：引入SDK

	
    <html>
        <body>
	    <script src="yunba.min.js"></script>
        </body> 
    </html>

### 第二步：创建Yunba实例并初始化

    var yunba = new Yunba();
    yunba.init();

### 第三步：连接消息服务器

	yunba.connect(function(success,msg){
  		if(success){
    		console.log('你已成功连接到消息服务器');
  		}else{
    		console.log(msg);
  		}
	});

### 第四步：收听消息（Subscribe）

如果你想接收一个频道的消息，你得先使用 subscribe() 方法收听该频道。

	yunba.subscribe(
	  {topic:'my_topic'},
	  function(success){
	    if(success){
	      console.log('你已成功收听频道：my_topic')
	    }
	  },
	  function(data){
	    console.log(data);
	  }
	);

### 第五步：推送消息（Publish）

你可以使用 publish() 方法向所有收听 my_topic 频道的终端发布一条‘你好！我亲爱的朋友。’消息。

	yunba.publish(
	  {topic:'my_topic',msg:'你好！我亲爱的朋友。'},
	  function(success){
	    if(success){
	      console.log('消息发布成功！');
	    }
	  }
	);

### 第六部：在两个浏览器窗口实例之间发送消息

将上面的例子扩展一下，我们就可以利用 yunba 实现在两个浏览器窗口实例之间收发消息。按照以上的步骤再打开一个浏览器窗口实例。

OK! 现在你应该对我们的 Yunba 消息服务有一个初步的了解了。


## API

目前版本API方法有：

* init()
* connect()
* subscribe()
* unsubscribe()
* publish()
* disconnect()

### Yunba.init()

|版本:0.1
|定义在：yunba.js

#### 说明：
我们的 JavaScript SDK 通过 Socket.IO 与消息服务器通信。创建的 Yunba 实例必须首先运行 init() 方法才能进行后面的所有操作。init() 运行后，实例就与后端建立了 Socket.IO 链接。
#### 基本使用：

	yunba.init(callback);

#### 参数：
* 参数名:callback
* 参数类型:function
* 可选/必须：可选
* 参数说明:
   yunba 实例初始化后，不管成功或失败都会回调 callback。传递给 callback 的参数有 success、msg。success 为 true 则表示初始化成功，否则初始化失败。初始化失败会返回错误信息msg。

### Yunba.connect()

#### 说明：
yunba 实例初始化后只表明与服务器建立了 socket 连接，还需要通过 connect() 方法连接上消息服务器。连接上消息服务器后才开始收发消息。

#### 基本使用

	yunba.connect(callback)

#### 参数：
* 参数名：callback
* 参数类型：function
* 可选/必须：可选
* 参数说明：
   连接成功后会调用 callback。

### Yunba.subscribe()

#### 说明：
通过 subscribe() 收听一个频道后，你就可以接收消息服务器向该频道推送的消息了。
#### 基本使用：

	yunba.subscribe(obj,cb1,cb2)
	
#### 参数 obj：
* 参数名：obj
* 参数类型：object
* 可选/必须：必选
* 参数说明：obj 包含两个字段，obj.topic 表示准备收听的频道，obj.qos 表示 qos 级别（可选，默认为 0）。
#### 参数 cb1
* 参数类型：function
* 可选/必须：可选
* 参数说明：收听成功或失败后的回调函数。传递回来的参数有 success、granted，sucees 为 true 表示收听成功，否则收听失败。如果收听成功则返回 granted，granted 为一个 object，含有两个字段，分别为收听的频道名称（granted.topic）和该频道的 qos 级别(granted.qos)。
#### 参数 cb2
* 参数类型：function
* 可选/必选： 必选
* 参数说明：收听成功后，通过该回调函数监听所收听频道的推送消息。传递回来的参数为 data 是一个 object，含有消息频道(data.topic)与消息内容(data.msg)。

### Yunba.unsubscribe()

#### 说明：
你可以通过 unsubscribe() 取消对一个频道的收听。
#### 基本使用：

	yunba.unsubscribe(obj,cb)

#### 参数 obj
* 参数类型：object
* 可选/必选：必选
* 参数说明：目前版本之要求 obj 包含一个属性字段为 topic，即准备取消收听的频道。
#### 参数 cb
* 参数类型：function
* 可选/必选：可选
* 参数说明：取消收听某频道成功或失败都会回调该函数。传递过来的参数有 success、msg。success 为 true 表示取消收听成功，否则表示失败。如果失败，则返回错误信息 msg。

### Yunba.publish()

#### 说明：
Yunba 客户端实例可以通过 publish() 向某频道发布消息。

#### 基本使用

	yunba.publish(obj,cb)

#### 参数 obj
* 参数类型：object
* 可选/必选：必选
* 参数说明：obj 含有两个属性字段，分别为要发送的 目标频道(obj.topic:string) 和 消息级别(obj.qos:number)，其中 obj.qos 为可选，默认值为 0.
#### 参数 cb
* 参数类型：function
* 可选/必选：可选
* 参数说明：不管消息发布是否成功或失败都会回调此函数。传递回的参数有 success、msg。success 值为 true 表示消息发布成功，否则发送失败。如果发送失败，则返回错误消息 msg。

### Yunba.disconnect()

#### 说明：
与 connect() 相对，通过 disconnect() 可以断开与消息服务器的连接。
#### 基本使用：

	msg.disconnect(cb)

#### 参数 cb
* 参数类型：function
* 可选/必选：可选
* 参数说明：不管断开连接失败还是成功，都会回调此函数。传递回的参数有 success、msg。如果 success 值为 true 表示成功，否则表示失败。如果失败，则返回错误消息msg。







