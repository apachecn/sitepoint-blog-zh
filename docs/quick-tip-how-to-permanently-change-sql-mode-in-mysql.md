# 快速提示:如何永久改变 MySQL 中的 SQL 模式

> 原文：<https://www.sitepoint.com/quick-tip-how-to-permanently-change-sql-mode-in-mysql/>

我最近在做一个遗留项目，需要从 MySQL 5.5 导入一些数据。代码中的所有查询在 MySQL 5.5 中都能完美运行，所以我假设升级到 5.7 会是无缝的。并非如此。

![MySQL logo](img/4a8339e744e31799a9dfb16e1f4b78fb.png)

首先，由于导入过程中 DateTime 列被填充了零，我得到了错误，然后当运行以下查询时:

```
select * 
  from ebay_order_items 
  where 
    z_shipmentno is null 
    and ExternalTransactionID is not null 
    and orderstatus = 'Completed' 
    and timestamp > '2015-02-25' 
  group by ExternalTransactionID 
  order by timestamp desc 
```

我得到了这个:

```
Expression #1 of SELECT list is not in GROUP BY 
clause and contains nonaggregated column 
'1066export.ebay_order_items.TransactionID' which 
is not functionally dependent on columns in GROUP BY 
clause; this is incompatible with sql_mode=only_full_group_by 
```

原来`only_full_group_by`模式在 5.7.5 版本中被[默认为](https://dev.mysql.com/doc/refman/5.7/en/group-by-handling.html)。，这打破了许多这样的天真的查询。事实上，更多信息请看[这里](http://www.tocker.ca/2014/01/24/proposal-to-enable-sql-mode-only-full-group-by-by-default.html)。

我*能不能*重写所有的查询以兼容 5.7 版本(向[乔治·费科特](https://www.sitepoint.com/author/gfekete/)致敬以获得解决方案)并做一些像这样糟糕的事情:

```
select extf.* from (
	select ExternalTransactionID
	from ebay_order_items
	where ExternalTransactionID is not null
	group by ExternalTransactionID
) extf JOIN ebay_order_items eoi 
ON (eoi.ExternalTransactionID = extf.ExternalTransactionID)
where eoi.z_shipmentno is null
and eoi.orderstatus = 'Completed'
and eoi.timestamp > '2015-02-25'
order by eoi.timestamp desc 
```

…但这将使已经很复杂的重构变得更加困难。暂时禁用这些检查并强制 MySQL 像在 5.6 中那样运行会好得多。

## 永久更改 SQL 模式

首先，我们找出 MySQL 安装更喜欢哪个配置文件。为此，我们需要二进制文件的位置:

```
$ which mysqld
/usr/sbin/mysqld 
```

然后，我们使用这个路径来执行查找:

```
$ /usr/sbin/mysqld --verbose --help | grep -A 1 "Default options"

Default options are read from the following files in the given order:
/etc/my.cnf /etc/mysql/my.cnf ~/.my.cnf 
```

我们可以看到，第一个首选的配置文件位于`etc`文件夹的根目录下。然而，我的系统中没有这个文件，所以我选择了第二个。

首先，我们找出当前的 sql 模式:

```
mysql -u homestead -psecret -e "select @@sql_mode"

+-------------------------------------------------------------------------------------------------------------------------------------------+
| @@sql_mode                                                                                                                                |
+-------------------------------------------------------------------------------------------------------------------------------------------+
| ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION |
+-------------------------------------------------------------------------------------------------------------------------------------------+ 
```

然后，我们复制这个查询产生的当前字符串，并删除所有我们不喜欢的内容。在我的例子中，我需要去掉`NO_ZERO_IN_DATE`、`NO_ZERO_DATE`，当然还有`ONLY_FULL_GROUP_BY`。新形成的字符串如下所示:

```
STRICT_TRANS_TABLES,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION 
```

我们打开之前决定的配置文件(`/etc/mysql/my.cnf`)，并将下面一行添加到`[mysqld]`部分:

```
[mysqld]
# ... other stuff will probably be here
sql_mode = "STRICT_TRANS_TABLES,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION" 
```

保存、退出并重启 MySQL:

```
sudo service mysql restart 
```

瞧，SQL 模式被永久地改变了，我可以继续开发遗留项目，直到我需要一些额外的严格性。

## 分享这篇文章