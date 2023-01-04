# 5 个用于备份和迁移的 WordPress 插件

> 原文：<https://www.sitepoint.com/5-wordpress-plugins-backups-migrations/>

![Hard drive](img/92c0984eda12032ef6a2041422ad5921.png)

*本文是与 [SiteGround](https://www.siteground.com/go/article-sp) 合作创作的系列文章的一部分。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

备份网站数据不再像以前那样复杂。所有的网站都需要一个备份计划，没有理由不准备一个——用现在可用的工具来处理真的很容易，尤其是在运行一个 WordPress 网站的时候。无论是大网站还是小网站，高流量网站还是没有很多访问者的网站——所有的网站都有一些工作，如果网站丢失或以某种方式损坏了，你不想再做这些工作。

在 WordPress 中，有许多插件和服务可以用来满足备份数据的需求。WordPress 的迁移工具经常携手并进的原因是，类似的工具通常可以用于两个目的，因为在某些情况下，迁移可以类似于备份网站，然后恢复它(尽管在另一个位置)。

你可以在 SitePoint 的课程[构建一个定制的 WordPress 主题](https://www.sitepoint.com/premium/courses/wordpress-theme-development-2931)中了解更多关于手动和轻松备份你的 WordPress 站点的信息。为了快速简单的介绍 WordPress 的安全性，看看我们的视频样本，来自[如何保护你的 WordPress 站点](https://www.sitepoint.com/premium/courses/how-to-secure-your-wordpress-site-2954)迷你课程，你可以在下面预览:

然而，如果你是使用 WordPress 插件备份而不是执行手动备份的爱好者，那么，废话不多说，这里有五个更好的 WordPress 备份和迁移工具:

## 1.一次 WP 迁移

[All in One WP 迁移](https://wordpress.org/plugins/all-in-one-wp-migration/)是 WordPress 网站的优秀迁移工具。这个插件是我个人最喜欢的 WordPress 网站迁移工具，到目前为止，它每次都能顺利运行。当我第一次将一个站点从开发环境迁移到生产环境，或者从私有环境迁移到公共环境时，我倾向于使用迁移工具，不管长期解决方案是否需要更复杂的部署过程。

All in One WP 迁移可以完美地与各种各样的 WordPress 站点兼容——托管平台、插件和主题似乎一点都不重要。上传到新平台是使用大块数据完成的，这样可以绕过一些服务器上的上传限制。

这个过程非常简单。一旦插件安装并激活，你只需从工具条中选择 All-inOne WP 迁移，然后“导出”。然后选择“导出到”-导出到文件(下载文件)，或通过以下可连接的服务之一导出:

*   文件传送协议
*   Dropbox
*   Google Drive
*   亚马逊 S3
*   OneDrive
*   箱子

此外，您可以指定查找-替换字段。这在将一个站点从一个域转移到另一个域时很有用(比如将一个 WordPress 站点从 dev.example.com 转移到 www.example.com)。这将允许插件在导出时用新文本替换旧文本，为无缝导入新环境准备数据。

导入是一个类似的过程。在新环境中，安装 WordPress，添加并激活同一个插件，然后选择 Import。按照提示从旧环境中导入导出的数据，很快您就会看到该站点的迁移镜像副本！

## 2.UpdraftPlus

[updraft plus WordPress Backup](https://wordpress.org/plugins/updraftplus/)是一款易于使用的 WordPress 网站备份解决方案。UpdraftPlus 可用于手动备份您的站点，也可以设置为特定的时间表以允许自动备份。除了能够备份到 WordPress 站点已经驻留的主机平台上，该插件还支持以下服务作为备份目的地:

*   亚马逊 S3
*   Dropbox
*   Google Drive
*   Rackspace
*   FTP + SFTP
*   …以及更多

此外，付费版本的 UpdraftPlus 包含了更多的选择。UpdraftPlus 是评级最高、使用最多的 WordPress backip 解决方案之一，它很难出错。

支持站点文件、插件、主题和 WordPress 数据库的备份，可以按不同的时间表备份，也可以一起备份。此外，UpdraftPlus 也可以用作迁移工具，尽管如果需要迁移而不是备份，一些更专用的迁移工具可能会更简单或更流畅。

从备份中恢复只需要设置插件并导入备份数据集。测试备份总是好的，所以偶尔启动一个测试 WordPress 站点并导入一个备份是值得的，以确保所有备份都在工作——使用 Updraft Plus，应该是这样！

## 3.复印机

[Duplicator](https://wordpress.org/plugins/duplicator/) 宣称自己是迁移和备份领域的一员。Duplicator 将 WordPress 站点备份到一个单独的 zip 文件中，包括插件、主题和数据库。在免费版本中，这可以手动完成。

作为一个迁移工具，或者当从备份中恢复时，Duplicator 与其他插件有些不同，因为它不需要现有的 WordPress 安装。相反，您可以将备份文件上传到有问题的服务器上，运行安装程序文件，站点文件就会为您创建。

Duplicator 的另一个有趣的特性是能够使用 Duplicator 包作为预先配置的站点。如果你经常为客户建立新的 WordPress 站点，并且通常从相同的插件、设置和主题开始，你可以建立一个客户项目的开发版本，没有那个项目的具体信息，然后保存备份包。

现在，当您开始一个新项目时，您只需启动安装程序并安装新的项目模板，更改一些项目，如网站 URL，然后就可以快速开始了。当你有另一个新项目时，你可以再次重复这个过程，在这个过程中节省大量时间。

## 4.VaultPress

VaultPress 是一个 WordPress 备份插件，它的独特之处有两个。首先，它是由 Automattic 制造的。这是一个*自动*的好处(虽然不好的双关语！)因为，当然，如果有人知道 WordPress 插件和支持的话，那一定是运营 WordPress.com 的公司！

关于 VaultPress 的第二个有趣的事实是，它现在被打包成另一个令人敬畏的功能 [Jetpack](https://jetpack.com/) ，许多 WordPress 网站已经在使用它。

所以，关于定价，你可以参考 VaultPress/Jetpack 上的定价页面，但你看到的是每年 39 美元或更高，所以对于 VaultPress 提供的所有功能来说还不错，当你包括所有 Jetpack 时，绝对是便宜的。

VaultPress 会备份一切。文件、页面、帖子、评论、数据库。它无所不能。再加上上述好处，如果您愿意为这项服务支付少量费用，使用 VaultPress 进行备份似乎不会出错。

## 5.WP 迁移数据库

WP Migrate DB 是我今天五个选择中的最后一个，但同样重要。这个插件的缺陷(也是它的优势)在于它的备份粒度方法。它详细解释了它在做什么——将数据库转储到 SQL 文件。

该服务仅用于备份数据库，包括帖子、页面、设置和其他不可替代的内容。那些不能被快速重新下载的东西，如果出了差错，反而会永远丢失。

这个插件可以让你选择要迁移或删除的数据库表，让你过滤你想要备份的文章类型，并且可以处理非常大的数据库。这是 WordPress 数据库备份的坚实平台。

它还有一个专业版，将启用更多的功能，以及一定程度的支持，这可能会使一些用户值得研究。

## 包装东西

这些仅仅是 WordPress 网站众多备份和迁移工具中的五个。它们不一定是最好的，但它们是五个被社区很好地评价的，并且我已经在其中的几个案例中使用了它们。对于 WordPress 开发者来说，这是一个很好的起点，但是我们不应该忘记备份的重要原则:经常备份，备份到另一个位置，确保你安排时间检查你的备份！

迁移工具实际上可以归结为在您的开发和迁移流程中最有效的工具，这对于我们所有人来说都是不同的。事实上，简单无误是您的主要目标—您需要像信任备份系统一样信任迁移系统！

最重要的是你正在做备份。您使用的服务取决于您的具体情况。事实上，一些 web 主机会负责迁移和备份。例如，[网站](https://www.siteground.com/go/article-sp)提供[日常备份服务](https://www.siteground.com/daily_backup_saves_your_site.htm)。这提供了持续、可靠的安全性，异地备份让客户高枕无忧。在这种情况下，任何备份插件都只是锦上添花！

## 分享这篇文章