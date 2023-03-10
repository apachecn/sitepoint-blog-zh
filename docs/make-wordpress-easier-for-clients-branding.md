# 如何让客户更容易使用 WordPress，第 1 部分:定制品牌

> 原文：<https://www.sitepoint.com/make-wordpress-easier-for-clients-branding/>

WordPress 是网络上最流行的内容管理系统之一。吸引力是显而易见的:它很灵活，很容易创建自定义模板，它提供了大量的插件，数百个主题可用，该应用程序是开源的，而且是免费的。客户理解起来也相对简单——这是我使用它的主要原因之一。我最近贴了一篇文章 [*你的客户使用他们的 CMS 吗？我的结论是，许多 CMS 让人望而生畏，显得技术化，使用行话，或者提供太多多余的选项。WordPress 可能是一个更简单的系统，但它还可以进一步简化。幸运的是，我们可以用一点 PHP 魔法解决许多问题。在这一系列文章中，我们将实现几个选项来使你的客户更容易使用 WordPress。你也许可以找到插件来实现同样的事情，但是这段代码简单、灵活，并且不需要更新(除非你想自己更新)。*](https://www.sitepoint.com/do-clients-use-cms/)

## functions.php

以下 WordPress 的修改改变了你的主题的**functions.php**文件。该文件是可选的，当您查看管理页面或网站本身时，它会自动加载。该文件通常用于定义常用的模板函数，但它也可以实现类似插件的行为。因为 functions.php 是用你的主题激活的，所以它很容易在 WordPress 安装之间分发，而不需要下载、启用或更新插件。如果您当前的主题不包含 functions.php 文件，只需创建一个并添加 PHP 分隔符:

```
<?php// code will go here?>
```

**tip:** PHP delimiters

结案陈词"？>”并不是绝对必要的，许多 PHP 开发人员会告诉您不应该使用它——但这是另一篇文章的主题！

## 改变 WordPress 登录标志

WordPress 的标志没有任何问题，但是很少有客户会关心他们使用的是什么 CMS。为什么不使用他们的标志或品牌？徽标文件是/WP-admimg/logo-log in . gif。尽管您可以删除或更改该文件，但它会在您更新 WordPress 时重新出现。这里有一个更好的解决方案: **1。创建/获取您客户的徽标**最好调整图像大小，使其不超过 326 x 82 像素。将图像文件复制到你的主题文件夹中——在下面的代码中，我们假设它被命名为 logo.png*。 **2。更新你的主题的 function.php 文件**将下面的代码放在 function.php 以改变默认的标志。如果命名不同，记得修改*logo.png*文件引用。*

```
// login page logofunction custom_login_logo() {	echo '<style type="text/css">h1 a { background: url('.get_bloginfo('template_directory').'/logo.png) 50% 50% no-repeat !important; }</style>';}add_action('login_head', 'custom_login_logo');
```

## 删除 WordPress 更新通知

当有新版本时，WordPress 管理面板会通知你。不幸的是，它告诉所有人——包括你的客户。这可能会导致不必要的担心，或者说服他们每半小时联系您一次，直到应用更新。将下面的代码添加到你的主题的 functions.php 文件中，禁用除 WordPress 管理员之外的所有人的更新通知:

```
// remove upgrade notificationfunction no_update_notification() {	remove_action('admin_notices', 'update_nag', 3);}if (!current_user_can('edit_users')) add_action('admin_notices', 'no_update_notification', 1);
```

我希望这两个例子对你有用。进一步的 WordPress 简化想法[现在可用…](https://www.sitepoint.com/how-to-hide-menus-in-wordpress)* 

## *分享这篇文章*