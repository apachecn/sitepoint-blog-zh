# 如何在 WordPress 中构建更好的标签云

> 原文：<https://www.sitepoint.com/better-wordpress-tag-cloud/>

一旦你为你的 WordPress 文章([或页面](https://www.sitepoint.com/wordpress-pages-use-tags/))定义了一组标签，你会想要在你的模板中的某个地方显示标签云。这通常是通过使用 [wp_tag_cloud()](http://codex.wordpress.org/Function_Reference/wp_tag_cloud) 或 [wp_generate_tag_cloud()](http://codex.wordpress.org/Function_Reference/wp_generate_tag_cloud) 函数来实现的，这些函数为您完成了繁重的工作:

```
<a href="http://www.mysite.com/tag/word" class="tag-link-7" title="1 topic" style="font-size: 8pt;">word</a>
<a href="http://www.mysite.com/tag/tag" class="tag-link-5" title="2 topics" style="font-size: 14.3pt;">tag</a>
<a href="http://www.mysite.com/tag/phrase" class="tag-link-6" title="4 topics" style="font-size: 22pt;">phrase</a>
<a href="http://www.mysite.com/tag/subject" class="tag-link-4" title="1 topic" style="font-size: 8pt;">subject</a> 
```

也许你对此很满意。我不是…

1.  内嵌样式？我们不是在 1998 年抛弃了那些吗？
2.  这些课程毫无意义。我可能永远不需要设计一个单独的标签，即使我需要，通过 ID 引用它也是脆弱的。
3.  我不需要完全限定的 URL。

提供多种选择，但我想要更多的控制！除了解决上述问题之外，我还想根据标签的受欢迎程度将它们分为五六类，例如“标签 1”代表最少使用的标签，而“标签 5”代表最多使用的标签。

让我们编写一个 PHP 函数来返回定制的标签云。它可以放在你的主题的 functions.php 文件(WP-content/themes/<themename>/functions . PHP)或者一个插件中。</themename>

首先，我们有一个函数名，它接受一组命名参数并设置默认值:

```
// generate tag cloud
function My_TagCloud($params = array()) {

	extract(shortcode_atts(array(
		'orderby' => 'name',		// sort by name or count
		'order' => 'ASC',		// in ASCending or DESCending order
		'number' => '',			// limit the number of tags
		'wrapper' => '',		// a tag wrapped around tag links, e.g. li
		'sizeclass' => 'tagged',	// the tag class name
		'sizemin' => 1,			// the smallest number applied to the tag class
		'sizemax' => 5			// the largest number applied to the tab class
	), $params)); 
```

我们现在初始化`$ret`，我们返回的 HTML，以及`$min`和`$max`——一个标签被使用的最小和最大次数:

```
 // initialize
	$ret = '';
	$min = 9999999; $max = 0; 
```

现在调用了函数 get_tags() 。它返回一个标记对象数组:

```
 // fetch all WordPress tags
	$tags = get_tags(array('orderby' => $orderby, 'order' => $order, 'number' => $number)); 
```

我们现在需要确定一个标签在我们的站点中使用的最小和最大次数。以下循环相应地设置`$min`和`$max`:

```
 // get minimum and maximum number tag counts
	foreach ($tags as $tag) {
		$min = min($min, $tag->count);
		$max = max($max, $tag->count);
	} 
```

我们现在可以创建我们的自定义标签云 HTML。我们需要再次遍历所有标签，并获取 URL 和链接标题——一条指示有多少文章使用了该标签的消息:

```
 // generate tag list
	foreach ($tags as $tag) {
		$url = get_tag_link($tag->term_id);
		$title = $tag->count . ' article' . ($tag->count == 1 ? '' : 's'); 
```

现在是棘手的部分。默认情况下，我们希望为最少使用的标记分配一个类“tagged1 ”,为最常用的标记分配一个类“tagged5 ”(调用函数时，可以通过设置`sizeclass`、`sizemin`和`sizemax`参数来覆盖类名和编号)。

我们知道一个标签可以被使用的最小和最大次数，所以一点数学就可以决定我们的类名。然而，如果每个标签只被使用一次，这个等式将导致被零除的错误。在这种情况下，我们将类设置为`$sizeclass`:

```
 if ($max > $min) {
			$class = $sizeclass . floor((($tag->count - $min) / ($max - $min)) * ($sizemax - $sizemin) + $sizemin);
		}
		else {
			$class = $sizeclass;
		} 
```

我们现在有足够的信息来为我们的单个标签创建 HTML 并结束循环:

```
 $ret .= 
			($wrapper ? "<$wrapper>" : '') . 
			"<a href="$url" class="$class" title="$title">{$tag->name}</a>" .
			($wrapper ? "</$wrapper>" : '');
	} 
```

最后，我们从`$ret`中删除博客域 URL，返回值并完成功能块:

```
 return str_replace(get_bloginfo('url'), '', $ret);
} 
```

使用`My_TagCloud();`可以在任何主题文件中调用该函数。参数可以作为关联数组传递，例如`My_TagCloud(array('orderby'=>'count','order'=>'DESC'));`。然而，我们也可以允许内容编辑者使用 WordPress shortcode 添加标签云，例如

```
// enable [tagcloud] shortcode
add_shortcode('tagcloud', 'My_TagCloud'); 
```

在下面的例子中，我们将在一个无序列表中创建一个标签云:

```
$tags = OW_Tags(array('wrapper' => 'li'));
if ($tags) {
	echo "<h2>Tags</h2>n<ul class="tagcloud">$tags</ul>n";
} 
```

这会产生更加整洁的 HTML 代码:

```
<h2>Tags</h2>
<ul class="tagcloud">
<li><a href="/tag/word" class="tagged1" title="1 article">word</a></li>
<li><a href="/tag/tag" class="tagged2" title="2 articles">tag</a></li>
<li><a href="/tag/phrase" class="tagged5" title="4 articles">phrase</a></li>
<li><a href="/tag/subject" class="tagged1" title="1 article">subject</a></li>
</ul> 
```

CSS 的样式和维护哪个更容易:

```
ul.tagcloud, ul.tagcloud li
{
	font-size: 1em;
	list-style-type: none;
	padding: 0;
	margin: 0;
}

ul.tagcloud li
{
	display: inline;
}

ul.tagcloud a
{
	text-decoration: none;
	padding: 3px 4px;
}

a.tagged1 { font-size: 0.60em; }
a.tagged2 { font-size: 0.80em; }
a.tagged3 { font-size: 1.00em; }
a.tagged4 { font-size: 1.20em; }
a.tagged5 { font-size: 1.40em; } 
```

希望你觉得有用。请在你自己的 WordPress 项目中使用和修改你喜欢的代码。

## 分享这篇文章