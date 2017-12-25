# 使用 Wireshark 对移动设备进行抓包
***运行环境需求：***

* Wireshark 2.2.1
* 一台可以 Wi-Fi 共享的 Mac （macOS Sierra 10.12.1）

## 准备步骤
1. 在[官网](https://www.wireshark.org/)下载 Wireshark，然后安装。
2. 开启 Wi-Fi 共享，如需帮助可以参考[这里](http://jingyan.baidu.com/article/17bd8e52e344cf85ab2bb8f0.html)。
3. 按照 [这个插件](https://github.com/johnzeng/Wireshark-MQTT) 的usage段安装mqtt插件，然后即可解析云巴mqtt协议。

## 开始进行
首先，打开 Wireshark，选择捕获的接口，本教程是通过 Wi-Fi 来进行抓包的，所以选择 Wi-Fi:en0。***注意：多个无线网卡可能会有多个选项，选择你用来进行共享的接口即可***

![productpng_kb_packet_capture_wireshark.png](https://raw.githubusercontent.com/yunba/docs/master/image/productpng_kb_packet_capture_wireshark.png)

把要进行抓包的设备连接到你分享出去的 Wi-Fi，就可以看到设备上的网络包接收和发送的情况，在`显示过滤器`中输入`mqtt`，就可以只观察 mqtt 的包。

![productpng_kb_packet_capture_wireshark_mqtt.png](https://raw.githubusercontent.com/yunba/docs/master/image/productpng_kb_packet_capture_wireshark_mqtt.png)

## 观察云巴服务
保持云巴的服务在前台，就可以观察云巴的服务运行状况，帮助定位问题。下面简单说明一下常见的云巴 MQTT 包的含义。

1. Connect Command 连接到云巴服务的信息，包含 client ID，username,password 等连接信息。
![productpng_kb_packet_capture_wireshark_connect_command.png](https://raw.githubusercontent.com/yunba/docs/master/image/productpng_kb_packet_capture_wireshark_connect_command.png)
2. Publish Message 包含推送的消息和频道等信息。
![productpng_kb_packet_capture_wireshark_publish_message.png](https://raw.githubusercontent.com/yunba/docs/master/image/productpng_kb_packet_capture_wireshark_publish_message.png)
3. Ping Request 心跳包，可用来观察设备是否和云巴服务正常连接。

## 补充
* 使用 Wireshark 同时也可以进行其它设备的抓包，原理是类似的，只要把监听的接口设置成设备所在的网络即可，不赘述。

* 可以在[这个网站](http://whatismyipaddress.com/)获取自己的 IP 地址。
