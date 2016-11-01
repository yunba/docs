# 故障排除
## 按照步骤操作但接收不到消息
1. 如果从未成功接收消息，一般为代码集成错误，请参考 [Yunba Android SDK 快速入门](android_sdk_quick_start.md)，检查集成步骤。
**提示：**确保完成了 AndroidManifest.xml 的添加权限、配置 AppKey、修改应用包名、添加 Service 、添加 Receiver 和初始化 SDK 几个步骤；检查自定义 Receiver （接收消息）部分的代码,可参考 YunBa Android Demo 的 AndroidManifest.xml 和 DemoReceiver.java。
2. 检查网络问题，在网络正常情况下为秒内延迟。网络不稳定可能导致客户端与云巴服务端的长连接断开。支持 2G，3G，4G，Wi-Fi 网络环境。
3. 检查是否有保留后台进程，详见下文的后台进程部分。

## 显示“等待来自服务器的响应时超时”
如果 `subscribe()` 和 `publish()` 等操作未成功过，显示“等待来自服务器的响应时超时”，则一般为代码集成错误；如果是近期的故障，请检查本地网络环境。

## 应用退出后收不到消息
在后台进程驻留的情况下，应用可以接收到消息。

当后台进程被系统杀死，则长连接断开，客户端收不到消息。解决方法：可以通过后台进程守护和进程相互拉起使 App 退出后仍能收到消息。请参考 [特殊版本的 SDK](https://raw.githubusercontent.com/yunba/yunba-sdk-releases/master/Android/YunBa-Android-sdk-1.6.3.zip)（该版本仅供测试）。
