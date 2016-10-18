##说明
该文件夹文件用于文档中心页面和SDK下载页面维护。

`修改后需要到管理中心的文档页面点击更新，前端方可更新`

[TOC]

###newDoc.json文件

文档中心页面配置json。请严格按照格式改动配置文件。

####字段说明
* 整个json是一个对象数组。对象格式如下：
```
{
        "name": "概览",
        "show": true,
        "release": true,
        "docs": [{
            "name": "平台简介",
            "markdown": "product_kb_whats_yunba.md",
            "url": "product_kb_whats_yunba",
            "show": true,
            "release": true
        }, {
            "name": "功能介绍",
            "markdown": "product_kb_topic_and_alias.md",
            "url": "product_kb_topic_and_alias",
            "show": true,
            "release": true
        }, {
            "name": "使用须知",
            "markdown": "product_kb_yunba_quick_start.md",
            "url": "product_kb_yunba_quick_start",
            "show": true,
            "release": true
        }]
    }
    
```

*  `name` 用于显示文档中心的每一块的标题

*  `show／release` 未使用该字段

*  `docs` 文档中心的每一块的文章列表
     * `name` 文章名字
      
     * `markdown/url`  分别对应md文件名和url，请注意唯一性

     * `show/release` 未使用该字段

####newDownloads.json文件
SDK下载页面配置json。请严格按照格式改动配置文件。

####字段说明
* 整个json是一个对象数组。对象格式如下：

```
{
    "sdkName": "Android",
    "display": "block",
    "latestVersion": [{
        "versionUrl": "/docs2/android_sdk_api_manual",
        "downloadUrl":"https://github.com/yunba/yunba-sdk-releases/raw/master/Android/YunBa-Android-sdk-1.8.0-beta.zip",
        "githubUrl":"",
        "versionNum": "Release 1.8.0-beta",
        "versionDetail": [
            "支持小米推送、华为推送"
        ]
    }],
    "revisionHistory": [{
        "versionUrl": "https://raw.githubusercontent.com/yunba/yunba-sdk-releases/master/Android/YunBa-Android-sdk-1.6.4.zip",
        "downloadUrl":"",
        "versionNum": "Release 1.6.4",
        "versionDetail": [
            "与 1.6.3 版本相同，增加了 so 文件。"
        ]
    }, {
        "versionUrl": "https://raw.githubusercontent.com/yunba/yunba-sdk-releases/master/Android/YunBa-Android-sdk-1.6.3.zip",
        "downloadUrl":"",
        "versionNum": "Release 1.6.3",
        "versionDetail": ["后台进程相互拉起的特殊版本 SDK。"]
    }, {
        "versionUrl": "/docs2/android_sdk_api_manual",
        "downloadUrl":"https://raw.githubusercontent.com/yunba/yunba-sdk-releases/master/Android/YunBa-Android-sdk-1.4.5.zip",
        "githubUrl":"",
        "versionNum": "Release 1.4.5",
        "versionDetail": [
            ""
        ]
    }]
}
```

*  `sdkName` 用于显示SDK下载页面的导航条的每一个标题和某些attr

*  `display` 请设置为block

*  `latestVersion` SDK下载页面的最新div
     * `versionUrl` SDK的文档链接相对地址
      
     * `downloadUrl`  SDK的文档链接绝对地址

     * `githubUrl` 开源SDK的github链接绝对地址，闭源则填空。

     * `versionNum` SDK的版本号

     * `versionDetail` 版本描述，格式：`数组`，用数组来区分每一个不同的特性。如['sdk特性1','sdk特性2','sdk特性3']，若为空，请设置[""]

*  `revisionHistory` SDK下载页面的历史版本div,若为空，请设置 `"revisionHistory":[]`

     * `versionNum` SDK的版本号

     * `versionUrl/githubUrl/downloadUrl` 未使用

     * `versionDetail` 版本描述，格式：`数组`，用数组来区分每一个不同的特性。如['sdk特性1','sdk特性2','sdk特性3']，若为空，请设置`"versionDetail":[""]`


