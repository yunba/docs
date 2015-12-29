##iOS Demo 编译时可能遇到 Failed to code sign “YunBaDemo” 错误

###问题
云巴的 iOS SDK Demo 程序在编译时，可能会遇到 Failed to code sign “YunBaDemo” 的错误提示：

>No codesigning identities (i.e. certificate and private key pairs) that match the provisioning profile specified in your build settings ("com.yunba.XXXXX_dev") were found.

###解决方案

确保 KeyChain 中导入的开发者证书（iPhone Developer）及私钥与 Demo 工程中指定的 Provisioning Profile 所包含的开发者证书及私钥是完全匹配的。

![keychain_cert.png](https://raw.githubusercontent.com/yunba/docs/master/image/for_support/for_troubleshooting/keychain_cert.png)

![provision_cert.png](https://raw.githubusercontent.com/yunba/docs/master/image/for_support/for_knowledgebase/provision_cert.png)

