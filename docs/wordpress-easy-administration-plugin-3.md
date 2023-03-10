# 如何通过删除不必要的菜单选项使 WordPress 对客户来说更容易

> 原文：<https://www.sitepoint.com/wordpress-easy-administration-plugin-3/>

![WordPress menu](img/9207427d79bd7089775ce1194381363e.png)

在我之前的 WordPress 帖子中，我们发现了如何[创建插件](https://www.sitepoint.com/wordpress-easy-administration-plugin-1)，[改变管理面板品牌](https://www.sitepoint.com/wordpress-easy-administration-plugin-1)，以及[移除不必要的仪表盘小工具和元框](https://www.sitepoint.com/wordpress-easy-administration-plugin-2)。

在这篇文章中，我们将对主管理菜单做一些根本性的改变。如果你还没有创建一个初始插件，请阅读第一部分。欢迎回来——让我们开始…

标准的 WordPress 菜单可能有点令人生畏——第三方插件经常会添加更多的项目。你可以限制用户角色，这样客户就看不到所有的菜单项，但是，除非你使用 WordPress 的所有功能，否则他们仍然会看到不适用于他们网站的选项。

我们将创建一个功能，删除多余的链接，简化客户的体验。下面是可以复制到 easy-admin.php 中的完整代码:

```
 // remove unnecessary menus
function remove_admin_menus() {

	global $menu, $submenu;

	// main menus removed for all users
	$restrict = explode(',', 'Links,Comments');

	// sub-menus removed for all users
	$restrictsub = explode(',', 'Categories,Post Tags');

	// main menus removed for everyone except administrators
	$restrict_user = explode(',', 'Media,Profile,Appearance,Plugins,Users,Tools,Settings');

	// sub-menus removed for everyone except administrators
	$restrictsub_user = explode(',', 'Updates,My Sites');

	// WP localization
	$f = create_function('$v,$i', 'return __($v);');
	array_walk($restrict, $f);
	if (!current_user_can('activate_plugins')) {
		array_walk($restrict_user, $f);
		$restrict = array_merge($restrict, $restrict_user);
		array_walk($restrictsub_user, $f);
		$restrictsub = array_merge($restrictsub, $restrictsub_user);
	}

	// remove menus
	end($menu);
	while (prev($menu)) {
		$k = key($menu);
		$v = explode(' ', $menu[$k][0]);
		if(in_array(is_null($v[0]) ? '' : $v[0] , $restrict)) unset($menu[$k]);
	}

	// remove sub-menus
	foreach ($submenu as $k => $p) {
		foreach($submenu[$k] as $j => $s) {
			if (in_array(is_null($s[0]) ? '' : $s[0] , $restrictsub)) unset($submenu[$k][$j]);
		}
	}

}
add_action('admin_menu', 'remove_admin_menus'); 
```

该功能顶部的行决定了哪些菜单项被删除:

*   **$restrict** (第 5 行)包含一个逗号分隔的主菜单项列表，它不会显示给任何用户，包括管理员。在上面的例子中，我们隐藏了链接和评论，因为它们不在我们的网站中使用。
*   **$restrictsub** (第 7 行)包含一个逗号分隔的子菜单项列表，它不会显示给任何用户。我们禁用了通常出现在主文章菜单中的类别和文章标签。
*   **$restrict_user** (第 9 行)包含一个逗号分隔的主菜单项列表，除了管理员之外，其他人*看不到这个列表。上面的示例禁用了除仪表板、页面和帖子之外的所有内容。(非管理员通常不会看到外观和插件，但其他插件可以改变功能)。*
*   **$restrictsub_user** (第 11 行)包含一个逗号分隔的子菜单项列表，除了管理员之外，所有人*都看不到这个列表。我们禁用了通常出现在主仪表板菜单中的更新和我的网站。*

![simplified WordPress menu](img/e243fbd1152156e0d3ba428a5f002451.png)

如果您不想删除某个特定值的任何项目，请将其设置为空数组，例如`$restrict = array();`

结果是一个简单得多的管理菜单，没有会让你的客户困惑的危险选项。

## 分享这篇文章