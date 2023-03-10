# 理解 MySQL 和其他关系数据库中的连接

> 原文：<https://www.sitepoint.com/understanding-sql-joins-mysql-database/>

*通过我们关于 MySQL 记录搜索和更新的[截屏，了解更多关于 MySQL 的信息。](https://www.sitepoint.com/premium/screencasts/improving-your-ability-to-search-for-and-update-mysql-records)*

*这篇文章写于 2011 年，至今仍是我们最受欢迎的帖子之一。如果你渴望了解更多关于 MySQL 的知识，你可能会对最近这篇关于管理 MySQL 的文章很感兴趣。*

“JOIN”是一个 SQL 关键字，用于查询两个或更多相关表中的数据。不幸的是，这个概念经常用抽象的术语来解释，或者在不同的数据库系统之间有所不同。经常让我很困惑。开发人员已经应付了足够多的困惑，所以这是我试图向我自己和任何感兴趣的人简单明了地解释连接。

## 相关表格

MySQL、PostgreSQL、Firebird、SQLite、SQL Server、Oracle 都是关系数据库系统。一个设计良好的数据库将提供许多包含相关数据的表格。一个非常简单的例子是用户(学生)和课程注册:

**‘用户’表:**

| 身份证明（identification） | 名字 | 课程 |
| one | 爱丽丝 | one |
| Two | 上下移动 | one |
| three | 查理时代的 | Two |
| four | 大卫 | five |
| five | 女子名 | (空) |

MySQL 表创建代码:

```
 CREATE TABLE `user` (
	`id` smallint(5) unsigned NOT NULL AUTO_INCREMENT,
	`name` varchar(30) NOT NULL,
	`course` smallint(5) unsigned DEFAULT NULL,
	PRIMARY KEY (`id`)
) ENGINE=InnoDB; 
```

课程编号与课程表中的科目相关…

**“课程”表:**

| 身份证明（identification） | 名字 |
| one | HTML5 |
| Two | CSS3 |
| three | Java Script 语言 |
| four | 服务器端编程语言（Professional Hypertext Preprocessor 的缩写） |
| five | 关系型数据库 |

MySQL 表创建代码:

```
 CREATE TABLE `course` (
	`id` smallint(5) unsigned NOT NULL AUTO_INCREMENT,
	`name` varchar(50) NOT NULL,
	PRIMARY KEY (`id`)
) ENGINE=InnoDB; 
```

因为我们使用 InnoDB 表，并且知道 user.course 和 course.id 是相关的，所以我们可以指定一个外键关系:

```
 ALTER TABLE `user`
ADD CONSTRAINT `FK_course`
FOREIGN KEY (`course`) REFERENCES `course` (`id`)
ON UPDATE CASCADE; 
```

本质上，MySQL 会自动:

*   如果 course.id 发生变化，则对 user.course 列中的相关条目重新编号
*   拒绝任何删除用户注册的课程的尝试。

**important:** This is terrible database design!

这个数据库效率不高。这个例子没问题，但是一个学生只能注册零门或一门课程。一个真正的系统需要克服这个限制——可能需要使用一个中间的“注册”表，将任意数量的学生映射到任意数量的课程。

连接允许我们以多种方式查询这些数据。

## 内部联接(或仅联接)

最常用的子句是 INNER JOIN。这将生成一组在用户表和课程表中都匹配的记录，即注册课程的所有用户:

```
 SELECT user.name, course.name
FROM `user`
INNER JOIN `course` on user.course = course.id; 
```

**结果:**

| user.name | course.name |
| 爱丽丝 | HTML5 |
| 上下移动 | HTML5 |
| 卡林 | CSS3 |
| 大卫 | 关系型数据库 |

## 左连接

如果我们需要一份所有学生及其课程的列表，即使他们没有注册，该怎么办？左连接产生一组匹配左表(用户)中每个条目的记录，而不考虑右表(课程)中的任何匹配条目:

```
 SELECT user.name, course.name
FROM `user`
LEFT JOIN `course` on user.course = course.id; 
```

**结果:**

| user.name | course.name |
| 爱丽丝 | HTML5 |
| 上下移动 | HTML5 |
| 卡林 | CSS3 |
| 大卫 | 关系型数据库 |
| 女子名 | (空) |

## 右连接

也许我们需要一份所有课程和学生的名单，即使没有人注册？右连接产生一组匹配右表(课程)中每个条目的记录，而不考虑左表(用户)中的任何匹配条目:

```
 SELECT user.name, course.name
FROM `user`
RIGHT JOIN `course` on user.course = course.id; 
```

**结果:**

| user.name | course.name |
| 爱丽丝 | HTML5 |
| 上下移动 | HTML5 |
| 卡林 | CSS3 |
| (空) | Java Script 语言 |
| (空) | 服务器端编程语言（Professional Hypertext Preprocessor 的缩写） |
| 大卫 | 关系型数据库 |

很少使用右连接，因为使用左连接可以表达相同的结果。对于数据库来说，这可以更高效、更快速地进行解析:

```
 SELECT user.name, course.name
FROM `course`
LEFT JOIN `user` on user.course = course.id; 
```

例如，我们可以统计每门课程的注册学生人数:

```
 SELECT course.name, COUNT(user.name)
FROM `course`
LEFT JOIN `user` ON user.course = course.id
GROUP BY course.id; 
```

**结果:**

| course.name | 计数() |
| HTML5 | Two |
| CSS3 | one |
| Java Script 语言 | Zero |
| 服务器端编程语言（Professional Hypertext Preprocessor 的缩写） | Zero |
| 关系型数据库 | one |

## 外部联接(或完全外部联接)

我们的最后一个选项是外部连接，它返回两个表中的所有记录，而不考虑任何匹配。如果不存在匹配，缺失的一方将包含 NULL。

外连接不如内连接、左连接或右连接有用，而且它没有在 MySQL 中实现。但是，您可以使用左连接和右连接的联合来解决这个限制，例如

```
 SELECT user.name, course.name
FROM `user`
LEFT JOIN `course` on user.course = course.id

UNION

SELECT user.name, course.name
FROM `user`
RIGHT JOIN `course` on user.course = course.id; 
```

**结果:**

| user.name | course.name |
| 爱丽丝 | HTML5 |
| 上下移动 | HTML5 |
| 卡林 | CSS3 |
| 大卫 | 关系型数据库 |
| 女子名 | (空) |
| (空) | Java Script 语言 |
| (空) | 服务器端编程语言（Professional Hypertext Preprocessor 的缩写） |

我希望这能让您更好地理解连接，并帮助您编写更高效的 SQL 查询。

*通过我们关于 MySQL 记录搜索和更新的[截屏，了解更多关于 MySQL 的信息。](https://www.sitepoint.com/premium/screencasts/improving-your-ability-to-search-for-and-update-mysql-records)*

对本文的评论已经关闭。有关于 MySQL 的问题吗？为什么不在我们的[论坛](https://www.sitepoint.com/forums/forumdisplay.php?88-Databases-amp-MySQL?utm_source=sitepoint&utm_medium=link&utm_campaign=forumlink)上问呢？

## 分享这篇文章