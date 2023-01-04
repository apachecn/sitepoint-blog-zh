# 如何创建你自己的 WordPress 简码

> 原文：<https://www.sitepoint.com/wordpress-shortcodes-tutorial/>

WordPress 通常不允许你在页面或文章中添加 PHP 代码。那是最好的；你不希望客户发现 [unlink](http://php.net/manual/en/function.unlink.php) 函数的威力！但是，您可以创建自定义函数，在 post 文本中遇到短代码时执行这些函数。

## 简单短码

短代码功能可以添加到插件代码或你的主题的 functions.php 文件。如果是后者，我建议创建一个单独的 shortcodes.php 文件，然后将`include('shortcodes.php');`添加到 functions.php。

下面是一个基本的“Hello World”示例:

```
 function HelloWorldShortcode() {
	return '<p>Hello World!</p>';
}
add_shortcode('helloworld', 'HelloWorldShortcode'); 
```

在页面或帖子中的某个位置输入`[helloworld]`,输出 HelloWorldShortcode()函数的结果。

## 参数化短代码

下面的 shortcode 函数生成一个页面层次结构站点地图。可以传递三个可选参数:标题、结果列表的 ID 和表示页面导航级别的深度值。

```
 function GenerateSitemap($params = array()) {

	// default parameters
	extract(shortcode_atts(array(
		'title' => 'Site map',
		'id' => 'sitemap',
	    'depth' => 2
	), $params));

	// create sitemap
	$sitemap = wp_list_pages("title_li=&depth=$depth&sort_column=menu_order&echo=0");
	if ($sitemap != '') {
		$sitemap =
			($title == '' ? '' : "<h2>$title</h2>") .
			'<ul' . ($id == '' ? '' : " id="$id"") . ">$sitemap</ul>";
	}

	return $sitemap;
}
add_shortcode('sitemap', 'GenerateSitemap'); 
```

一个定制的站点地图可以被添加到任何使用短代码的页面上，比如`[sitemap id='deepmap',depth=5]`。

## BB 码短码

添加短代码的最后一种方法是使用[bbcode]BB code 语法[/bbcode]:

```
 function StyleText($params, $content = null) {

	// default parameters
	extract(shortcode_atts(array(
		'style' => ''
	), $params));

  return
	'<span' .
	($style == '' ? '' : " style="$style"") .
	">$content</span>";
}
add_shortcode('format','StyleText'); 
```

该功能允许作者在文章中嵌入 CSS 样式，例如`[format style="font-size:1.5em;color:#f00;">Important![/format]`。也许这不是一个好主意！…

你在 WordPress 项目中看到过什么有趣的短码用法吗？

## 分享这篇文章