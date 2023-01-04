# 参数化 SQL 查询

> 原文：<https://www.sitepoint.com/parameterised-sql-queries/>

在论坛上看，这个问题经常出现。我们应该如何以及为什么在我们的 SQL 查询中使用参数？

重要的事情先来。下面是一个参数化的查询:

 `sqlConnection1.Open();
SqlCommand comm = new SqlCommand("select * from foo where id=@fooId",sqlConnection1);`

 `//用参数的名称创建一个新参数，在本例中是值
SQL parameter id param = new SQL parameter(" @ fooId "，1004)；

comm . parameters . add(id param)；` 

`SqlDataReader dr = comm.ExecuteReader();`

注意，在我们的 SQL 语句中，我们将前缀为“@”的参数放在我们通常输入原始数据的地方。然后用这个替换的名称和要输入的值创建一个参数。

**优势**

参数化查询比非参数化查询有几个优点。首先，它们有助于抵御[注入攻击](https://www.sitepoint.com/blog/),因为 SQL 参数的传递方式阻止了使用撇号和连字符，而这些字符可能会在这种攻击中使用。如果与存储过程结合使用，您还可以使用权限保护过程的执行，并且任何输入都需要在权限持有者的上下文中。

SQL server 还将缓存参数化查询的执行计划。这意味着下次运行相同的查询时，数据库已经知道如何在缓存中执行查询，从而加快访问速度。

**mySQL 和参数**

但不是全部。NET 开发人员使用 SQL Server。一些数据库管理系统，包括 mySQL 不使用命名参数；也就是说，不能为需要添加的参数指定名称。

因此，一个问号“？”用于指定 SQL 语句中正在使用的参数，参数是按照在语句中出现的顺序添加的，如下所示:

 `OdbcCommand comm = new OdbcCommand("INSERT INTO myUsers(name, address, telephone, postcode) VALUES(?, ?, ?, ?);", odbcConn);`

 `comm.Parameters.Add(新 OdbcParameter("，strName))；

comm.Parameters.Add(新 OdbcParameter("，strAddress))；

comm.Parameters.Add(新 OdbcParameter("，strTelephone))；` 

`comm.Parameters.Add(new OdbcParameter("", strPostcode));`

## 分享这篇文章