# 将您当前的 WordPress 博客迁移到 WordPress 多站点博客

> 原文：<https://www.sitepoint.com/migrating-your-current-wordpress-blog-to-a-wordpress-multisite-blog/>

上个月，我[写了](https://www.sitepoint.com/video-how-to-host-multiple-sites-in-wordpress-3-0/)并展示了一个关于如何在 WordPress 3.0 中托管多个站点的[视频](https://vimeo.com/19643426)。这就提出了一个问题，如果你已经有了多个 WordPress 站点，并且想把它们放在一起利用 WordPress 多站点特性的力量，该怎么办。

直到最近，将你所有的博客迁移到一个单一的多站点博客还是一项艰巨的任务，因为需要解决不同表之间的 id 冲突。幸运的是，较新的 WordPress 版本有内置功能，让我们所有人的生活变得更简单。

你可以在这里找到官方的 WordPress multisite 迁移文档[，但是我会在这里给你一个过程的总结，加上另一个关于这个主题的视频。这是视频:](http://codex.WordPress.org/Migrating_Multiple_Blogs_into_WordPress_3.0_Multisite)

[vimeo 20772164 600 375]

在这个总结中，我将使用“multisite”来指代主 WordPress 博客，“singlesite”来指代您想要迁移的单个 WordPress 博客，而“newsite”作为 WordPress 安装，在“multisite”下的子目录中充当占位符站点。在域名迁移发生之前，来自“单一站点”的内容需要位于“新站点”中。

首先要做的是做一些准备。

确保你已经安装了最新版本的 WordPress:我们现在已经升级到 3.1 版本了。这对于安装之间的一致性非常重要，同时也确保您可以访问多站点功能。如有必要，升级 WordPress 并确保启用多站点功能。详情查看之前的[视频](https://vimeo.com/19643426)。

备份多站点和单站点的所有数据库。

暂时不要激活新站点的域映射。

这是一个很好的时间来做一点家务，删除所有旧的垃圾评论，过期的用户和不需要的帖子。你将减少不想要的内容，让你以后的生活稍微轻松一点。

将主题和插件从单站点复制到多站点。如何实现这一点取决于你的网站如何托管。您可能需要使用 cPanel 或类似的工具，或者独立的 FTP。

下一步是将 singlesite 的内容导出到一个可下载的文件中。在左边栏的**工具**下的 singlesite WordPress 管理面板中，点击**导出**。点击**下载导出文件**按钮将网站内容导出为 xml 文件。

把你的注意力转移到新网站 WordPress 的安装上。为了导入我们刚刚导出的单站点 xml 文件，我们需要安装一个插件。点击管理面板左侧栏中**插件**下的**添加新的**，并搜索术语“WordPress Importer”。按照说明安装特定的插件。

然后进入**工具** / **导入**，点击 **WordPress** ，上传单点导出 xml 文件。仅继续导入到**分配作者**页面。

此时，您可以选择将作者从 singlesite 映射到 newsite。这是这个过程中最具挑战性的部分，因为 WordPress 不允许你在你的多站点中的不同站点上有重复的作者，所以你需要一种方法来识别和解决重复的作者。

为此，您可以比较作者的用户名和电子邮件地址。

转到 **phpMyAdmin** 获取单站点数据库。复制`wp_users`表，将其命名为`wp_users_tm` p，然后导出该表。然后切换到多站点数据库并导入`wp_users_tmp`。

要比较两个数据库中的`wp_user`表，请在多站点数据库上运行此查询，以给出所有重复电子邮件的列表。

```
 SELECT t.user_login as singlesite_user, w.user_login as multisite_user, w.user_email
FROM wp_users w, wp_users_tmp t WHERE w.user_email = t.user_email 
```

现在运行这个查询来检查任何重复的用户名。

```
 SELECT t.user_login as singlesite_user, w.user_login as multisite_user, w.user_email
FROM wp_users w, wp_users_tmp t
WHERE w.user_login = t.user_login and w.user_email != t.user_email 
```

返回到多站点导入流程中的**分配作者**页面，在用户重复的情况下，将“_ 单站点”或另一个唯一标识符附加到现有的单站点用户名。

勾选**下载和导入文件附件**复选框，点击**提交**。

由于所有用户现在都有唯一的名称，您可以将它们与您在 singlesite 中的配置文件进行比较。现在，您可以在用户部分手动输入他们的个人资料详细信息。

重新激活所有插件，并重新设置新网站的小工具。您也可以在**超级管理员** / **站点**下配置主题。

将你的浏览器指向 http://multisite.something/newsite/and，检查一下 newsite 现在包含了 singlesite 的内容。

更改单站点域的 IP 地址以指向您的多站点主机。

在**超级管理员** / **域**下添加单站点域作为主域。

这个迁移过程比前一个过程复杂得多，我建议你先看一下[第一个](https://vimeo.com/19643426)和[这个最新的视频](https://vimeo.com/20772164)，在你着手处理之前彻底熟悉这个过程。

如果你正在考虑迁移你的 WordPress 博客，我希望这能帮助你。暂时就这样了。

## 分享这篇文章