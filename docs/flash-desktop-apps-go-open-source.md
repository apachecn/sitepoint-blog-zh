# Flash 桌面应用程序开始开源

> 原文：<https://www.sitepoint.com/flash-desktop-apps-go-open-source/>

所有开发看起来像桌面应用程序的 Flash Web 应用程序的工作都回避了这样一个问题:“为什么不用 Flash 开发桌面应用程序？”Macromedia 试图用 Macromedia Central 来回答这个问题，Macromedia Central 渴望被开发人员采用，现在免费提供。

Screenweaver 是这个问题的另一个答案。它最初是一个用于创建基于 Flash 的屏幕保护程序的简单应用程序，后来发展成为基于 Flash 的桌面应用程序的集成开发环境(IDE)。Screenweaver 也是一个商业上的失败，但是一小群勇敢的开发者已经将它从二进制遗忘中拯救出来，继续作为一个开源项目进行开发。

[公告](https://secure.sourcesecure.co.uk/trac/osflash/screenweaver/wiki/SwOsAnnouncement)提供了 Screenweaver 起源的一点历史，以及一个名为 Screenweaver Core 的附带项目——一个在 Windows 桌面上的 Visual Basic、C++和 Python 等通用编程语言中使用 Flash 的库——它也正在复活。

如果你想玩 Screenweaver 3 的初始开源版本，点击项目的[主 wiki 页面](https://secure.sourcesecure.co.uk/trac/osflash/screenweaver/wiki)，点击下载，获取预编译的二进制文件。您可能还想访问文档的链接，该链接还没有包含在下载中。

开源工作的最初计划是增强 Screenweaver，以支持使用 Screenweaver 和其他操作系统组件构建的 Flash 应用程序之间的同步通信。即将发布的 Flash Player 8 包括对 ExternalInterface 的支持，这是一种新的 ActionScript API，允许 Flash 电影暂停并等待请求(如在浏览器中调用 JavaScript 函数)完成后再继续。

与之前支持的主机环境异步接口相比，同步通信管理起来要简单得多，像[达隆·沙尔](http://www.darronschall.com/)(screen weaver OS 项目的[发起人](http://www.darronschall.com/weblog/archives/000179.cfm))这样的开发人员相信，这种类型的通信将是让 Flash 桌面应用程序开发成为流行现实的关键。

## 分享这篇文章