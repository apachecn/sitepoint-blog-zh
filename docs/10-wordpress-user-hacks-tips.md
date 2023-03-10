# 让 WordPress 用户友好的 20 个片段和技巧第 1 部分

> 原文：<https://www.sitepoint.com/10-wordpress-user-hacks-tips/>

除了我们通常的 jQuery 帖子，我一直在使用 WordPress，尤其是为客户开发它，这里有一些提示。开箱即用，WordPress 是一个相当简单的 CMS，任何人都可以学习。但是对于你那些不懂技术的客户来说，这对你刚刚建立的网站来说可能是一场噩梦和潜在的灾难。当然，有许多插件将实现与这些代码片段相同的功能，但是**通过代码，您可以获得额外的控制和安全性**，并且在大多数情况下，不需要更新。

相关帖子:

*   [**【第二部分】**](http://www.jquery4u.com/snippets/9-snippets-hacks-wordpress-user-friendly-clients/)

## 1.禁用插件停用

如果您已经授予客户端插件激活/停用权限(允许他们自己添加新插件)，但您构建的网站需要一些核心插件才能运行，并且永远不应该停用，则此片段特别有用。
下面的代码将从您认为重要的插件中删除“停用”链接，并从所有插件中删除“编辑”链接。
![Disabling-Plugin-Deactivation.jpg](img/cdc243617d771eb4e6872cac94cc1e57.png)
[来源](http://sltaylor.co.uk/blog/disabling-wordpress-plugin-deactivation-theme-changing/)

## 2.禁用主题更改

下面的代码将从仪表板中删除“外观”菜单选项。就像上面的插件禁用代码一样，你真的不希望你的客户修补或试验任何主题变化。
![Disabling-Theme-Changing.jpg](img/fc209ca246e864c7032e1a8a073c84b7.png)
[来源](http://sltaylor.co.uk/blog/disabling-wordpress-plugin-deactivation-theme-changing/)

## 3.从管理面板禁用顶级菜单

有了这个代码片段，你可以隐藏任何你需要的顶层菜单(文章、媒体、链接、工具……)。一切取决于你的客户的要求。
![Disable-Top-Level-Menus-from-the-Admin-Panel.jpg](img/123af8b8562ff338db06e28d9313290f.png)
[来源](http://www.wprecipes.com/how-to-remove-menus-in-wordpress-dashboard)

## 4.从管理面板禁用子菜单

如果您的客户不需要上述代码片段所采取的激烈行动，而只需要禁用/隐藏一些关键的子菜单项，这段代码会有所帮助。
![Disable-Submenus-from-the-Admin-Panel.jpg](img/d71ab8703795608ffb7ade8992bf0fb0.png)
[来源](http://wp-snippets.com/32/disable-submenus-from-admin-panel/)

## 5.基于用户名限制管理菜单项

如果在任何情况下，您想要限制客户端对某些顶级菜单项的访问，但是您仍然想要维护主管理员的完整菜单，此代码片段将会有所帮助。
![Restrict-Admin-Menu-Items-Based-on-Username.jpg](img/dcacbf15871f7415f6877c279817bdb2.png)
[来源](http://wordpress.stackexchange.com/questions/1567/best-collection-of-code-for-your-functions-php-file)

## 6.从帖子和页面编辑器屏幕中移除元框

或许你选择 WordPress 的主要原因是你客户的需求。发布帖子和页面很可能是他们业务的关键。为了避免帖子/页面编辑器屏幕中的任何混乱，移除未使用的元框(自定义字段、最近评论、帖子标签等)可能会有所帮助。
![Remove-Meta-Boxes-from-Posts-Pages-Editor-Screens.jpg](img/e9fec46a9fe7041a489bdae061180e54.png)
[来源](http://webdesignfan.com/customizing-the-wordpress-admin-area/)

## 7.移除页面栏

添加此代码允许您从“页面”页面中删除任何您认为对您的客户端不必要的列。
![Remove-Pages-Columns.jpg](img/902d28172d5d9e4a561dac2c6deb2e6c.png)
[来源](http://wp-snippets.com/52/remove-pages-columns/)

## 8.删除文章栏

与上面的代码片段类似，这段代码将从文章页面中删除列。
![Remove-Posts-Columns.jpg](img/1b43ca209982b7b45edddf04b5f0aa86.png)
[来源](http://wpsnipp.com/index.php/functions-php/remove-post-columns/)

## 9.从仪表板中移除默认小组件

这个代码片段将删除您在仪表板中定义的任何小部件。大多数默认的仪表盘小部件对你的非技术客户来说可能是不必要的，并且可能是一个灾难性的干扰。
![Removing-Default-Widgets-from-the-Dashboard.jpg](img/dd3fe55a3362cfdd40d8aa9825c13cbf.png)
[来源](http://hankis.me/modifying-the-wordpress-dashboard/)

## 10.创建个性化的仪表板小部件

这个代码片段将创建一个简单的“Hello World”小部件，并且可以根据您自己的要求轻松编辑。
![Create-Personalized-Dashboard-Widgets.jpg](img/bc28a5ad422d9f1bb28d35dfd171359e.png)
[来源](http://hankis.me/modifying-the-wordpress-dashboard)

请继续关注本周晚些时候的第 2 部分！

## 分享这篇文章