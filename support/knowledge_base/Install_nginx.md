##**如何安装 nginx**
---

本文涉及的安装环境：
* Mac OS X 10.11.1
* Homebrew 0.9.5

###**安装步骤**

1. 参考 [如何安装 Homebrew 一文](https://github.com/yunba/docs/edit/master/support/knowledge_base/Install_Homebrew.md)，
安装 Homebrew。

2. 在 Mac 的 Terminal 中输入如下指令，即可自动下载安装 nginx，以及相关的 dependencies。
 ```
$ brew install nginx
```

3. 看到如下的安装日志，即表明 nginx 安装成功：

 ```
==> Installing nginx
==> Downloading https://homebrew.bintray.com/bottles/nginx-1.8.0.el_capitan.bottle.1.tar.gz
######################################################################## 100.0%
==> Pouring nginx-1.8.0.el_capitan.bottle.1.tar.gz
==> Caveats
Docroot is: /usr/local/var/www

The default port has been set in /usr/local/etc/nginx/nginx.conf to 8080 so that
nginx can run without sudo.

nginx will load all files in /usr/local/etc/nginx/servers/.

To have launchd start nginx at login:
  ln -sfv /usr/local/opt/nginx/*.plist ~/Library/LaunchAgents
Then to load nginx now:
  launchctl load ~/Library/LaunchAgents/homebrew.mxcl.nginx.plist
Or, if you don't want/need launchctl, you can just run:
  nginx
==> Summary
   /usr/local/Cellar/nginx/1.8.0: 7 files, 964K
```

4. 安装好后，在 Terminal 输入 “nginx” 即可启动 nginx。打开浏览器，输入“localhost:8080”，即可看到 nginx 欢迎页面。

 ```
Welcome to nginx!

If you see this page, the nginx web server is successfully installed and working. Further configuration is required.

For online documentation and support please refer to nginx.org.
Commercial support is available at nginx.com.

Thank you for using nginx.
```
