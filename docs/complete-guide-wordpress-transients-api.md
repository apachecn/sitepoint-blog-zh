# WordPress 瞬态 API 完全指南

> 原文：<https://www.sitepoint.com/complete-guide-wordpress-transients-api/>

WordPress 从 2.8 版本开始就支持[瞬态 API](http://codex.wordpress.org/Transients_API) ，但是仍然有许多 WordPress 开发者不知道它的存在和用途。简而言之，WordPress Transients API 允许我们存储带有到期时间的键值对数据。

在本教程中，我们将深入讨论如何使用这个 API。我们还将看到它与 Options API 有什么不同，它如何与 WordPress 缓存系统交互，以及它的一些用例。

![WordPress Transients API](img/012c2815249c57578225e53ef87eca03.png)

### 选项 API 与瞬态 API

大多数 WordPress 开发者都知道 [WordPress 选项 API](http://codex.wordpress.org/Options_API) 。Options API 允许我们将数据的键值对永久存储在数据库中。许多 WordPress 开发者没有意识到的是，Options API 实现了一个缓存层(即 WordPress 对象缓存)来缓存选项。如果没有启用永久高速缓存，则在每个 HTTP 请求上创建一个新的高速缓存会话，否则选项 API 将使用永久高速缓存。

几乎每个 WordPress API 都使用 WordPress 对象缓存与 MySQL 进行交互，以缓存数据来防止多次 MySQL 点击。

瞬态 API 的工作方式与所有其他 API 略有不同。只有在没有启用持久缓存的情况下，它才在 MySQL 中存储数据的键值对，否则它只使用对象缓存。而所有其他 API 都使用这两者来同步数据，以确保数据的持久性。因此，瞬态不是持久的，即不应用于存储关键数据。所以 **Transients API 非常适合缓存数据**。

**注意:**如果没有启用持久缓存，那么 Transients API 使用 Options API 来存储数据的键值对，否则它直接使用对象缓存。瞬变存储在选项表中。每个瞬态由两个选项组成，即键-值对数据和键-值对截止日期。

### 创建一个瞬变

要设置瞬变，我们需要使用`set_transient()`功能。这个函数有三个参数:

1.  瞬态名称(必需):必须是字符串。字符串长度不能超过 40 个字符，否则将无法创建瞬态。
2.  瞬态值(必需):必须是字符串。如果你传递一个对象或数组，那么它会被[序列化](http://codex.wordpress.org/Function_Reference/is_serialized)，即转换成一个字符串。
3.  过期秒数(可选):瞬变将在其中过期的秒数。瞬态也可能在到期时间之前到期，因为缓存的数据(即存储在对象缓存中的数据)是易变的。

下面是一个使用`set_transient()`函数的代码示例:

```
set_transient("Website", "SitePoint", 3600);
```

在这里，我们将名为“Website”的值为“SitePoint”的密钥存储 1 小时。1 小时后，该密钥将不再可用。

`set_transient`如果瞬态创建成功，则返回 true，否则返回 false。

如果您不提供到期时间或提供“0”作为到期时间，那么它永远不会使瞬变到期。

**注意:**如果未提供到期时间或到期时间为‘0’，则瞬变被自动加载(即每当请求页面时加载到内存中)。

Transients API 还提供了另一个创建瞬态的函数，即`set_site_transient`。它也像`set_transient`一样采用相同的三个参数。它们之间的大部分功能是相同的。`set_transient`和`set_site_transient`的区别在于:

1.  当`set_site_transient`用于多站点网络时，网络中的所有站点都可以使用瞬态。
2.  无论是否有过期时间，使用`set_site_transient`创建的瞬变总是自动加载。

最后，如果您运行一个现有的临时键的`set_transient`,那么这个值和到期时间将被更新为新提供的值和到期时间。过期时间从第一次设置瞬变开始计算。

### 检索瞬变

要检索存储的瞬态，您需要使用`get_transient`功能。它只接受一个参数，即瞬态的名称。

```
$value = get_transient("Website");

if($value === false)
{
	echo "Expired or not found";
}
```

`get_transient`如果瞬态已过期或不存在，则返回 false。否则，它返回瞬态的值。

如果 transient 过期或者没有找到，它将返回 false，因此你不应该在 transient 中存储布尔值。如果你想存储布尔值，那么使用整数形式，即 0 或 1。

如果你已经使用`set_site_transient`设置了一个瞬变，那么使用`get_site_transient`而不是`get_transient`来检索它。

### 删除瞬变

要删除瞬变，您需要使用`delete_transient`功能。它只接受一个参数，即瞬态的名称。

这里有一个例子:

```
delete_transient("Website");
```

如果瞬态被成功删除，则返回 true。如果找不到瞬态或者由于其他原因，它不能删除瞬态，然后返回 false。

如果您已经使用`set_site_transient`设置了瞬变，那么使用`delete_site_transient`而不是`delete_transient`删除它。

### 检索和缓存特定类别的帖子

瞬态 API 可以用来缓存任何东西。大多数插件使用这个 API 来缓存数据。为了举例，让我们看看如何检索和缓存一个类别的帖子。

```
<?php
  if(($posts = get_transient("sitepoint_wordpress_posts")) === false) 
  {
    $posts = new WP_Query("category_name=wordpress");
    set_transient("sitepoint_wordpress_posts", $posts, 3600);
  } 

?>

<?php if($posts->have_posts()): ?>
   <?php while($posts->have_posts()) : $posts->the_post(); ?>
    //display the post
   <?php endwhile; ?>
   <?php else: ?>
    //display no posts found message
<?php endif; ?>
<?php wp_reset_postdata(); ?>
```

在这里，我们将类别帖子缓存一个小时。我们正在使用`WP_Query`类检索帖子。`WP_Query`被序列化并存储为一个瞬态。在检索时，它被反序列化。

### 最后的想法

这篇文章展示了使用 Transients API 在 WordPress 中缓存数据是多么容易。

你可以使用 [Memcached 对象缓存](https://wordpress.org/plugins/memcached/)或者 [WP Redis](https://wordpress.org/plugins/wp-redis/) 插件在 WordPress 中启用永久缓存。

请在下面的评论中告诉我你对这个 API 的体验。

## 分享这篇文章