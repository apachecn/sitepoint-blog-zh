# MySQL 跨平台表命名

> 原文：<https://www.sitepoint.com/mysql-table-naming/>

开发人员很少考虑 MySQL 表名的大小写。不幸的是，如果您打算将您的应用程序从 Windows 或 Mac 开发环境移植到基于 Linux 或 Unix 的主机上，这可能会带来很大的麻烦。

## 表名区分大小写

MySQL 数据库中的每个表都对应一个物理表。数据库文件夹中的 frm 文件。因此，表是否区分大小写取决于底层操作系统。例如，假设我们已经创建了下表，并在其中填入了数据:

```
 CREATE MyTable (
  id SMALLINT unsigned NOT NULL,
  name VARCHAR(50)
) 
```

然后，我们在代码中运行以下查询:

```
 SELECT id, name FROM mytable WHERE id = 1; 
```

该查询将在 Windows 或 Mac 主机上正常工作。不幸的是，一旦您的应用程序被复制到 Linux 或 Unix，它就会失败。

请注意，数据库、表和触发器的名称受区分大小写的影响。列、索引、存储过程、事件名称和别名不区分大小写。

## 小写表名

**lower_case_table_names** 是一个 MySQL 系统变量，它控制数据库如何解析表名的大小写。它可以作为 mysqld 的启动参数传递，也可以在 my.ini 中传递(尽管这已知会导致 MySQL 的早期版本失败)。该值可以设置为:

**0** *(Linux/Unix 默认)*
表名比较区分大小写。表格使用指定的字母大小写存储在磁盘上。

**1** *(Windows 默认)*
表名比较不区分大小写。MySQL 在存储和查找时将所有表名转换为小写。

**2** *(Mac 默认)*
表名比较不区分大小写。MyISAM 表使用指定的字母大小写存储在磁盘上，但是 MySQL 在查找时会将它们转换为小写。InnoDB 表以小写形式存储。

因此，在您的系统上将 lower_case_table_names 设置为零是很有诱惑力的。然而，Windows 用户应该**而不是**这样做。该手册警告说，如果在不区分大小写的文件系统上设置 lower-case-table-names=0，并使用不同的字母大小写访问 MyISAM 表名，可能会导致索引损坏。恶心。

## 推荐

就个人而言，我更喜欢对所有数据库、表、索引、键和其他 MySQL 名称使用小写命名约定。这似乎是最简单的解决办法。

另请参见:

*   [MySQL 手册:标识符区分大小写](http://dev.mysql.com/doc/refman/5.1/en/identifier-case-sensitivity.html)
*   MySQL:MyISAM 表的利与弊
*   [MySQL:InnoDB 表的利与弊](https://www.sitepoint.com/mysql-innodb-table-pros-cons/)
*   [如何使用 MySQL 外键加快数据库开发](https://www.sitepoint.com/mysql-foreign-keys-quicker-database-development/)

MySQL table casing 曾经让你措手不及吗？

## 分享这篇文章