# 运行 Yunba PHP Demo

本文介绍如何运行 Yunba PHP SDK 中的 examples 示例程序。<br><br>
**提示：**此 SDK 只能在 cli 模式下以阻塞方式运行，如果只用于发布消息,推荐使用更简单的 [RESTful API](http://yunba.io/docs2/restful_Quick_Start/)。
<br>
本文涉及的运行环境如下：

* Windows 8.1
* WampServer 1.6.1.33
* YunBa PHP SDK

## 准备工作

###1. 下载云巴 PHP SDK
这里使用的是 Yunba PHP SDK，可打开 [其 GitHub 页](https://github.com/yunba/yunba-php-sdk)，下载 Zip 文件。<br>


###2. 注册云巴开发者账号
打开 [云巴官方网站](http://yunba.io "云巴官方网站")，点击右上角的“注册”按钮创建账号。  


## 详细步骤
本文以 [Yunba PHP SDK](https://github.com/yunba/yunba-csharp-sdk) 的 "subscribe.php" 和 "publish.php" 为例，演示 PHP SDK 的使用。
###1. 在云巴 Portal 上创建新应用
请参考 [运行 Yunba Android Demo](https://github.com/yunba/docs/blob/master/quickstart/demo/Demo_Android.md) 一文中的该步骤的做法，获得一个 AppKey。

###2. 配置工程文件
在 "WampServer" 解压后的 "...\wamp\bin\php" 路径下搜索 "php.init" 文件并打开该文件，确保 ```extension=php_openssl.dll``` 、```extension=php_curl.dll``` 和 ```extension_dir = ".../ext/"``` 语句**取消注释**（删去代码语句前的"；"）。如下图：
<br><br>
![php_extention_php_openssl.png](https://raw.githubusercontent.com/yunba/docs/master/image/for_quickstart/php_extention_php_openssl.png)
<br><br>
![php_extention_php_curl.png](https://raw.githubusercontent.com/yunba/docs/master/image/for_quickstart/php_extention_php_curl.png)
<br><br>
![php_extention_dir.png](https://raw.githubusercontent.com/yunba/docs/master/image/for_quickstart/php_extention_dir.png)
<br><br>
在工程文件**包含**"yunba.php" 文件：
解压下载的 Zip 文件，在 "...\yunba-php-sdk-master\examples" 路径下的 "subscribe.php" 和 "publish.php" 文件的第三行添加 "yunba.php" 文件所在路径。如下图：
<br><br>
![php_include_yunba.php.png](https://raw.githubusercontent.com/yunba/docs/master/image/for_quickstart/php_include_yunba.php.png)



###2. 运行 examples
**订阅**<br><br>
打开 "subscribe.php",必须修改 "appkey"，替换为自己的 AppKey。可定义 "topic"、"qos" 等参数，参数含义可参考 [yunba-knowledgeBase](https://github.com/yunba/kb) 和 [官网](http://yunba.io/developers/) 。代码示例如下：<br>
```
//构造实例
$yunba = new Yunba(array(
	"appkey" => "Your AppKey", //替换为自己的 AppKey
	"sessionFilePath" => "session1.dat"
));
```
```
//订阅topic1
$yunba->subscribe(array(
	"topic" => "news",
	"qos" =>2
), function ($success) {
	echo "[Yunba]subscribe topic1\n";
}, function ($data) {
	echo "[YunBa]received topic1 " . var_export($data, true) . "\n";
});
```
找到 "php.exe" 文件所在的路径（从 "WampServer" 下搜索），把这个路径添加到**环境变量**中，运行 "subscribe.php" 文件进行该话题的**订阅**，如下图：
<br><br>
![php_subscribe.png](https://raw.githubusercontent.com/yunba/docs/master/image/for_quickstart/php_subscribe_masked.png)
<br><br>
订阅成功如下图：
<br><br>
![php_subscribe_success.png](https://raw.githubusercontent.com/yunba/docs/master/image/for_quickstart/php_subscribe_success.png)
<br><br>
**发布消息**
<br><br>
打开 "publish.php" 文件，替换 "appkey"，可定义 "topic"、"news"、"qos"、"msg" 参数。<br>
重启 CMD，运行 "publish.php" 进行该话题下的**消息发布**，如下图：
<br><br>
![php_publish.php.png](https://raw.githubusercontent.com/yunba/docs/master/image/for_quickstart/php_publish_masked.png)
<br><br>
消息成功发布如下图：
<br><br>
![php_publish_success.png](https://raw.githubusercontent.com/yunba/docs/master/image/for_quickstart/php_publish_success.png)
<br>

同一个 AppKey 下并且订阅了该 Topic 的 Android 端将收到消息，如下图：
<br><br>
![php_phone_receiveMSG.jpg](https://raw.githubusercontent.com/yunba/docs/master/image/for_quickstart/php_phone_receiveMSG.jpg)
<br><br><br>
详细的程序逻辑，请参考项目源程序。
<br>
完整的 API 文档可以参考 [yunba-php-sdk](https://github.com/yunba/yunba-php-sdk) 。




