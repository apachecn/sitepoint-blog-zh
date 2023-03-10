# 使用 MySQL 事件

> 原文：<https://www.sitepoint.com/working-with-mysql-events/>

MySQL 事件是在 MySQL 5.1.6 中添加的，它提供了对计划任务和 cron 作业的替代。事件可用于创建备份、删除陈旧记录、聚合报告数据等。与给定特定条件下执行的标准触发器不同，事件是由时间的推移触发的对象，有时被称为*时间触发器*。当您知道服务器流量较低时，您可以安排事件运行一次或重复运行。

在本文中，我将解释开始使用事件需要了解的内容:启动事件调度器、添加要运行一次或多次的事件、查看现有事件以及更改事件。我还将分享如何使用 MySQL events，使用预定的博客文章作为实际例子。

## 启动事件计划程序

MySQL 事件调度程序是一个在后台运行的进程，它不断地寻找要执行的事件。在创建或调度事件之前，您需要首先打开调度程序，这可以通过发出以下命令来完成:

```
mysql> SET GLOBAL event_scheduler = ON;
```

同样，要关闭所有事件，您可以使用:

```
mysql> SET GLOBAL event_scheduler = OFF;
```

一旦事件调度程序启动，您可以在 MySQL 的进程列表中查看它的状态。

```
mysql> SHOW PROCESSLISTG
...
     Id: 79
   User: event_scheduler
   Host: localhost
     db: NULL
Command: Daemon
   Time: 12
  State: Waiting on empty queue
   Info: NULL
```

## 处理事件

值得注意的是，当事件被创建时，它只能执行创建该事件的 MySQL 用户有权执行的操作。一些附加限制包括:

*   事件名称的长度限制为 64 个字符。
*   从 MySQL 5.1.8 开始，事件名称不区分大小写；无论大小写，每个事件名称都应该是唯一的。
*   事件不能由另一个事件创建、更改或删除。

设置事件计划时，不能引用存储函数或用户定义函数。

### 创建事件

以下示例创建了一个事件:

```
DELIMITER |

CREATE EVENT myevent
    ON SCHEDULE AT CURRENT_TIMESTAMP + INTERVAL 1 HOUR
    DO
      BEGIN
        UPDATE mytable SET mycol = mycol + 1;
      END |

DELIMITER ;
```

此事件将在创建一小时后运行一次。`BEGIN`和`END`语句包含一个或多个将在指定时间执行的查询。因为需要分号来终止`UPDATE`语句，所以您需要在发出`CREATE EVENT`语句之前切换分隔符，如果您是通过客户端工作的话，那么之后再切换回来。

您可以使用`SHOW EVENTS`查看所有现有事件的列表。

```
mysql> SHOW EVENTSG
********************** 1\. row **********************
                  Db: mysql
                Name: myevent
             Definer: dbuser@localhost
           Time zone: SYSTEM
                Type: ONE TIME
          Execute At: 2011-10-26 20:24:19
      Interval Value: NULL
      Interval Field: NULL
              Starts: NULL
                Ends: NULL
              Status: ENABLED
          Originator: 0
character_set_client: utf8
collation_connection: utf8_general_ci
```

事件过期后，它将被自动删除，除非您使用`ON COMPLETION`子句明确声明，例如:

```
CREATE EVENT myevent
    ON SCHEDULE AT CURRENT_TIMESTAMP + INTERVAL 1 HOUR
    ON COMPLETION PRESERVE
    DO
      BEGIN
        UPDATE mytable SET mycol = mycol + 1;
      END |
```

在本例中，即使事件已经过期，它仍将保留在数据库中，这将允许您更改并在以后再次运行它，或者您可能只是想保留它以供参考。

要自己永久删除事件，您可以使用`DROP EVENT`:

```
DROP EVENT myevent;
```

要指定重复事件，您可以使用`EVERY`子句:

```
CREATE EVENT myevent
    ON SCHEDULE EVERY 1 HOUR
    DO
      BEGIN
        UPDATE mytable SET mycol = mycol + 1;
      END |
```

您也可以使用`START`和`END`子句安排一个仅在特定时间段内有效的重复事件，而不是让一个事件只运行一次或永远运行:

```
CREATE EVENT myevent
    ON SCHEDULE EVERY 1 HOUR
    STARTS CURRENT_TIMESTAMP + INTERVAL 1 DAY
    ENDS CURRENT_TIMESTAMP + INTERVAL 1 YEAR
    DO
      BEGIN
        UPDATE mytable SET mycol = mycol + 1;
      END |
```

在本例中，重复发生的事件将从明天开始，并持续一整年每小时运行一次。

关于定时，指定的间隔可以是`YEAR`、`MONTH`、`WEEK`、`DAY`、`HOUR`、`MINUTE`或`SECOND`。请记住，关键字是以单数形式给出的；编写类似于`INTERVAL 5 MINUTE`的代码对你来说可能有些笨拙，但对 MySQL 来说是完全正确的。

### 更新事件

如果您想要更改现有事件的行为，而不是删除并重新创建它，您可以使用`ALTER EVENT`。例如，要将前一个事件的计划更改为每月运行一次，从未来某个日期的凌晨 1 点开始，您可以使用以下命令:

```
ALTER EVENT myevent
    ON SCHEDULE EVERY 1 MONTH
    STARTS '2011-12-01 01:00:00' |
```

要用一组不同的查询更新事件，您可以使用:

```
ALTER EVENT myevent
    DO
      BEGIN
        INSERT INTO mystats (total)
          SELECT COUNT(*) FROM sessions;
        TRUNCATE sessions;
      END |
```

要重命名事件，您需要指定一个`RENAME`子句:

```
ALTER EVENT myevent
    RENAME TO yourevent;
```

## 博客文章调度

为了让我给你看一个实际的例子，假设你有一个博客，你想选择在未来的某个时间发布文章。实现这一点的一种方法是向数据库记录添加时间戳和发布标志。cron 脚本将每分钟执行一次，以检查时间戳并为任何应该发布的帖子翻转标记。但是这样好像效率不是很高。另一种方法是使用 MySQL 事件，当你想发布文章时，这些事件就会触发。

您的博客条目表单可能有一个复选框，选中该复选框时，表示这是一篇预定的文章。此外，该表单还会有输入字段，供您输入应该发布帖子的日期和时间。接收脚本将负责将博客条目添加到数据库中，并管理事件，以便在不是即时发布的情况下对其进行调度。相关代码如下所示:

```
<?php
// establish database connection and filter incoming data
// ...

// insert blog post with pending status, get id assigned to post
$query = "INSERT INTO blog_posts (id, title, post_text, status) 
    VALUES (NULL, :title, :postText, 'pending')";
$stm = $db->prepare($query);
$stm->execute(array(":title" => $title, ":postText" => $text));
$id = $db->lastInsertId();

// is this a future post?
if (isset($_POST["schedule"], $_POST["time"])) {
    $scheduleDate = strtotime($_POST["time"]);

    $query = "CREATE EVENT publish_:id
    ON SCHEDULE AT FROM_UNIXTIME(:scheduleDate)
    DO
      BEGIN
        UPDATE blog_posts SET status = 'published' WHERE id = :id;
      END";
    $stm = $db->prepare($query);
    $stm->execute(array(":id" => $id, ":scheduleDate" => $scheduleDate));
}
// this is not a future post, publish now
else {
    $query = "UPDATE blog_posts SET status = 'published' WHERE id = :id";
    $stm = $db->prepare($query);
    $stm->execute(array(":id" => $id));
}
```

当文章存储在数据库中时，它以待定状态保存。如果这是一个预定的帖子，您就有机会安排事件，否则状态可以立即更新为已发布。

如果您要在以后编辑文章，您可以使用`DROP EVENT IF EXISTS`删除事件，并使用新的预定时间重新添加事件。

## 摘要

现在，您应该对什么是 MySQL 事件以及如何创建和管理您自己的事件有了深入的了解。虽然事件不能替代 cron 作业或计划任务，因为事件不能执行外部代码，如 PHP 脚本，但对于特定于 MySQL 数据库的依赖于时间的任务，它们是一种有用的替代方法。和往常一样，如果你有兴趣了解更多，一定要阅读官方文档。

图片通过[【加西娅】](http://www.shutterstock.com/gallery-427987p1.html) / [快门闭锁](http://www.shutterstock.com/)

## 分享这篇文章