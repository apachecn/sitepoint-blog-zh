# 使用 Git 的 WordPress 版本控制

> 原文：<https://www.sitepoint.com/wordpress-version-control-git/>

![Git](img/1042c81bdcbf4af459ea354bc323a136.png)

*本文是与 [SiteGround](https://www.siteground.com/sitepoint-recommended?afcode=97a975da3502771c04e59cbae092b1dd&campaign=wordpress-version-control) 合作创作的系列文章的一部分。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

版本控制是 web 开发工作流程中不可或缺的一部分，对于 WordPress 站点来说也同样必要。然而，用版本控制，或者更具体地说，用 Git 来设置 WordPress 站点，在几个方面都具有挑战性。知道向 Git 存储库提交什么和忽略什么可能是一个挑战。同步数据库更改也是如此。WordPress 很容易直接更新和添加到实时网站，使得直接更新实时网站变得非常容易，扰乱了版本控制过程。

下面是一些在 WordPress 上使用 Git 的选项，一些关于众所周知的 Git 插件 VersionPress 的信息，以及一个基于主机的 Git 实现的简介。

## 在 WordPress 中使用 Git

在 WordPress 上使用 Git 可能是一个挑战。这里有一些技巧可以帮助你朝着正确的方向前进(注意，这些技巧假设你已经对 Git 有了基本的了解):

关于您的数据库连接，您应该做两件事情中的一件:要么在开发、登台和生产环境中使用完全相同的数据库名称和凭证，这样您的`wp-config.php`数据库连接信息就没有区别，要么完全使用`.gitignore`您的`wp-config.php`,这样它就不会被来自您的本地开发环境的信息覆盖。

说到要忽略的东西，你可能也应该`.gitignore`你的上传目录。没有必要同步上传，上传是唯一可以添加到生产文件系统的东西，所以没有必要引起不必要的同步问题！

禁用 WordPress 中的某些功能也很有用。

*   通过将`define( 'AUTOMATIC_UPDATER_DISABLED', true );`添加到`wp-config.php`来禁用自动更新。这将完全阻止生产站点上的自动更新。
*   通过添加`define( 'DISALLOW_FILE_EDIT', true );`来禁用管理面板的文件编辑器，以防止它被用来修改主题代码和生产站点上的其他文件。
*   或者，用`define( 'DISALLOW_FILE_MODS', true );`阻止主题、插件等被修改或添加(使之前的`wp-config.php`建议变得不必要)。这确保了所有更新首先在开发或试运行环境中完成，然后手动推送到实时站点。
*   请注意，使用任何这些限制都应该与创建一个过程相结合，以确保定期执行更新。自动更新的存在是有原因的，如果你不确保你的网站是最新的，你会使他们更容易受到一种类型的风险和更多的风险。

手动或插件创建的备份应该存储在 Git 存储库之外的某个地方(最好是异地或云备份)。

## 使用版本印刷

对于 WordPress 网站来说，VersionPress 是传统 Git 存储库的一种替代方式。安装 VersionPress 和其他插件一样简单！作为安装过程的一部分，VersionPress 将检查主机系统上所需的先决条件，如果不存在这些条件，将向您发出警告或停止安装。

一旦你安装好了，你就可以开始了！VersionPress 跟踪网站的每一个变化——添加、修改和删除的帖子或页面，插件变化等。您可以看到跟踪事件的列表，并且可以单击任何单个事件旁边的“撤销”来撤销特定的过去事件，或者可以单击“回滚”将整个站点回滚到该事件发生时的状态。相当酷！

最重要的是，超级用户可以使用他们普通的 Git 客户端来管理 VersionPress 安装，因为 VersionPress 执行的每个操作和命令都是由 Git 直接驱动的。

## 主机提供 Git 服务

一些主机提供他们自己的版本控制服务，允许 WordPress 管理员从使用 Git 库来控制他们的站点中获益，而不需要在某个地方建立一个库并维护它。例如，SiteGround 使用 Git 驱动的 cPanel 插件[在你的 cPanel 中提供版本控制服务](https://www.siteground.com/tutorials/siteground-git/)，这使得它非常容易使用。

无论你选择什么，对于 WordPress 的管理员来说，版本控制仍然是可以实现的，这是保持你的网站更新、安全和易于管理的最好方法！

## 分享这篇文章