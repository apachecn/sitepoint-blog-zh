# 如何改变 WordPress 文章中的图片属性

> 原文：<https://www.sitepoint.com/wordpress-change-img-tag-html/>

WordPress 允许用户将图片放入页面或发布内容。通常，会生成以下 HTML 代码:

```
 <img src="http://www.mysite.com/wp-content/uploads/2011/07/myimage.jpg" 
alt="my image" title="my image title" 
width="100" height="100" class="aligncenter size-full wp-image-123" /> 
```

没什么太可怕的，但是如果你想:

*   添加您自己的类
*   从 URL 中删除域名(除非您使用 RSS 源，否则可能没有必要)
*   在响应式设计中删除缩放图像的宽度和高度属性？

幸运的是，WordPress 提供了一些过滤功能来帮助你修改 HTML。它们可以放在你的主题的 functions.php 文件中或者插件中的[。](https://www.sitepoint.com/wordpress-easy-administration-plugin-1/)

**important:** <img> code is created on insert

与其他 WordPress 内容过滤器不同，标签是在编辑将图片插入文章时生成的。因此，这些功能不会改变已经添加到帖子中的图像的代码。

因此，测试代码的唯一方法是重复添加和删除图像。我建议切换到 WordPress 的 HTML 视图来查看结果。

## 更改图像类

最简单和最常用的过滤器是用于图像类的:

```
 function image_tag_class($class, $id, $align, $size) {
	return $align;
}
add_filter('get_image_tag_class', 'image_tag_class', 0, 4); 
```

image_tag_class()函数接受 4 个字符串参数:现有的类、图像 ID(数字)、对齐方式(无、左、右、居中)和大小(通常为“满”)。它返回一个字符串，这个字符串将成为标签类。在这个例子中，我们只是返回对齐字符串。

## 高级图像修改

如果我们需要对标签进行根本性的修改，我们需要一个过滤器，它可以使用字符串或正则表达式替换来直接修改返回的 HTML，例如

```
 function image_tag($html, $id, $alt, $title) {
	return preg_replace(array(
			'/'.str_replace('//','//',get_bloginfo('url')).'/i',
			'/s+width="d+"/i',
			'/s+height="d+"/i',
			'/alt=""/i'
		),
		array(
			'',
			'',
			'',
			'alt="' . $title . '"'
		),
		$html);
}
add_filter('get_image_tag', 'image_tag', 0, 4); 
```

image_tag()函数接受 4 个字符串参数:生成的 HTML、图像 ID(数字)、alt 和标题文本。在这个例子中，我们从 src URL 中剥离了域名，删除了高度和宽度属性，并用标题文本替换了空的 alt 属性。您可以根据自己的替换相应地更改功能。

虽然 HTML 标签修改不是你每天都需要的，但是知道 WordPress 支持这个特性是很有用的。

## 分享这篇文章