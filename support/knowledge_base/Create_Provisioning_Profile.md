## 生成 Provisioning Profile

本节以“开发”类型的 Provisioning Profile 为例，介绍如何生成 Provisioning Profile，并导入 Xcode 中。

1. 打开 Apple 开发者网站的 [Provisioning Profiles 管理界面](https://developer.apple.com/account/ios/profile/profileList.action "Provisioning Profiles 管理界面")，并用您的苹果开发者账号登录。
如图所示，界面上列出了现有的 Provisioning Profiles。

	![provision_all.png](https://raw.githubusercontent.com/yunba/docs/master/image/for_support/for_knowledgebase/provision_all.png)

2. 点击右上角的新建按钮，选择 Profile 的类型，并点击 Continue 按钮。

	![provision_type.png](https://raw.githubusercontent.com/yunba/docs/master/image/for_support/for_knowledgebase/provision_type.png)

3. 选择要创建 Provisioning Profile 的 App ID。

	![provision_app_id.png](https://raw.githubusercontent.com/yunba/docs/master/image/for_support/for_knowledgebase/provision_app_id.png)

4. 选择开发者证书。

	![provision_cert.png](https://raw.githubusercontent.com/yunba/docs/master/image/for_support/for_knowledgebase/provision_cert.png)

5. 选择允许哪些设备使用该 Provisioning Profile。一般情况下，全选即可。

	![provision_device.png](https://raw.githubusercontent.com/yunba/docs/master/image/for_support/for_knowledgebase/provision_device.png)

6. 为该 Provisioning Profile 命名，并点击 Generate，即可生成。

	![provision_name.png](https://raw.githubusercontent.com/yunba/docs/master/image/for_support/for_knowledgebase/provision_name.png)

7. 点击 Download，下载到本地。

	![provision_done.png](https://raw.githubusercontent.com/yunba/docs/master/image/for_support/for_knowledgebase/provision_done.png)

8. 双击 Provisioning Profile 文件，即可将其导入到 Xcode 中。




