# 运行 Yunba PHP Demo

本文介绍如何运行 Yunba PHP SDK 中的 examples 示例程序。

**提示：**此 SDK 只能在 [CLI](http://php.net/manual/zh/features.commandline.php) 模式下以阻塞方式运行，如果只用于发布消息，推荐使用更简单的 [RESTful API](restful_api_api_manual.md)。

本文涉及的运行环境如下：

Windows 平台：
* Windows 8.1
* WampServer 1.6.1.33
* YunBa PHP SDK

Mac 平台：
* OS X 10.11.2
* bash 3.2.57
* PHP 5.5.30
* libcurl
* YunBa PHP SDK

## 准备工作

###1. 注册云巴开发者账号
打开 [云巴官方网站](https://yunba.io)，点击右上角的 “注册” 按钮创建账号。  

###2. 下载云巴 PHP SDK
这里使用的是 Yunba PHP SDK，可打开 [其 GitHub 页](https://github.com/yunba/yunba-php-sdk)，下载 Zip 文件。

###3. 配置环境

**Windows 平台**

在 WampServer 解压后的 “...\wamp\bin\php” 路径下搜索 php.init 文件并打开该文件，确保 ```extension=php_openssl.dll``` 、```extension=php_curl.dll``` 和 ```extension_dir = ".../ext/"``` 语句**取消注释**（删去代码语句前的"；"）。如下图：


![phppng_demo_extention_openssl.png](https://raw.githubusercontent.com/yunba/docs/master/image/phppng_demo_extention_openssl.png)


![phppng_demo_curl.png](https://raw.githubusercontent.com/yunba/docs/master/image/phppng_demo_curl.png)


![phppng_demo_extention_dir.png](https://raw.githubusercontent.com/yunba/docs/master/image/phppng_demo_extention_dir.png)

**Mac 平台**

安装 PHP 及 PHP 的 cURL 依赖。

注意，从 PHP 4.0.2 起，只需安装 libcurl，即可在 PHP 中使用 cURL 的函数。详细说明请参考：[PHP 手册](http://php.net/manual/en/curl.requirements.php)。


## 详细步骤
本文以 [Yunba PHP SDK](https://github.com/yunba/yunba-php-sdk) 的 subscribe.php 和 publish.php 为例，演示 PHP SDK 的使用。

###1. 在云巴 Portal 上创建新应用
请参考 [运行 Yunba Android Demo](android_demo_quick_start.md) 一文中的该步骤的做法，获得一个 AppKey。

###2. 引入 API 库

在工程文件中**包含** yunba.php 文件：
解压下载的 Zip 文件，在 “...\yunba-php-sdk-master\examples” 路径下的 subscribe.php 和 publish.php 文件的第三行添加 yunba.php 文件所在路径。如下图：

![phppng_demo_include_lib.png](https://raw.githubusercontent.com/yunba/docs/master/image/phppng_demo_include_lib.png)

###3. 运行 examples

**Windows 平台**

**1. 修改 AppKey**

打开 subscribe.php，必须修改 appkey，替换为自己的 AppKey。

```
//构造实例
$yunba = new Yunba(array(
	"appkey" => "Your AppKey", //替换为自己的 AppKey
	"sessionFilePath" => "session1.dat"
));
```
**2. 订阅**

可定义 [topic](product_kb_topic_and_alias.md)、[qos](product_kb_qos.md) 等参数。代码示例如下：

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

找到 php.exe 文件所在的路径（从 WampServer 下搜索），把这个路径添加到**环境变量**中，运行 subscribe.php 文件进行该话题的**订阅**，如下图：

![php_subscribe.png](https://raw.githubusercontent.com/yunba/docs/master/image/phppng_demo_subscribe_masked.png)

订阅成功如下图：

![phppng_demo_subscribe_success.png](https://raw.githubusercontent.com/yunba/docs/master/image/phppng_demo_subscribe_success.png)


**3. 发布消息**

打开 publish.php 文件，替换 appkey，可定义 topic、qos、msg 参数。

重启 CMD，运行 publish.php 进行该话题下的**消息发布**，如下图：

![php_publish.php.png](https://raw.githubusercontent.com/yunba/docs/master/image/phppng_demo_publish_masked.png)

消息成功发布如下图：

![phppng_demo_publish_success.png](https://raw.githubusercontent.com/yunba/docs/master/image/phppng_demo_publish_success.png)


同一个 AppKey 下并且订阅了该 Topic 的 Android 端将收到消息，如下图：

![phppng_demo_received_message.png](https://raw.githubusercontent.com/yunba/docs/master/image/phppng_demo_received_message.png)


**Mac 平台**

同样地，修改代码中的 AppKey 后，在 bash 中运行 subscribe.php 和 publish.php。如下所示。也可根据需要，修改代码中的 Topic 等参数。

```bash
$ php subscribe.php 
```

```bash
[YunBa]init success
[YunBa]connect success
[Yunba]subscribe topic1
[Yunba]subscribe topic2
```

```bash
$ php publish.php
```

```bash
[YunBa]init success
[YunBa]connect success
[YunBa]publish1 success
[YunBa]publish2 success
```

详细的程序逻辑，请参考项目源程序。

完整的 API 文档可以参考 [yunba-php-sdk](https://github.com/yunba/yunba-php-sdk) 。
