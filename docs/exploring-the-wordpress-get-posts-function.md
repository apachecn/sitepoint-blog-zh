# 探索 WordPress get_posts 函数

> 原文：<https://www.sitepoint.com/exploring-the-wordpress-get-posts-function/>

许多 WordPress 插件通过定制排序顺序从数据库中检索文章，根据特定的元关键字或分类法检索文章。你有没有想过这些插件是如何在不编写任何 SQL 查询的情况下检索定制的帖子列表的？在本教程中，我们将学习如何做到这一点。

![XML-RPC for WordPress](img/7c81df121ce03113683306b6ad807c97.png)

在本文中，我们将探索`get_posts()`函数，并通过一些例子说明如何在自己的项目中使用它。我们还将介绍这个函数的一些典型用例，以及它与`WP_Query`对象和`get_pages`函数的不同之处。

## `get_posts()`功能是什么？

从 WordPress 1.2.0 开始，WordPress 核心就有了`get_posts`功能。这个函数主要用于从数据库中检索文章，方法是应用自定义过滤器，并根据一组参数对最终结果进行排序。

`get_posts()`函数返回一组`WP_Post`对象。每个`WP_Post`对象代表一个单独的帖子。

在内部，`get_posts`使用`WP_Query`对象基于传递的参数集构建和执行 SQL 查询。

**注:帖子是指帖子、页面和自定义帖子类型。**

## 为什么不直接使用`WP_Query`对象？

插件使用`get_posts`函数而不是`WP_Query`对象，因为使用`WP_Query`对象会直接改变主循环(即全局`$wp_query`变量)，这会导致站点问题。

## `get_posts()`和`get_pages()`功能有什么区别？

它们都是用来从 WordPress 数据库中检索文章的，然而，这里有一些它们之间的区别:

*   它们之间有几个参数名称和值不同。尽管不管参数的名称如何，它们的行为都是一样的。
*   `get_pages()`功能目前不检索基于`meta_key`和`meta_value`参数的帖子。
*   `get_pages()`函数不使用`WP_Query`对象。相反，它直接构造和执行 SQL 查询。

## `get_posts()`功能参数

`get_posts`函数只接受一个参数作为数组。该数组包含应用自定义过滤器和对结果进行排序的不同参数。

下面是一个代码示例，展示了如何调用该函数以及各种可用的参数:

```
<?php 

$args = array(
	"posts_per_page"   => 5,
	"paged"            => 1
	"tax_query" => array(
		array(
			"taxonomy" => "category",
			"field"    => "slug",
			"terms"    => "videos,movies",
		)
	),
	"orderby"          => "post_date",
	"order"            => "DESC",
	"exclude"          => "1123, 4456",
	"meta_key"         => "",
	"meta_value"       => "",
	"post_type"        => "post",
	"post_status"      => "publish"
);

$posts_array = get_posts($args); 

?>
```

还有更多可用的参数，但这些是最常用的参数。让我们来看看这些参数中的每一个:

1.  **posts_per_page** :该参数定义返回的帖子数量。如果想要所有的帖子，请使用-1。
2.  **分页**:允许我们使用`posts_per_page`参数在一组文章之间导航。它用于分页。例如:假设`posts_per_page`是 10，结果中有 20 篇文章，那么如果你将`paged`赋值为 2，那么最后 10 篇文章将被返回。
3.  **tax_query** :显示一个特定分类 slug 的帖子，即过滤掉另一个分类 slug 的帖子。`terms`可以用一个逗号分隔的字符串代表多个分类 slugs。
4.  **orderby** :用于对检索到的帖子进行排序。一些可能的值是:“无”、“日期”、“rand”、“注释计数”、“元值”、“元值数量”等。使用“meta_value”和“meta_value_num”排序时，您需要提供`meta_key`参数。
5.  **顺序**:指定`orderby`参数的升序或降序。可能的值是“DESC”或“ASC”。
6.  **exclude** :它接受一个逗号分隔的文章 id 列表，该列表将被排除在数据库搜索之外。
7.  **meta_key** 和 **meta_value** :如果你只提供`meta_key`，那么拥有该键的帖子将被返回。如果您还提供了`meta_value`，那么将返回与`meta_key`的`meta_value`匹配的帖子。
8.  **post_type** :根据帖子、页面或自定义帖子类型检索内容。记住默认的`post_type`只设置显示文章而不显示页面。
9.  **post_status** :根据帖子的状态检索帖子。可能的值有:“发布”、“待定”、“草稿”、“未来”、“任何”或“垃圾”。

## `WP_Post`物体

`get_posts`函数返回一个包含`WP_Post`对象的数组。以下是`WP_Post`对象的重要属性:

1.  **ID** :帖子的 ID
2.  **帖子 _ 作者**:帖子的作者姓名
3.  **帖子类型**:帖子的类型
4.  **帖子 _ 标题**:帖子的标题
5.  **post_date** :帖子发布的日期。格式:0000-00-00 00:00:00
6.  **post_content** :帖子的内容。
7.  **post_status** :帖子的状态
8.  **comment_count** :帖子的评论数

## `get_posts`的例子

让我们看看一些使用`get_posts`函数的例子。

### 最受欢迎的帖子

如果你想显示站点上讨论最多的前 n 个帖子，那么你可以使用`get_posts`来检索它们。这里有一个例子:

```
<?php
	$args = array("posts_per_page" => 10, "orderby" => "comment_count");
	$posts_array = get_posts($args);
	foreach($posts_array as $post)
	{
	  echo "<h1>" . $post->post_title . "</h1><br>";
	  echo "<p>" . $post->post_content . "</p><br>";
	} 
?>
```

这里，我们使用`orderby`参数根据评论的数量对文章进行排序，检索前 10 篇文章。

### 随机帖子

您还可以轻松地检索随机帖子。这有助于在用户阅读完当前文章后，向他们推荐你网站上的另一篇文章。这是它的代码:

```
<?php
  $args = array("posts_per_page" => 1, "orderby" => "rand");
  $posts_array = get_posts($args);
  foreach($posts_array as $post)
  {
    echo "<h1>" . $post->post_title . "</h1><br>";
    echo "<p>" . $post->post_content . "</p><br>";
  } 
?>
```

在上面的例子中，我们将值`rand`传递给了参数`order_by`。

### 具有匹配元键和值的帖子

我们可能希望检索所有分配了特定元键和值的帖子。例如:有些博客每篇文章都有一个评论者。我们可能想要检索由特定评论者评论的文章。

下面是实现这一点的代码:

```
<?php
  $args = array("posts_per_page" => -1, "meta_key" => "reviewer", "meta_value" = "narayanprusty");
  $posts_array = get_posts($args);
  foreach($posts_array as $post)
  {
    echo "<h1>" . $post->post_title . "</h1><br>";
    echo "<p>" . $post->post_content . "</p><br>";
  } 
?>
```

在这里，我们正在检索“narayanprusty”评论的所有帖子。我们假设通过元键“reviewer”存储每个帖子的评论者姓名。

### 具有自定义分类的自定义帖子类型

我们可能希望检索带有自定义分类名称的自定义帖子类型的帖子。考虑这个代码示例:

```
<?php
  $args = array("posts_per_page" => -1, "post_type" => "coupons", "tax_query" => array(
    array(
      "taxonomy" => "coupon_category",
      "field"    => "slug",
      "terms"    => "plugins,themes",
    )
  ));
  $posts_array = get_posts($args);
  foreach($posts_array as $post)
  {
    echo "<h1>" . $post->post_title . "</h1><br>";
    echo "<p>" . $post->post_content . "</p><br>";
  } 
?>
```

在这个例子中，我们正在检索名为“优惠券”的自定义帖子类型的帖子，它属于“插件”和“主题”自定义分类。

## 结论

在本文中，我们看到了`get_posts`函数是如何工作的，它支持的各种参数，循环返回的结果和一些示例用例。`get_posts`功能是最常用的 WordPress 功能之一，我希望你现在可以开始在自己的项目中使用它。

## 分享这篇文章