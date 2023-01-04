# 用 Buster 创建一个静态的 Ghost 博客

> 原文：<https://www.sitepoint.com/create-static-ghost-blog-buster/>

如果我们是在 2000 年代中期，开始博客的一致选择会是 [WordPress](http://wordpress.com) 或 [Blogger](http://www.blogger.com) 。过去几年里，博客平台的数量不断增加，这对于喜欢尝试的人来说是一个绝佳的时机。去年年底向公众发布的一个这样的选项是[幽灵](https://ghost.org/)。

Ghost 最棒的地方是支持 Markdown——让你专注于写作。不过，如果你还是持怀疑态度的话，你应该看看[大卫·布雷恩(David Blane)对 Ghost 的特征与三巨头](https://www.sitepoint.com/comparing-ghost-to-the-big-three/)的对比。

如果你同意你应该给幽灵一个尝试，有一个小问题。如果你想使用 Ghost.org 托管你的博客，基本计划[每月 5 美元](https://ghost.org/pricing/)起，限制你 10，000 次页面浏览(如果你的帖子在黑客新闻上表现良好，你可以很容易达到)。如果你下载源代码，你需要把它放在一个单独的服务器上，这需要一些专业知识。

但是我们可以克服这些问题。在这篇文章中，我将向你展示一个简单的方法来为你的博客使用 Ghost，并且免费托管它。

## 什么是静态网站？

静态站点是一组 HTML 文档或静态网页，不多也不少。这意味着无论用户请求的是哪个站点，内容都将保持不变。相反，对于一个动态站点，同一个 URL 可能向不同的用户显示不同的内容(例如，`https://twitter.com`向我显示的内容与它向你显示的完全不同，假设我们中的一个已经登录)。

与动态网站相比，静态网站的托管费用通常较低或免费。但是，要生成和更新静态站点，您需要一些特定的技能。当然，除非你有[巴斯特](https://github.com/axitkhurana/buster)。

## 巴斯特是什么？

[巴斯特](https://github.com/axitkhurana/buster)是一个“蛮力静态站点发生器供幽灵使用”([明白了吗？](http://www.imdb.com/title/tt0087332/))。Buster 允许您预览生成的静态博客，并部署到 GitHub 来托管您的站点。它是用 Python 写的，很容易安装。你可以通过 [pip](https://pypi.python.org/pypi/pip) 下载或者克隆库并安装。

我们将遵循的流程是这样的:

*   使用暴力从幽灵站点生成静态页面
*   使用 Buster 预览它们(可选)
*   部署到我们的服务器(像 GitHub 页面)

## 使用 GitHub 页面托管您的静态网站

GitHub 让你通过 [GitHub Pages](https://pages.github.com/) 免费托管你的静态页面。您在 GitHub 上的任何存储库都可以配置为在 web 上提供预览。您需要做的就是创建一个名为`gh-pages`的分支，您的存储库可以在`username.github.io/repository-name`访问，在这里您可以用它们各自的值替换`username`和`repository-name`。

GitHub 页面有很多替代品，其中之一就是 [My。drop ages](http://my.droppages.com/)一个非官方的 Dropbox 应用。尽管 Buster 被配置为推送 GitHub 页面，但是您也可以只生成静态站点，然后手动上传到其他地方。在本帖中，我们将继续假设您将把您的网站上传到 GitHub Pages。

## 向 GitHub 页面添加自定义子域

如果您需要一个域指向您的 GitHub Pages 存储库，您需要在您的域的 DNS 文件中添加一个 [CNAME 记录](http://en.wikipedia.org/wiki/CNAME_record)。对于这样的博客，您需要为所需的子域添加一个值为`username.github.io`的 CNAME 记录。

![Subdomain example](img/53d2d98672a2ca8c9cdf1fbadd9de316.png)

接下来，您需要将一个名为 CNAME 的文件添加到您的存储库中。该文件的内容只是字符串`my_subdomain.my_domain.com`(再次用您实际的子域和域替换这些值)。

## 如何使用巴斯特

各种命令如下，可以大致按时间顺序使用:

*   `buster setup [--gh-repo=<repo-url>]` —创建一个`static/`目录，并在其中初始化一个 Git 存储库。您应该在 GitHub 上建立了一个基本的存储库。
*   `buster generate [--domain=<local-address>]` —从本地运行实例生成页面(或覆盖现有页面)。本地地址一般是`127.0.0.1:2368`。
*   `buster preview` —在`localhost:9000`预览本地静态站点。
*   `buster add-domain [domain-name]` —将自定义域或子域添加到存储库中的 CNAME。
*   `buster deploy` —将您的更改推送到 GitHub。这是通过`https`协议完成的，你需要输入你的 GitHub 用户名和密码。

*注意:您应该在`static`目录之外运行这些命令。如果您在 Git 管理的不同目录中运行它们，`generate`命令将创建一个静态文件夹，并提交您现有存储库中的文件。*

## 向静态页面添加注释

静态网站的一个缺点是不能添加评论。然而，像 [Disqus](https://www.disqus.com/) 这样的第三方服务只需要你插入一个`<script>`标签，就可以得到一个评论框。Disqus 识别生成请求的 URL，并自动将注释保存到相关线程中。

要在您的静态站点上插入 Disqus 评论系统，您需要将它插入 Ghost 模板，静态站点就是从这个模板生成的。一旦你注册了 Disqus，你会得到一个代码片段插入到你的网站上。

在 Ghost 的主题目录下找到文件`post.hbs`(通常是`/content/themes/[theme_name]/post.hbs`)并粘贴到`{{/post}}`和`</article>`之间。要了解更多信息，您可以查看 Disqus 上关于其在 Ghost 站点上安装的详细帮助页面。如果你做得正确，Disqus 应该会出现在你的本地站点上，Buster 会自动提取它。

## 最后的话

我想用一个演示来结束这篇文章，[一个我维护的博客](http://gsoc.theblogbowl.in/)来报告我在谷歌代码之夏的每周进展。巴斯特[生成的静态站点在 GitHub](https://github.com/sdaityari/blog) 上有。

你觉得巴斯特怎么样？你知道有其他选择吗？请在下面的评论中告诉我们。

## 分享这篇文章