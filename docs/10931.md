# 如何禁用谷歌浏览器更新

> 原文：<https://www.sitepoint.com/how-to-disable-google-chrome-updates/>

谷歌 Chrome 的自动更新是一把双刃剑。从好的方面来看，它确保了用户拥有最新版本的浏览器:Chrome 4 更新在几周内就向所有人推出了。然而，许多企业采取更加谨慎的方法。很少有人愿意允许大规模未经测试的软件更新，这可能会导致 it 支持问题或内部网应用程序故障。IE6 在商业世界的坚持证明了许多公司更喜欢不稳定浏览器的已知风险，而不是闪亮的现代浏览器的未知风险。转而使用 Chrome 的公司可能会制定企业范围的更新政策。Google 为微软 Windows 组策略编辑器提供了一个管理模板。让我们假设你正在为一家在工作场所使用 Chrome 4 的公司开发一个 web 应用程序。你最不需要的就是 Chrome 在开发过程中神奇地更新到版本 5。幸运的是，可以在所有操作系统上禁用自动更新。

## Windows 操作系统

如果您与公司网络断开连接并使用 Windows XP/Vista Home edition，组策略编辑器可能不一定可用。因此，您必须修补注册表…

**Warning:**Abandon hope all ye who enter here.

登记处是个危险的地方。你知道规矩——无论是我还是 SitePoint 都不会对你的电脑或你的精神状态的损害承担任何责任！

1.  定位`HKEY_LOCAL_MACHINESOFTWAREPoliciesGoogleUpdate`
2.  添加一个名为 **`DisableAutoUpdateChecksCheckboxValue`** 的新 DWORD 值，并将其设置为`1`以禁用自动更新，或者设置为`0`以重新启用自动更新。
3.  添加一个名为 **`AutoUpdateCheckPeriodMinutes`** 的新 DWORD 值并设置分钟数；例如，`1440`代表一天一次，`10080`代表一周一次，等等。

## mac 操作系统

在终端应用程序中输入以下命令。要禁用自动更新:

```
$ defaults write com.google.Keystone.Agent checkInterval 0
```

将`0`更改为`1`以重新启用更新。要设置更新频率:

```
$ defaults write com.google.Keystone.Agent checkInterval <frequency>
```

其中`<frequency>`是以秒为单位的间隔；例如，`86400`代表一天一次，`604800`代表一周一次，等等。

## Linux 操作系统

Chrome 的更新是通过标准的包管理系统进行的，所以在点击“是”自动更新通知时要小心。

## 分享这篇文章