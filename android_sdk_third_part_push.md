# 第三方推送集成指南

[云巴](https://yunba.io/)Android SDK 从 v1.8.0-beta 起支持小米、华为推送，通过在这两类机型上的小米、华为推送进程来拉起云巴进程，从而实现杀掉 App 也收到推送的效果。

注意：

* 云巴会自动识别这两类机型，所以在使用第三方推送时不影响其它型号手机接收推送；
* 华为使用的是透传，小米用的是通知栏，所以在小米机型上会有收到两条通知栏消息的情况；
* 需要应用开启自启动权限。

## 准备工作

### 小米推送
* 进入[小米开放平台官网](http://dev.xiaomi.com/console/)，注册账号，选择`应用服务`->`小米推送`，进入小米推送服务，启用小米推送需要在小米控制台添加你的应用名和包名，然后获取小米推送的 AppID 和 AppKey，同时在项目中集成小米 SDK；可以参照小米的[启用指南](http://dev.xiaomi.com/doc/?p=1621)。

### 华为推送
* 进入[华为开发者联盟](http://developer.huawei.com/cn/consumer)，注册账号，根据[官方教程](http://developer.huawei.com/cn/consumer/wiki/index.php?title=%E6%8E%A5%E5%85%A5%E8%AF%B4%E6%98%8E#2.2_.E5.88.9B.E5.BB.BA.E5.BA.94.E7.94.A8)设置接入华为推送服务，集成华为 SDK，然后就可以在云巴中使用华为推送了。可以参考华为的[接入说明](http://developer.huawei.com/cn/consumer/wiki/index.php?title=%E6%8E%A5%E5%85%A5%E8%AF%B4%E6%98%8E#2_.E5.9C.A8.E5.BC.80.E5.8F.91.E8.80.85.E8.81.94.E7.9B.9F.E4.B8.8A.E5.BC.80.E9.80.9A.E5.8D.8E.E4.B8.BA.E6.8E.A8.E9.80.81.E6.9D.83.E7.9B.8A)。

### 云巴服务
参考我们的[快速接入文档](https://yunba.io/docs2/android_sdk_quick_start)快速设置接入云巴服务；如果你已经接入云巴服务，可以直接开始在云巴中集成小米和华为推送。

#### 在 portal 上设置

1. 进入 portal 页面，在应用列表中找到需要接入厂商推送的应用，点击`厂商推送`。
![applist](https://raw.githubusercontent.com/yunba/docs/master/image/productpng_portal_third_push_applist.png)

2. 在选项中选择小米或华为，在`是否启用`处选择`是`，填写在小米推送中[注册](http://dev.xiaomi.com/doc/?p=1621)使用的主包名和获取的 AppSecret;同理，如果需要启用华为推送，则选择`华为`选项，并填写在华为推送中[注册](http://developer.huawei.com/cn/consumer/wiki/index.php?title=%E6%8E%A5%E5%85%A5%E8%AF%B4%E6%98%8E#2.2_.E5.88.9B.E5.BB.BA.E5.BA.94.E7.94.A8)时使用的应用包名和获得的 APP ID 以及 APP SECRET。然后点击保存。
![xiaomi_setup](https://raw.githubusercontent.com/yunba/docs/master/image/productpng_portal_third_push_xiaomi_setup.png)
![huawei_setup](https://raw.githubusercontent.com/yunba/docs/master/image/productpng_portal_third_push_huawei_setup.png)

## 设置步骤

### 设置 AndroidManifest.xml
在[设置了云巴推送](https://yunba.io/docs2/android_sdk_quick_start)的基础上，在 AndroidManifest.xml 中加入第三方推送的权限。

#### 对于小米推送，需要在 AndroidManifest.xml 中进行以下设置：

(1) 在`<manifest>......</manifest>`中添加以下代码来添加 permission，注意*app包名要替换成自己的包名*:

```xml
<!--这里com.xiaomi.mipushdemo改成app的包名-->
<permission android:name="com.xiaomi.mipushdemo.permission.MIPUSH_RECEIVE" android:protectionLevel="signature" />
<!--这里com.xiaomi.mipushdemo改成app的包名-->
<uses-permission android:name="com.xiaomi.mipushdemo.permission.MIPUSH_RECEIVE" />
```

(2) 在`<application>......</application>`中添加以下代码来添加 service 和 receiver：

```xml
<service
	android:name="com.xiaomi.push.service.XMJobService"
	android:enabled="true"
	android:exported="false"
	android:permission="android.permission.BIND_JOB_SERVICE"
	android:process=":pushservice" />
<service
	android:name="com.xiaomi.push.service.XMPushService"
	android:enabled="true"
	android:process=":pushservice" />
<service
    android:name="com.xiaomi.mipush.sdk.PushMessageHandler"
    android:enabled="true"
    android:exported="true" />
<service
    android:name="com.xiaomi.mipush.sdk.MessageHandleService"
    android:enabled="true" />
<receiver
    android:name="io.yunba.android.thirdparty.receiver.ThirdPartyXMReceiver"
    android:exported="true">
    <intent-filter>
        <action android:name="com.xiaomi.mipush.RECEIVE_MESSAGE" />
    </intent-filter>
    <intent-filter>
        <action android:name="com.xiaomi.mipush.MESSAGE_ARRIVED" />
    </intent-filter>
    <intent-filter>
        <action android:name="com.xiaomi.mipush.ERROR" />
    </intent-filter>
</receiver>
<receiver
    android:name="com.xiaomi.push.service.receivers.NetworkStatusReceiver"
    android:exported="true">
    <intent-filter>
        <action android:name="android.net.conn.CONNECTIVITY_CHANGE" />
        <category android:name="android.intent.category.DEFAULT" />
    </intent-filter>
</receiver>
<receiver
    android:name="com.xiaomi.push.service.receivers.PingReceiver"
    android:exported="false"
    android:process=":pushservice">
    <intent-filter>
        <action android:name="com.xiaomi.push.PING_TIMER" />
    </intent-filter>
</receiver>
```

#### 对于华为推送，需要在 AndroidManifest.xml 中进行以下设置：

在`<application>......</application>`中添加 service 和 receiver：

```xml
<service
    android:name="com.huawei.android.pushagent.PushService"
    android:process=":pushservice" >
</service>
<!-- 这是第三方华为的接收器权限声明 -->
<receiver android:name="io.yunba.android.thirdparty.receiver.ThirdPartyHWReceiver">
    <intent-filter>
        <!-- 必须,用于接收token-->
        <action android:name="com.huawei.android.push.intent.REGISTRATION" />
        <!-- 必须，用于接收消息-->
        <action android:name="com.huawei.android.push.intent.RECEIVE" />
        <!-- 可选，用于点击通知栏或通知栏上的按钮后触发onEvent回调-->
        <action android:name="com.huawei.android.push.intent.CLICK" />
        <!-- 可选，查看push通道是否连接，不查看则不需要-->
        <action android:name="com.huawei.intent.action.PUSH_STATE" />
        <!-- 可选，标签、地理位置上报回应，不上报则不需要 -->
        <action android:name="com.huawei.android.push.plugin.RESPONSE" />
    </intent-filter>
</receiver>
<receiver
    android:name="com.huawei.android.pushagent.PushEventReceiver"
    android:process=":pushservice" >
    <intent-filter>
        <action android:name="com.huawei.android.push.intent.REFRESH_PUSH_CHANNEL" />
        <action android:name="com.huawei.intent.action.PUSH" />
        <action android:name="com.huawei.intent.action.PUSH_ON" />
        <action android:name="com.huawei.android.push.PLUGIN" />
    </intent-filter>
    <intent-filter>
        <action android:name="android.intent.action.PACKAGE_ADDED" />
        <action android:name="android.intent.action.PACKAGE_REMOVED" />              
        <data android:scheme="package" />
    </intent-filter>
</receiver>
<receiver
    android:name="com.huawei.android.pushagent.PushBootReceiver"
    android:process=":pushservice" >
    <intent-filter>
        <action android:name="com.huawei.android.push.intent.REGISTER" />
        <action android:name="android.net.conn.CONNECTIVITY_CHANGE" />
    </intent-filter>
    <meta-data
        android:name="CS_cloud_version"
        android:value="\u0032\u0037\u0030\u0035" />
</receiver>
```
### 初始化设置

要启用第三方推送，要在[初始化云巴服务](https://yunba.io/docs2/android_sdk_api_manual#start)**之前**调用这个 API：`YunBaManager.setThirdPartyEnable(getApplicationContext(), true)`，**对于小米推送，还要额外调用`YunBaManager.setXMRegister(String appid,String appkey)`**。

#### 代码示例
* 启用小米和华为推送

```java
YunBaManager.setThirdPartyEnable(getApplicationContext(), true);
YunBaManager.setXMRegister("0000000000000000000","0000000000000");
YunBaManager.start(getApplicationContext());
```

* 仅启用华为推送

```java
YunBaManager.setThirdPartyEnable(getApplicationContext(), true);
YunBaManager.start(getApplicationContext());
```
## 备注：权限列表

* 这里云巴需要的其他权限，可以在[快速接入云巴服务](https://yunba.io/docs2/android_sdk_quick_start)中找到，供对照：

```xml
<uses-permission android:name="android.permission.INTERNET" />
   <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
   <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
   <uses-permission android:name="android.permission.READ_PHONE_STATE" />
   <uses-permission android:name="android.permission.WAKE_LOCK" />
   <uses-permission android:name="android.permission.GET_TASKS" />
   <uses-permission android:name="android.permission.VIBRATE"/>
   <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
   <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
   <uses-permission android:name="android.permission.RECEIVE_USER_PRESENT" />
   <uses-permission android:name="android.permission.MOUNT_UNMOUNT_FILESYSTEMS" />
   <uses-permission android:name="android.permission.WRITE_SETTINGS" />

   <!-- for location only -->
   <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
   <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
   <uses-permission android:name="android.permission.CHANGE_WIFI_STATE"/>
```