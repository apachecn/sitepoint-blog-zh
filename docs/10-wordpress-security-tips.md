# 确保 WordPress 安装安全的 10 种方法

> 原文：<https://www.sitepoint.com/10-wordpress-security-tips/>

WordPress 是你可以安装在服务器上的最危险的网络软件之一。一个黑客可以通过访问你的 WordPress 管理员账户造成真正的破坏。相信我，我亲身经历过！虽然黑客或垃圾邮件发送者可以恶意删除或更改内容，但最坏的人不会。他们更有可能在你的内容中隐藏一些链接，或者在你的文件结构中放置钓鱼网站。只有当谷歌、贝宝或银行联系你时，你才会知道你的网站已经被入侵。更改密码也是不够的。黑客首先会安装一个文件浏览插件。这将使他们能够上传系统，创建额外的 WordPress 管理员账户，或者在你的网站上放置更多的钓鱼页面。

## 第一次安装？

如果你开始一个新的 WordPress 安装，有几个步骤可以从第一天开始提供额外的安全性。 **1。使用强大的 MySQL 数据库名称**我建议为每个 WordPress 安装使用一个新的数据库。仅仅因为 WordPress 可以在现有的数据库中安装表格，并不意味着你应该这样做！强 id 和密码是必不可少的:避免用用户 ID“user”和密码“password”来命名数据库“wordpress”您只可能设置一次，所以它们可以像您喜欢的那样复杂。如果忘记了，可以查看`wp-config.php`中的详细内容。最后，记得定期备份你的数据库。自动化这个过程，这样就不可能忘记。 **2。设置自定义表格前缀**默认情况下，WordPress 使用表格前缀`wp_`——例如`wp_posts`、`wp_users`等等。改变前缀，使其更难运行 SQL 注入查询和类似的攻击。

## 安装后安全性

成功安装后，您可以添加更多安全墙。 **3。锁定`wp-config.php`** 如前所述，`wp-config.php`包含关于您的安装的重要信息，只有您有权访问。根目录的`.htaccess`文件中的以下条目应该可以防止未经授权的复制:

```
<files wp-config.php>	order deny,allow	deny from all</files>
```

**4。避免使用默认的“admin”id**在版本 3 之前，WordPress 将第一个管理员帐户的 ID 设置为“admin”第 3 版安装程序给了你改变它的选项，但我怀疑许多从早期版本更新的人从来没有这样做过。如果黑客知道你的 ID 是“admin”，他们所要做的就是猜测密码。很容易定义一个新的管理员帐户，然后从 Users 部分删除“admin”。记住也要使用强密码。 **5。限制对你的 IP 地址的访问**如果只有几个 WordPress 用户访问管理面板，你可以限制对这些 IP 地址的访问。如果您使用的是 Apache，用下面的代码在`wp-admin`中创建一个`.htaccess`文件:

```
order deny, allowallow from 1.2.3.4 # user 1 IPallow from 5.6.7.8 # user 2 IP, etcdeny from all
```

可以为 IIS 配置类似的条件。请注意，如果您有动态 IP 地址，则不能使用此方法；但是，如果有必要，您可以实现额外级别的基本服务器密码验证。 **6。从你的主题中删除 WordPress 的引用**黑客只有在知道你在使用 WordPress 的情况下才会试图入侵它。您可以删除主题文件中对 CMS 及其版本号的所有引用；例如，大多数`header.php`文件包含如下代码:

```
<meta name="generator" content="WordPress" />
```

删除这些引用将无法阻止一个顽固的黑客，但它会使脚本小子不那么明显。 **7。定期更新**如果你还在使用 WordPress 1.5，也许是时候升级了。较新的版本修复了许多公开的漏洞，通过使用最新版本，你知道黑客有更少的时间来磨练他们的技能。 **8。安装好的安全插件…**WordPress 网站有一个[安全插件](http://wordpress.org/extend/plugins/tags/security)的集合，里面有其他用户有用的描述和评论。WP 安全扫描插件扫描你的 WordPress 安装的漏洞并给出安全修复建议。 **9。…但是要小心你安装的东西**网络上充斥着伟大的和不那么伟大的免费 WordPress 插件和主题。它们中的任何一个都可能包含恶意代码，将驱动密钥交给您的安装。在将代码部署到您的实时服务器之前，请小心，并且尽可能在本地测试代码。

## 10.我被黑了！我该怎么办？

如果您收到了一封提醒您网站上存在网络钓鱼内容的电子邮件，我建议您采取以下行动:

1.  更改所有站点密码，如 FTP、cPanel、SSH 等。
2.  如果你没有最近的数据库备份，将你的文章导出到 WordPress XML 文件中。仔细检查任何 PHP 代码或意外的内容。
3.  记下您正在使用的任何插件或设置。
4.  如果绝对有必要，下载资源文件，如图像或视频，但检查每一个，以确保它是你所期望的。
5.  从你的网站上删除你的文件和数据库。这是所有的文件夹，不仅仅是 WordPress 文件夹。
6.  返回本页顶部，重新安装。

我打赌你希望你早点保护好 WordPress！有什么提高 WordPress 安全性的小技巧吗？你有什么黑客恐吓故事吗？

**note:**Want more?

如果你想阅读更多 Craig 的文章，请订阅我们每周的科技极客时事通讯， *[《科技时报》](https://www.sitepoint.com/newsletter/)* 。

## 分享这篇文章