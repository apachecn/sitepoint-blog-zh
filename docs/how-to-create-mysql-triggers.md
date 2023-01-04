# 如何在 MySQL 中创建触发器

> 原文：<https://www.sitepoint.com/how-to-create-mysql-triggers/>

*这篇文章写于 2011 年，至今仍是我们最受欢迎的帖子之一。如果你渴望了解更多关于 MySQL 的知识，你可能会对最近这篇关于管理 MySQL 的文章很感兴趣。*

 *这是关于使用触发器和事件的数据库自动化系列文章的第二篇。触发器是在特定数据库表上发生插入、更新或删除事件之前或之后运行的 SQL 代码。从版本 5.0.2 开始，MySQL 就支持触发器。

## 我们的数据库计划

我们将为博客应用程序创建一个小型示例数据库。需要两个表格:

*   ` blog `:存储唯一的文章 ID、标题、内容和已删除标志。
*   ` audit `:存储一组基本的历史更改，包括记录 ID、博客帖子 ID、更改类型(新建、编辑或删除)以及更改的日期/时间。

下面的 SQL 创建了“blog ”,并对删除的列进行了索引:

```
 CREATE TABLE `blog` (
	`id` mediumint(8) unsigned NOT NULL AUTO_INCREMENT,
	`title` text,
	`content` text,
	`deleted` tinyint(1) unsigned NOT NULL DEFAULT '0',
	PRIMARY KEY (`id`),
	KEY `ix_deleted` (`deleted`)
) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8 COMMENT='Blog posts'; 
```

下面的 SQL 创建了“audit”表。所有列都被索引，并且为 audit.blog_id 定义了一个外键，该外键引用 blog.id。因此，当我们物理删除一个博客条目时，它的完整审计历史也被删除。

```
 CREATE TABLE `audit` (
	`id` mediumint(8) unsigned NOT NULL AUTO_INCREMENT,
	`blog_id` mediumint(8) unsigned NOT NULL,
	`changetype` enum('NEW','EDIT','DELETE') NOT NULL,
	`changetime` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
	PRIMARY KEY (`id`),
	KEY `ix_blog_id` (`blog_id`),
	KEY `ix_changetype` (`changetype`),
	KEY `ix_changetime` (`changetime`),
	CONSTRAINT `FK_audit_blog_id` FOREIGN KEY (`blog_id`) REFERENCES `blog` (`id`) ON DELETE CASCADE ON UPDATE CASCADE
) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8; 
```

## 创建触发器

我们现在需要两个触发器:

*   当一条记录被插入到 blog 表中时，我们希望在 audit 表中添加一个新条目，该条目包含 blog ID 和“new”类型(如果它被立即删除，则为“DELETE”)。
*   当 blog 表中的记录被更新时，我们希望在审计表中添加一个新条目，该条目包含 blog ID 和类型“编辑”或“删除”(如果设置了删除标志)。

请注意，changetime 字段将自动设置为当前时间。

每个触发器都需要:

1.  一个**唯一的名字**。我更喜欢使用描述表和动作的名称，例如 blog_before_insert 或 blog_after_update。
2.  触发事件的**表**。一个触发器只能监视一个表。
3.  **当触发发生时**。这可以在插入、更新或删除之前或之后。如果需要修改传入数据，必须使用 BEFORE 触发器。如果要将新的/已更改的记录作为另一个表中记录的外键进行引用，则必须使用 AFTER 触发器。
4.  **触发体**；要运行的一组 SQL 命令。请注意，您可以使用 OLD.col_name(以前的值)或 NEW.col_name(新值)来引用主题表中的列。NEW.col_name 的值可以在插入和更新触发器之前进行更改。

基本的触发器语法是:

```
 CREATE
    TRIGGER `event_name` BEFORE/AFTER INSERT/UPDATE/DELETE
    ON `database`.`table`
    FOR EACH ROW BEGIN
		-- trigger body
		-- this code is applied to every 
		-- inserted/updated/deleted row
    END; 
```

我们需要两个触发器 blog 表上的插入后和更新后。没有必要定义删除触发器，因为通过将文章的 deleted 字段设置为 true，文章被标记为已删除。

我们将发出的第一个 MySQL 命令有点不寻常:

```
 DELIMITER $$ 
```

我们的触发器主体需要用分号(；).要创建完整的触发器代码，我们必须将 delimiter 改为其他值，比如$$。

现在可以定义我们的 AFTER INSERT 触发器了。它确定是否设置了删除标志，相应地设置@changetype 变量，并在审计表中插入一条新记录:

```
 CREATE
	TRIGGER `blog_after_insert` AFTER INSERT 
	ON `blog` 
	FOR EACH ROW BEGIN

		IF NEW.deleted THEN
			SET @changetype = 'DELETE';
		ELSE
			SET @changetype = 'NEW';
		END IF;

		INSERT INTO audit (blog_id, changetype) VALUES (NEW.id, @changetype);

    END$$ 
```

最后，我们将分隔符重新设置为分号:

```
 DELIMITER ; 
```

更新后触发器几乎完全相同:

```
 DELIMITER $$

CREATE
	TRIGGER `blog_after_update` AFTER UPDATE 
	ON `blog` 
	FOR EACH ROW BEGIN

		IF NEW.deleted THEN
			SET @changetype = 'DELETE';
		ELSE
			SET @changetype = 'EDIT';
		END IF;

		INSERT INTO audit (blog_id, changetype) VALUES (NEW.id, @changetype);

    END$$

DELIMITER ; 
```

这超出了本文的范围，但是您可以考虑调用一个单独的存储过程来处理这两个触发器。

## 触发快乐？

让我们看看当我们在博客表中插入一篇新文章时会发生什么:

```
 INSERT INTO blog (title, content) VALUES ('Article One', 'Initial text.'); 
```

正如您所料,“博客”表中出现了一个新条目:

| 身份证明（identification） | 标题 | 内容 | 删除 |
| one | 第一条 | 初始文本 | Zero |

此外，我们的“审计”表中出现了一个新条目:

| 身份证明（identification） | 博客 id | 变更类型 | 改变时间 |
| one | one | 新的 | 2011-05-20 09:00:00 |

让我们更新我们的博客文本:

```
 UPDATE blog SET content = 'Edited text' WHERE id = 1; 
```

除了更改职位之外,“审计”表中还出现了一个新条目:

| 身份证明（identification） | 博客 id | 变更类型 | 改变时间 |
| one | one | 新的 | 2011-05-20 09:00:00 |
| Two | one | 编辑 | 2011-05-20 09:01:00 |

最后，让我们将帖子标记为已删除:

```
 UPDATE blog SET deleted = 1 WHERE id = 1; 
```

“审计”表会相应更新，我们会记录何时发生了变化:

| 身份证明（identification） | 博客 id | 变更类型 | 改变时间 |
| one | one | 新的 | 2011-05-20 09:00:00 |
| Two | one | 编辑 | 2011-05-20 09:01:00 |
| three | one | 删除 | 2011-05-20 09:03:00 |

这是一个简单的例子，但我希望它能让你对 MySQL 触发器的威力有所了解。在我的下一篇文章中，我们将实现一个预定事件来归档删除的文章。

如果你喜欢读这篇文章，你会爱上[可学的](https://learnable.com/)；向大师们学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和交互式在线课程，如 [PHP & MySQL 初学者网络开发](https://learnable.com/courses/php-mysql-web-development-for-beginners-13)。

## 分享这篇文章*