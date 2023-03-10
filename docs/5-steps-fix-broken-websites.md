# 我的网站坏了:确定问题的 5 个步骤

> 原文：<https://www.sitepoint.com/5-steps-fix-broken-websites/>

我们都经历过。你出色的网站已经成功运行了几个月，然后——嘭——它消失了。或者，更常见的是，某些功能停止工作。尽管你抗议说一切都没有改变，但你的客户并不高兴。为几个小时令人沮丧的问题探查做好准备。

## 步骤 1:识别问题

这听起来可能是显而易见的，但我知道许多开发人员打开他们的 IDE，开始随意编写代码。在此阶段，确定问题比确定原因更重要。该网站不可用吗？特定页面或功能是否失败？是否仅限于特定的浏览器？

## 步骤 2:测试资源可用性

十有八九，这个问题是由你或客户的连接问题引起的。如果你无法访问任何其他页面，你的网站消失了也就不足为奇了。也就是说，这并不总是显而易见的；某些 IP 范围、国家或部分互联网可能会被暂时封锁。

从多个位置测试您的站点——公共代理服务器将有助于确定是本地问题还是全球问题。如果可能，检查从同一服务器或 web 主机运行的其他站点的状态。

一个不太明显的问题是磁盘空间。如果您运行的是一个繁忙的站点，即使应用程序的存储需求很低，服务器日志也会快速使用可用空间。

请记住，您可能正在使用其他服务器上的资源。这包括 CDN 托管的文件、数据库服务器或远程托管的 API，如 Google Maps、YouTube、Twitter、广告服务等。

您还应该检查您的服务器负载。流量激增或拒绝服务攻击会导致访问问题。

最后，您的域名注册是否有效，DNS 服务器的响应是否正常？

## 第三步:确定什么发生了变化

一旦您拒绝了连接、流量、DNS 和磁盘空间，就该确定什么发生了变化。1000 次中有 999 次问题是由更新引起的。

你可能没有动过这些文件，但你确定其他人没有吗？询问每个有权限但不一定相信他们的人。以下是你会遇到的典型对话…

**客户端:**我的网站无法运行。你打算怎么办？
**你:**我来搞定。你最近有什么变化吗？
**客户:**没有，我来的时候就这样了。
*……五个小时的疯狂调查……*
**你:**你换了 X，不是吗？
**客户端:** X？哦，是的，我改变了 x，当我摆弄 Y 和 z 的时候。

您的应用程序可能不是直接的原因。您的 web 主机是否更新了操作系统、语言运行时、数据库软件或文件权限？而厂商则试图确保 PHP、Ruby、Python、MySQL、PostgreSQL 等。保持向后兼容，功能几乎肯定会在版本之间改变或中断。

## 第四步:拒绝边缘案例

虽然很少，但你应该寻找破裂的迹象。像 WordPress、Joomla 和 OScommerce 这样的软件是显而易见的目标，然而，由于黑客想要保留访问权限，改变往往是微妙的。例如，您可能会发现安装了一个文件资源管理器附件，或者文件结构深处出现了网络钓鱼页面。

最后，你永远不要排除硬件问题。损坏的内存芯片或磁盘扇区可能是许多奇怪问题的原因。如果可能，在类似的设置上评估您的应用程序，或者在同一服务器上安装单独的测试版本。

## 第五步:修复你的应用

一旦你排除了不可能，无论剩下的是什么，不管多么不可思议，都一定是事实。也许你的代码没有你想象的那么完美…

有什么诊断网站或应用问题的小技巧吗？你遇到的最困难的问题是什么？

## 分享这篇文章