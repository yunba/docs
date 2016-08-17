# 多证书

## 场景

某些 iOS 应用在发布时可能会分为不同的版本，如打车软件的司机版和乘客版、交易软件的买家版和卖家版。
开发者往往希望在发推送时，不同版本的应用都能同时收到。

在这种场景下，可以使用云巴提供的 “多证书” 功能。

## 使用对象

**“多证书” 针对的对象是：同一个 [AppKey](product_kb_app_key.md)、不同版本的 iOS 应用。**

## 如何使用

“多证书” 的使用很简单，只需在 [Portal](product_kb_portal.md) 创建应用时上传多个证书即可。
上传的界面如下图所示。


云巴会自动检测哪个证书对应哪个设备，确保消息送达所有的订阅设备。


注意：*如果您使用了生产和开发证书两个证书来进行推送：云巴会根据 SDK 上报的状态自动选择生产或者开发证书，也就是说，消息会推送给使用开发或生产证书的所有设备。如果您只需要发送给某一个证书下的 App，建议使用测试的 Topic，或暂时删除另一个证书。*
![iospng_protal_multi_certificate.png](https://raw.githubusercontent.com/yunba/docs/master/image/iospng_protal_multi_certificate.png)