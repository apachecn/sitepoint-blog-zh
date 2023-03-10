# WordPress 4.2 的新功能

> 原文：<https://www.sitepoint.com/whats-new-in-wordpress-4-2/>

*这篇文章已经过时了，[点击](https://www.sitepoint.com/whats-new-in-wordpress-4-6/)查看 WordPress 4.6 的新功能。*

WordPress 4.2 (Powell)刚刚登陆，它为用户和开发者带来了很多好处。在 10up 的 Drew Jaynes 的带领下，由 283 名贡献者组成的团队为我们带来了几十个新功能，并修复了总共 231 个错误。

WordPress 4.2 版本被命名为 Powell，以爵士音乐家巴德·鲍威尔的名字命名。

![WordPress 4.2 Upgrade](img/c1e05bb739b98bb172d6f55de8bf3888.png)

下面是这个最新版本的概述。

## 如何获得 WordPress 4.2？

从今天开始，一旦你登录到你的 WordPress 仪表盘，你应该会收到以下消息:

![WordPress 4.2 Update Message](img/36392fe1640ec2c093049e6cc94bed8c.png)

点击该链接会将您带到“更新”页面，在那里您会看到一个友好的提醒，首先备份您的数据库和文件。WordPress 的维护需要定期进行以保持良好的状态，所以希望你已经解决了这个问题——如果没有，查看我们的[WordPress 维护权威指南](https://www.sitepoint.com/definitive-guide-to-wordpress-maintenance/)开始吧。

我已经在几十个网站上运行了更新，没有任何问题，而且我总是发现 WordPress 核心更新总是很可靠。通常是你的主题和插件需要你密切关注，并确保你已经在你的开发或测试环境中测试了你的更新。这也是一个很好的提醒，为什么你应该只使用积极开发的高质量的主题和插件。高质量的主题和插件现在应该已经发布了发行说明，所以去看看吧。

## 新的和改进的“按这个”

Press 这并不是新功能，但它已经通过一些新功能得到了更新，包括向您的媒体库添加资产，这是一个很好的时间节省器。如果你发布了很多内容，还没有查看“按这个”，现在是时候看看了。你可以在“工具”菜单下找到 Press This。

![WordPress 4.2 Press This Feature](img/91ed654bd1a59525a0a671bff073cc5d.png)

## 在定制器中更改主题

作为移动到先前改进的定制器面板的一部分，您现在也可以在那里更改主题。如果你仍然在仪表板的“主题”区域配置你的主题，老派，这里有另一个理由来看看定制器。

![WordPress 4.2 Theme Switcher](img/4f3cc20d071b3715f657c941043a79eb.png)

## 本地中文、日文、韩文字符和符号支持

WordPress 数据库表已经从使用 utf8 变成了 utf8mb4，这意味着 WordPress 可以使用 4 字节字符而不是 3 字节。4 字节字符让 WordPress 现在可以存储任何 Unicode 字符，这为更多的字符和符号提供了支持，包括表情符号。

对更多字符的原生支持意味着你也可以在任何地方使用流行的表情符号 emoji。我说的任何地方，就是任何地方！Brian Krogsgard 在他最近的帖子中展示了他对 WordPress 4.2 的热爱。

![WordPress 4.2 Emoji](img/417f9cb7745aaf9743b6107b95955e39.png)

虽然表情符号似乎吸引了最多的关注，但 WordPress 原生支持中文、日文和韩文字符是 4.2 最令人兴奋的功能之一，因为它展示了 WordPress 团队的承诺，即保持一个包容性的平台，供全球所有人使用。

你可以在这里阅读更多关于[数据库变化的信息。](https://make.wordpress.org/core/2015/04/02/the-utf8mb4-upgrade/)

## 更好的插件更新体验

插件对于大多数网站来说都是必不可少的组件，所以这意味着我们需要不断地更新它们。WordPress 4.2 给我们带来了一个新的插件更新体验，你不会离开插件更新页面。这意味着更少的点击，这总是一件好事。

![WordPress-4.2-Update-Plugins](img/7a80a0ac4772a0588bc1bc124819b70e.png)

## Tumblr 和 Kickstarter 嵌入式支持

WordPress 4.1 给了我们更多对 oEmbed 的支持，4.2 现在增加了 Tumblr 和 Kickstarter。内容编辑器中的 oEmbed 提供了一种通过标准 API 包含(嵌入)资源(如照片或视频)的简单方法。你可以点击此处的 oEmbed 阅读更多[。](http://www.oembed.com/)

![WordPress 4.2 oEmbed](img/41159f3999fe371341012a15c552134d.png)

从表面上看，这似乎没什么大不了的，但这是一个我经常看到网络出版新手立刻爱上的特性。它为非开发人员提供了一种简单的方法来包含外部内容，而不用担心 HTML。

## 默认永久链接

设置永久链接通常是你在建立一个新网站时首先要做的事情之一，所以现在默认情况下永久链接是打开的，这是一个好消息。当然，如果需要的话，你可以把它们关掉。

永久链接，也称为漂亮的网址，漂亮的链接，搜索友好的网址，把这样的东西:

`https://www.sitepoint.com/?p=12345` 

变成这样:

`https://www.sitepoint.com/something-meaningful/`

同样，这是一个很小的变化，但可以节省几次点击。

## 借助 wp.a11y.speak 让 WordPress 说话

4.2 中一个很好的补充就是对 wp.a11y.speak()所做的工作。这使得 JavaScript 生成的消息可以被屏幕阅读器读取。基于 JavaScript 的消息会导致可访问性问题，所以这是一个受插件和主题开发者欢迎的特性，他们也想发送文本和声音通知。

你可以阅读[更多关于 wp.a11y.speak 这里](https://make.wordpress.org/accessibility/2015/04/15/let-wordpress-speak-new-in-wordpress-4-2/)。

## 更多开发者改进

我建议查看一下 Aaron Jorbin 的现场笔记，它涵盖了开发人员应该知道的一系列事情，但这里是他的帖子的摘要。

*   捆绑的 jQuery UI 和 jQuery 版本升级
*   较小的管理配色方案更改
*   分类术语拆分([更多详细信息](https://make.wordpress.org/core/2015/02/16/taxonomy-term-splitting-in-4-2-a-developer-guide/)
*   实验 TinyMCE 视图 API
*   更简单复杂的查询排序([更多细节](https://make.wordpress.org/core/2015/03/30/query-improvements-in-wp-4-2-orderby-and-meta_query/)

## 更多 WordPress 4.2 新闻

如果你正在寻找更多的关于 WordPress 4.2 版本的报道，看看下面的网站，做进一步的阅读。

*   通过 WordPress.org
*   [WordPress 4.2“鲍威尔”现在可以通过 WP Tavern 下载](http://wptavern.com/wordpress-4-2-powell-is-now-available-for-download)
*   [通过 Make.WordPress.org 的 WordPress 4.2 现场笔记](https://make.wordpress.org/core/2015/04/23/wordpress-4-2-field-notes/)

感谢阅读，希望你的更新顺利(记得备份！).请在下面随意评论。

## 分享这篇文章