# Webmin 的杰米·卡梅隆的问答

> 原文：<https://www.sitepoint.com/q-and-a-with-jamie-cameron-of-webmin/>

Webmin 最初是作为商业软件产品发布的，早期版本是在 2000 年之前作为测试版发布的。就在那时，Caldera 赞助了 Webmin 的开发，此后它就在免费许可下发布了。

在我的关于使用 Webmin 进行 Linux 管理的两部分系列文章中，我还与 Jamie Cameron 交换了一封电子邮件，他是 Webmin 项目的发起人和首席开发人员。

BW:你统计过 Sourceforge 网站的下载量吗？你的每周下载率是多少？

JC:是的，Sourceforge 有下载统计。事实上，它们是从
[【http://www.sourceforge.net/projects/webadmin】](http://www.sourceforge.net/projects/webadmin)联系起来的。上次我检查的时候，平均每天大约有 5000 次下载。

BW:Webmin 的下一步是什么？你想分享什么路线图信息吗？您希望看到开发者在哪些特定领域做出贡献？

JC:我目前最大的目标是:

*   为目前不被 Webmin 支持的服务器或服务添加新模块，如 vsftp 和 INN。
*   跟上所有新发布的 Linux 发行版，以便 Webmin 保持与它们的兼容性。
*   重新分解和重新编写一些现有的模块，以使用新的通用 UI 生成代码，而不是直接打印 HTML。这将提供一个更加一致的用户界面，简化代码，并且所有的主题对模块的外观有更多的控制。
*   扩展我的虚拟主机模块，以支持新的功能，如虚拟管家邮件列表托管，每个用户多个域，经销商访问等。

BW:你能更详细地解释一下如何从一个 Webmin 界面管理几个服务器吗？

JC: Webmin 有自己的 RPC 机制，允许一台服务器调用
函数，并在另一台运行 Webmin 的服务器上运行命令。有几个模块利用这个特性，通过在每台服务器上调用用户创建函数来一次在多台主机上创建 T2 Unix 用户。

## 分享这篇文章