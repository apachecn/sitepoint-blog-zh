# 如何在 WordPress 中创建嵌套的短代码

> 原文：<https://www.sitepoint.com/wordpress-nested-shortcodes/>

当一个编辑添加一个[WordPress【short code】](https://www.sitepoint.com/wordpress-shortcodes-tutorial/)到一篇文章或一个页面时，它会被一个插件或主题的 functions.php 文件中的处理函数返回的输出所代替。让我们创建一个简单的例子:

```
 // create a styled button
function ContactButton($params, $content = null) {

	extract(shortcode_atts(array(
		'url' => '/contact-us',
		'type' => 'style1'
	), $params));

	return
		'<a href="' . $url . '" class="button ' . $type. '">' . ucwords($content) . '</a>';

}
add_shortcode('button','ContactButton'); 
```

当在页面或 post 中遇到以下代码时:

```
 [button]contact us today[/button] 
```

它会被翻译成:

```
 <a href="/contact-us" class="button style1">Contact Us Today</a> 
```

编辑的工作变得简单多了，他们不需要担心学习 HTML。让我们看看另一个创建简单标注框的示例:

```
 // callout box
function CalloutBox($params, $content = null) {

	extract(shortcode_atts(array(
		'type' => 'style1'
	), $params));

	return
		'<aside class="callout ' . $type . '">' . $content . '</aside>';

}
add_shortcode('callout','CalloutBox'); 
```

但是如果我们的编辑想要在他们的标注框中插入一个按钮呢？…

```
 [callout]For more information [button]contact us today[/button][/callout] 
```

按照现在的情况，当前代码将会失败。首先调用 CalloutBox 函数，但是内部的[按钮]不会相应地被翻译。

解决这个问题的关键函数是[do _ shortcode()](http://codex.wordpress.org/Function_Reference/do_shortcode)——它将 WordPress 的 short code 过滤器应用于任何内容。在这种情况下，我们希望允许编辑器在我们的[callout]中添加一个[button],这样我们就可以相应地修改 CalloutBox 的 return 语句:

```
 return
	'<aside class="callout ' . $type . '">' . 
	do_shortcode($content) . 
	'</aside>'; 
```

上面的嵌套代码现在将按预期工作。但是，不允许编辑器将[标注]嵌套在[按钮]中。正是这样的灵活性使得 WordPress 使用起来很愉快—[3.3 版本现在已经推出](https://www.sitepoint.com/wordpress-33-whats-new/)。

## 分享这篇文章