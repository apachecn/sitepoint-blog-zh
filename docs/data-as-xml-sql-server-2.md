# 从 SQL Server 项目中检索 XML 格式的数据

> 原文：<https://www.sitepoint.com/data-as-xml-sql-server-2/>

##### 步骤 2:创建存储过程

为了检索 XML 格式的数据，我们将把查询批处理压缩到一个存储过程中。这鼓励了代码重用策略，并且在将来很容易修改。让我们从在我们的数据库服务器上加载查询分析器开始(`Start -> Programs -> Microsoft SQL Server -> Query Analyser`)。

出现提示时，输入您的数据库登录凭据。您应该连接到创建 myProducts 数据库的服务器。接下来，输入下面的 T-SQL，我将很快解释它:

```
use myProducts  

go  

CREATE PROCEDURE sp_GetExplicitXML  

AS  

  SELECT 1 AS Tag,  

    NULL AS Parent,  

    c.catName as [Category!1!CatName],  

    NULL as [Product!2!ProdName],  

    NULL as [Product!2!Description]  

  FROM categories c  

UNION ALL  

  SELECT 2 AS Tag,  

    1 AS Parent,  

    c.catName,  

    p.productName,  

    d.descText  

  FROM categories c, products p, descriptions d  

  WHERE c.catId = p.productCat AND p.productId = d.descProdId  

  ORDER BY [Category!1!CatName], [Product!2!ProdName]  

  FOR XML EXPLICIT
```

不要被上面存储过程的代码吓到，我保证，这很简单！请允许我一步一步地解释代码。

```
use myProducts  

go  

CREATE PROCEDURE sp_GetExplicitXML  

AS
```

如果您曾经使用过 T-SQL，那么您应该熟悉这些命令。“使用 myProducts”命令告诉 SQL Server“运行我们对 myProducts 数据库执行的任何查询”。“go”命令告诉 SQL server 立即执行该行以上的所有代码。接下来，我们告诉 SQL server 我们将创建一个名为`sp_GetExplicitXML`的新存储过程。存储过程不接受任何输入参数，也不返回任何输出值。

```
 SELECT 1 AS Tag,  

    NULL AS Parent,  

    c.catName as [Category!1!CatName],  

    NULL as [Product!2!ProdName],  

    NULL as [Product!2!Description]  

  FROM categories c  

UNION ALL  

  SELECT 2 AS Tag,  

    1 AS Parent,  

    c.catName,  

    p.productName,  

    d.descText  

  FROM categories c, products p, descriptions d
```

这是我们存储过程的主要代码块。还记得我之前说过，当使用“FOR XML EXPLICIT”模式时，您可以控制返回的 XML 的形状、列名和内容吗？这是为我们做这些的代码。上面的代码作为一个模板，将在其中创建一个通用表。通用表类似于正常映射的 SQL 表，只有几处不同:

*   通用表通过使用“标签”和“父”字段来度量数据层次的深度，从而模拟了层次结构。您会注意到，在上面的代码中，第一个 select 命令的标签为 1，没有父命令。在下一个 select 语句中，有一个标记 2，其父级为 1，这意味着从第二个 select 语句返回的所有结果都将是第一个 select 语句的子级。
*   您可能已经注意到，通用表的列名不同于普通的列名。让我们从上面的代码中分解出一个通用表列名的名称:
*   `[Category!1!CatName]` As you can see, the column name has three values separated by an exclamation mark. The first value is the name of the XML element that the universal table will produce. The second is the tag index, which lets SQL’s internal XML pointer know which level of the hierarchy into which this data will be placed. The last value, "`CatName`" is the name of the attribute into which the value of the select statement will be inserted. A sample category element from our database would look like this:

    <category catname="ASP"></category>

注意第一个 select 语句中有两个空值？这是因为在第一条语句中，我们只关心图书的类别，这些类别来自 categories 表("`FROM categories c`")。这些值保留为空，因为它们将在下一个 select 语句中填充(在这里我们处理 categories、products 和 descriptions 表)。如果这一切听起来有点混乱，请看下图:

![515diagagain](img/fb125e32aedfa92bcd19f3872a542317.png)

现在，到代码的最后一部分。

```
 WHERE c.catId = p.productCat AND p.productId = d.descProdId  

  ORDER BY [Category!1!CatName], [Product!2!ProdName]  

  FOR XML EXPLICIT
```

在我们代码的最后一部分，我们确保每个类别只显示其`productCat`字段等于其`catId`字段的产品。此外，我们使用“`p.productId = d.descProdId`”测试将每个产品与其描述相匹配。最后，我们按照升序的类别名称和产品名称对 XML 输出进行排序(注意:记得按 Alt+X 运行代码)。

**Go to page:** [1](/data-as-xml-sql-server) | [2](/data-as-xml-sql-server-2/) | [3](/data-as-xml-sql-server-3/)

## 分享这篇文章