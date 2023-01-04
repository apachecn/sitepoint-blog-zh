# 在中使用 XML 查询定义。网络应用

> 原文：<https://www.sitepoint.com/definitions-net-applications/>

**ADO.NET 的`Command`对象(比如`OleDbCommand`和`SqlCommand`)是。NET 数据库访问策略。如果使用得当，它们可以提供出色的性能和安全性。**

但是，获得这些好处需要你首先付出。NET 提供了大量关于要执行的查询和要使用的确切参数的信息。对于这种配置，您可以使用几个选项中的任何一个，包括。NET IDE 窗体设计器本身(例如当您直接向窗体添加一个`OleDbCommand`或`SqlCommand`对象时)或代码，如:

`// Instantiate a new OleDbCommand object
 OleDbCommand cmdQuery = new OleDbCommand();`

 `//配置实际查询字符串，设置类型(Text vs . stored procedure)
cmd query。CommandText = " SELECT * FROM Customer WHERE Id = @ CustId "；
cmdQuery。CommandType = CommandType。文本；` 

` // Configure any parameters (variables) that need to be passed into the query
 cmdQuery.Parameters.Add("@CustId", OleDbType.Integer);`

这两种方法都有一个小小的缺点。将`Command`配置硬编码到程序中意味着每次您需要做最微小的改变(可能是改变数据库对象名，增加字符串参数的最大长度，修复连接中的错误，等等)。)您必须修改代码、重新编译和重新部署——这是一个不容忽视的过程。

另一种选择是将有关所有查询的配置信息存储在配置文件或设置数据库中，并在运行时使用这些信息，而不是在设计时对查询的确切属性进行硬编码。这样，就有可能将数据库查询从实际应用程序中分离出来，同时仍然保留通过正确构建`Command`对象所提供的强类型优势。这使您可以灵活地修改查询定义，并使这些更改立即生效，而无需重新编译或重新部署。而且，结合非常基本的 XML 语法和解析例程，这个选项可以成为您的工具箱的一部分。

##### XML 语法

在为我的应用程序编写了多年的定制配置文件之后，我终于意识到(和世界上其他人一样),为了这个目的，XML 可以用更少的努力提供更多的功能。我为查询配置选定的语法非常基本。最初，我指定查询的实际文本(或名称)和类型(为此，我使用“text”与“SP”或“StoredProcedure”的值)，并且可能指定超时值(以秒为单位):

`<query command='' type='' timeout='' />`

接下来是一个或多个可选的参数描述:

`<param name='' type='' length='' default='' direction='' />`

从技术上讲，上述所有信息都是可选的，但是在实践中，如果没有某些信息，配置字符串将毫无用处。在初始查询描述中，需要“command”标记，对于参数描述，应该始终指定“name”和“type”标记。

那么，这种语法在实践中是如何工作的呢？假设您有一个 MS SQL Server 存储过程，其定义如下:

`GetCustomerList @StoreId int, @CustomerType varchar(50)`

假设这个存储过程在 Northwind 数据库中。您的 XML 配置字符串类似于:

`<query command='Northwind.dbo.GetCustomerList' type='SP'>
   <param name='retval' type='int' direction='return' />
   <param name='StoreId' type='int' />
   <param name='CustomerType' type='varchar' length='50' />
 </query>`

请注意，我指定了存储过程的完全限定名(包括数据库和所有者)—作为一种毫不费力的性能增强，我尝试在我的存储过程调用中总是这样做(这节省了 SQL Server 在内部查找过程所在位置的工作量)。我还将过程的返回值作为一个参数，在您的应用程序中可能需要，也可能不需要。与该查询中的其他(输入)参数不同，您不能设置“retval”的值，但是您将能够在执行查询后读取它的值。

如果需要覆盖存储过程的 ADO.NET 超时(默认情况下是 30 秒)，可以将查询配置的初始部分修改为:

`<query command='Northwind.dbo.GetCustomerList' type='SP' timeout='300'>`

这将把查询的超时设置为 300 秒，即 5 分钟。

对于基本的文本查询，即任何不是存储过程的查询，只需将整个查询粘贴到“command”属性中，如下所示:

`<query command='SELECT * FROM Transactions
           WHERE TransType NOT IN ("Sales", "Service")' type='Text' />`

如果需要，可以在这种类型的查询中包含参数，例如:

`<query command='SELECT * FROM Transactions
         WHERE TransType=@TransType' type='Text'>
   <param name='TransType' type='varchar' length='50' default='Sales' />
 </query>`

当查询执行时，您可以传入要替换到查询中的“TransType”参数的值。该参数有一个默认值“Sales ”,因此，如果您没有在代码中指定值，将会使用该值。

这里有一个非常不标准的例子，万一你需要发出一批语句(这个例子在 MS SQL Server 中工作)，声明变量等等。：

`<query command="
     IF @StoreId = 0 SET @BatchDesc = '_NoStore_';
     ELSE SET @BatchDesc = CAST(@StoreId AS varchar(25)) + '-';
     SET @BatchDesc = @BatchDesc + CONVERT(char(10), GETDATE(), 101);"
   type='Text'>
   <param name='StoreId' type='int' />
   <param name='BatchDesc' type='varchar' length='255' direction='output' />
 </query>`

该查询将接受一个参数(“StoreId”)并产生一个输出参数(“BatchDesc”)，该输出参数将来自数据库服务器的当前日期与输入的“StoreId”值(如果“StoreId”等于 0，则为“_NoStore_”值)连接成一个字符串。当然，这是一个奇怪的例子，但是它的思想是，您可以自己编写的任何查询(或查询组)，包括逻辑，都可以用这种格式封装。

我应该提到，因为查询配置字符串将被解析为 XML，所以`<`和`>`字符可能会给您带来一点麻烦；你可以通过简单地将它们分别编码为`lt;`和`gt;`来解决这个问题(就像在 HTML 中一样)。所以，与其写这个:

`<query command='SELECT * FROM Customer WHERE Id < 0' type='Text' />`

你会写:

`<query command='SELECT * FROM Customer WHERE Id < 0' type='Text' />`

##### 将 XML 解析为 OleDbCommand 对象

给定上述查询定义的 XML 格式，剩下的工作就是编写一个解析器，将这些信息转换成实际软件中完全配置的`OleDbCommand`对象。我下面描述的解析器是用 C#编写的(尽管将代码转换成其他代码应该很简单。Net 语言，如 VB.NET)并返回一个`OleDbCommand`对象。如果您主要处理 MS SQL Server，并且希望使用`SqlCommand`对象，请继续阅读——我一会儿会谈到这一点。解析器的输入是一个 XML 字符串，如上所述，返回值是经过实例化和配置的`OleDbCommand`:

`public static OleDbCommand cmdGetCommandFromXML(string sXmlFragment)`

通过声明函数`static`，您将能够在不实际实例化任何类型的对象的情况下调用它。在函数内部，您需要做的第一件事是声明一个`OleDbCommand`变量，作为您将要创建的配置的目标:

`OleDbCommand cmdResult = new OleDbCommand();`

接下来，您需要为您的查询定义打开一个 XML 阅读器——这将为您提供一个简单的方法来遍历所有的属性和参数。`XmlDocument`和`XmlNodeReader`类(在`System.Xml`中找到)提供了一种简单的方法来处理这个问题，尽管它们需要一个比我们最初的查询定义稍微更好的 XML 字符串。幸运的是，只需将原始 XML 包含在任何附加标记中就足够了:

`XmlDocument xmlDoc = new XmlDocument();
 xmlDoc.LoadXml("<commanddetail>" + sXmlFragment + "</commanddetail>");
 XmlNodeReader reader = new XmlNodeReader(xmlDoc);`

`XmlNodeReader`类提供了一个`Read()`方法，结合一个简单的“while”循环和几个“case”语句，允许我们遍历 XML 的节点，即`<query />`和`<param />`信息。基本循环如下所示:

```
while (reader.Read()) 

{ 

  switch (reader.Name.ToLower()) 

  { 

    case "query":  

      break; 

    case "param": 

      break; 

    default: 

      break; 

  } 

}
```

现在您有了一个简单的结构，可以将整个 XML 字符串解析成不同的`<query />`和`<param />`标记，但是如何处理每个标记中包含的实际信息呢？`XmlNodeReader`提供了两个属性/方法，`AttributeCount`和`MoveToAttribute`，这将对此有所帮助。

属性告诉你当前节点中有多少属性；例如`<query command='Q' type='SP' />`有两个属性:`command`和`type`。`MoveToAttribute`将允许您将 XML 中的`XmlNodeReader`前进到特定的属性，并获得值。对于`<query />`标签，我们知道我们最多只能处理`command`、`type`和`timeout`标签，所以这段代码变成了:

```
case "query": 

  for (int i = 0; i < reader.AttributeCount; i++) 

  { 

    reader.MoveToAttribute(i); 

    switch (reader.Name.ToLower()) 

    { 

      case "command": 

        cmdResult.CommandText = reader.Value; 

        break; 

      case "type": 

        cmdResult.CommandType = (reader.Value.ToString().ToLower() == "text") ? CommandType.Text : CommandType.StoredProcedure; 

        break; 

      case "timeout": 

        cmdResult.CommandTimeout = Int32.Parse(reader.Value.ToString()); 

        break; 

    } 

  } 

  break;
```

处理`<param />`信息稍微复杂一点，主要是因为需要考虑更多的属性(`name`、`type`、`length`、`default`、`direction`)。对于`type`和`direction`属性，传入 XML 中的值必须映射到。NET(例如:一个类型的`int`必须转换为常量`OleDbType.Integer`的值，而一个方向的`return`必须解释为`ParameterDirection.ReturnValue`)。下一段代码展示了处理这一问题的基础:

```
case "param": 

  OleDbParameter curParm = new OleDbParameter(); 

  for (int i = 0; i < reader.AttributeCount; i++) 

  { 

    reader.MoveToAttribute(i); 

    switch (reader.Name.ToLower()) 

    { 

      case "name": 

        curParm.ParameterName = "@" + reader.Value.ToString(); 

        break;  

      case "type": 

        // Convert the string representation of this data type to 

        // an actual OleDb type constant 

        switch (reader.Value.ToLower())  

        { 

          case "int": 

            curParm.OleDbType = OleDbType.Integer; 

            break; 

          case "tinyint": 

            curParm.OleDbType = OleDbType.TinyInt; 

            break; 

          case "varchar": 

            curParm.OleDbType = OleDbType.VarChar; 

            break; 

          default: 

            break; 

        } 

        break; 

      case "length": 

        curParm.Size = Int32.Parse(reader.Value.ToString()); 

        break; 

      case "default": 

        curParm.Value = reader.Value; 

        break;  

      case "direction": 

        // Convert the string representation of this value 

        // into an actual ParameterDirection constant 

        switch (reader.Value.ToLower()) 

        { 

          case "return": 

            curParm.Direction = ParameterDirection.ReturnValue; 

            break; 

          case "output": 

            curParm.Direction = ParameterDirection.Output; 

            break; 

          case "inputoutput": 

            curParm.Direction = ParameterDirection.InputOutput; 

            break; 

          default: 

            break; 

        } 

        break; 

      default: 

        break; 

    } 

  } 

  cmdResult.Parameters.Add(curParm); 

  break;
```

为了节省空间，我没有指定可以由`OleDbCommand`处理的每一种可能的数据类型。但是，使用上面关于`int`、`tinyint`和`varchar`的例子作为模式，您应该能够看到扩展代码来处理全套的`OleDbType`值是多么容易。

##### SqlCommand 与 OleDbCommand

如果您碰巧在 MS SQL Server 环境中工作，并且希望从 ADO.NET 数据库访问中获得最佳性能，那么您最好使用`SqlCommand`对象，而不是`OleDbCommand`。采用上面提供的解析例程，并构建一个替代版本来配置一个`SqlCommand`对象，是相当容易的。事实上，一旦您将`OleDbCommand`类型更改为`SqlCommand`，您基本上可以对剩余的代码进行搜索和替换，将`Ole`更改为`Sql`。之后，您需要做的唯一一件事就是为`<param />`节点重写`type`开关，以便它使用实际的`SqlDbType`值而不是`OleDbType`(例如`SqlDbType.Int`而不是`OleDbType.Integer`)。

##### 用法示例

这涵盖了如何构建 XML 查询定义以及如何解析它们，但是您可能仍然想知道如何将它们付诸实践。正如我前面提到的，一种选择是将查询定义存储在数据库中，并将一个查询硬编码到您的应用程序中，以便在运行时检索它们。另一种选择是将 web.config 或 app.config 文件用于您的。NET Web 或桌面应用程序来存储这些信息。例如，考虑以下查询定义:

`<query command='SELECT * FROM Customer WHERE Id = @CustId' type='Text'>
   <param name='CustId' type='int' />
 </query>`

鉴于此，您可以在*中的`<appSettings>`部分添加以下内容。config 文件来表示查询(首先选择引用它的名称，在本例中是`GetCustomer`):

`<?xml version="1.0" encoding="utf-8"?>
 <configuration>
   <appSettings>
     <add key="GetCustomer" value="
     <query command='SELECT * FROM Customer WHERE Id = @CustId' type='Text'>
       <param name='CustId' type='int' />
     </query>
     " />
   </appSettings>
 </configuration>`

注意所有`<`和`>`分别转换为`lt;`和`gt;`。在 XML 文档中嵌入 XML 字符串有时会有点棘手。事实上，如果您的查询在正文中使用了“小于”或“大于”，您将不得不对其进行进一步编码:`<`将变成`&amp;lt;`，而`>`将变成`&amp;gt;`。鉴于这种困难，如果你决定使用*。方法，您可能希望编写一个实用程序来在编码的 XML 和标准格式之间来回转换。

现在，要使用*中的信息。在运行时，您可以使用`System.Configuration.AppSettingsReader`对象首先检索 XML 配置字符串，使用您最初选择的名称来表示查询定义(`GetCustomer`)。然后，调用我们在上面构建的解析类来配置`Command`对象，就像这样:

`// Retrieve the XML query configuration string
 System.Configuration.AppSettingsReader rdr = new
   System.Configuration.AppSettingsReader();
 string sQueryDef = (string) rdr.GetValue("GetCustomer",  
                       System.Type.GetType("System.String"));`

 `//实例化并配置 OleDbCommand 对象
oledb command cmd query = oledbdatautils . cmdgetcommandfromxml(sQueryDef)；

//设置任意参数的值，例如:
cmdQuery。参数["@CustId"]。值= 10；` 

` // Set the database configuration, and use the cmdQuery object to populate
 // a DataReader or DataSet, etc.`

##### 结论

对于数据库驱动的应用程序，您在软件中构建查询的方式会对您适应变化的速度产生很大的影响。在已编译的应用程序中，尤其如此。每当发现一个缺陷，数据库发生变化，或者仅仅是发现一个改进，软件就必须被调整、编译和部署。如果您足够幸运地使用支持存储过程的数据库系统，那么您的问题会大大减少——但不会完全消除！在这种情况下，只有当存储过程的名称或参数定义发生变化时，才需要编译和部署软件。

通过将数据库查询从代码的其余部分中分离出来，可以提高应用程序的灵活性和易维护性，并且这可以在不大幅牺牲性能或安全性的情况下实现。无论您选择哪种方法来存储配置信息(无论是在配置文件中，还是在设置数据库中)，使用类似于我在这里描述的语法和解析器意味着许多修改只需要对配置信息做很小的更改，并且可能需要重新启动应用程序。这相当于更容易维护和长期节省时间——我怀疑许多开发人员会抱怨这一点！

## 分享这篇文章