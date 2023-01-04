# VirtualBox 2.2.2 升级问题的解决方案

> 原文：<https://www.sitepoint.com/virtualbox-222-upgrade-problem/>

我是 Sun 的 VirtualBox 虚拟机软件的忠实粉丝。在我 4 月份的[审查后不久，Sun 发布了 2.2.2 版本。我总是热衷于保持最新，所以我下载了它，并启动了升级。它失败了。没有日志文件或错误报告，只有一个无用的*“安装提前结束”*消息。](https://www.sitepoint.com/virtualbox-review/)

似乎许多其他人也有同样的问题。幸运的是，我为那些升级到 v2.2.2 遇到问题的人找到了一个解决方案。

**1。修复安装**
从控制面板启动“程序和功能”(Vista)或“添加和删除程序”(XP)。单击 VirtualBox 并选择修复。

如果修复成功，您应该可以毫无问题地升级。否则，继续第 2 步…

**2。提取 VirtualBox 文件**
修复时会显示一条信息说找不到 VirtualBox 安装文件。如果出现这种情况，找到您的旧安装程序——如果您已经删除了它，请从 [VirtualBox 旧版本页面](http://www.virtualbox.org/wiki/Download_Old_Builds)再次下载它。

打开命令提示符(cmd.exe)和“cd”到安装程序所在的文件夹。使用"-extract "参数运行它，例如

```
 VirtualBox-2.2.0-45846-Win.exe -extract 
```

几分钟后，会出现一个对话框，提示 VirtualBox 文件已被复制，通常复制到用户(Vista)或文档和设置(XP)文件夹中的临时文件夹。记下文件夹的位置！

**3。完成**修复后
再次开始修复。当“缺少文件”对话框出现时，单击“浏览”并导航到步骤 2 中记录的文件夹。修复现在应该成功完成。

如果修复仍然失败，请尝试重新安装旧版本。如果正常的安装程序不工作，导航到上面的文件夹，您会发现两个文件:x86 和 AMD64 版本的 VirtualBox 安装程序。运行适合您系统的版本–如果出现错误，请尝试另一个版本！

**4。如果其他都失败…**
升级前卸载之前的版本。

希望您的升级将顺利进行，而不需要做任何这些！不管有什么安装问题，VirtualBox 仍然是一个伟大的产品。

参见: [VirtualBox 虚拟机软件:实践回顾](https://www.sitepoint.com/virtualbox-review/)

## 分享这篇文章