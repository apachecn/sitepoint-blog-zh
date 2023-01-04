# 为用户简化 WordPress 的 7 种方法

> 原文：<https://www.sitepoint.com/make-wordpress-simpler-users/>

![simpler WordPress](img/3dc8bd6962cce1049504de719c0cef57.png)

*本文是与 [SiteGround](https://www.siteground.com/sitepoint-recommended?afcode=97a975da3502771c04e59cbae092b1dd&campaign=wordpress-simpler) 合作创作的系列文章的一部分。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

WordPress 是比较简单的内容管理系统之一。尽管 WordPress 的用户界面简洁，但对于那些不经常使用它或只具备基本 it 技能的人来说，它可能会令人望而生畏。它还提供了相当大的权力；如果不小心，很容易安装恶意插件或删除数据库。

幸运的是，WordPress 可以通过配置和定制来拯救用户！这包括每个组织中那些大大高估自己技术能力的人…

## 1.授予适当的用户角色

WordPress 提供了一系列的角色和功能。在大多数情况下，用户应该是:

*   一个**编辑**:可以发布和管理自己和他人帖子的人。
*   an **Author** :可以发布和管理自己帖子的人。
*   a **投稿人**:可以撰写和管理自己的帖子但不能发布的人。

这些角色都不能安装插件、更改主题、批准更新、编辑文件或执行其他对**管理员**可用的危险任务。当然，一些用户会声称他们需要完全的权利——如果他们准备好冒风险并支付失去一切的代价，那也没什么！

## 2.为高级功能创建短代码

用户经常需要高级功能，如 Twitter 小部件、股票价格跟踪、会员链接等。与其让他们添加任意的第三方代码，不如允许他们通过插件或主题的**functions.php**文件中定义的短代码来调用你的代码:

```
// include a specific PHP file
function customIncludeFile($params = array()) {

	extract(shortcode_atts(array(
	    'file' => 'contact-form'
	), $params));

	ob_start();
	include(get_theme_root() . '/' . get_template() . "/$file.php");
	return ob_get_clean();
}

add_shortcode('include', 'customIncludeFile');
```

这个短代码允许用户在编辑器中输入`[include myfile]`来包含模板文件夹中的**myfile.php**。

## 3.删除 WordPress 更新通知

WordPress 更新通知可能对你有用，但是会让你的用户担心。在插件或主题的**functions.php**文件中禁用它:

```
// remove update notifications
<?php
function no_update_notification() {
	if (!current_user_can('activate_plugins')) remove_action('admin_notices', 'update_nag', 3);
}
add_action('admin_notices', 'no_update_notification', 1);
```

## 4.删除不必要的菜单

很少有网站使用 WordPress 的所有功能。例如，您的安装可能不需要注释功能。可以在插件或**functions.php**中使用以下代码删除不必要的项目:

```
// remove unnecessary menus
function remove_admin_menus () {
	global $menu;

	// all users
	$restrict = explode(',', 'Links,Comments');

	// non-administrator users
	$restrict_user = explode(',', 'Media,Profile,Users,Tools,Settings');

	// WP localization
	$f = create_function('$v,$i', 'return __($v);');
	array_walk($restrict, $f);
	if (!current_user_can('activate_plugins')) {
		array_walk($restrict_user, $f);
		$restrict = array_merge($restrict, $restrict_user);
	}

	// remove menus
	end($menu);
	while (prev($menu)) {
		$k = key($menu);
		$v = explode(' ', $menu[$k][0]);
		if(in_array(is_null($v[0]) ? '' : $v[0] , $restrict)) unset($menu[$k]);
	}

}
add_action('admin_menu', 'remove_admin_menus');
```

相应地设置以下变量:

*   **$restrict** —逗号分隔的菜单项列表，不会显示给任何用户，包括管理员。在上面的例子中，我们隐藏了链接和评论。
*   **$restrict_user** —逗号分隔的菜单项列表，不向非管理员显示。上面的例子禁用了除仪表板、页面和文章之外的所有内容。外观和插件也将默认隐藏非管理员。

## 5.删除不必要的页面和帖子元框

很少有人——甚至是管理员——需要页面、帖子和自定义帖子的所有可用选项。使用编辑屏幕右上角的**屏幕选项**菜单可以隐藏这些选项，但用户仍然可以重新启用选项。你可以通过添加下面的代码到一个插件或者 functions.php**来永久移除这些盒子:**

```
// remove unnecessary page/post meta boxes
function remove_meta_boxes() {

	// posts
	remove_meta_box('postcustom','post','normal');
	remove_meta_box('trackbacksdiv','post','normal');
	remove_meta_box('commentstatusdiv','post','normal');
	remove_meta_box('commentsdiv','post','normal');
	remove_meta_box('categorydiv','post','normal');
	remove_meta_box('tagsdiv-post_tag','post','normal');
	remove_meta_box('slugdiv','post','normal');
	remove_meta_box('authordiv','post','normal');

	// pages
	remove_meta_box('postcustom','page','normal');
	remove_meta_box('commentstatusdiv','page','normal');
	remove_meta_box('trackbacksdiv','page','normal');
	remove_meta_box('commentsdiv','page','normal');
	remove_meta_box('slugdiv','page','normal');
	remove_meta_box('authordiv','page','normal');

}
add_action('admin_init','remove_meta_boxes');
```

必要时添加或删除`remove_meta_box`行。第一个参数是分配给元盒的`div`元素的 ID——在 HTML 源代码中或使用开发工具找到它。

## 6.删除不必要的仪表板小部件

类似地，仪表板可以为一些用户提供一系列令人困惑的选项。使用以下插件或**functions.php**代码删除仪表板小部件:

```
// remove unnecessary dashboard widgets
function remove_dashboard_widgets(){

	global $wp_meta_boxes;

	// only remove "Right Now" for non-administrators
	if (!current_user_can('activate_plugins')) {
		unset($wp_meta_boxes['dashboard']['normal']['core']['dashboard_right_now']);
	}

	// remove widgets for everyone
	unset($wp_meta_boxes['dashboard']['normal']['core']['dashboard_plugins']);
	unset($wp_meta_boxes['dashboard']['normal']['core']['dashboard_recent_comments']);
	unset($wp_meta_boxes['dashboard']['normal']['core']['dashboard_incoming_links']);
	unset($wp_meta_boxes['dashboard']['side']['core']['dashboard_primary']);
	unset($wp_meta_boxes['dashboard']['side']['core']['dashboard_secondary']);

}
add_action('wp_dashboard_setup', 'remove_dashboard_widgets');
```

这可以根据需要进行配置。dashboard 小部件的 ID 被分配给它的`div`元素——在 HTML 源代码中或者使用开发工具找到它。

## 7.删除管理栏

登录后，WordPress 会在你的 live 站点顶部显示一个深灰色的管理栏。你可能觉得没什么用。或者你的用户认为所有的访问者都可以看到这个栏？你可以用你的插件中的一行 PHP 或者**functions.php**代码来删除它:

```
// remove admin bar
add_filter('show_admin_bar', '__return_false');
```

你有其他简化 WordPress 的建议吗？

## 分享这篇文章**