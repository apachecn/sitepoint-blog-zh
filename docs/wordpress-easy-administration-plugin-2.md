# 如何通过移除小部件、元框和选项使 WordPress 更简单

> 原文：<https://www.sitepoint.com/wordpress-easy-administration-plugin-2/>

在我之前的文章中，我们创建了一个新的 [WordPress 插件，为你的客户简化了管理面板](https://www.sitepoint.com/wordpress-easy-administration-plugin-1)。如果你没有读过，请先阅读。在本文中，我们将使用相同的插件文件进行更深入的配置更改。

## 删除 WordPress 更新通知

当有可用的更新时，WordPress 会通知您。不幸的是，它告诉所有人——包括你的客户。这可能会导致不必要的担心，或者诱使他们每半小时给你打一次电话，直到升级为止。

将以下代码添加到 easy-admin.php，删除除 WordPress 管理员之外的所有人的通知:

```
 function no_update_notification() {
	if (!current_user_can('activate_plugins')) remove_action('admin_notices', 'update_nag', 3);
}
add_action('admin_notices', 'no_update_notification', 1); 
```

## 删除不必要的仪表板小部件

您可以通过以用户身份登录并取消选中“屏幕选项”下拉面板中的项目来删除用户的仪表板小部件。然而，这可能并不实际，也没有什么可以阻止您的客户端重新启用它们。

将以下函数添加到 easy-admin.php 中，以删除不必要的仪表板小部件。您可能需要根据需要添加、删除或修改未设置的命令。例如，第一部分(第 5-7 行)删除了除 WordPress 管理员之外的所有人的“现在”。第二部分(第 9-13 行)删除小部件，而不考虑用户的权限。

仪表板小部件的 ID 被分配给它的 box `div`元素——使用 Firebug 或检查源代码来找到该值。

```
 // remove unnecessary dashboard widgets
function remove_dashboard_widgets(){

	global $wp_meta_boxes;

	// do not remove "Right Now" for administrators
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

## 删除不必要的页面和帖子元框

很少有开发者会使用 WordPress 提供的所有功能。例如，如果所有文章都被分配到一个默认类别，则不需要类别框。或者您可能不允许评论，并可以删除相关的框。

将下面的函数添加到 easy-admin.php 中，从帖子和页面面板中删除不必要的元框。您可能需要在此函数中添加或删除 remove_meta_box()调用。第一个参数是分配给盒子的`div`元素的 ID——同样，这可以在源代码中或使用 Firebug 发现。

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

## 移除收藏的动作

收藏夹操作按钮位于“你好”消息旁边的标题中。它通常提供新帖子、草稿、新页面、上传的快速链接，也许还有一些插件特有的选项，比如“空缓存”。让我们通过将以下代码附加到 easy-admin.php 来删除我们不需要的选项:

```
 // remove favorite actions
function remove_favorite_actions($actions) {

	if (!current_user_can('activate_plugins')) { 
		unset($actions['edit-comments.php']);
	}	

	return $actions;
}
add_filter('favorite_actions', 'remove_favorite_actions'); 
```

在本例中，我们删除了除管理员之外的所有人的评论链接。要删除其他项目，您需要在页面源中找到该操作的 URL。找到 ID 为“favorite-actions”的元素，并在其中找到 ID 为“favorite-inside”的元素。子进程`div`包含到 URL 的链接，比如“media-new.php”。要删除该选项，只需在函数中添加`unset($actions['media-new.php']);`。

唷。在我的下一篇 WordPress 文章中，我们将解决 WordPress 菜单并移除所有你想对客户隐藏的危险选项。

## 分享这篇文章