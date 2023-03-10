# 如何控制 WordPress 中的文章修订

> 原文：<https://www.sitepoint.com/configure-wordpress-post-revisions/>

WordPress 会自动修改你的内容。每当您保存帖子或页面时，旧版本都会保留，以便您可以随时恢复。旧的修订永远不会被删除，所以你总是有一个所有页面更改的完整历史。然而，有时做一点家务是必要的。每个修订都需要在 WordPress 的 posts 表中有一个单独的行，并且可能在 postmeta 和 term_relationships 表中有多个条目。删除旧版本将释放磁盘空间，减轻 MySQL 的处理负担。![WordPress revisions](img/397fb1a4a31ef354e427a0566a6a0f7b.png)

## 删除旧版本

我只说一次:**备份你的数据库！**我们将直接在你的 MySQL 表上运行 SQL 语句，一个小小的失误就可能毁掉你的 WordPress 安装。首先，您需要找到您的 WordPress 表前缀，它在 wp-config.php 的下面一行中指定:

```
$table_prefix  = 'wp_';
```

wp_ 是默认设置，但是可以更改它来为您的安装增加一点安全性。我们将假设 wp_ 已经在下面的代码中被指定。要删除所有页面和帖子的所有修订，请启动 MySQL 管理工具，如 [phpMyAdmin](http://www.phpmyadmin.net/) ，并运行以下 SQL 语句:

```
DELETE a,b,cFROM wp_posts a  LEFT JOIN wp_term_relationships b ON (a.ID = b.object_id)  LEFT JOIN wp_postmeta c ON (a.ID = c.post_id)  WHERE a.post_type = 'revision';
```

*(如有必要，记得将所有表格引用从“wp_”更改为您的表格前缀。)*

**tip:** SQL Shenanigans

非常感谢 Michael Ambrosio 指出了这个 SQL 语句的一个微妙问题，这个问题会导致 WordPress 链接被删除。[查看他的文章了解更多信息……](http://www.ambrosite.com/blog/clean-up-wordpress-revisions-using-a-mysql-multi-table-delete)

如果这有点太严重，您可以删除特定日期之前的所有修订，例如，以下语句将删除除 2010 年 1 月 1 日之后所做的修订之外的所有修订:

```
DELETE a,b,cFROM wp_posts a  LEFT JOIN wp_term_relationships b ON (a.ID = b.object_id)  LEFT JOIN wp_postmeta c ON (a.ID = c.post_id)  WHERE a.post_type = 'revision'AND a.post_date < '2010-01-01';
```

*(注意 MySQL 日期是用 YYYY-MM-DD 符号指定的。)*

## 禁用或限制修订

将下面的声明添加到你的 WordPress wp-config.php 文件中，永久关闭帖子修订:

```
define('WP_POST_REVISIONS', false);
```

该值可以设置为“true”以重新启用修订。或者，您可以使用正整数来限制允许的修订数量，例如

```
define('WP_POST_REVISIONS', 5);
```

这将为每篇文章创建最多 5 个修订，加上一个自动保存的目的。旧版本将被自动删除。

## WordPress 插件

如果这一切听起来有点可怕，你会很高兴地知道有很多的 [WordPress 插件提供版本控制](http://wordpress.org/extend/plugins/tags/revisions)。对于控制 WordPress 版本，你有什么进一步的建议吗？

## 分享这篇文章