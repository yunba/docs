## **运行 Yunba JavaScript Demo**
---

本文介绍如何运行 Yunba JavaScript SDK 中的 Demo 示例程序。
<br>
本文涉及的运行环境如下：

* Mac OS X 10.11.1
* nginx 1.8.0
* YunBa JavaScript SDK 2.0.3

###**准备工作**

####1. 安装 nginx
参考 [如何安装 nginx 一文](https://github.com/yunba/docs/blob/master/support/knowledge_base/Install_nginx.md)，
安装 nginx
<br>
####2. 下载云巴 JavaScript SDK
打开 [云巴开发者页面](http://yunba.io/developers "云巴开发者页面")，下载最新版本的 JavaScript SDK。
<br>
####3. 注册云巴开发者账号
打开 [云巴官方网站](http://yunba.io "云巴官方网站")，点击右上角的“注册”按钮创建账号。  

###**详细步骤**

####1. 在云巴 Portal 上创建新应用
请参考 [Yunba Android Demo](https://github.com/yunba/docs/edit/master/quickstart/demo/Demo_Android.md) 
一文中的该步骤的做法，获得一个 AppKey。


####2. 配置 nginx
新建一个文件夹，或选择一个现有的文件夹，将 yunba-javascript-sdk 里的 examples 目录下的所有文件 copy 到这个选定的文件夹下。
例如，放在 /usr/local/www 下。修改 /usr/local/etc/nginx/nginx.conf 文件：
<br>
请将
```
  location / {
      root   html;
      index  index.html index.htm;
  }
```
改为：
```
   location / {
      root   /usr/local/www;
      index  yunba_javascript_demo.html yunba_javascript_demo.htm;
  }
```
在 conf 文件中如有其他类似的描述 index 的地方，也需要作出相应的修改。

####3. 修改 AppKey
请打开需要用到的 yunba_javascript_demo.html 或 yunba_javascript_demo_customid.html 文件，在开头处找到 AppKey 所在的位置，
替换为步骤 1 中在 Portal 创建新应用时获取到的 AppKey。
```
    var yunba = new Yunba({appkey: 'xxxxxxxxxxxxxxxxx'});
```
####4. 运行 Demo
打开 Mac 的 Terminal，输入 nginx，启动 nginx。在浏览器中打开 “locahost:8080”，即可运行。



