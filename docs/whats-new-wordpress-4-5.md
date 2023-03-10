# WordPress 4.5 的新功能

> 原文：<https://www.sitepoint.com/whats-new-wordpress-4-5/>

*这篇文章已经过时了，[点击](https://www.sitepoint.com/whats-new-in-wordpress-4-6/)查看 WordPress 4.6 的新功能。*

WordPress 4.5 刚刚正式发布，命名为“科尔曼”，以纪念爵士音乐家科尔曼·霍金斯。在亚当·银色啤酒杯乐队和梅尔·乔斯以及总共 298 名投稿人的帮助下，发布会由迈克·施罗德牵头。

在这篇文章中，我将概述这个最新版本中的新内容，以及在更新你的 WordPress 站点时你需要记住的内容。

## 开始之前——备份你的网站

在升级 WordPress 4.5 之前，不要忘记执行完整的网站备份(数据库和文件)。你可以选择直接从你的主机上运行(例如 cPanel 或 Plesk backup)，但是也有一些流行的备份插件，例如 [Updraft Plus](https://wordpress.org/plugins/updraftplus/) 、 [BackupBuddy](https://ithemes.com/purchase/backupbuddy/) 和 [Backup to Dropbox](https://wordpress.org/plugins/wordpress-backup-to-dropbox/) 。

Amit Diwan 有一个[最好的 WordPress 备份插件](https://www.sitepoint.com/best-wordpress-backup-plugins/)的大对比。

升级你的网站时，你可能会发现问题(通常是第三方插件和主题)。如果发生这种情况，您可以将站点回滚到最新的备份。同样值得检查的是主题或插件提供者是否发布了任何关于 WordPress 4.5 支持的内容。

在可能的情况下，最好将更新应用到测试或试运行环境中，这样您就可以在将它们应用到实际站点之前测试这些更改。如果那是不可能的，试着选择一个对你的访客影响最小的时间。

如果您在升级过程中遇到任何问题并需要帮助，请尝试基本的故障诊断步骤，例如禁用您的插件并选择默认主题来隔离问题所在。查尔斯·科斯塔[分享了一些关于 WordPress 常见问题以及如何解决这些问题的技巧](https://www.sitepoint.com/common-wordpress-issues-and-how-to-fix-them/)。别忘了你也可以访问官方 WordPress 支持论坛，只要[确保你阅读了欢迎帖](https://codex.wordpress.org/Forum_Welcome)。

## 应用 WordPress 4.5 更新

首先，你需要登录到你的 WordPress 仪表盘，在那里你会看到下面的消息:

![WordPress 4.5 Update Message](img/a670c529599866131b4a38b4fc13a636.png)

然后你点击“请立即更新”,这将带你到下一页，提示你“立即更新”到 WordPress 4.5。

最后，当 WordPress 4.5 安装完成后，你会看到下面的成功屏幕欢迎你。

![WordPress 4.5 Welcome and What's New](img/454b60e05fb841fd619b0a54e08768fd.png)

## WordPress 4.5 的新功能

### 网站徽标

对于有经验的开发人员或高级用户来说，这个特性似乎没什么大不了的，但是对于很多用户来说，添加徽标的简单过程并不总是一个选项。

您现在可以在站点标识>徽标中找到徽标设置。

![WordPress 4.5 Site Logo](img/fd223c871c4142fa634ef59ec7208ab1.png)

你的里程会根据你选择的主题而有所不同，但是标准的默认主题和流行的主题会开始使用这种方法来让用户轻松控制 logo。

### 响应预览显示

从 WordPress 4.5 开始，你可以通过仪表盘上的定制器在不同的设备屏幕上预览你的网站。

![New Live Responsive View in Customizer](img/53afbe49b7728da3dc4b1f16955c585c.png)

以前，你可能会使用一个扩展，你的浏览器开发工具，甚至只是调整你的窗口大小-但现在这个方便的功能在定制中很容易实现。

![WordPress 4.5 Customizer Responsive Preview](img/d363c64f56faee0c5a64144dddaa1d0c.png)

### 内嵌链接编辑

页面和帖子的可视化编辑器中的链接过程已经进一步简化。现在，当您在 WYSIWYG 编辑器中单击标准链接按钮时，文本下方会自动出现一个字段，允许您粘贴链接或在您的站点上搜索链接。

![WordPress 4.5 New Insert Link](img/2d37b2b2cb09bf86ed2e88ebf47eda2b.png)

如果你想调出以前点击这个按钮时出现的旧链接屏幕功能，只需点击 cog 图标。

![WordPress 4.5 Old Link Editor](img/4832c93a15ee9435281c87380458f6ac.png)

### 新快捷方式

如果你在 WordPress 里面写了很多内容，有几个新的快捷方式会帮助你进一步简化你的写作:

```内容显示代码前后的一个反勾

`—`对于水平标尺

下面是使用反勾插入代码的样子:

![Inserting Code by Backticks in Editor](img/dc818dd8afa14c4d0899175d13d117c7.png)

### 评论审核改进

WordPress 4.5 有一个更新的评论审核屏幕样式，使得管理评论更加容易。现在还可以在编辑器中查看评论。

### 图像优化改进

图像优化得到了进一步增强，在保持图像质量的同时，创建的文件可减少 50%。

### 以用户名和电子邮件登录

从 4.5 开始，你可以通过用户名和电子邮件登录。您可以一直使用您的电子邮件地址作为用户名，但现在登录接受您的帐户电子邮件地址和用户名。它会派上用场，用户不太可能忘记他们的电子邮件地址！

### 开发者功能

对于开发人员来说，还有一些新功能:

*   JavaScript 库更新(jQuery 1.12.3、jQuery Migrate 1.4.0、Backbone 1.2.3 和下划线 1.8.3)
*   嵌入式模板
*   术语编辑页面修改(增加了`wp-admin/term.php`)
*   选择性刷新

WordPress 4.5 领域指南详细列出了所有与开发者相关的最新变化，以及一些活跃的讨论。

## 包扎

如果你想了解更多关于 WordPress 4.5 版本的信息，这里有一些资源可供进一步阅读:

*   [WordPress 4.5“科尔曼”公告](https://wordpress.org/news/2016/04/coleman/)
*   [WordPress 4.5 Codex 文档](https://codex.wordpress.org/Version_4.5)
*   [制作 WordPress Core 4.5 标签](https://make.wordpress.org/core/tag/4-5/)
*   [WordPress 4.5 领域指南](https://make.wordpress.org/core/2016/03/30/wordpress-4-5-field-guide/)

我很想听听你对最新发布的 WordPress 4.5 的想法和反馈。

## 分享这篇文章