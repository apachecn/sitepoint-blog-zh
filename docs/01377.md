# 如何备份你的 WordPress 站点

> 原文：<https://www.sitepoint.com/backup-wordpress-site/>

![How to Backup Your WordPress System](img/badcf115edf1bebeb2b5a8b9fbaa9563.png)

*本文是与 [SiteGround](https://www.siteground.com/go/article-sp) 合作创作的系列文章的一部分。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

很少有人关心备份，直到为时已晚。问题*“我们有备份吗？”*仅在发生灾难性损失后引发。**备份必不可少**。如果你没有准备好，那就做好失败的准备。

以下是备份成功的五个步骤:

1.  教育你的老板/客户
    如果他们了解网络开发的技术细节，他们就不会需要你了。用简单的金钱术语解释情况，例如，如果我们的服务器磁盘今天崩溃，我们可能会失去一切。重建系统需要 M 个月的时间，花费 X 美元，我们可能会损失 Y 美元。一个可行的备份系统将花费 Z 美元来实现，我们将在 N 小时内回来。我假设你已经吓到了你的老板/客户，他们愿意继续。
2.  确定你需要备份什么
    备份 WordPress 系统有点复杂，因为它依赖于特定的文件和数据库。只有两者都有最新的副本，备份才是可行的。
3.  **实施适当的备份解决方案**
    这取决于您使用的技术和提供的服务。例如，每周备份可能适用于每月接收少量更新的博客。最坏的情况是，你可能会丢失一两篇文章。基于 WordPress 的网上商店可能需要持续的备份来确保客户订单不会丢失。
4.  自动化！
    备份应该从人类手中取出。考虑一个每天只需点击一次就能启动备份的系统。你开始会有良好的意图，但很快就会变成一件苦差事。当你去度假的时候会发生什么？离开公司怎么办？计算机擅长安排时间——让它们来处理工作。
5.  **验证并测试您的备份**
    您的系统只有在最后一次成功备份后才会恢复正常。理想情况下，您应该自动验证备份的完整性，将备份文件移动到异地，定期测试您的恢复过程，并将以前的备份保留一段合理的时间(通常是三个旧版本)。

你可以在 SitePoint 的课程[如何保护你的 WordPress 站点](https://www.sitepoint.com/premium/courses/how-to-secure-your-wordpress-site-2954)中了解更多关于手动和轻松备份你的 WordPress 站点的信息，你可以在下面预览。关于 WordPress 的更多信息，请查看我们关于[构建定制 WordPress 主题](https://www.sitepoint.com/premium/courses/wordpress-theme-development-2931)的综合课程。

## WordPress 组件

WordPress 组件有不同的备份需求。

### WordPress 安装文件

你可能从 wordpress.org 的一个档案中下载了 WordPress 安装程序。它包括以下文件和文件夹:

| 位置 | 描述 |
| `/` | 根 PHP、自述文件和许可文件 |
| `/wp-admin/` | 主要的 WordPress 控制面板代码 |
| `/wp-includes/` | 库和第三方组件 |
| `/wp-content/` | 主题、插件和媒体文件 |

你永远不需要备份默认的 WordPress 文件。您的代码(和第三方插件)不应该修改或添加文件到`/wp-admin/`或`/wp-includes/`。我建议保留一份安装程序的副本，但是所有版本的 WordPress 都可以从[发布档案](https://wordpress.org/download/release-archive/)下载。此外，您的安装最终会收到更新，因此实时版本将不同于您下载的版本。

### 根配置文件

必须备份几个根文件夹文件:

*   `wp-config.php` —包含数据库连接字符串在内的设置
*   `.htaccess` —服务器配置文件
*   站长工具识别文件如`google*CODE*.html`、`BingSiteAuth.xml`
*   您添加的任何其他文件

通常，您可以在安装完成后备份这些文件。然而，要警惕对配置做进一步改变的主题和插件。

### `wp-content`文件

`/wp-content/`文件夹包含所有主题、插件和媒体资产，如上传的图像。此文件夹必须备份，但这将取决于您的开发过程。

如果你正在开发自己的定制主题和插件，使用 Git 这样的源代码控制系统，并在 [GitHub](https://github.com/) 、 [BitBucket](https://bitbucket.org/) 或类似的提供商上创建一个中央存储库是有益的。这些服务在所有开发人员的机器上复制源代码；很难丢失任何东西，所以备份`/wp-content/themes/`和`/wp-content/plugins/`文件夹可能不是绝对必要的。

然而，我怀疑大多数 WordPress 网站使用了第三方主题和插件的组合，并做了一些自定义修改。此外，在多站点安装中，媒体文件包含在`/wp-content/uploads/`或`/wp-content/blogs.dir/`文件夹中。因此，备份`/wp-content/`文件夹中的所有东西通常是最简单的。

备份的频率将取决于您的更新活动。对于许多网站来说，每隔几天一次可能就足够了，但是当编辑定期添加内容和图片时，可能需要更频繁。

### WordPress 数据库

该数据库包含所有页面内容，历史修订，媒体参考，网站配置，主题选项，插件激活，用户帐户等。经常备份数据库是非常必要的——对于一个普通的博客来说可能是一天一次，对于一个电子商务系统来说可能是每小时一次。

请注意，数据库可能会变得很大，从而减慢备份过程。

## 手动备份

如果你没有备份，现在就做！我建议偶尔进行一次手动备份，即使你已经实现了一个自动化系统— *你永远不会有太多的*。

像 [SiteGround](https://www.siteground.com/go/article-sp) 这样的主机提供了像 [cPanel](https://cpanel.com/) 这样的系统，可以让你备份所有的文件、数据库和设置。搜索**备份**或**备份向导**并按照步骤操作。这些通常会创建一个可以在同一系统中恢复的文件。

如果您喜欢更实际的方法:

1.  使用 FTP、 [SCP](https://en.wikipedia.org/wiki/Secure_copy) 、 [wget](https://www.gnu.org/software/wget/) 或你手头的任何其他工具下载根配置和`/wp-content/`文件。
2.  下载数据库的快照，其中包含重新创建表和填充数据的 SQL 命令。

大多数主机都提供 SQL 工具，比如 phpMyAdmin T1 或 T2 管理员 T3，或者你可以在任何支持 PHP 的主机上安装它们。要登录，您需要:

*   主机名——通常是`localhost`，但是检查`wp-config.php`中的`DB_HOST`设置
*   数据库名称——参见`wp-config.php`中的`DB_NAME`
*   数据库用户名——参见`wp-config.php`中的`DB_USER`,以及
*   数据库用户的密码——参见`wp-config.php`中的`DB_PASSWORD`。

一旦成功连接，您应该会看到许多表格，如`wp_posts`、`wp_users`和`wp_comments`。*(注意表格前缀由`wp-config.php`中的`$table_prefix`设定，不一定总是`wp_`。)*

寻找一个选项，如**转储**、**导出**或**备份**。在下载 SQL 文件之前，确保选择了每个表(使用 drop and create)和所有数据。

具有 SSH 访问权限的命令行 ninjas 可以使用 [mysqldump 命令](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html)备份数据库:

```
mysqldump --host=hostname --user=dbuser --password=dbpass --add-drop-table --result-file=backup.sql dbname
```

其中`hostname`、`dbname`、`dbuser`和`dbpass`是我们上面指定的凭证。完整的备份会输出到当前文件夹下的`backup.sql`文件中，可以下载。

恢复手动备份就是将文件上传到正确的位置，并使用 SQL 工具或命令导入 MySQL 数据:

```
mysql --host=hostname --user=dbuser --password=dbpass dbname < backup.sql
```

所有手动备份过程都可以使用工具自动化，例如也可以通过 cPanel 控制的 [cron](https://en.wikipedia.org/wiki/Cron) 。

## 备份插件

幸运的是，有几十个 WordPress 备份插件可供选择，可以自动化你的过程，并将备份导出到异地数据存储。最受欢迎的包括:

### [喷气背包](https://wordpress.org/plugins/jetpack/)

作为最接近官方插件的选项，Jetpack 提供了一系列搜索引擎优化、安全和网站管理功能，包括实时备份到专业付费计划的无限存储空间。

### [UdraftPlus](https://wordpress.org/plugins/updraftplus/)

UdraftPlus 是排名最高的备份插件之一，拥有超过 100 万的活跃安装量，可以在 Dropbox、Google Drive、OneDrive、亚马逊 S3 或您自己的服务器等远程系统上计划和存储备份。

### [备份 WordPress](https://wordpress.org/plugins/backupwordpress/)

一个简单的插件，它可以根据你的需求来备份你的数据库和文件。[商业版](https://bwp.hmn.md/)支持异地云存储备份。

### [后退](https://wordpress.org/plugins/backwpup/)

BackWPup 的免费版允许你备份你的文件和数据库到各种你自己的和第三方的存储空间。[商业版](https://backwpup.com/)提供调度、差异备份和自动更新。

### [复印机](https://wordpress.org/plugins/duplicator/)

Duplicator 通常用于迁移和克隆，但也提供手动备份和恢复功能。[商业版](https://snapcreek.com/duplicator/)支持调度和云备份。

### [x 克隆体](https://wordpress.org/plugins/xcloner-backup-and-restore/)

一个开源插件，提供了一系列的调度和云存储选项。

## 备份服务

或者，你可以选择第三方服务来管理你的 WordPress 备份。主机如 [SiteGround](https://www.siteground.com/go/article-sp) 是最好的起点；他们可以为所有托管计划实施备份选项。SiteGround 甚至有一个内部备份服务，可以自动保存你网站的最新 30 个副本，并让你按需恢复你需要的那个。

其他服务包括:

### [VaultPress](https://vaultpress.com/)

VaultPress 使用 Jetpack 异地存储每日或实时自动备份，以及扫描恶意软件、垃圾邮件发送者攻击和主机故障。计划从每月 3.50 美元开始。

### [WP 时间胶囊](https://wptimecapsule.com/)

WP Time Capsule 进行增量备份；仅备份更改过的文件和数据，因此该过程比备份所有内容的过程要快得多。每次 WordPress 更新前都会自动备份。Lite 计划在每日备份和 15 天恢复点方面不需要任何成本。

### [管理 WP](https://managewp.com/)

管理 WP 有帮助你管理多个 WordPress 安装的工具。高级计划从每个网站每月 2 美元起，包括可以计划每小时进行一次的增量备份。其他功能包括性能监控、安全检查、分析和报告。

## 立即备份！

WordPress 备份可能比其他软件更复杂，但是工具、插件和服务的范围简化了这个过程。如果您没有备份程序，现在就拍一张快照，研究一下选项。你每拖延一分钟，就会增加灾难性失败的风险。

## 分享这篇文章