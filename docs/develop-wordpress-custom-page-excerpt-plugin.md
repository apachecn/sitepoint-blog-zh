# 如何为 WordPress 开发一个自定义的页面摘录插件

> 原文：<https://www.sitepoint.com/develop-wordpress-custom-page-excerpt-plugin/>

在 WordPress 中显示文章列表很容易。你的主题的 index.php 文件可能会处理你所有的类别、作者和搜索请求。然而，有时你需要更多一点的控制。也许您有多个产品页面，但只想在主页上显示两三个，而不需要手动重新输入详细信息。

在本教程中，我们将开发一个小的 WordPress 插件，它将一个[shortcode]替换成一个特定页面或文章的链接。该链接将显示标题，缩略图和摘录，但你可以配置它来显示你需要的任何细节。

## 创建插件

在你的插件文件夹(wp-content/plugins)中创建一个名为**showexcerptlink.php**的新文件，并添加一个标题，这样它可以被 WordPress 识别:

```
 <?php
/*
Plugin Name: Show Page/Post Excerpt
Plugin URI: https://www.sitepoint.com/
Description: Replaces a shortcode with a link to a specific page or post
Version: 1.0
Author: Craig Buckler
Author URI: http://optimalworks.net/
License: Use this how you like!
*/ 
```

接下来是我们的主要函数 ShowExcerptLink()。我们需要一个参数:slug/permalink 名称。虽然我们可以传递 ID，但 slug 是一个更好的选择，因为它不太可能在您编辑页面时发生变化。您可以添加更多的参数来控制 HTML 输出。

```
 function ShowExcerptLink($params = array()) {

	extract(shortcode_atts(array(
		'slug' => ''
	), $params));

	$html = '';
	if ($slug == '') return $html; 
```

我们现在可以使用 WordPress WP_Query 对象查找页面或文章。下面的代码试图查找页面段塞，但是如果不存在，它将查找 post 段塞:

```
 $q = new WP_Query("pagename=$slug");
	if (!$q->have_posts()) {
		$q = new WP_Query("name=$slug");
	} 
```

我们现在可以开始一个 WordPress 循环——尽管它只有零个或一个帖子:

```
 // the loop
	while ($q->have_posts()) {

		$q->the_post(); 
```

HTML 输出现在在$html 字符串中生成。你可以使用在 WordPress 循环中常见的任何标准函数。在本例中，我们将输出标题(H2)、缩略图和摘录:

```
 // generate HTML
		$link = '<a href="' . get_permalink() . '>';

		$html .=
			'<h2>' . $link . the_title('','',false) . "</a></h2>n" .
			(has_post_thumbnail() ? 
				$link . get_the_post_thumbnail() . '</a>' : ''
			) .
			get_the_excerpt(); 
```

*(这段代码在任何版本的 HTML 中都有效，尽管 HTML5 允许锚围绕着
整个块。)*

我们现在可以结束循环，返回 HTML 字符串并完成函数:

```
 }

	return $html;
} 
```

最后，我们将把我们的函数注册为短代码处理程序:

```
 // register shortcode
add_shortcode('showexcerptlink', 'ShowExcerptLink'); 
```

保存文件并在你的 WordPress 控制面板中激活插件。

## 显示摘录

现在可以将以下短代码添加到任何页面或帖子的内容中:

```
 [showexcerptlink slug=page-or-post-slug-name] 
```

如果你引用了一个有一个或多个父页面的页面，这些 slugs 必须用正斜杠分开，例如

```
 [showexcerptlink slug=grand-parent-slug/parent-slug/page-slug] 
```

请随意使用和修改代码。

## 分享这篇文章