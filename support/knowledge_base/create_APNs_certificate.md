## **生成 APNs 证书的步骤**
---

### **准备工作**

在生成 APNs 证书之前，请先生成 CSR 文件（CertificateSigningRequest.certSigningRequest）。CSR 文件的新建步骤请参考[新建 CSR 文件的步骤](https://github.com/yunba/docs/blob/master/support/knowledge_base/create_CSR_file.md "新建 CSR 文件的步骤")。

### **详细步骤**

1. 打开 Apple 开发者网站的[证书管理界面](https://developer.apple.com/account/ios/certificate "证书管理界面")，并用您的苹果开发者账号登录；

2. 点击新建证书的按钮；

	![create_ios_cert.png](https://raw.githubusercontent.com/yunba/docs/master/image/create_ios_cert.png)

3. 选择新建证书的类型（开发或者生产推送环境）；

	![create_cert_choose_type.png](https://raw.githubusercontent.com/yunba/docs/master/image/create_cert_choose_type.png)

	**注**：如果你的系统中没有中间签名证书，则需要下载并安装到你的系统中：

	![before_create_cert.png](https://raw.githubusercontent.com/yunba/docs/master/image/before_create_cert.png)

4. 为证书选择对应需要推送功能的 APP ID，点击继续；

	![create_cert_choose_appid.png](https://raw.githubusercontent.com/yunba/docs/master/image/create_cert_choose_appid.png)

5. 制作推送证书时，需要一个 CSR 文件，来使新生成的推送证书与私钥相匹配。在界面上点击“Continue”按钮后，请上传前文提到的 CSR 证书。（CSR 文件的新建步骤请参考[新建 CSR 文件的步骤](https://github.com/yunba/docs/blob/master/support/knowledge_base/create_CSR_file.md "新建 CSR 文件的步骤")）。

	![create_cert_upload_csr.png](https://raw.githubusercontent.com/yunba/docs/master/image/create_cert_upload_csr.png)
	

6. 点击“Generate”按钮生成 APNs 证书，然后点击“Download”按钮下载即可，点击“Done”按钮完成整个操作。

	![download_created_cert.png](https://raw.githubusercontent.com/yunba/docs/master/image/download_created_cert.png)

7. 证书下载后，双击打开证书文件（*.cer）使之加入到钥匙串中。如遇到“不能修改‘System Roots’钥匙串”的警告，请参考[相应文档](https://github.com/yunba/docs/blob/master/support/troubleshooting/APNs_certificate_key_chain_warning.md "相应文档")解决。


8. 将证书导出为“个人信息交换（.p12）”格式。如遇到“个人信息交换（.p12）”的选项为灰色不可选状态，请您再次确认您的 APNs 证书是否是严格按照上述步骤进行生成的，建议删除重新做一次。

	![export_certs_to_p12.png](https://raw.githubusercontent.com/yunba/docs/master/image/export_certs_to_p12.png)

	导出时可以添加自定义密码以提高安全性:

	![export_p12_password.png](https://raw.githubusercontent.com/yunba/docs/master/image/export_p12_password.png)

至此，APNs 证书生成完毕。<br>在 Yunba 的 Portal 页面创建新应用时，在 iOS 开发/生产证书处上传这份个人信息交换（.12）即可。
