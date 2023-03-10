# 如何让 WordPress 对客户更容易，第 2 部分:隐藏菜单

> 原文：<https://www.sitepoint.com/how-to-hide-menus-in-wordpress/>

在这个系列的第一部分中，我们发现为客户简化[界面是多么容易。](http://wordpress.org/)

在本文中，我描述了一种隐藏不必要菜单的技术。我们为什么要这么做？你的客户选择越少，你需要解释的就越少，他们需要记住的就越少，他们做一些意想不到的事情的可能性就越小。

在你添加任何代码之前，我建议你检查一下你的用户角色。我怀疑大多数开发人员将他们的客户添加为编辑(发布和管理所有帖子)或作者(发布和管理他们自己的帖子)。避免将任何人添加为管理员，除非他们明确要求该控制级别。

![Easier WordPress](img/4c775f060d1142b64de5eca09fd0f442.png)我还建议你以该用户身份登录，并使用**屏幕选项**下拉菜单删除任何不必要的项目。您的客户端可能不需要使用标签、slugs 或自定义字段等功能。

现在记下客户不需要的菜单。配置文件、工具和设置等选项是显而易见的选择。也许该网站不使用链接或评论？或者你的客户应该能够管理文章，但不能管理静态页面？

好了，让我们给你的主题的**function.php**文件添加一点代码。不确定那是什么？看一看第一部分[。准备好了吗？代码如下:](https://www.sitepoint.com/blogs/make-wordpress-easier-for-clients-branding/)

```
 // remove unnecessary menus
function remove_admin_menus () {
	global $menu;

	// all users
	$restrict = explode(',', 'Links,Comments');

	// non-administrator users
	$restrict_user = explode(',', 'Media,Profile,Appearance,Plugins,Users,Tools,Settings');

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

您应该编辑两行:

*   **$restrict** (第 5 行)包含一个逗号分隔的菜单项列表，它不会显示给任何用户，包括管理员。在上面的例子中，我们隐藏了链接和评论，但是你的需求可能不同。
*   **$restrict_user** (第 8 行)包含一个逗号分隔的菜单项列表，该列表不会向非管理员显示。上面的例子禁用了除仪表板、页面和文章之外的所有内容。(请注意，非管理用户通常不会看到外观和插件，但其他插件可能会改变功能。)

我希望你和你的客户会觉得有用。你对简化 WordPress 有进一步的建议吗？

## 分享这篇文章