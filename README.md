# Yunba Documentation

## 资源列表

- 快速入门
  - [云巴是什么](product_kb_whats_yunba.md)
  - [常见问题解答（FAQ）](product_faq.md)
  - [云巴快速入门教程](product_kb_yunba_quick_start.md)

- 案例
  - [云巴视频直播弹幕](https://github.com/yunbademo/yunba-live-video) 
  - [云巴智能小屋](https://github.com/yunbademo/yunba-smarthome) 
  - [云巴智能办公室](https://github.com/yunbademo/yunba-smartoffice)
  - [云巴多屏互动](https://github.com/yunbademo/yunba-multi-screen) 
  - [云巴聊天室](https://github.com/yunbademo/yunba-chatroom)  
  - [云巴 Yo](https://github.com/yunbademo/YunBa-Yo)
  - [更多案例](https://github.com/yunbademo) 

- 功能介绍
  - [频道和别名](product_kb_topic_and_alias.md)
  - [AppKey](product_kb_app_key.md)
  - [Portal](product_kb_portal.md)
  - [云巴的实时在线](product_kb_presence.md)
  - [云巴的离线消息](product_kb_offline_message.md)
  - [QoS](product_kb_qos.md)
  - [云巴服务的 IP 是不固定的](product_kb_yunba_unfixed_ip.md)

- Android
  - [云巴 Android 消息推送](android_kb_android_push.md)
  - [Android SDK 快速入门](android_sdk_quick_start.md)
  - [Android SDK API 手册](android_sdk_api_manual.md)
  - [Android SDK 使用指南](android_sdk_tutorial.md)
  - [运行 Yunba Android Demo](android_demo_quick_start.md)

- iOS
  - [云巴 iOS 消息推送](ios_kb_ios_push.md)
  - [iOS SDK 快速入门](ios_sdk_quick_start.md)
  - [iOS SDK API 手册](ios_sdk_api_manual.md)
  - [运行 Yunba iOS Demo](ios_demo_quick_start.md)
  - [如何通过云巴实现 APNs 推送](ios_kb_apns_implementation.md)
  - [生成 APNs 证书的步骤](ios_kb_create_apns_certificate.md)
  - [新建 App ID 的步骤](ios_kb_create_app_id.md)
  - [新建 CSR 文件的步骤](ios_kb_create_csr_file.md)
  - [生成 Provisioning Profile](ios_kb_create_provisioning_profile.md)
  - [APNs 推送的 Payload 介绍](ios_kb_payload.md)
  - [多证书](ios_kb_multiple_certificates.md)

- JavaScript
  - [JavaScript SDK 快速入门](js_sdk_quick_start.md)
  - [JavaScript SDK API 手册](js_sdk_api_manual.md)
  - [运行 Yunba JavaScript Demo](js_demo_quick_start.md)

- C 和 Embedded C
  - [快速入门](c_sdk_quick_start.md)
  - [C SDK API 手册](c_sdk_api_manual.md)
  - [Embedded C API 手册](embeddedc_sdk_api_manual.md)
  - [运行 Yunba C Demo](c_demo_quick_start.md)

- RESTful
  - [API 手册](restful_api_api_manual.md)

- Socket.IO
  - [API 手册](socketio_api_api_manual.md)
  - [运行 Yunba Socket.IO Python Demo](socketiopython_demo_quick_start.md)
  - [运行 Yunba Socket.IO Java Demo](SocketIOjava_demo_quick_start.md)

- C Sharp
 -  [运行 Yunba C Sharp Demo](csharp_demo_quick_start.md)
 
- Java
  - [API 手册](java_sdk_api_manual.md)
  - [Yunba Java Demo](java_demo_quick_start.md)

- PHP(Cli)
  - [运行 Yunba PHP Demo](php_demo_quick_start.md)

## 文档风格

- 文档采用 Github 兼容 Markdown 格式编写，后渲染到官网上。
- 中文文档中如果出现英文、数字、超链接或某些特殊字符的文本时，需要在文本两侧加上空格进行分隔。遇到标点符号时可省略空格。如果必须对文本使用引号，需要使用中文的引号。例如：在 WampServer 解压后的 “...\wamp\bin\php” 路径下搜索 php.init 文件；Yunba 的 Android/iOS 消息推送；保留 50 条。
- Github Markdown 的中文引号始终显示为半角引号，在使用引号后，引号外侧需再加个空格。同样地，遇到标点符号时可空格省略。（参考上面的例子）
- 中文文档，一律使用中文标点符号。夹杂英文或其他非中文字符时，尽量只在字符外侧使用空格进行分隔，不使用引号。
- 专用名词统一。特别是中英文翻译，使用统一的对照表。
- API 输入参数使用表格描述。
- 文档的图片使用 Github URL：https://raw.githubusercontent.com/yunba/docs/master/image/xxx.png
- 提到某个函数时，用：`函数名`。例如，`subscribe()`。
- 代码示例中涉及到 AppKey、SecKey、Message ID、Session ID 时，使用统一的示例（代码示例较长，ID 必须变化的情况除外）：
  - AppKey：567a4a754407a3cd028aaf6b
  - SecKey：sec-mj64xlu0ob1Xs1wWuZzmGZOYZqrpFmFxp5jHULr13eUZCVpS
  - Message ID：11833652203486491112
  - Session ID：567a4a754407a3cd028aaf6b-f02bf150-c653-4557-973f-8526b078d736
- 注意事项另起一行，并用引用的方式写：**注**：……。
- 文档标题为 # 级别，以此类推。
- 需要用到序号时，使用：1. 1.1 1.2 样式。
- 添加代码的首行统一顶格，其余行则遵守代码格式。

## 渲染后效果问题
- 换行：Github Markdown（简称 GM）支持 br 换行，但云巴网页（简称 YB）渲染后不支持。（GM 一次换行无效果，两次换行最终效果是一次换行。）
- 标题字号：GM 上 #### 级的字，在 YB 上比普通正文字体还要小。因此，约定 GM 上最多使用 ### 级，再往下，采用双星号加粗表示标题。还可以使用 1. 1.1 样式。
- GM 上使用单个大于号所引导的内容，在 GM 上显示为灰色背景的深灰色字，而在 YB 上渲染后为红色字、有缩进且带大边框，不建议使用。

## 文件命名规则

- 文档的文件名由模块名、文档类型和文档的名称三个部分组成，各个部分用 "_" 连接，全部规定为小写英文字母，除专有英文缩写外不允许缩写。
  - 第一部分的名字是平台名，如 ios、android 等，通用的则命名为 product；
  - 第二部分的名字是类型，现有 sdk、demo、kb 和 faq 四种；
  - 第三部分的名字是文档的名称，没有具体限制，要足够直观，为了简洁性，最好在五个英文单词内，并用"_"连接。

`例如：android_sdk_quick_start.md`

- 图片的命名规则同文档类似，也是三个部分。
  - 第一部分是平台名后加"png"；
  - 第二部分是所用在的类型，如：cert、demo、troubleshooting、sdk 和 portal；
  - 第三部分是描述这个图片所进行的行为，要简洁、直观，限定在五个英文单词内，并用"_"连接。

`例如：productpng_demo_quote_stock`

## 图片标注规则

- 约定使用 Paintbrush；
- 选中：用方框第三行首个红色(Maraschino, sRGB: 237.70.47)3号粗；
- 覆盖：用直线首行倒数第四个(Magnesium, sRGB:192.192.192)9号粗。
