# 如何定制 3.3 工具栏

> 原文：<https://www.sitepoint.com/change-wordpress-33-toolbar/>

你可以通过移除[不必要的菜单](https://www.sitepoint.com/how-to-hide-menus-in-wordpress/)、[小工具和元框](https://www.sitepoint.com/wordpress-easy-administration-plugin-2/)来使 WordPress 界面对客户来说更容易。然而，在 WordPress 3.3 中，管理栏和标题栏被合并成了一个单独的工具栏。它也可能包含您想要隐藏的选项…

## WordPress 工具栏 API

新工具栏是使用一个单独的`WP_Admin_Bar`对象定义的(参见 WP-includes/class-WP-admin-bar . PHP)。这提供了许多有用的方法:

*   `add_node()` —添加新的工具栏项目
*   `remove_node()` —移除工具栏项目
*   `get_node()` —获取节点的属性
*   `get_nodes()` —获取所有节点的列表

## 移除工具栏项目

我们将把代码放入一个名为 WP-content/plugins/change-toolbar . PHP 的可重用插件中，但是你可以把它放在你的主题的 functions.php 文件中。

WordPress 插件要求在文件的顶部有一个标题，例如

```
 <?php
/*
Plugin Name: Change Toolbar
Plugin URI: https://www.sitepoint.com/change-wordpress-33-toolbar
Description: Modifies the WordPress 3.3+ toolbar.
Version: 1.0
Author: Craig Buckler
Author URI: http://optimalworks.net/
License: GPL2
*/ 
```

我们现在需要一个函数，我们将在其中进行更改:

```
 function change_toolbar($wp_toolbar) {
	/* ... code to go here ... */
} 
```

后跟一个操作挂钩，它运行函数并传递工具栏对象:

```
 add_action('admin_bar_menu', 'change_toolbar', 999); 
```

我们现在可以删除`change_toolbar()`功能中的工具栏项目。例如，下面一行通过引用它的 ID“WP-logo”隐藏了 WordPress 徽标和帮助子菜单:

```
 $wp_toolbar->remove_node('wp-logo'); 
```

若要删除其他项目，您需要发现他们使用的 ID。您可以钻研 PHP 代码，但有一种更简单的方法:

*   打开 Firebug 或您喜欢的类似 Firebug 的开发控制台。
*   找到要移除的工具栏项目(在大多数浏览器中，您可以右键单击该项目并选择“检查元素”)。
*   向上导航父节点，直到找到 LI 标记。它将有一个以“wp-admin-bar-”开头的 ID，后跟内部 ID 代码:

![WordPress toolbar ID](img/683889e1de9dad94cb352b891075a769.png)

在本例中，注释项被突出显示。因此，要从工具栏中删除它，我们使用:

```
 $wp_toolbar->remove_node('comments'); 
```

## 添加工具栏项目

我们可以在同一个功能中添加工具栏项目。语法是:

```
 $wp_toolbar->add_node($arg); 
```

其中$arg 是一个关联数组，包含:

*   `id` —项目的 ID
*   `title` —标题文本
*   `parent` —父菜单 ID(可选)
*   `href` —链接 URL(可选)
*   `group` —如果节点是组，则为真(可选)
*   `meta` —提供其他键的另一个数组，包括:html、class、onclick、target、title、tabindex

让我们添加一个链接到我们支持页面的“帮助”项目:

```
 $wp_toolbar->add_node(array(
	'id' => 'myhelp',
	'title' => 'Help',
	'href' => 'http://mysite.com/support/',
	'meta' => array('target' => 'help')
)); 
```

我们现在可以通过引用`parent`中的“my help”ID 在子菜单中添加一个电子邮件支持链接:

```
 $wp_toolbar->add_node(array(
	'id' => 'mysupport',
	'title' => 'Email Support',
	'parent' => 'myhelp',
	'href' => 'mailto:support@mysite.com?subject=support%20request'
)); 
```

我希望你会发现它很有用——使用几个 API 调用就可以很容易地定制整个 WordPress 3.3 工具栏。

## 分享这篇文章