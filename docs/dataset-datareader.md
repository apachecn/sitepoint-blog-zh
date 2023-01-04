# 数据集与 DataReader 文章

> 原文：<https://www.sitepoint.com/dataset-datareader/>

**我喜欢在编码的时候做尽可能少的工作，所以我曾经很喜欢数据集。只需 3 行代码就可以填充并准备好，然后使用一个漂亮、简单的 foreach 循环进行迭代(如果使用类型化数据集就更容易了！).这是一个很好的收集工作。但是通常，性能是以牺牲优雅为代价的——尤其是在性能关键的 Web 应用程序上。**

数据集实际上使用 DataReader 来填充自身。DataReader 是一种精简的访问方法，只要结果可用就返回，而不是等待整个查询被填充到数据集中。这可以极大地提高您的应用程序性能，而且，一旦您习惯了这种方法，它本身就非常优雅。

##### DataReader 的实际优势

为了突出使用 DataReader 相对于 DataSet 的优势，下面是一个使用 DataSet 的示例。以下代码使用表中的结果填充数据集，并输出每行中的第一个字段:

```
 SqlConnection conn = new SqlConnection(connectionString); 

  SqlDataAdapter a = new SqlDataAdapter 

 ("select * from mytable;",conn); 

  DataSet s = new DataSet(); 

  a.Fill(s); 

  foreach (DataRow dr in s.Tables[0].Rows) 

  { 

    Console.WriteLine(dr[0].ToString()); 

  }
```

如您所见，我们实际上并没有开始实际的数据检查(foreach 循环)，直到填充了整个数据集。有时我们可能不会使用所有的结果，或者我们可能会在检查时执行其他代码(进度条的进度就是一个小例子)。使用数据集时，这只能在获取完整的结果并将其传递到数据集中的各个集合中之后发生。

相比之下，下面的代码使用 DataReader 代替 DataSet 获得了相同的结果:

```
 SqlConnection conn = new SqlConnection(connectionString); 

  SqlCommand comm = new SqlCommand("select * from mytable", conn); 

  comm.Connection.Open(); 

  SqlDataReader r =  

      comm.ExecuteReader(CommandBehavior.CloseConnection); 

  while(r.Read())  

  { 

    Console.WriteLine(r.GetString(0)); 

  } 

  r.Close(); 

  conn.Close(); 
```

在这里，通过使用`while`循环，一旦数据可用，就进行检查，如果没有找到更多结果，则`r.Read()`返回 false。因此，我们不仅可以随时检查，而且 DataReader 在客户机上一次只存储一个结果。与存储整个查询的数据集相比，这大大减少了内存使用和系统资源。

##### 当只有数据集满足要求时

现在，有时候只有一个数据集就足够了。通常，您需要序列化结果，或者将查询结果传递给应用程序的下一层。在这些情况下，需要一个集合，数据集为此提供了一个支持良好的机制。例如，您可以通过调用 WriteXML 方法将数据集快速序列化为 XML，或者在 SOAP 方法中传递数据集。虽然您可以创建自己的集合来存储您的结果，但有了这些内置的优化功能，数据集仍然是一个需要记住的强大类型。

然而，对于 Web 应用程序使用的大多数查询，数据被发现、显示，然后被遗忘，DataReader 将显著提高您的性能，只需要一点额外的工作。听起来对我来说是一笔好交易！

## 分享这篇文章