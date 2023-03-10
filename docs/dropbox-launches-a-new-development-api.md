# Dropbox 推出新的开发 API

> 原文：<https://www.sitepoint.com/dropbox-launches-a-new-development-api/>

如果你没有安装 [Dropbox](http://www.dropbox.com/) 你不知道你错过了什么。我想不出有哪家初创公司对开发者和用户的生活产生了更大的积极影响。据[报道【Dropbox 甚至拒绝了苹果九位数的收购报价。](http://www.pcmag.com/article2/0,2817,2394896,00.asp#fbid=jKq1fQEA86v)

Dropbox 是一个小应用程序，它可以监控一个文件夹，并在网络上来回传输其内容。如果您在多台电脑或移动设备上运行 Dropbox 文件夹，它会自动共享。第一个 2GB 是免费的，但对于那些需要更多空间的用户，有[定价计划](http://www.dropbox.com/pricing)。好处包括:

*   文件会在您的所有设备上自动同步。这通常比使用标准网络文件传输更快。
*   所有文件都备份到云中。
*   保留以前的文件，因此可以获得文档的早期版本。
*   您可以与任意数量的 Dropbox 用户共享子文件夹。
*   整个体验简单、流畅、不张扬。

还有更多富有想象力的用途，如音乐共享、项目协作、便携式应用程序库，甚至还有一个网站内容管理系统。

然而，现在[公司发布了一个 API](http://blog.dropbox.com/?p=915) ，我们可能会看到 Dropbox 的替代用途激增。任何桌面或 web 应用程序都可以使用该 API，它包括以下关键特性:

1.  应用程序必须在 Dropbox 注册才能获得应用程序密钥。
2.  实现了 REST API，所有请求都必须通过 SSL 进行。
3.  OAuth 被用于用户认证和授权。
4.  应用程序可以搜索、下载和上传文件。修订系统是公开的，因此当两个或多个用户访问相同的文件时，可以取消删除、恢复到以前的版本或防止覆盖。
5.  出于安全原因，应用程序只能访问自己专用文件夹中的文件。你可以请求访问整个 Dropbox 文件夹，但你需要提供一个好的理由，并且你的系统将在发布前接受审查。
6.  iOS、Android、Python、Ruby、Java、C#和 PHP 的 [CodeIgniter 框架](http://codeigniter.com/)都有开发 SDK 和样本代码。

可能性是无穷的，我怀疑未来几个月将会出现一系列令人兴奋和真正有用的应用程序。至少，你可以使用 Dropbox 作为在线应用的数据存储，这样你的用户就可以随时访问他们的文件。如果你有什么有趣的计划，请告诉我们…

更多信息请参考优秀的 [Dropbox 开发者文档](https://www.dropbox.com/developers)。

## 分享这篇文章