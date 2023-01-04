# 视频:如何在 WordPress 3.0 中托管多个站点

> 原文：<https://www.sitepoint.com/video-how-to-host-multiple-sites-in-wordpress-3-0/>

在 WordPress 的 3.0 版本中，可以让一个 WordPress 安装托管多个网站。对于那些在不同领域拥有多个博客的人来说，这是个好消息。将所有的博客合并到一个安装中不仅节省了磁盘空间，还使得维护和升级变得容易。如果你使用的是 WordPress 或更高版本，这个特性已经为你内置了。可惜默认不开。你可以在 WordPress Codex 上找到[的安装说明，但是为了让你的生活更轻松，我录制了一个快速的视频教程，向你展示如何启用这个功能并正确配置它:](http://codex.wordpress.org/Create_A_Network)

[vimeo 19643426 600 375]

[WordPress 3.0 多站点设置](https://vimeo.com/19643426)

下面是所需步骤的快速总结:

第一步:备份你当前的 WordPress 数据库。

**第二步:**将这一行添加到你的【wp-config.php】文件中:

```
define('WP_ALLOW_MULTISITE', true);
```

**第三步:**登录管理区，点击工具- >网络。选择“子目录”选项。提交表单(接受其他选项的默认值)，然后按照下一页给出的说明进行操作。

**第四步:**注销，重新登录。现在你应该在 WordPress 管理菜单的顶部有一个“超级管理员”面板。在 Superadmin - >站点下，添加您的新站点。

第五步:测试你的新网站是否正常工作。

这就对了。现在你有了一个运行在子目录中的新 WordPress 站点。但是如果你想让你的新 WordPress 站点位于一个完全不同的域名呢？

在这种情况下，您需要再经历几个步骤:

**第六步:**下载[域名映射插件](http://wordpress.org/extend/plugins/wordpress-mu-domain-mapping/)，按照安装说明操作。

**第七步:**注销，重新登录。用您的服务器的 IP 地址更新 Superadmin - >域映射，然后转到 Superadmin - >域，用站点 ID 链接一个域。

第八步:测试你的新域。

暂时就这样吧！欢迎任何反馈。

## 分享这篇文章