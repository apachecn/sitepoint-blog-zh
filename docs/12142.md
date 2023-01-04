# 滚动你自己的推特克隆

> 原文：<https://www.sitepoint.com/roll-your-own-twitter-clone/>

![birdy](img/c7f30aab83c54d37d0637be58d236916.png "birdy") **大家都在说话[叽叽喳喳，](http://twitter.com)对吧？还有 [Plurk、](http://plurk.com) [ Jaiku、](http://jaiku.com)和[Identica](http://identi.ca)——事实上，每周都有数十种新的微博服务涌现。**

如果你和我一样，你可能已经想知道制作自己的微博有多简单了——也许你想为你和你的同事们建立一个微博来分享链接和进行讨论。或者，也许你有一个围绕你最喜欢的话题的新社交网络的好主意。

无论你有什么计划，好消息是，使用几个免费工具中的任何一个，你都可以很容易地拥有一个自己的微博。今天，我们将看看三个新的解决方案，它们易于设置，具有适度的主机需求，甚至包括一些让 Twitter 大吃一惊的功能。

## WordPress 的 P2

上个月发布了 [P2、](http://en.blog.wordpress.com/2009/03/11/p2-the-new-prologue/)一个与[WordPress.com](http://wordpress.com)一起使用的特殊主题和自主安装的 [WordPress](http://wordpress.org) 设备，使得一个普通的博客看起来和行为更像一个微博。这是去年年初发布的 Prologue 主题的升级，如果你熟悉 WordPress，你肯定会发现这很容易理解。

对于那些正在运行自己的 WordPress 安装程序的人来说，添加 P2 就像激活一个主题一样简单:只需将 P2 文件上传到你的 web 服务器上的适当位置，然后在 WordPress 管理面板中激活它。[从主题目录中获取](http://wordpress.org/extend/themes/p2)或通过 SVN 获取:

`svn checkout http://svn.automattic.com/wpcom-themes/p2/`

否则，你可以马上在免费的 WordPress.com 博客上试试 P2。

P2 有许多 Ajax 增强功能，使得浏览和发布速度极快。提供键盘快捷键，让浏览 P2 微博像打字一样快；无需刷新页面即可添加和编辑帖子和评论；甚至还有一个提醒通知区，让你立即知道自从你第一次打开页面以来是否有新的内容出现。如果你正在寻找类似 Twitter 的功能，比如将一个项目标记为收藏或者与其他用户成为朋友的能力，这些功能在默认情况下是不可用的，但是 WordPress 的各种插件可以帮助你添加这些功能。新用户可以马上成为博客作者，所以如果你的目标是创建一个新的免费的微博，你可以通过 P2 来实现。

WordPress 的安装要求非常简单，应该可以在大多数网络主机上使用:你只需要 PHP 和 MySQL。WordPress 和 P2 是免费的。

## 移动式动作

上个月刚刚披露， [Motion](http://movabletype.org/motion) 是博客平台[mobile Type Pro、](http://movabletype.com)的扩展，它是社交聚合器和微博服务的有趣结合。

安装 Motion 相当简单——只需安装 Movable Type，创建一个新博客，当向导询问您希望创建的博客类型时，使用 Motion 设置。

访问者可以直接通过你的可移动类型实例来注册这项服务，或者通过使用他们的账户，通过其他一些认证方法，如[谷歌账户、](http://google.com/accounts) [【脸书连接】、](http://developers.facebook.com/connect.php)或 [OpenID。与上面的 WordPress 不同，新注册的用户只能在博客上发表评论——但是如果需要的话，博客管理员可以很容易地提升该用户的全部发帖权；或者，他们可以选择为每个注册用户创建一个新的独立的 Motion 博客。](http://openid.net)

Motion 的一个有趣的功能是能够聚合网络上其他位置的社交活动。作者可以在其他社交媒体服务上发布帖子，比如他们的 Twitter 流、RSS 源或他们的 [Delicious](http://delicious.com) 书签——还有几十个其他网站可供选择。这些行为成为每个作者个人页面的一部分，连同他们对你自己博客的贡献，也可以添加到你博客的主页上。

可移动类型的安装需求很容易满足——您需要 Apache 或 IIS web 服务器、PostgreSQL 或 MySQL 数据库，以及使用 Perl 的能力。成本取决于您的可移动类型 Pro 许可证，这取决于您的组织-非营利组织、教育机构和个人可以免费使用它，但盈利组织需要支付现金。你可以在[活字下载页面找到详细信息。](http://www.movabletype.com/download)

## 拉科尼卡

如果你正在寻找一个最像 Twitter 的解决方案， [Laconica](http://laconi.ca) 可能是你的选择。Laconica 是支持 [Identica](http://identi.ca) 和 [TWiT Army](http://army.twit.tv/) 的平台，它包含许多有趣的功能，如群组、即时消息、通过电子邮件发帖，以及从其他 Laconica 设备发送和接收消息的能力。桌面客户端已经可以使用了，如果你需要的话，还有一个 API 可以使用。

Laconica 的基于 web 的安装向导只需花几分钟就可以初始化您的数据库并设置您的服务；一旦完成，你就可以马上开始微博了。但是要开始利用 Laconica 的一些更有趣的特性，比如通过即时消息或 SMS 支持来发帖，您将需要使用一个配置文件。幸运的是，配置指令在 README 文件[中有很好的记录，并且有一个示例配置文件向您展示它是如何完成的。](http://gitorious.org/projects/laconica/repos/mainline/blobs/master/README)

调整外观也有点麻烦:Laconica 只支持基于 CSS 的主题，目前还没有计划允许用户在不编辑应用程序本身的情况下编辑 Laconica 生成的 HTML。幸运的是，标记中充满了 id 和类，您可以用它们来施展 CSS 魔法。

Laconica 的安装要求相当适中:您需要一个运行 Apache、PHP 5.2 或更高版本、MySQL 5.x 的 web 服务器，以及一些额外的 PEAR 模块——系统要求的完整列表可以在下载时附带的 README 文件中找到。更重要的是，它是开源的——黑客攻击！

## 结论

我们只看了三个微博应用，每个都有不同的优缺点。你选择的平台真的取决于你的目标——你是为你和你的朋友或同事创建一个网站，还是打算创建一个 Twitter 杀手？无论你的目标是什么，到现在为止，你都有希望知道从哪里开始。

## 分享这篇文章