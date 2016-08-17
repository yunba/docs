# 生成 APNs 证书的步骤


## 准备工作

1. 制作 APNs 证书时，需要一个 CSR（CertificateSigningRequest.certSigningRequest）文件，来使新生成的推送证书与私钥相匹配。CSR 文件的新建步骤请参考 [新建 CSR 文件的步骤](ios_kb_create_csr_file.md)。

2. 如果您还没有创建 App ID，请参考 [新建 App ID 的步骤](ios_kb_create_app.md) 一文，创建一个 App ID，并为 App 开启 Push Notification 功能。如果已经创建过了，可跳过此步，只需在设置页面中确认该 App 已开启了 Push Notification 功能即可。**注意，这里的 App ID 不可以使用通配符。**

## 详细步骤

1. 打开 Apple 开发者网站的 [证书管理界面](https://developer.apple.com/account/ios/certificate)，并用您的苹果开发者账号登录；

2. 点击新建证书的按钮；

	![iospng_cert_creat_certificate.png](https://raw.githubusercontent.com/yunba/docs/master/image/iospng_cert_creat_certificate.png)

3. 选择新建证书的类型（开发或者生产推送环境）；

	![iospng_cert_choose_type.png](https://raw.githubusercontent.com/yunba/docs/master/image/iospng_cert_choose_type.png)

	**注**：如果你的系统中没有中间签名证书，则需要下载并安装到你的系统中：

	![iospng_cert_before_create_certificate.png](https://raw.githubusercontent.com/yunba/docs/master/image/iospng_cert_before_create_certificate.png)

4. 为证书选择对应需要推送功能的 App ID，点击继续；

	![iospng_cert_choose_appid.png](https://raw.githubusercontent.com/yunba/docs/master/image/iospng_cert_choose_appid.png)

5. 在界面上点击 Continue 按钮后，请上传前文提到的 CSR 证书。（CSR 文件的新建步骤请参考 [新建 CSR 文件的步骤](ios_kb_create_csr_file.md))。

	![iospng_cert_upload_csr.png](https://raw.githubusercontent.com/yunba/docs/master/image/iospng_cert_upload_csr.png)
	

6. 点击 Generate 按钮生成 APNs 证书，然后点击 Download 按钮下载即可，点击 Done 按钮完成整个操作。

	![iospng_cert_download_certifcate.png](https://raw.githubusercontent.com/yunba/docs/master/image/iospng_cert_download_certifcate.png)

7. 证书下载后，双击打开证书文件（*.cer）使之加入到钥匙串中。如遇到“不能修改‘System Roots’钥匙串”的警告，请参考 [相关文档](https://github.com/yunba/docs/blob/master/support/troubleshooting/APNs_certificate_key_chain_warning.md "相关文档") 解决。


8. 将证书导出为“个人信息交换（.p12）”格式。如遇到“个人信息交换（.p12）”的选项为灰色不可选状态，请您再次确认您的 APNs 证书是否是严格按照上述步骤进行生成的，建议删除重新做一次。

	![iospng_cert_export_certificate.png](https://raw.githubusercontent.com/yunba/docs/master/image/iospng_cert_export_certificate.png)

	导出时可以添加自定义密码以提高安全性:

	![iospng_cert_p12_password.png](https://raw.githubusercontent.com/yunba/docs/master/image/iospng_cert_p12_password.png)

至此，APNs 证书生成完毕。

在 Yunba 的 Portal 页面创建新应用时，在 iOS 开发/生产证书处上传这份个人信息交换（.p12）即可。
