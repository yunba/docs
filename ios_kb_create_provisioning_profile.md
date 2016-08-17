# 生成 Provisioning Profile

本节以“开发”类型的 Provisioning Profile 为例，介绍如何生成 Provisioning Profile，并导入 Xcode 中。

1. 打开 Apple 开发者网站的 [Provisioning Profiles 管理界面](https://developer.apple.com/account/ios/profile/profileList.action)，并用您的苹果开发者账号登录。
如图所示，界面上列出了现有的 Provisioning Profiles。

	![iospng_cert_provision_all.png](https://raw.githubusercontent.com/yunba/docs/master/image/iospng_cert_provision_all.png)

2. 点击右上角的新建按钮，选择 Profile 的类型，并点击 Continue 按钮。

	![iospng_cert_provision_type.png](https://raw.githubusercontent.com/yunba/docs/master/image/iospng_cert_provision_type.png)

3. 选择要创建 Provisioning Profile 的 App ID。

	![iospng_cert_provision_appId.png](https://raw.githubusercontent.com/yunba/docs/master/image/iospng_cert_provision_appId.png)

4. 选择开发者证书。

	![iospng_cert_provision_certificate.png](https://raw.githubusercontent.com/yunba/docs/master/image/iospng_cert_provision_certificate.png)

5. 选择允许哪些设备使用该 Provisioning Profile。一般情况下，全选即可。

	![iospng_cert_provision_device.png](https://raw.githubusercontent.com/yunba/docs/master/image/iospng_cert_provision_device.png)

6. 为该 Provisioning Profile 命名，并点击 Generate，即可生成。

	![iospng_cert_provision_name.png](https://raw.githubusercontent.com/yunba/docs/master/image/iospng_cert_provision_name.png)

7. 点击 Download，下载到本地。

	![iospng_cert_provision_done.png](https://raw.githubusercontent.com/yunba/docs/master/image/iospng_cert_provision_done.png)

8. 双击 Provisioning Profile 文件，即可将其导入到 Xcode 中。




