[TOC]

# Yunba Javascript SDK 使用文档

通过利用Yunba Javascript SDK 提供的接口API，你可以很方便的在你的智能手机、平板电脑、网站等终端应用上使用Yunba的各种消息服务。 

# 获取 SDK

你可以通过以下几种方式获得最新 Yunba Javascript SDK.

* [下载最新SDK](http://yunba.io)
* 通过下面的命令git clone SDK repos:

	
	$ git clone https://wawik@bitbucket.org/wawik/yunba_javascript_sdk.git/wik

* 你也可以通过下面的url直接从Yunba CDN引用SDK:

	http://cdn.yunba.com/yunba.min.js
	
在你的Web应用中，在'</body>'标签之前插入'<script>'标签，就可以引入Yunba Javascript SDK了。
	
	<html>
	    <body>
		<script src="cdn.yunba.com/yunba.min.js"></script>
	    </body> 
	</html>

# 新手上路

你只须在你的个人电脑上安装一个带有JavaScript Console功能的网页浏览器（例如：Google Chrome）就可以开始使用我们的SDK了。Chrome本身自带JavaScript Console,其它浏览器可能需要安装相应插件。例如FireFox需要安装FireBug。

### 第一步：引入SDK

	
    <html>
        <body>
	    <script src="http://cdn.yunba.com/yunba.min.js"></script>
        </body> 
    </html>


将以上代码复制到文本编辑器，然后保存为yunba.html。使用Chrome打开该html文件。

### 第二步：创建Yunba实例并初始化


	var yunba = new Yunba();
	yunba.init();
	


将以上复制粘帖到Chrome的console并单击回车。

![chrome console] (1.png)


### 第三步：连接消息服务器


	yunba.connect(function(success,msg){
  		if(success){
    		console.log('你已成功连接到消息服务器');
  		}else{
    		console.log(msg);
  		}
	});

将以上复制粘帖到Chrome的console并单击回车。

![chrome console] (2.png)



### 第四步：收听消息（Subscribe）

如果你想接收一个主题的消息，你得先使用subscribe()方法收听该主题。


	yunba.subscribe(
	  {topic:'my_topic'},
	  function(success){
	    if(success){
	      console.log('你已成功收听主题：my_topic')
	    }
	  },
	  function(data){
	    console.log(data);
	  }
	);

将以上复制粘帖到Chrome的console并单击回车。

![chrome console] (3.png)


### 第五步：推送消息（Publish）

你可以使用publish()方法向所有收听my_topic主题的终端发布一条‘你好！我亲爱的朋友。’消息。


	#!javascript
	yunba.publish(
	  {topic:'my_topic',msg:'你好！我亲爱的朋友。'},
	  function(success){
	    if(success){
	      console.log('消息发布成功！');
	    }
	  }
	);


将以上复制粘帖到Chrome的console并单击回车。你可以看到消息发布成功，并且自己也收到了该条消息（因为你在上一部已收听过该主题）。当然你可以继续publish更多的消息。

![chrome console] (4.png)


### 第六部：在两个浏览器窗口实例之间发送消息

将上面的例子扩展一下，我们就可以利用yunba实现在两个浏览器窗口实例之间收发消息。按照以上的步骤再打开一个浏览器窗口实例（其实就是重复用chrome打开yunba.html）。我们将之前的打开的窗口命名为client #1，后来打开的窗口命名为client #2。

以下就是client #1 与client #2之间的通信截图。

![client #1] (5.png 'client #1') ![client #1] (6.png 'client #2')


OK! 现在你应该对我们的Yunba消息服务有一个初步的了解了。


# API

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
我们的JS SDK通过web socket与消息服务器通信。创建的Yunba实例必须首先运行init()方法才能进行后面的所有操作。init()运行后，实例就与后端建立了web socket通道。
#### 基本使用：

	yunba.init(callback);

#### 参数：
* 参数名:callback
* 参数类型:function
* 可选/必须：可选
* 参数说明:
   yunba实例初始化后，不管成功或失败都会回调callback。传递给callback的参数有success、msg。success为true则表示初始化成功，否则初始化失败。初始化失败会返回错误信息msg。

### Yunba.connect()

#### 说明：
yunba实例初始化后只表明与服务器建立了socket连接，还需要通过connect()方法连接上消息服务器。连接上消息服务器后才开始收发消息。
#### 基本使用


	yunba.connect(callback)

#### 参数：
* 参数名：callback
* 参数类型：function
* 可选/必须：可选
* 参数说明：
   连接成功后会调用callback。

### Yunba.subscribe()

#### 说明：
通过subscribe()收听一个主题后，你就可以接收消息服务器向该主题推送的消息了。
#### 基本使用：


	yunba.subscribe(obj,cb1,cb2)
	
#### 参数 obj：
* 参数名：obj
* 参数类型：object
* 可选/必须：必选
* 参数说明：obj包含两个字段，obj.topic表示准备收听的主题，obj.qos表示qos级别（可选，默认为0）。
#### 参数 cb1
* 参数类型：function
* 可选/必须：可选
* 参数说明：收听成功或失败后的回调函数。传递回来的参数有success、granted，sucees为true表示收听成功，否则收听失败。如果收听成功则返回granted，granted为一个object，含有两个字段，分别为收听的主题名称（granted.topic）和该主题的qos级别(granted.qos)。
#### 参数 cb2
* 参数类型：function
* 可选/必选： 必选
* 参数说明：收听成功后，通过该回调函数监听所收听主题的推送消息。传递回来的参数为data是一个object，含有消息主题(data.topic)与消息内容(data.msg)。

### Yunba.unsubscribe()

#### 说明：
你可以通过unsubscribe()取消对一个主题的收听。
#### 基本使用：

	yunba.unsubscribe(obj,cb)

#### 参数 obj
* 参数类型：object
* 可选/必选：必选
* 参数说明：目前版本之要求obj包含一个属性字段为topic，即准备取消收听的主题。
#### 参数 cb
* 参数类型：function
* 可选/必选：可选
* 参数说明：取消收听某主题成功或失败都会回调该函数。传递过来的参数有success、msg。success为true表示取消收听成功，否则表示失败。如果失败，则返回错误信息msg。

### Yunba.publish()

#### 说明：
Yunba客户端实例可以通过publish()向某主题发布消息。
#### 基本使用

	yunba.publish(obj,cb)

#### 参数 obj
* 参数类型：object
* 可选/必选：必选
* 参数说明：obj含有两个属性字段，分别为要发送的目标主题(obj.topic:string)和消息级别(obj.qos:number)，其中obj.qos为可选，默认值为0. 
#### 参数 cb
* 参数类型：function
* 可选/必选：可选
* 参数说明：不管消息发布是否成功或失败都会回调此函数。传递回的参数有success、msg。success值为true表示消息发布成功，否则发送失败。如果发送失败，则返回错误消息msg。

### Yunba.disconnect()

#### 说明：
与connect()相对，通过disconnect()可以断开与消息服务器的连接。
#### 基本使用：

	msg.disconnect(cb)

#### 参数 cb
* 参数类型：function
* 可选/必选：可选
* 参数说明：不管断开连接失败还是成功，都会回调此函数。传递回的参数有success、msg。如果success值为true表示成功，否则表示失败。如果失败，则返回错误消息msg。







