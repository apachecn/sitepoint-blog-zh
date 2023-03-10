# 如何在 WordPress 中控制页面和文章的修改

> 原文：<https://www.sitepoint.com/wordpress-post-revision-control/>

![Control of Page and Post Revisions in WordPress](img/e1fa0f7fee01a416e6e307e1e0ef3a1e.png)

*本文是与 [SiteGround](https://www.siteground.com/sitepoint-recommended?afcode=97a975da3502771c04e59cbae092b1dd&campaign=post-revisions) 合作创作的系列文章的一部分。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

WordPress 提供了一个修改系统，当它被保存时，会记录下每一页和帖子的完整副本。优点:您可以在任何时候恢复到文档的早期版本并进行比较。或者你可以发现谁该为拼写和语法错误负责。

默认情况下，每页或每篇文章存储的修订数量没有限制。*(注意每个编辑的每篇文章只进行一次自动保存——最近的自动保存会覆盖之前的自动保存。)*每一次修改都需要在 WordPress 的`posts`表中有单独的一行，并且可能在`postmeta`和`term_relationships`表中有多个条目。对于较小的站点来说，这很少是个问题，但是它可能会影响较大安装的性能和效率。表格最终会被永远不会用到的冗余数据填满。

## 限制修订

修订的数量可以在 WordPress 的**wp-config.php**文件中设置。如果您不知道该文件，我建议您向开发人员寻求进一步的指导。在编辑之前复制一份 wp-config.php 的 T2，因为最小的错误都会破坏你的 WordPress 网站。

要完全禁用修订，请将下面一行添加到**wp-config.php**:

```
define('WP_POST_REVISIONS', 0);
```

要限制修订，请将数字更改为正整数，例如，每页/帖子不超过 10 次修订:

```
define('WP_POST_REVISIONS', 10);
```

要恢复到无限制修订，请删除该行或将值更改为`-1`:

```
define('WP_POST_REVISIONS', -1);
```

## 修订插件

如果文件编辑感觉有点太硬核，有几个[插件控制修订](https://wordpress.org/plugins/tags/revisions/)。例如， [WP 修订限制](https://wordpress.org/plugins/wp-revisions-limit/)以相同的方式设置修订限制。

## 以编程方式限制修订

[`wp_revisions_to_keep`过滤器](https://codex.wordpress.org/Plugin_API/Filter_Reference/wp_revisions_to_keep)允许插件或者你的主题的**functions.php**文件控制给定帖子保留多少修订。

过滤器的函数传递了两个参数:

*   要保留的默认修订数
*   当前帖子的`WP_Post`对象

并且必须返回允许的修订数量。

以下示例为“custom_post”类型的帖子设置了五次修订的限制:

```
add_filter( 'wp_revisions_to_keep', 'control_revisions', 10, 2 );

function control_revisions($num, $post) {

  if('custom_post' == $post->post_type) $num = 5;
  return $num;

}
```

你也可以使用 [WordPress REST API](https://developer.wordpress.org/rest-api/) 来[列表、检索和删除修订](https://developer.wordpress.org/rest-api/reference/post-revisions/)。

## 如何删除旧版本

上面的方法会立即激活，所以理想情况下，你应该在安装 WordPress 后不久设置`WP_POST_REVISIONS`。但是，该设置不会删除 MySQL 数据库中的旧版本。清除旧数据是可能的，但请注意危险。在采取任何行动之前，请记住…

备份您的数据库！

最简单的选择是使用一个插件，比如 [WP-Optimize](https://wordpress.org/plugins/wp-optimize/) ，它通过删除修订和其他优化来清理你的 WordPress 数据库。您可以运行一次或设置一个定期计划。

或者，您可以冒险运行一个 SQL 命令来清除修订。首先，找到你的 WordPress 表前缀——它在**wp-config.php**中指定，例如

```
$table_prefix = 'wp_';
```

**wp_** 是默认值。我们假设已经为下面的代码指定了 **wp_** ，但是如果有必要的话可以改变引用。要删除所有页面和帖子的所有修订，请启动 MySQL 管理工具，如 [phpMyAdmin](http://www.phpmyadmin.net/) ，并运行以下 SQL 命令:

```
DELETE a,b,c
FROM wp_posts a
LEFT JOIN wp_term_relationships b ON ( a.ID = b.object_id )
LEFT JOIN wp_postmeta c ON ( a.ID = c.post_id )
LEFT JOIN wp_term_taxonomy d ON ( b.term_taxonomy_id = d.term_taxonomy_id )
WHERE a.post_type = 'revision'
AND d.taxonomy != 'link_category';
```

*感谢 [Michael Ambrosio 在我的原始代码出现问题时](http://www.ambrosite.com/blog/clean-up-wordpress-revisions-using-a-mysql-multi-table-delete)提供了修复。*

一切顺利的话，你将会有一个闪闪发光的干净的数据库，WordPress 将会明显更快。或者，WordPress 不会启动，你会丢失十年来的精彩帖子。*但是你首先做了备份，当然……*

## 分享这篇文章