# 第三方推送集成指南

---
[云巴](https://yunba.io/) Android SDK 从 v1.8.0 起支持小米、华为推送，通过向这两类手机发通知的方式，实现杀掉 App 也能收到推送的效果。

用户无需设置“应用自启动”，第三方推送通道的通知会强制弹出。

App 被杀的情况下，收到第三方推送通道的通知并不会将 App 唤醒，这个通知栏消息的作用仅仅是引导用户进行点击。当用户点击后，才会打开 App。


开发者按照本指南进行集成，并在推送时带上第三方推送的专用字段`thrid_party_push`，即可实现如下效果（在小米、华为自身推送链路正常的状态下）：

| | 第三方推送通道的通知  | 云巴通道的消息 |
|:-----|:-----|:-----|
| App 在前台 | 小米手机不会弹出，华为会弹出 | 可以收到，应用开发者可以选择是否弹出通知|
| App 在后台 | 小米、华为都会弹出 | 可以收到，应用开发者可以选择是否弹出通知|
| App 被杀  | 小米、华为都会弹出 | 收不到 |


**注意：**
* **云巴可以保证将消息送达小米和华为网关，但是小米和华为自身推送链路的稳定性依赖于小米和华为（我们测试时发现，在华为手机上有时会收不到这条通知）；**
* 云巴会自动识别这两类机型，所以在使用第三方推送时不影响其它型号手机接收推送；
* 不需要打开“应用自启动”权限；
* **应用未被杀的情况下，可以收到“云巴通道的消息”。如果应用开发者选择弹出通知，那么加上“第三方推送通道的通知”，就可能会出现两条通知栏的现象，参见上方表格以及 [FAQ](android_faq#9)**；

---

第三方推送的集成和使用包含五个步骤：

* 创建第三方应用
* 在云巴 Portal 上设置第三方推送
* 云巴服务接入
* 第三方推送开发设置
* 使用特殊字段进行推送

下面依次介绍：

## 1、创建第三方应用

在集成之前，需要先在小米、华为平台创建应用。

### 云巴自动创建

**为了极大地提高集成效率，云巴推出了“自动创建应用”的功能，免去了注册小米、华为账号的繁琐步骤。**详见后面的描述。

### 开发者自行创建

对于希望自己创建应用的开发者们，可前往小米、华为 Portal 创建应用：

* 进入[小米开放平台官网](http://dev.xiaomi.com/console/)，注册账号，选择`应用服务`->`小米推送`，进入小米推送服务，启用小米推送需要在小米控制台添加你的应用名和包名，然后获取小米推送的 AppID 和 AppKey，同时在项目中集成小米 SDK；可以参照小米的[启用指南](http://dev.xiaomi.com/doc/?p=1621)。

* 进入[华为开发者联盟](http://developer.huawei.com/cn/consumer)，注册账号，根据[官方教程](http://developer.huawei.com/cn/consumer/wiki/index.php?title=%E6%8E%A5%E5%85%A5%E8%AF%B4%E6%98%8E#2.2_.E5.88.9B.E5.BB.BA.E5.BA.94.E7.94.A8)设置接入华为推送服务，集成华为 SDK，然后就可以在云巴中使用华为推送了。可以参考华为的[接入说明](http://developer.huawei.com/cn/consumer/wiki/index.php?title=%E6%8E%A5%E5%85%A5%E8%AF%B4%E6%98%8E#2_.E5.9C.A8.E5.BC.80.E5.8F.91.E8.80.85.E8.81.94.E7.9B.9F.E4.B8.8A.E5.BC.80.E9.80.9A.E5.8D.8E.E4.B8.BA.E6.8E.A8.E9.80.81.E6.9D.83.E7.9B.8A)。


## 2、在云巴 Portal 上设置第三方推送


### 启用第三方推送

* 进入云巴 Portal 页面，新建应用，或在应用列表中找到需要接入第三方推送的应用，点击右侧的`第三方推送`。

![applist](https://raw.githubusercontent.com/yunba/docs/master/image/productpng_portal_third_push_applist.png)

* 在选项中选择小米或华为，在`是否启用`处选择`是`。


### 选择应用来源

* 云巴自动创建

如图所示，用户只需选择`云巴自动创建`并保存，云巴就会立刻代替用户进行小米或华为应用的申请，并在申请成功后，将获取到的 AppID、AppKey 等应用信息显示在当前页面上。

![appsource](https://raw.githubusercontent.com/yunba/docs/master/image/productpng_portal_third_push_appsource.png)

* 开发者自行创建

如果用户选择`开发者自行创建`，需将自己注册的小米、华为应用的应用信息填写到云巴 Portal 中。

对于[小米应用](http://dev.xiaomi.com/doc/?p=1621)，需填写的内容包括：应用包名、AppID、AppKey、AppSecret；
对于[华为应用](http://developer.huawei.com/cn/consumer/wiki/index.php?title=%E6%8E%A5%E5%85%A5%E8%AF%B4%E6%98%8E#2.2_.E5.88.9B.E5.BB.BA.E5.BA.94.E7.94.A8)，需填写的内容包括：应用包名、AppID、AppSecret；

![xiaomi_setup](https://raw.githubusercontent.com/yunba/docs/master/image/productpng_portal_third_push_xiaomi_setup.png)
![huawei_setup](https://raw.githubusercontent.com/yunba/docs/master/image/productpng_portal_third_push_huawei_setup.png)

## 3、云巴服务接入

请参考我们的[快速接入文档](https://yunba.io/docs2/android_sdk_quick_start)快速设置接入云巴服务；如果你已经接入云巴服务，可以直接开始在云巴中集成小米和华为推送。

## 4、第三方推送开发设置

### 设置 AndroidManifest.xml

在[设置了云巴推送](https://yunba.io/docs2/android_sdk_quick_start)的基础上，在 AndroidManifest.xml 中加入第三方推送的权限。注意用户需要自行引入第三方推送的 jar 包，云巴第三方推送使用的 jar 包可以在小米和华为的官网上下载，也可以在我们的 demo 程序中的 libs 中找到。如下图所示：

![androidpng_thirdpart_jar_path](https://raw.githubusercontent.com/yunba/docs/master/image/androidpng_thirdpart_jar_path.png)

* 对于小米推送，需要在 AndroidManifest.xml 中进行以下设置：

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

* 对于华为推送，需要在 AndroidManifest.xml 中进行以下设置：

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

要启用第三方推送，要在[初始化云巴服务](android_sdk_api_manual#start)**之前**调用这个 API：`YunBaManager.setThirdPartyEnable(getApplicationContext(), true)`。

**重要：对于小米推送，还要额外调用`YunBaManager.setXMRegister(String appid, String appkey)` 详见下方代码示例。**

#### 代码示例

* 启用小米和华为推送

```java
YunBaManager.setThirdPartyEnable(getApplicationContext(), true);
YunBaManager.setXMRegister(<your_xiaomi_appid>,<your_xiaomi_appkey>);
YunBaManager.start(getApplicationContext());
```

* 仅启用华为推送

```java
YunBaManager.setThirdPartyEnable(getApplicationContext(), true);
YunBaManager.start(getApplicationContext());
```


完成以上设置后，就可以连接小米、华为设备进行运行测试了。


## 5、使用特殊字段进行推送

在完成上述的集成步骤后，用户还需要在推送时使用指定的字段。
目前只支持通过 RESTful 发送第三方推送，做法如下，详见 [RESTful 文档](restful_api_api_manual)。其他 SDK 还未上线。

- 我们在 RESTful 的`publish`、`publish_to_alias`、`publish_to_alias_batch`的`opts`参数中增加了`third_party_push`字段。如果推送时携带该字段，就会发送小米通知、华为通知，否则就不会发。参考下方示例（再次重申，带上该字段就会发通知，但是客户端否能收到通知，还要依赖小米、华为自身的推送链路）。
- 通知的标题由`notification_title`字段指定，内容由 `notification_content`字段指定。
- 发布消息时，如果应用被杀或在后台，都会弹出这条通知；如果应用在前台，小米手机不会弹出这个通知，华为则一直会弹出。

```json
{
    "method":<method>, 
    "appkey":<app-key>, 
    "seckey":<secret-key>, 
    "topic":<topic>, 
    "msg":<message>,
    "opts":{
                "third_party_push": {
                    "notification_title":"通知的标题",
                    "notification_content":"通知的内容"
           } 
    }
}
```

## 备注：权限列表

* 这里是云巴需要的其他权限，可以在[快速接入云巴服务](https://yunba.io/docs2/android_sdk_quick_start)中找到，供对照：

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
