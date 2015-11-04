# 如何安装 virtualenv

本文简单介绍下如何在 Mac 下安装和使用 virtualenv。<br>
本文涉及的安装环境：
* Mac OS X 10.11.1
* pip 7.1.2
* virtualenv 13.1.2

## 安装步骤

1. 在 Mac 的 Terminal 中输入如下命令，安装 pip。根据提示，输入系统密码后，即可自动进行下载和安装。

 >$ sudo easy_install pip

2. 安装完成后，执行如下命令，安装 virtualenv。同样地，输入系统密码后，会自动进行下载和安装。

 >$ sudo pip install virtualenv

3. 安装好后，输入如下命令，会新建一个叫“ENV”的文件夹作为虚拟环境：

 >$ virtualenv ENV

4. 用下面的命令进行激活，激活后，命令行的开头处会多一个(ENV)，ENV 为虚拟环境名称，接下来所有模块都只会安装到该目录中去。

 >$ source ENV/bin/activate

5. 要退出这个虚拟环境，只需输入：

 >$ deactivate

更多信息，请参考 [virtualenv 的官方文档](https://virtualenv.pypa.io/en/latest/userguide.html)。
