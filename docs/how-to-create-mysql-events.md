# 如何在 MySQL 中创建预定事件

> 原文：<https://www.sitepoint.com/how-to-create-mysql-events/>

这是关于使用触发器和事件的数据库自动化系列文章的第三篇也是最后一篇。如果你还没有这样做，请阅读[如何在 MySQL](https://www.sitepoint.com/how-to-create-mysql-triggers/) 中创建触发器，它介绍了这里讨论的许多概念。

事件类似于触发器。但是，事件可以计划在特定时间段内运行任意次数，而不是响应数据更改而运行。实际上，这是一个纯数据库的 cron 作业。

从 5.1 版本开始，MySQL 就支持事件。它们非常适合可安排在非高峰时间执行的数据归档或报告生成等维护任务。

## 我们的数据库计划

我们的博客数据库有问题。旧帖子被标记为删除，而不是从“博客”表中删除。我们的表将无限增长，并随着时间的推移变得越来越慢。我们可以清除旧帖子，但这将永远删除它们。因此，我们将帖子及其相关的审计记录移动到归档表中。存档表可以在不影响主 web 应用程序速度的情况下增长，如果有必要，我们可以取消删除旧帖子。

需要两个存档表:

*   ` blog_archive `:除了不需要删除标志或自动递增 ID 之外，与` blog '表相同。
*   ` audit_archive `:与` audit '表相同，只是时间戳不是自动生成的，也不需要自动递增的 ID。

以下 SQL 创建了这两个表:

```
 CREATE TABLE `blog_archive` (
	`id` mediumint(8) unsigned NOT NULL,
	`title` text,
	`content` text,
	PRIMARY KEY (`id`),
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='Blog posts archive';
-- 
CREATE TABLE `audit_archive` (
	`id` mediumint(8) unsigned NOT NULL,
	`blog_id` mediumint(8) unsigned NOT NULL,
	`changetype` enum('NEW','EDIT','DELETE') NOT NULL,
	`changetime` timestamp NOT NULL,
	PRIMARY KEY (`id`),
	KEY `ix_blog_id` (`blog_id`),
	KEY `ix_changetype` (`changetype`),
	KEY `ix_changetime` (`changetime`),
	CONSTRAINT `FK_audit_blog_archive_id` FOREIGN KEY (`blog_id`) REFERENCES `blog_archive` (`id`) ON DELETE CASCADE ON UPDATE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8; 
```

## 启动 MySQL 的事件调度程序

MySQL 事件由一个特殊的事件调度器线程执行。默认情况下它是禁用的，所以使用以下 MySQL 命令可以确定它是否正在运行:

```
 SHOW PROCESSLIST; 
```

如果调度程序正在运行，将至少显示两行，其中一行的用户字段将设置为“event_scheduler”。如果只返回一行，调度程序将被禁用，事件将不会运行。

您可以通过命令行选项`--event-scheduler=ON`或设置 MySQL 配置文件(Windows 上的 my.cnf 或 my.ini)中的`event_scheduler=ON`来确保调度程序在 MySQL 启动时启动。

或者，您可以从 MySQL 命令行启动调度程序:

```
 SET GLOBAL event_scheduler = ON; 
```

## 创建事件

我们需要一个预定事件:

*   当删除标志设置为 1 时，将帖子从“博客”复制到“博客存档”。
*   将这些帖子的相关审计条目从“审计”复制到“审计 _ 存档”中。
*   从“博客”表中物理删除存档的帖子。引用完整性已用外键定义，因此这些帖子的所有关联审计条目也将被删除。

假设您拥有创建事件的 MySQL 权限，基本语法是:

```
 CREATE EVENT `event_name` 
ON SCHEDULE schedule
[ON COMPLETION [NOT] PRESERVE] 
[ENABLE | DISABLE | DISABLE ON SLAVE]
DO BEGIN
	-- event body
END; 
```

可以为*日程*分配各种设置，例如

*   在特定日期/时间运行一次:
    在' YYYY-MM-DD HH:MM.SS'
    例如在' 2011-06-01 02:00.00 '
*   在特定时间段过后运行一次:
    在当前时间戳+间隔 n[小时|月|周|天|分钟]
    例如，在当前时间戳+间隔 1 天
*   永远以特定的时间间隔运行:
    每 n[小时|月|周|天|分钟]
    ，例如每 1 天
*   在特定时间段内以特定间隔运行:
    每 n[小时|月|周|日|分钟]开始日期结束日期
    例如，每 1 天开始 CURRENT_TIMESTAMP +间隔 1 周结束' 2012-01-01 00:00.00 '

一旦时间表过期，事件通常会被丢弃(完成时不保留)。设置完成保留以防止该行为。MySQL 创建事件语法文档提供了更多的细节。

我们现在可以定义我们的事件了(记得先设置分隔符)。我们将它设置为从每周的周日早上开始运行:

```
 DELIMITER $$

CREATE 
	EVENT `archive_blogs` 
	ON SCHEDULE EVERY 1 WEEK STARTS '2011-07-24 03:00:00' 
	DO BEGIN

		-- copy deleted posts
		INSERT INTO blog_archive (id, title, content) 
		SELECT id, title, content
		FROM blog
		WHERE deleted = 1;

		-- copy associated audit records
		INSERT INTO audit_archive (id, blog_id, changetype, changetime) 
		SELECT audit.id, audit.blog_id, audit.changetype, audit.changetime 
		FROM audit
		JOIN blog ON audit.blog_id = blog.id
		WHERE blog.deleted = 1;

		-- remove deleted blogs and audit entries
		DELETE FROM blog WHERE deleted = 1;

	END */$$

DELIMITER ; 
```

这是一个简单的例子，但你可以添加更多的功能，例如，只移动至少 1 个月前删除的帖子，并清除所有存档超过 1 年的帖子。我希望您喜欢这个系列，并在您的下一个项目中考虑数据库触发器和事件。

## 分享这篇文章