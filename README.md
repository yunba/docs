# docs
Yunba Documentation

## 文档风格

- 文档采用 github 兼容 markdown 格式编写。
- 英文、数字的前后要加空格，如遇到标点、斜杠等字符时可以省略空格。例如：“Yunba 实时消息、Yunba 的 Android/iOS 消息推送。”“保留 50 条。”
- 超链接文本的前后要加上空格，遇到标点符号时省略空格。
- 中英文标点不要混用。中文文档内容统一使用中文标点，英文文档统一使用英文标点。
- 专用名词统一。特别是中英文翻译，使用统一的对照表。
- API 输入参数使用表格描述。
- 文档的图片使用 github URL：https://raw.githubusercontent.com/yunba/docs/master/image/xxx.png

## 文档文件名命名规则

- 文档的文件名由模块名、文档类型组成。
- 文件名各个部分用 "_" 连接。

例如：Android_QuickStart.md

## 文档细节规范
- 提到某个函数时，用：`函数名`。例如，`subscribe()`。
- 命令行，用“>”引言表示。例如，> $ sudo easy_install pip
- 不便透露的内容，用大写的 X 表示。例如，appkey:"XXXXXX"
- 注意事项另起一行，这样写：**注**：XXXX。
- 标题为 # 级别，以此类推。
- 需要用到序号时，使用：1. 1.1 1.2  样式

## 文档目录结构

### 1. 产品介绍 ［/products］
* [产品简介](https://github.com/yunba/docs/blob/master/products/product_briefing.md)
* 消息推送概述 [/products/briefing_push_notification.md]
* 实时消息概述 [/products/briefing_realtime_messaging.md]
* 实时统计概述 [/products/briefing_analytics.md]
* 实时在线概述 [/products/briefing_presence.md]

### 2. 开发指南 ［/tutorials］
* [消息推送开发指南](https://github.com/yunba/docs/tree/master/tutorials/tutorials_push_notification)
* 实时消息开发指南 [/tutorials/tutorials_realtime_messaging]
* 实时统计开发指南 [/tutorials/tutorials_analytics]
* 实时在线开发指南 [/tutorials/tutorials_presence]

### 3. 快速入门 [/quickstart]
* 运行 Yunba SDK Demo [/quickstart/demo]
 - [运行 Yunba Android Demo](https://github.com/yunba/docs/blob/master/quickstart/demo/Demo_Android.md)
 - [运行 Yunba iOS Demo](https://github.com/yunba/docs/blob/master/quickstart/demo/Demo_iOS.md)
 - [运行 Yunba C Demo](https://github.com/yunba/docs/blob/master/quickstart/demo/Demo_C.md)
 - [运行 Yunba JavaScript Demo](https://github.com/yunba/docs/blob/master/quickstart/demo/Demo_JavaScript.md)
 - [运行 Yunba C# Demo](https://github.com/yunba/docs/blob/master/quickstart/demo/Demo_CSharp.md)
 - [运行 Yunba Socket.io Python Demo](https://github.com/yunba/docs/blob/master/quickstart/demo/Demo_SocketIO_Python.md)
 - [运行 Yunba Socket.io Java Demo](https://github.com/yunba/docs/blob/master/quickstart/demo/Demo_SocketIO_Java.md)
 - [运行 Yunba PHP Demo](https://github.com/yunba/docs/blob/master/quickstart/demo/Demo_PHP.md)
 - [运行 Yunba RESTful API Demo](https://github.com/yunba/docs/blob/master/quickstart/demo/Demo_RESTful.md)


### 4. SDK 手册 [/sdk]
* [Android SDK 使用指南](https://github.com/yunba/docs/blob/master/sdk/Android%20SDK%20%E4%BD%BF%E7%94%A8%E6%8C%87%E5%8D%97.md)

### 5. 技术支持 [/support]
* [FAQ](https://github.com/yunba/docs/blob/master/support/faq/faq.md)
* [云巴知识库](https://github.com/yunba/kb/tree/master)
* 更新日志 [/support/change_log]
* 技术博客 [/support/blog]
* 开发论坛 [/support/forum]

### 6. 案例分析 [/demos]
