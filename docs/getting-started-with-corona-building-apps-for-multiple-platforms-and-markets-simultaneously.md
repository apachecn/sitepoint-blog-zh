# Corona 入门:同时为多个平台和市场构建应用

> 原文：<https://www.sitepoint.com/getting-started-with-corona-building-apps-for-multiple-platforms-and-markets-simultaneously/>

如今应用程序风靡一时，许多开发者通过销售它们赚了很多钱。也许你想加入乐趣，兑现你的应用程序想法，但你不确定从哪里开始。应该为 Android 还是 iOS (iPhone、iPad、iTouch)开发？Nook 和 Kindle Fire 呢？

要为 Android(包括 Nook 和 Fire)开发，你需要学习 Android 架构、Java 语言、各种 Java API(Java 和面向 Android 的)以及 Android 专用工具。要为 iOS 开发，您需要学习 iOS 架构、Objective-C、各种 iOS APIs 和 iOS 专用工具。

突然，想到所有这些学习，你的眼睛变得呆滞。难道没有一种方法可以通过使用一种语言、一套 API 和一套适用于 Android 和 iOS 的开发工具来开发应用程序，从而最大限度地缩短学习曲线吗？还好，答案是肯定的。通过使用 Corona，您可以最小化多个平台的陡峭学习曲线。

这一系列文章将向您介绍 Corona。今天，我们将收到这项技术的简要概述，然后下载 Corona 试用版。然后，我们将在您的 Mac OS X 或 Windows XP(或更高版本)平台上安装该软件。在下一篇文章中，我们将探索 Corona，检查和修改一个示例应用程序项目。

### 什么是电晕？

*Corona* 是一个软件开发套件，用于编写多媒体丰富的高性能应用程序(无论是游戏应用程序、非游戏应用程序还是交互式电子书应用程序)，测试它们，并构建/打包它们以部署到 Android 或 iOS 平台。此 SDK 适用于 Mac OS X 和 Windows XP(或更高版本)。

* * *

**注意:**你可以通过 Mac OS X 版的 SDK 为 Android 和 iOS 开发一个应用。然而，由于 Apple 许可问题，您只能通过 Windows XP(或更高版本)为 Android 构建。

* * *

你用一种叫做 *Lua* (葡萄牙语中月亮的意思)的脚本语言编写一个应用程序，并利用强大的 API 让你快速编写应用程序。有许多 API 可用，包括那些通过应用内购买、横幅广告和基于报价的虚拟货币来帮助你将应用货币化的 API。

* * *

**注:** Corona 包括一个物理引擎，让你可以在游戏或其他应用程序中轻松处理物理现象(如重力)、检测碰撞等。

* * *

你可以通过 Corona 的模拟器(稍后讨论)预览 Android、iPhone、iPad 或 iTouch 的应用程序。可以在模拟器中快速查看更改，从而提高工作效率。当你准备好了，你可以点击一个按钮，为 Android 或 iOS(仅 Mac OS X SDK 版本)构建应用的分发文件。

* * *

**注意:** Corona 不会自动将你的应用发布到应用商店(如 iTunes 或谷歌 Android Market)。您必须手动执行此任务。

* * *

Corona 是总部位于加州帕洛阿尔托的移动软件公司 Ansca Mobile 的产品。联合创始人卡洛斯·伊卡萨和沃尔特·卢赫在 2007 年离开 Adobe 后创办了 Ansca Mobile。在 Adobe，他们开发 Flash Lite，这是一个用于移动设备的轻量级 Adobe Flash Player 版本。

### 下载 Corona 试用版

Ansca Mobile 提供 Corona 的试用版和订阅版。试用版是免费的，并且[无限长](http://blog.anscamobile.com/2010/12/happy-holidays-an-update-on-whats-coming-in-2011/)；订阅版本需要 349 美元的年费(注册 2 年或 3 年有折扣)。

我建议您下载试用版，在投资该软件之前，先了解 Corona 是否是满足您应用程序开发需求的合适工具。除了免费之外，试用版还提供了以下优势(订阅版也是如此):

*   在 Corona 模拟器中测试和预览桌面应用
*   为 Android 或 iPhone/iPad/iPod Touch(仅限 Mac OS X SDK)设备构建应用程序
*   获得对 Corona 所有简单而强大的 API 的无限制访问

* * *

**注意:**要查看试用版和订阅版的完整对比，请浏览 Ansca Mobile 的[购买 Corona](http://www.anscamobile.com/pricing/whysubscribe.html) 页面。

* * *

让我们下载试用版。首先将浏览器指向 Ansca Mobile 的主页，如图 1 所示。

![](img/b6d2186696dd6b7a029e59afed972659.png "figure1")

图 1:单击“Try Corona”链接开始获取 Corona SDK 试用版。

在窗口底部附近，您会看到一个 **Try Corona** 链接。单击该链接会将您带到图 2 所示的注册窗口。

![](img/c2e7577e9bb6f6099fd8048d1020ae64.png "figure2")

图 2:在下载 SDK 之前需要注册。

您必须注册才能下载 Corona SDK。输入您的电子邮件地址和密码，并表明您接受 SDK 的条款和条件，然后单击**继续**。图 3 显示了结果页面。

![](img/70638fcb3b23a85e39a3956c02df5ac1.png "figure3")

图 3:按照验证电子邮件中的说明完成注册。

Ansca Mobile 通过向之前输入的电子邮件地址发送验证电子邮件进行响应。按照此消息的说明(包括单击验证链接)完成注册，以便您可以获得网站的完全访问权限。(您无需回复此电子邮件即可下载 SDK。)

最后，通过单击相应的单选按钮选择您的操作系统(Mac OS X 或 Microsoft Windows)，并单击**下载**按钮。在撰写本文时，您可以下载 Windows 的`CoronaSDK-2011.704.msi`安装程序或 Mac OS X 的`CoronaSDK-2011.704a.dmg`安装程序。

* * *

**注意:**Mac OS X 版本的 SDK 需要 Mac OS X 10.6(或更高版本)、英特尔 Mac 开发平台。使用 Mac 版 SDK 创建的应用支持 iOS 3.1 或更高版本(包括 iOS 5)，并支持 Android OS 2.2 或更高版本(在 ARMv7 架构上)。Windows 版本的 SDK 需要运行速度为 1 GHz 或更高的 Windows XP(或更高版本)开发平台。使用 Windows 版 SDK 创建的应用支持 Android 2.2 或更高版本(在 ARMv7 架构上)。对于这两种 SDK，Corona 构建的应用程序都不会安装在 Android ARMv6 设备上。

* * *

我把`CoronaSDK-2011.704.msi`下载到我的 64 位 Windows 7 平台上。下载这个安装文件后，我验证了它的完整性，以确保它没有被篡改或以其他方式损坏，这是不太可能的。(你的杀毒软件可能不会检测到每一种可能性。)

查看图 3，您将看到与`CoronaSDK-2011.704.msi`安装程序文件相关联的下面一行:

```
md5: cc5909f812604b03bf3897b05510a7b6
```

这一行标识了文件的 MD5 签名，它表示为 32 个十六进制数字的序列。(MD5 代表消息摘要版本 5。这种算法通常用于检查数据完整性。访问维基百科的 [MD5](http://en.wikipedia.org/wiki/MD5) 条目，了解更多关于 MD5 的信息。)

要验证安装程序文件是否损坏，您必须获得一个程序来计算文件的 MD5 签名，将其与上面的签名进行比较，并输出文件是否正常的指示。一个这样的程序是 MD5 命令行消息摘要实用程序，位于[这里是](http://www.fourmilab.ch/md5/)。

该实用程序由一个`md5`可执行文件组成，该文件将预期签名和文件名作为参数，该文件的签名将被计算并与预期签名进行比较。`md5`当签名匹配时返回零(文件是正确的),否则返回非零值。考虑下面的例子:

```
md5 -ccc5909f812604b03bf3897b05510a7b6 CoronaSDK-2011.704.msi
```

您将在 Windows 批处理文件或等效的 Unix shell 脚本的上下文中运行`md5`。例如，清单 1 显示了运行`md5`的基于 Windows 的`check.bat`文件的内容。

```
@echo off
 md5 -ccc5909f812604b03bf3897b05510a7b6 CoronaSDK-2011.704.msi
rem Read md5's return code and transfer execution to label err if it's not 0.
if errorlevel 1 goto err
echo okay
goto end
:err
echo error
:end
```

**清单 1(如上):**用于测试`CoronaSDK-2011.704.msi`安装程序文件完整性的批处理文件

假设是 Windows 平台，当前目录包含`check.bat`和安装文件，在命令行中单独执行`check`。如果一切正常，您将看到输出为 **okay** 。否则，您会观察到**错误**。

### 安装 Corona 试用版

要安装 Corona，双击安装程序文件。在 Windows 平台上这样做将导致如图 4 所示的介绍性对话框。

![](img/1668572fb05f4219bcff88c334fe3622.png "figure4")

图 4: Corona 的安装向导引导您完成安装过程。

点击**下一个**按钮，您将看到如图 5 所示的许可协议对话框。

![](img/05cbe57f583bbd093ee2de24eba542d4.png "figure5")

图 5:在安装 SDK 之前，您必须同意许可协议。

阅读许可协议，如果同意，单击**我同意**单选按钮接受许可协议。然后点击**下一个**按钮，选择该单选按钮后该按钮被激活。图 6 显示了结果对话框。

![](img/9bc4f4c370bfa3738084b67dd3ada18b.png "figure6")

图 6:保留默认安装文件夹或选择另一个文件夹位置。

您可以保留默认安装文件夹或选择其他位置。点击**磁盘开销**按钮，确定目标磁盘上是否有足够的空间。点击**下一个**，你会看到图 7。

![](img/242b89268fd6899af405f37b8ae84aae.png "figure7")

图 7:单击“Back”更改安装文件夹和/或谁可以使用 Corona。

点击下一个的**开始安装。Corona 显示安装进度对话框，如图 8 所示。**

![](img/2035276255792ad59018267c117dffd4.png "figure8")

图 8:安装只需要几秒钟。

当安装完成时，安装程序会显示一个带有**关闭**按钮的完成对话框，单击该按钮可以终止安装程序。至此，Corona 已经安装在您的平台上。

我在我的 Windows 7 平台上安装了 Corona to `C:Program Files (x86)AnscaCorona SDK`。CoronaSDK 主目录包含几个文件和子目录，包括一个示例代码目录，该目录将 SDK 中包含的示例代码组织到各个子目录中。例如，Sample Code 的`GettingStarted`子目录组织了各种帮助您开始使用 Corona 的项目。

这应该足够让你和科罗娜一起工作了。下一次，我们将进一步探索 Corona，并在其中构建我们的第一个应用。

## 分享这篇文章