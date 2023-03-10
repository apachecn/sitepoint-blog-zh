# 如何编写自己的易于管理的 WordPress 插件

> 原文：<https://www.sitepoint.com/wordpress-easy-administration-plugin-1/>

WordPress 的流行很大程度上归功于其简单的管理面板。不幸的是，对于像你的客户这样的非技术用户来说，这仍然令人望而生畏。充其量，他们需要一点培训，手把手和支持。在最坏的情况下，他们会玩插件安装，编辑一些主题代码，然后期望你清理混乱。

我已经写了一些“让 WordPress 更容易为客户所用”的文章(见[第一部分](https://www.sitepoint.com/make-wordpress-easier-for-clients-branding/)和[第二部分](https://www.sitepoint.com/how-to-hide-menus-in-wordpress/))。在这些例子中，代码被放在主题的 functions.php 文件中。如果你在每个客户端上都安装了一个 WordPress 或者每个客户端都有不同的配置，这仍然是一个可行的解决方案。

然而，在本文中，我们将创建一个插件。插件有几个优点:

1.  您的代码驻留在一个文件中，这使得维护更容易。
2.  如果你正在运行一个有多个站点的 WordPress 网络(以前叫做 WordPress MU)，你可以在网络上激活一个插件，这样它就可以应用到每个站点。

## WordPress 插件基础

我们的插件将包含在一个 PHP 文件中。我们将把它命名为 easy-admin.php，并放在 WordPress 插件文件夹(wp-content/plugins/)中。理想情况下，该文件应该是 UTF-8 编码。如果你的文本编辑器不支持 UTF 8，那么，使用一个更好的编辑器！也就是说，那些使用英语的人不太可能遇到 ANSI 编码文件的问题。

文件的顶部需要一个 PHP 标签和头注释，例如

```
 <?php
/*
Plugin Name: Easy Administration
Plugin URI: https://www.sitepoint.com/wordpress-easy-administration-plugin-1
Description: Simplifies WordPress administration panels.
Version: 1.0
Author: Craig Buckler
Author URI: http://optimalworks.net/
License: GPL2
*/ 
```

你可以改变标题的细节，但是要确保定义标签不变——WordPress 使用它们来识别你的插件。

你现在可以通过在 WordPress 管理面板的“插件”部分激活来安装你的插件。拥有 WordPress 网络的用户可以在“网络管理”部分为所有网站激活它。它不会做任何事情，但你现在可以添加任何你需要的功能…

## 改变 WordPress 登录页面的标志

WordPress 的标志很可爱，但是很少有客户会关心他们用的是什么 CMS。显示他们的网站名称可能会更有帮助。将以下代码添加到 easy-admin.php 中；它用名称替换了登录页面的徽标，并使用了令人愉悦的 CSS3 字体文本:

```
 // login page logo
function custom_login_logo() {
	echo '<style>h1 a, h1 a:hover, h1 a:focus { font-size: 1.4em; font-weight: normal; text-align: center; text-indent: 0; line-height: 1.1em; text-decoration: none; color: #dadada; text-shadow: 0 -1px 1px #666, 0 1px 1px #fff; background-image: none !important; }</style>';
}
add_action('login_head', 'custom_login_logo'); 
```

![WordPress alternative login page logo](img/659a70d6e2766d5ac712f0aa864ea52f.png)

## 从管理面板标题中删除 WordPress 图标

WordPress 图标显示在标题中网站名称的旁边。这没有错，但是一些客户会问为什么他们的网站旁边有一个“W”。要删除它，请将以下代码附加到 easy-admin.php 中:

```
 // remove administration page header logo
function remove_admin_logo() {
	echo '<style>img#header-logo { display: none; }</style>';
}
add_action('admin_head', 'remove_admin_logo'); 
```

## 更改 WordPress 管理面板页脚文本

页脚提供了 WordPress、文档和反馈的链接。很少有客户会觉得它有用，所以你可以用你自己的支持细节来代替它。将以下代码附加到 easy-admin.php，并将 echo 语句更改为输出到合适的消息:

```
 // change administration panel footer
function change_footer_admin() {
	echo 'For support, please call 123456 or email <a href="mailto:support@mysite.net">mailto:support@mysite.net</a>';
}
add_filter('admin_footer_text', 'change_footer_admin'); 
```

## 删除 WordPress 管理栏

深灰色的管理栏是在 WordPress 3.1 中引入的[。个人觉得不是特别有用。这也会让客户感到困惑；他们可能认为所有的访问者都可以看到酒吧，或者使用它来访问危险的功能，如“外观”。幸运的是，我们可以用 easy-admin.php 中的一行代码删除它:](https://www.sitepoint.com/wordpress-31-whats-new/)

```
 // remove admin bar
add_filter('show_admin_bar', '__return_false'); 
```

今天的配置足够了。在我的下一篇文章中，我们将添加更多的功能来简化仪表板、文章和页面面板。

## 分享这篇文章