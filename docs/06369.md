# 如何给你的 WordPress 站点添加一个高级搜索

> 原文：<https://www.sitepoint.com/add-advanced-search-wordpress-site/>

大多数 WordPress 搜索表单都设置了一个名为“s”的 querystring 参数:

```
<form method="get" action="<?php bloginfo('url'); ?>">
<fieldset>
<input type="text" name="s" value="" placeholder="search&hellip;" maxlength="50" required="required" />
<button type="submit">Search</button>
</fieldset>
</form>
```

这没有错，我认为这是很多很多年来唯一的选择。

然而，没有插件的帮助，更高级的查询也是可能的(尽管[有很多可用的](http://wordpress.org/plugins/tags/better-search))。在 WordPress 核心内部，应用程序解析其他几个 querystring 参数，并使用它们返回一组更集中的搜索结果。有一些[基本文档](http://codex.wordpress.org/WordPress_Query_Vars)列出了参数名称:

*   附件
*   附件 id
*   作者
*   作者姓名
*   猫
*   类别名称
*   评论 _ 弹出窗口
*   天
*   错误
*   饲料
*   小时
*   m
*   分钟
*   蒙特努姆
*   名字
*   p
*   page_id
*   分页的
*   页面名称
*   post_parent
*   post_type
*   试映
*   第二
*   静电
*   子帖子
*   subpost_id
*   标签
*   标签标识
*   肺结核
*   w
*   年

我不确信所有这些工作都像预期的那样，有些有点无意义，但它们与您可以传递给 [WP_Query](http://codex.wordpress.org/Class_Reference/WP_Query) 的参数相匹配。因此，我们可以创建一个高级搜索表单，使用 HTML 和少量的 PHP 来自动化选项。

## 按类别细化搜索

您可以通过将结果的 slug 传递给`category_name`参数来将结果限制在一个类别中，例如

```
http://yoursite.com/?s=search+term&category_name=kittens
```

我们的搜索表单允许用户将搜索细化到特定类别:

```
<form method="get" action="<?php bloginfo('url'); ?>">
<fieldset>
<input type="text" name="s" value="" placeholder="search&hellip;" maxlength="50" required="required" />
<select name="category_name">
<option value="">all categories</option>
<option value="kittens">cute kittens</option>
<option value="puppies">adorable puppies</option>
</select>
<button type="submit">Search</button>
</fieldset>
</form>
```

如果您想列出所有类别，请在`<select>`和`</select>`标签之间添加以下代码:

```
<?php
// generate list of categories
$categories = get_categories();
foreach ($categories as $category) {
	echo '<option value="', $category->slug, '">', $category->name, "</option>\n";
}
?>
```

## 按标签细化搜索

通过将标签的 slug 传递给`tag`参数，可以将搜索结果限制为一个标签，例如

```
http://yoursite.com/?s=search+term&tag=cockroach
```

因此，您的搜索结果可能会局限于某些标签，例如

```
<form method="get" action="<?php bloginfo('url'); ?>">
<fieldset>
<input type="text" name="s" value="" placeholder="search&hellip;" maxlength="50" required="required" />
<select name="tag">
<option value="">any tag</option>
<option value="cockroach">cockroaches</option>
<option value="snake">snakes</option>
</select>
<button type="submit">Search</button>
</fieldset>
</form>
```

类似地，您可以为`select`字段生成所有标签的列表:

```
<?php
// generate list of tags
$tags = get_tags();
foreach ($tags as $tag) {
	echo '<option value="', $tag->slug, '">', $tag->name, "</option>\n";
}
?>
```

## 推进高级搜索

如果您想通过多个值来细化搜索，该怎么办？例如，用户可以选择两个或更多的标签，结果页面必须将它们都设置好。我们不能只使用 URL 参数来实现这一点，但是让我们从定义一个 HTML 搜索表单开始:

```
<form method="get" action="<?php bloginfo('url'); ?>">
<fieldset>
<input type="text" name="s" value="" placeholder="search&hellip;" maxlength="50" required="required" />
<p>Refine search to posts containing chosen tags:</p>
<?php
// generate list of tags
$tags = get_tags();
foreach ($tags as $tag) {
	echo 
		'<label>',
		'<input type="checkbox" name="taglist[]" value="',  $tag->slug, '" /> ',
		$tag->name,
		"</label>\n";
}
?>
<button type="submit">Search</button>
</fieldset>
</form>
```

注意，我使用了一个名为`taglist`的 PHP 数组参数。你可以使用 WordPress 已经保留的名字以外的任何名字(见上面的列表)。

我们现在可以在 WordPress 主题的 functions.php 文件中截取一个搜索提交。`advanced_search_query`函数检测搜索是否有效，然后相应地设置 WP_Query `tag_slug__and`参数。

```
// advanced search functionality
function advanced_search_query($query) {

	if($query->is_search()) {

		// tag search
		if (isset($_GET['taglist']) && is_array($_GET['taglist'])) {
			$query->set('tag_slug__and', $_GET['taglist']);
		}

		return $query;
	}

}
```

最后，在执行查询之前，我们使用`pre_get_posts`操作钩子来运行我们的`advanced_search_query`函数:

```
add_action('pre_get_posts', 'advanced_search_query', 1000);
```

向 WordPress 添加高级搜索工具非常容易，但是很少有开发者意识到这是可能的……*也许是因为文档和例子有点少*。我偶然发现了它，所以我希望你在下一个 WordPress 项目中发现这段代码有用。

## 分享这篇文章