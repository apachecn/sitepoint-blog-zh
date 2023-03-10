# 如何在 WordPress 页面中启用标签

> 原文：<https://www.sitepoint.com/wordpress-pages-use-tags/>

自从 2007 年发布 2.3 版本以来，WordPress 一直支持标签。如果使用得当，它们可能是比类别或菜单更有效的导航形式。但是为什么 WordPress 页面从来没有标签呢？

幸运的是，WordPress 提供了工具来帮助我们在页面和任何其他类型的文章中启用标签。在表面之下，WordPress 以几乎相同的方式对待页面、帖子和其他内容；页面只是一个自定义的文章类型。因此，启用标签只是简单地说一句*“嘿，WordPress，我想在我的页面上使用标签，别忘了将它们包含在标签云中！”*

让我们把它转换成可以插入主题的 functions.php 文件(WP-content/themes/<themename>/functions . PHP)的代码:</themename>

```
// add tag support to pages
function tags_support_all() {
	register_taxonomy_for_object_type('post_tag', 'page');
}

// ensure all tags are included in queries
function tags_support_query($wp_query) {
	if ($wp_query->get('tag')) $wp_query->set('post_type', 'any');
}

// tag hooks
add_action('init', 'tags_support_all');
add_action('pre_get_posts', 'tags_support_query'); 
```

简单。如果您有更多需要标签的自定义 post 类型，您需要为每个类型添加`register_taxonomy_for_object_type`调用——第二个参数是类型名称。

那些运行几个 WordPress 站点或网络的人可能会发现将代码转换成插件更容易，因此可以相应地启用和禁用它。本质上，就是将上面的代码添加到一个适当命名的插件文件中，即 WP-content/plugins/enable-tags . PHP，并在顶部放置注释:

```
<?php
/*
Plugin Name: Enable Tags in WordPress Pages
Plugin URI: https://www.sitepoint.com/
Description: Enables tags in all content
Version: 1.0
Author: Craig Buckler
Author URI: http://twitter.com/craigbuckler
License: Free to use and adapt
*/

// add tag support to pages
// ... rest of code ... 
```

希望你觉得有用。请在您自己的项目中随意使用和修改这些代码——欢迎链接到本文。

## 分享这篇文章