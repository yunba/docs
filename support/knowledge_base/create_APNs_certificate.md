## **生成 APNs 证书的步骤**
---

1. 打开 Apple 开发者网站的[证书管理界面](https://developer.apple.com/account/ios/certificate "证书管理界面")；

2. 点击新建证书的按钮；

	![create_ios_cert.png](https://raw.githubusercontent.com/yunba/docs/master/image/create_ios_cert.png)

3. 选择新建证书的类型（开发或者生产推送环境）；

	![create_cert_choose_type.png](https://raw.githubusercontent.com/yunba/docs/master/image/create_cert_choose_type.png)

	**注**：如果你的系统中没有中间签名证书，则需要下载并安装到你的系统中：

	![before_create_cert.png](https://raw.githubusercontent.com/yunba/docs/master/image/before_create_cert.png)

4. 为证书选择对应需要推送功能的 APP ID；

	![create_cert_choose_appid.png](https://raw.githubusercontent.com/yunba/docs/master/image/create_cert_choose_appid.png)

5. 为制作推送证书，需要有一个 CSR 文件用以使新生成的推送证书与私钥相匹配：

	![create_cert_upload_csr.png](https://raw.githubusercontent.com/yunba/docs/master/image/create_cert_upload_csr.png)
	
	注：如果你没有 CSR 文件，则需要新建一个，步骤请参考[新建 CSR 文件的步骤](https://github.com/yunba/docs/blob/master/support/knowledge_base/create_CSR_file.md "新建 CSR 文件的步骤")。

6. 点击继续之后生成 APNs 证书成功，然后点击下载即可；

	![download_created_cert.png](https://raw.githubusercontent.com/yunba/docs/master/image/download_created_cert.png)

7. 导出证书为 p12 文件；

	![export_certs_to_p12.png](https://raw.githubusercontent.com/yunba/docs/master/image/export_certs_to_p12.png)

	导出时可以添加自定义密码以提高安全性:

	![export_p12_password.png](https://raw.githubusercontent.com/yunba/docs/master/image/export_p12_password.png)

8. 制作完成后，在 Portal 上传 APNs 证书以激活 APN 推送功能。
	
