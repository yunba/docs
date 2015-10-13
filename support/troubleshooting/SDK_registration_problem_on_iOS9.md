##**云巴 SDK 对 iOS 9 无法正常注册**
---
###**问题**
云巴 iOS SDK 1.5.2 之前的版本对 iOS 9 无法正常注册。
<br>

###**解决方案**
**注**：这是一个临时解决方案，让 App 能正常访问云巴的 HTTP 服务。云巴将在稍后更新 SDK 以彻底解决此问题。

打开 App 的 info.plist 文件，加入如下键值（注意键值类型，暂时只能手动添加，没有对应的键可选）:
```
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSAllowsArbitraryLoads</key>
    <true/>
</dict>
```
<br>
以 iOS SDK 中的 YunBaDemo 为例，键值增加界面如下图所示：
<br><br>
![SDK_registration_problem_on_iOS9_plist_add_key.png](https://raw.githubusercontent.com/yunba/docs/master/image/for_support/for_troubleshooting/SDK_registration_problem_on_iOS9_plist_add_key.png)

