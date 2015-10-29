##**如何安装 Homebrew**
---

本文涉及的安装环境：
* Mac OS X 10.11.1
* Xcode 7.1

###**安装步骤**

1. 打开 [Homebrew 官方网站](http://brew.sh)，并根据提示，在 Mac 的 Terminal 中输入安装的指令：

 ```
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```


2. 打开 Xcode，同意相关的许可文件，或在 Terminal 中输入如下指令，根据提示输入系统密码，再重新执行上一步的安装命令。

 ```
sudo xcodebuild -license
```


3. Terminal 中会出现如下的安装描述，按下回车，确认继续安装。根据提示，输入系统密码后，开始自动下载和安装。出现“Installation successful”即表明安装完成：

 ```
==> This script will install:
/usr/local/bin/brew
/usr/local/Library/...
/usr/local/share/man/man1/brew.1
==> The following directories will be made group writable:
/usr/local/.
==> The following directories will have their owner set to YourID:
/usr/local/.
==> The following directories will have their group set to admin:
/usr/local/.

Press RETURN to continue or any other key to abort
==> /usr/bin/sudo /bin/chmod g+rwx /usr/local/.
Password:
==> /usr/bin/sudo /usr/sbin/chown YourID /usr/local/.
==> /usr/bin/sudo /usr/bin/chgrp admin /usr/local/.
==> /usr/bin/sudo /bin/mkdir /Library/Caches/Homebrew
==> /usr/bin/sudo /bin/chmod g+rwx /Library/Caches/Homebrew
==> /usr/bin/sudo /usr/sbin/chown YourID /Library/Caches/Homebrew
==> Downloading and installing Homebrew...
remote: Counting objects: 3799, done.
remote: Compressing objects: 100% (3641/3641), done.
remote: Total 3799 (delta 40), reused 507 (delta 22), pack-reused 0
Receiving objects: 100% (3799/3799), 3.24 MiB | 4.00 KiB/s, done.
Resolving deltas: 100% (40/40), done.
From https://github.com/Homebrew/homebrew
 * [new branch]      master     -> origin/master
HEAD is now at e37f843 jvgrep: update 4.4 bottle.
==> Installation successful!
==> Next steps
Run `brew help` to get started
```
