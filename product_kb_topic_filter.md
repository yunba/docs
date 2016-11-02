# 频道过滤器

在信息过剩的今天，几乎每一款资讯产品都会对不同话题的信息做出细致的种类和层级划分，让用户可以自主筛选、获得定制的资讯。

为了满足这种话题分级管理、筛选订阅的需求，云巴实现了 MQTT v3.1.1 协议中的 [Topic Filter](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718106)（即，频道过滤器），客户端可以借助这一功能实现对不同层级的 [频道](product_kb_topic_and_alias.md) 的筛选订阅。本文将先介绍与之相关的 频道分级符、频道通配符 的概念，再给出 频道过滤器 的说明。

> 注意：Android、iOS SDK 尚未支持该功能，近期会上线。


## 频道分级符

我们用 /（U+002F）表示 频道分级符（Topic level separator）。借助 频道分级符 给频道分级，可以形成一个层次清晰的频道树。

含有 频道分级符 的频道称为 多级频道 。例如，sports/ballgame/baseball/play1 就是一个四级频道。


## 频道通配符

频道通配符（Topic wildcards），顾名思义，提供的是频道匹配的功能。
频道通配符 包括多级和单级两种。

### 多级通配符

多级通配符（Multi-level wildcard）用 #（U+0023）表示，用于匹配任意层级的频道（包含 0 级）。它只能出现在 频道过滤器 的最后一级，且出现时必须占据该层级。

* 正确示例：sports/# 可以匹配 sports、sports/ballgame、sports/ballgame/baseball 等；

* 正确示例：# 可以匹配 sports、news、sports/ballgame/baseball 等任意等级的频道；

* 错误示例：sports/ballgame# 和 sports/ballgame/#/baseball 都是错误的。

### 单级通配符

单级通配符（Single level wildcard）用 +（U+002B）表示。它可以出现在 频道过滤器 的任意一级，且出现时必须占据该层级。单级通配符 只能匹配其所在的那一级。


* 正确示例：ballgame/+/player1 可以匹配 ballgame/baseball/player1、ballgame/basketball/player1 等；

* 正确示例：ballgame/baseball/+ 可以匹配 ballgame/baseball/player1、ballgame/baseball/player2  等；

* 正确示例：+ 可以匹配 sports、news 等频道；

* 错误示例：sports+ 是错误的。


## 频道过滤器

在订阅频道时，用于指定目标频道的那串字符串中如含有 频道通配符（#、+），则称之为 频道过滤器（Topic Filter），否则称为 频道名称（Topic Name）。

例如，sports/ballgame/baseball 是 频道名称，而 ballgame/+/player1、sports/# 是 频道过滤器。 


在频道种类繁多、层级复杂的情况下，如果用户需要订阅的是某一个或多个层级下的频道，就可以在订阅时指定 频道过滤器，借助 频道通配符 匹配相应的频道，实现对一系列频道的订阅。

* 频道名称 可以包含英文、数字、下划线、正斜杠，长度不超过 50 个字符。
* 频道过滤器 长度不超过 128 个字符，支持付费升级。






