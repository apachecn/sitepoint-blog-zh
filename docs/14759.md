# 。NET 连接到不同的数据库…一个请求

> 原文：<https://www.sitepoint.com/net-connecting-to-different-databasesa-request/>

你的愿望就是我的命令:)一个关于如何获得快速入门的请求。NET 连接到不同的数据库。

虽然有许多不同的数据库，但几乎所有数据库都允许您通过支持的以下方法之一进行连接。NET: ODBC 和 OLE DB。OLE DB 通常可以提供更好的性能，因为它在向数据库发送请求和从数据库接收请求方面做的工作更少，并且提供了更高级别的数据访问接口，使其更适合于非 SQL 和非结构化数据源。

许多数据库制造商(和第三方)也生产数据库的本地 ADO.NET 接口，这同样可以提高性能。

因此，首先看看您的数据库是否提供了本地 ADO.NET 支持，如果没有，请尝试 OLE DB 支持，如果所有其他方法都失败了，就退回到使用 ODBC:)

所有 ADO.NET 数据提供程序都遵循一组通用的接口，提供连接器、DataReader 和 DataAdapter。还有更多的组件，但让我们暂时专注于这些。

我们以 MySQL 为例。您可以使用第三方数据提供程序，但是，我将展示如何通过 OLE DB 连接到数据库:

通过 OLE DB 到 MySQL 的连接字符串采用以下形式:

`string connectionString = "Provider=MySQLProv;Data Source=mydb;User Id=UserName;Password=password;";`

只需填写您的数据库信息来创建您自己的连接字符串，您可以使用 OleDbConnection 类来创建到您的数据库的连接:

 `OleDbConnection conn = new OleDbConnection(connectionString);`

我们现在可以选择使用 DataSet(对您的数据库执行 SQL 查询，在返回之前检索所有结果，并断开与数据库的连接)，或者 DataReader(执行 SQL 查询，如果结果可用，则根据您的命令进行检索)。

我将概述两者的代码:

使用数据集:

 `OleDbDataAdapter da = new OleDbDataAdapter("select id from foo",conn);
DataSet ds = new DataSet();
int i = adapter.Fill(ds); // i will contain the number of results`

`foreach (DataRow dr in ds.Tables[0].Rows)
{
Console.WriteLine("id: "+dr[0].ToString());
}`

现在，您可以使用数据集来查询结果。

使用 DataReader:

 `OleDbCommand comm = new OleDbCommand("select id from foo",conn);
conn.Open();
OleDbDataReader reader;
reader = comm.ExecuteReader();`

`while (myReader.Read()) {
Console.WriteLine("id: "+reader.GetInt32(0));
}
// Close when done reading.
reader.Close();
// Close the connection.
conn.Close();
}`

如您所见，只有连接字符串选择您的数据库。[Connectionstrings.com](http://www.connectionstrings.com/)是查找特定数据库的连接字符串的绝佳资源。

对于 Odbc，只需将上面示例中对“OleDb”的任何引用替换为“ODBC”。所有都共享相同的接口:)您可能还需要从 Microsoft 下载 Odbc ADO.NET 提供程序，具体取决于您的。NET 和 VS.NET 版(你可以在这里找到这个)。

我希望这能澄清一些事情。试试吧，一如既往，如果你有什么要补充的，就加个评论吧:)

## 分享这篇文章