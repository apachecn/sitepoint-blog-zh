# 如何使用 MySQL 外键加快数据库开发

> 原文：<https://www.sitepoint.com/mysql-foreign-keys-quicker-database-development/>

这篇文章写于 2009 年，至今仍是我们最受欢迎的帖子之一。如果你渴望了解更多关于 MySQL 和数据库开发的知识，你可能会对这篇最近的文章很感兴趣。

MySQL 是一个奇妙的开源数据库，被成千上万的网络应用程序所使用。默认的 MyISAM 表系统是最简单和最快的系统之一，但是它不支持外键(尽管它计划在版本 6 中使用)。

幸运的是，很少使用的 **InnoDB table** 类型支持外键:本文将展示它们如何减少您的编码工作并增加数据完整性。

## 什么是外键？

外键在两个表之间建立关系或约束。

**免责声明！**在本例中，我们将创建两个简单的数据库表。它们设计得不好，但是会展示外键的威力！

*   **雇员**:公司雇员表，其中每个成员都被分配了一个惟一的 ID
*   **借书**:借书表。每条记录都会引用借书人的员工 ID。

我们将在两个表中定义雇员 ID 之间的外键关系。这提供了几个优点:

1.  不可能在“借用”表中输入无效的员工 id。
2.  员工更改由 MySQL 自动处理。

## 创建示例数据库

我们的示例数据库创建如下:

```
CREATE DATABASE mydb;
USE mydb;
```

我们现在定义我们的两个表。注意，InnoDB 被指定为表类型，我们还将为雇员的姓氏添加一个索引。

```
 CREATE TABLE employee (
	id smallint(5) unsigned NOT NULL,
	firstname varchar(30),
	lastname varchar(30),
	birthdate date,
	PRIMARY KEY (id),
	KEY idx_lastname (lastname)
) ENGINE=InnoDB;

CREATE TABLE borrowed (
	ref int(10) unsigned NOT NULL auto_increment,
	employeeid smallint(5) unsigned NOT NULL,
	book varchar(50),
	PRIMARY KEY (ref)
) ENGINE=InnoDB; 
```

我们现在可以指定我们的外键(这可以在 CREATE TABLE 语句中处理，但在这里是单独显示的):

```
 ALTER TABLE borrowed 
ADD CONSTRAINT FK_borrowed 
FOREIGN KEY (employeeid) REFERENCES employee(id) 
ON UPDATE CASCADE
ON DELETE CASCADE; 
```

这告诉 MySQL 我们想通过添加一个名为“FK _ 借入”的约束来改变借入的表。employeeid 列将引用 employee 表中的 id 列——换句话说，员工必须存在才能借书。

最后两行也许是最有趣的。他们声明如果一个雇员 ID 被更新或者一个雇员被删除，这些改变应该被应用到被借用的表上。

## 添加表格数据

我们现在将用数据填充表格。请记住，必须首先添加我们的员工:

**员工:**

| 身份证明（identification） | 西方人名的第一个字 | 姓 | 出生年月日 |
| --- | --- | --- | --- |
| one | 约翰 | 史密斯（姓氏） | 1976-01-02 |
| Two | 劳拉 | 琼斯 | 1969-09-05 |
| three | 简（女子名） | 格林（姓氏）；绿色的 | 1967-07-15 |

**借:**

| 裁判员 | 员工 id | 书 |
| --- | --- | --- |
| one | one | SitePoint 简单 SQL |
| Two | one | SitePoint 终极 HTML 参考 |
| three | one | SitePoint 终极 CSS 参考 |
| four | Two | JavaScript 的艺术和科学 |

表格显示约翰借了 3 本书，劳拉借了 1 本，简一本也没借。可以运行标准的 SQL 查询来查找有用的信息，例如“John 借了哪些书”:

```
 SELECT book FROM borrowed 
JOIN employee ON employee.id=borrowed.employeeid 
WHERE employee.lastname='Smith'; 
```

**结果:**
SitePoint 简单 SQL
SitePoint 终极 HTML 引用
SitePoint 终极 CSS 引用

## 级联在行动

会计部打电话给我们一个问题:由于笔误，Laura 的员工 ID 必须从 2 改为 22。对于标准的 MyISAM 表，您需要更改引用雇员 ID 的每个表。但是，我们的 InnoDB 约束确保更改在单次更新后级联:

```
 UPDATE employee SET id=22 WHERE id=2; 
```

如果我们检查我们借用的表，我们会发现更新已经发生，而我们不需要运行额外的代码:

**借:**

| 裁判员 | 员工 id | 书 |
| --- | --- | --- |
| one | one | SitePoint 简单 SQL |
| Two | one | SitePoint 终极 HTML 参考 |
| three | one | SitePoint 终极 CSS 参考 |
| four | Twenty-two | JavaScript 的艺术和科学 |

这是忙碌的一天，我们现在有人事部的电话。约翰从 SitePoint 书籍中学到了很多东西，他离开了公司去建立自己的公司(他在门口被搜身，以确保他归还了所有书籍)。同样，我们需要一条 SQL 语句:

```
 DELETE FROM employee WHERE id=1; 
```

删除操作级联到我们借用的表，因此所有 John 的引用都被删除:

**借:**

| 裁判员 | 员工 id | 书 |
| --- | --- | --- |
| four | Twenty-two | JavaScript 的艺术和科学 |

虽然这是一个简单的例子，但它展示了外键的强大功能。无需额外的代码或一系列复杂的 SQL 命令，就可以轻松保持数据完整性。请注意，在您的更新和删除定义中，除了“级联”还有其他替代方法:

*   **无操作**或**限制**:如果引用表中有一个或多个相关的外键值，更新/删除将被拒绝，即在员工的帐簿被退回之前，您不能删除员工。
*   **SET NULL** :更新/删除父表行，但是将我们子表中不匹配的外键列设置为 NULL(注意表列一定不能定义为 NOT NULL)。

同样的概念可以应用于包含几十个具有相互链接关系的表的大型数据库。

## 分享这篇文章