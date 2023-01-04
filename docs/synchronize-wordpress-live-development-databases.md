# 如何同步 WordPress Live 和开发数据库

> 原文：<https://www.sitepoint.com/synchronize-wordpress-live-development-databases/>

![database synchronization](img/d6f4718563a4a0f82cae66af965e3cbf.png)

*本文是与 [SiteGround](https://www.siteground.com/go/article-sp) 合作创作的系列文章的一部分。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

使用真实内容开发 WordPress 主题要容易得多。当人们添加页面、帖子、媒体和评论时，意想不到的情况开始出现。在以下情况下，您漂亮的模板可能会损坏:

*   编辑器使用对断点来说太大或太小的资源
*   经理们引入了一个新的菜单项，它不适合较低的屏幕尺寸
*   你的主题实现了一个两级菜单层次结构，这不能满足不可避免的第三级页面
*   过长或嵌套过深的注释变得不可读。

理想情况下，您的开发服务器应该有您的实时生产服务器的数据库的快照。如果内容在发生更改时自动同步，您的工作流程可以得到进一步改进。

## 同步障碍

单向 WordPress 数据库复制可能比你想象的更具挑战性。有充分的理由说明为什么很少有程序员生活在这种开发梦境中…

### 数据差异

WordPress 在其数据库中存储各种配置设置。这包括在帖子和资产引用中使用的域名和路径。将这些信息导入本地数据库(可能位于 http://localhost/)会导致不良后果。它可能看起来工作正常，但图像和链接网址可能会把你带回服务器。

更复杂的是，WordPress 序列化了一些数据。解析和更改这些值可能会很困难。

### 多重安装

当您是唯一一个在本地安装并上传到单个活动服务器的开发人员时，同步就不那么必要和复杂了。对于有多个开发人员、临时服务器的团队来说，生活比生产服务器更复杂。

### 数据库可能很大

SitePoint 在 WordPress 上运行，包含数千篇文章、草稿和修改。即使实现了快速增量同步，您也不太可能想要或需要大量兆字节的 post 数据。

### 不仅仅是数据库

WordPress 将上传的图片和其他媒体存储在本地文件系统中。这些需要下载，数据库参考必须相应更新。

您还必须确保主题和插件文件同步，启用和配置适当。也就是说，你可能不想启用某些插件，例如多站点、缓存等。

### 很危险！

您的同步过程会失败吗？同步是否有可能被错误配置，从而导致开发数据上线？实时数据库是否存在被破坏或擦除的风险？

绝对是。

无论您选择哪种解决方案，**确保您从您的实时和开发服务器中定期备份文件和数据库**。想象一下失去一切。花几分钟运行 [`mysqldump`](https://dev.mysql.com/doc/en/mysqldump.html) 比花很多天重写丢失的内容更省事！

## 同步可能吗？

是的，但是没有放之四海而皆准的选择。您可能需要双向数据库同步。您可能需要从现场服务器到开发服务器的单向复制。或者你可能想从开发推进到生活。你可能只需要几页。还是要帖子、评论、用户等数据？数据库数据是否足够，或者您是否需要所有媒体资产？您喜欢手动同步、按预定义的时间间隔同步还是自动同步？

根据您的具体情况，以下一个或多个选项可能是合适的…

### [WP 迁移 DB Pro](https://deliciousbrains.com/wp-migrate-db-pro/)

也许最著名和最容易的选择是来自 [Delicious Brains](https://deliciousbrains.com/) 的 WP Migrate DB Pro WordPress 插件。商业产品有一个媒体文件插件和多站点功能，但也有一个[免费版](https://wordpress.org/plugins/wp-migrate-db/)。

注意数据是使用浏览器内 Ajax 方法提取的，因此大型数据库可能需要时间来同步。

### [WP 同步数据库](http://wp-sync-db.github.io/)

WP Sync DB 是 WP Migrate DB 的开源分支。

### [版本按下](https://versionpress.net/)

VersionPress 是一个基于 Git 的 WordPress 版本控制插件，可以管理数据库合并。该产品正在开发中，但似乎很有前途。

### [内容的 WPSiteSync】](https://wordpress.org/plugins/wpsitesynccontent/)

WPSiteSync 提供免费版和商业版，允许页面、帖子和其他数据的实时同步。

### [数据库同步](https://wordpress.org/plugins/database-sync/)

数据库同步是一个免费的数据库同步插件。它已经有一段时间没有更新了，与多站点安装不兼容，但对于一些开发人员来说可能已经足够了。

### [WordPress 导入器](https://wordpress.org/plugins/wordpress-importer/)

如果你喜欢一个更安全、更手工的过程，WordPress Importer 会将文章、页面、评论、类别、标签、作者和其他数据导出到一个 XML 文件中，该文件可以导入到另一个安装中。如果你在一个开发系统中从文章中构建一个实时站点，这也是一个不错的选择。

### [SyncDB](https://github.com/jplew/SyncDB)

SyncDB 是同步本地和远程 WordPress 数据库的 bash 脚本。

### [WP 暂存](https://wordpress.org/plugins/wp-staging/)

WordPress WP Staging 插件能够将数据和文件从 live 克隆到开发或 Staging 服务器。

### [WordPress GitHub Sync](https://wordpress.org/plugins/wp-github-sync/)

WordPress GitHub 同步插件允许你同步来自 GitHub 库或 Jekyll 创建的网站的内容。对于需要内容编辑协作和拉式请求批准工作流的团队来说，这可能是一个有趣的选项。

### [PushLive](https://wordpress.org/plugins/pushlive/)

PushLive 插件只需点击一下，就可以复制到实时网站。支持多站点 WordPress 安装。

### [WP 驿站马车](https://wpstagecoach.com/)

一种商业在线服务，允许将暂存数据合并到您的实时数据库中。

### MySQL 同步工具

大多数好的 MySQL 数据库管理工具都提供数据库导入、导出和/或同步功能。免费的 MySQL 工作台是一个很好的起点。更高级的工具如[symmetrics](https://www.symmetricds.org/)提供了数据转换和过滤等功能。最后，您可以考虑像 [MySQL 复制](https://dev.mysql.com/doc/refman/5.7/en/replication.html)这样的选项，将数据从实时主数据库自动复制到开发从数据库。

### 构建您自己的解决方案

唯一完美的解决方案是您根据自己的需求构建的解决方案。以下工具可能会有所帮助:

*   [mysqldump](https://dev.mysql.com/doc/en/mysqldump.html) —最初的 MySQL 模式和数据备份工具
*   [MySQL pump](https://dev.mysql.com/doc/en/mysqlpump.html)—v 5 . 7 . 8 中引入的新 MySQL 备份实用程序，它应该提供更快的并行处理和压缩。请注意，它与数据库的早期版本不向后兼容。
*   [rsync](https://rsync.samba.org/) —增量文件传输
*   [Git](https://git-scm.com/) 部署选项，如 [WP 推送器](https://wppusher.com/)、[豆茎](http://beanstalkapp.com/)和[部署机器人](https://deploybot.com/)
*   任务运行者如 [Gulp.js](http://gulpjs.com/) 或 PHP 替代者如 [Robo](http://robo.li/) 、 [Task](https://taskphp.github.io/) 和 [Bldr](http://bldr.io/) 。

你有完善的 WordPress 开发部署工作流程吗？请在下面留下你的建议！

对于令人敬畏的 WordPress 主机，我们推荐 [SiteGround](https://www.siteground.com/go/article-sp) 。SiteGround 在每个计划中都免费提供自动 WordPress 核心和插件更新、每日备份和 SSL 证书。

## 分享这篇文章