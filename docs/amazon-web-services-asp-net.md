# 在 ASP.NET 使用亚马逊网络服务

> 原文：<https://www.sitepoint.com/amazon-web-services-asp-net/>

如果你曾经在网上购物，你无疑会在某个时候使用亚马逊。作为 90 年代末网络泡沫破灭后幸存下来的少数零售商之一，亚马逊正日益壮大。使用强大的亚马逊网络服务程序，你可以为你的访问者提供新的功能和建议，并在这个过程中赚一些钱。

##### 亚马逊网络服务 4.0

到底什么是 Web 服务？Web 服务允许您提取和分发应用程序功能的特定方面，以便其他应用程序可以重用这些方面。网络服务使亚马逊能够向开发者提供其目录系统的详细接口。这使得开发人员可以向 Amazon 发送查询，并检索结构化结果以在应用程序中使用，正如我们将在本文中看到的那样。

##### 须知

现在在 4.0 版本中，Amazon 服务需要一个订阅 ID，您必须将它与所有请求一起传递给服务。通过这种方式，您的使用受到监控，并且可以对滥用服务的人进行限制。

订阅是让你使用亚马逊服务的第一步，也是最重要的一步，所以请务必[注册并收到你的订阅 ID](http://www.amazon.com/gp/aws/registration/registration-form.html) 。

那么，我提到的这些限制是什么呢？首先，你不能每秒发出一个以上的请求。这不是自动实施的硬性限制；但是，如果超出此限制，您将违反许可协议。

对缓存也有限制。为了保持您的应用程序响应时间平稳和快速，缓存来自 Web 服务的结果是非常重要的。使用 Amazon Web services，产品信息在系统缓存中的保存时间不能超过 24 小时(注意，某些产品的信息在缓存中的保存时间可以超过 24 小时；查看[许可协议了解详情](http://www.amazon.com/gp/browse.html/ref=sc_fe_c_5_3434651_1/104-8393458-3451965?%5Fencoding=UTF8&node=3440661&no=3434651&me=A36L942TSJ2AJA)。这有助于确保您向访客展示的信息与亚马逊目录同步。此外，任何超过一小时的缓存结果都必须附有时间戳，以显示从 Amazon 检索信息的确切时间。

最后，此免责声明必须附在所有定价信息中:

*“价格以所示日期/时间为准。价格和产品供应可能会发生变化。购买时亚马逊网站上显示的任何价格都将决定该产品的销售。”*

##### 使用亚马逊网络服务 4.0:入门

注意:我已经使用 Visual Web Developer 2005 Express Beta(可以免费下载)开发了以下 Web 应用程序；但是，对于使用 Visual Studio 的用户来说，这个过程是相同的。NET 2003。

一旦您获得了订阅 ID，您就可以开始开发一个简单的应用程序来检索一本书的价格。

1.  打开 Visual Studio，创建一个新的 Web 应用程序:

![1419_image1](img/cd4f0b2f5331fb874d1db2de22e78098.png)

*   创建对 Amazon Web Services 的 Web 引用。

*   单击 IDE 中的网站菜单，并选择添加 Web 引用。
*   输入亚马逊服务的 WSDL(服务描述)文件的地址，当前为[http://web services . Amazon . com/AWSECommerceService/AWSECommerceService . wsdl](http://webservices.amazon.com/AWSECommerceService/AWSECommerceService.wsdl)。

![1419_image2](img/82626b94f27e7a94496ae5b083d3b353.png)

*   单击“添加参考”按钮完成此任务。

*   现在 Web 服务引用已经在您的应用程序中了，您可以构建一个简单的 GUI 来检索和显示图书搜索查询。

*   将文本框、按钮和标签拖放到新应用程序的 Web 表单上。

![1419_image3](img/3c57fa9e04f8b7e679c7a1b204860e1a.png)

您现在可以开始使用该服务。有几种方法可以从 Amazon 上搜索和检索信息，但是我们将使用 ItemSearch。ItemSearch 允许您使用关键字、标题或制造商等标准进行搜索。但是，ItemLookup 允许您进行特定的搜索，例如，使用产品的 ISBN 号。

双击你刚刚放下的按钮。这将打开按钮单击事件处理程序代码。现在，输入以下代码:

```
//namespace 

using com_webservices_amazon 

AWSECommerceService aws = new AWSECommerceService(); 

ItemSearchRequest request = new ItemSearchRequest(); 

request.SearchIndex = "Books"; 

request.Power = "title:" + TextBox1.Text; 

request.ResponseGroup = new string[] { "Small" }; 

request.Sort = "salesrank"; 

ItemSearchRequest[] requests = new ItemSearchRequest[] { request }; 

ItemSearch itemSearch = new ItemSearch(); 

itemSearch.AssociateTag = "myassociatetag-20"; 

itemSearch.SubscriptionId = "XXXXXXXXXXXXXXXXX"; 

itemSearch.Request = requests; 

try 

{ 

  ItemSearchResponse response = aws.ItemSearch(itemSearch); 

  Items info = response.Items[0]; 

  Item[] items = info.Item; 

  Label1.Text = ""; 

  for (int i = 0; i < items.Length; i++) 

  { 

    Item item = items[i]; 

    Label1.Text += "Book Title: " + item.ItemAttributes.Title + "<br />"; 

  } 

} 

catch (Exception ex) 

{ 

  Label1.Text = (ex.ToString()); 

}
```

看起来很复杂？我们来分解一下。任何 Amazon 查询的第一步都是创建一个服务实例，并且是我们希望执行的搜索类型:

`AWSECommerceService aws = new AWSECommerceService();
ItemSearchRequest request = new ItemSearchRequest();`

然后，我们可以用我们想要返回的产品类型的详细信息填充我们的请求对象。

```
request.SearchIndex = "Books"; 

request.Power = "title:" + TextBox1.Text; 

request.ResponseGroup = new string[] { "Small" }; 

request.Sort = "salesrank";
```

```
SearchIndex specifies the particular catalogue we're going to search. Amazon has made available a full list of the catalogues you can search. Power uses the Amazon Power Search syntax to enable in-depth field searching, which is particularly useful with books.

在上面的例子中，我们正在搜索包含表单文本框中输入的字符串的标题。我们可以将它改为“author:”来搜索作者，或者将它们组合起来产生复杂的查询。亚马逊 Power Search 的完整参考也是可用的。

或者，我们可以使用以下代码通过简单的关键字搜索返回所有书籍:

```

```
request.Keywords = TextBox1.Text;
```

属性允许我们定制搜索结果，使其只包含我们需要的信息。使用“小”是因为它包括在搜索结果列表中显示一个典型项目所需的所有数据(产品名称、作者、亚马逊的 URL 等)。).查看[回复群组类型的完整列表](http://www.amazon.com/gp/aws/sdk/)，您可以使用它来更好地定制您的搜索。

最后，我们指定我们希望按照亚马逊上的销售排名对结果进行排序。因此，更受欢迎的产品将出现在我们的结果中。您可以根据列表中的任何[排序类型对搜索结果进行排序。](http://www.amazon.com/gp/aws/sdk/)

```
ItemSearchRequest[] requests = new ItemSearchRequest[] { request };
```

现在，我们可以将请求对象添加到搜索请求数组中。使用这种结构是为了将来我们可以在一个请求中提交更多的搜索来完成。

```
ItemSearch itemSearch = new ItemSearch(); 

itemSearch.AssociateTag = "myassociatetag-20"; 

itemSearch.SubscriptionId = "XXXXXXXXXXXXXXXXX"; 

itemSearch.Request = requests;
```

这个过程的下一步是创建一个`ItemSearch`对象，它封装了我们的搜索请求和我们在 Amazon 上注册的详细信息(订阅 ID 和关联标签，如果有的话)。使用 associate 标签，该服务生成的所有 URL 都将指向您的 associate 帐户，这意味着从您的网站推荐购买的任何产品都将获得报酬。

现在，我们可以使用之前创建的 aws 服务实例来执行搜索。我们将查询结果存储在一个`ItemSearchResponse`对象中；实际的产品结果可以通过一个`Item`对象获得。

`ItemSearchResponse response = aws.ItemSearch(itemSearch);`

正如我前面提到的，我们可以同时向 Amazon 服务发送多个搜索请求；这些都可以通过`response.Items`集合访问。在这种情况下，我们只发送了一个查询，因此我们的结果存储在这个集合的第一个项目中—结果集合位于这个:

`Items info = response.Items[0];
 Item[] items = info.Item;`

我们现在处于特定搜索项目的级别。遍历 items 集合，我们可以通过 item 的`ItemAttributes`属性检索结果的特定值。下面的示例显示了一本书的标题:

`Label1.Text = "";
 for (int i = 0; i < items.Length; i++)
 {
   Item item = items[i];
   Label1.Text += "Book Title: " + item.ItemAttributes.Title + "<br />";
 }
}`

如果我们运行应用程序，并输入 ASP.NET 的查询，我们会得到以下结果:

![1419_image4](img/76442219c7954da198fefb4082a8eb65.png)

##### 贮藏

正如我们之前讨论的，Amazon 服务需要被缓存。多亏了。NET 的易于使用的缓存引擎，我们可以在几行代码中为我们的应用程序添加缓存功能。

首先，我们需要将搜索的响应添加到缓存中。这保存在`ItemSearchResponse`响应对象中。从缓存中访问结果的关键是存储在文本框中的值，这是我们搜索查询的独特部分。根据许可协议要求，此缓存项目将在我们添加后 24 小时内过期:

```
Cache.Insert(TextBox1.Text.ToLower(), response, null,  

    DateTime.Now.AddHours(24), TimeSpan.Zero);
```

在查询开始时，我们现在可以使用`TextBox1.Text`作为我们的键，检查我们的项目是否已经在缓存中:

```
if (Cache[TextBox1.Text.ToLower()] == null)
```

如果我们还没有获得缓存的查询结果，我们可以使用前面定义的代码来获取它。否则，我们需要从系统缓存中检索我们的`ItemSearchResponse`:

```
response = (ItemSearchResponse)Cache[TextBox1.Text];
```

我们完成的代码如下所示，启用了缓存(新行以粗体显示):

```
//have we this in the cache already?  

ItemSearchResponse response;  

if (Cache[TextBox1.Text] == null)  

{  

  AWSECommerceService aws = new AWSECommerceService();  

  ItemSearchRequest request = new ItemSearchRequest();  

  request.SearchIndex = "Books";  

  request.Power = "title:" + TextBox1.Text;  

  request.ResponseGroup = new string[] { "Small" };  

  request.Sort = "salesrank";  

  ItemSearchRequest[] requests = new ItemSearchRequest[] { request };  

  ItemSearch itemSearch = new ItemSearch();  

  itemSearch.AssociateTag = "myassociatetag-20";  

  itemSearch.SubscriptionId = "XXXXXXXXXXXXXXXXX";  

  itemSearch.Request = requests;  

  response = aws.ItemSearch(itemSearch);  

  Cache.Insert(TextBox1.Text, response, null,  

      DateTime.Now.AddHours(24), TimeSpan.Zero);  

}else  

{  

  response = (ItemSearchResponse)Cache[TextBox1.Text];  

}  

Items info = response.Items[0];  

Item[] items = info.Item;  

Label1.Text = "";  

for (int i = 0; i < items.Length; i++)  

{  

  Item item = items[i];  

  Label1.Text += "Book Title: " + item.ItemAttributes.Title + "<br />";  

}  

}
```

##### 亚马逊网络服务到数据集

现在是时候封装这个功能了，这样我们可以更容易地重用服务。内的`DataSet`类型。NET 非常适合在我们的 Web 表单上显示结构化数据。

我们的代码将被分成两个方法。`AmazonToDataSet`方法将从一个`ItemSearchResponse`对象产生一个`DataSet`；`grabAmazonSearch`将封装我们的代码以从查询字符串中检索`ItemSearchResponse`:

```
public DataSet AmazonToDataSet(string PowerSearchString)  

{  

  DataSet ds;  

  //have we this in the cache already?  

  if (Cache[PowerSearchString] == null)  

  {  

    //create our query  

    ItemSearchResponse response =   

        grabAmazonSearch(PowerSearchString);  

    //Construct the dataset to store the results  

    ds = new DataSet();  

    ds.Tables.Add();  

    ds.Tables[0].Columns.Add("ProductName");  

    ds.Tables[0].Columns.Add("AuthorName");  

    ds.Tables[0].Columns.Add("Price");  

    ds.Tables[0].Columns.Add("URL");  

    Items info = response.Items[0];  

    Item[] items = info.Item;  

    DataRow dr;  

    //iterate the results adding new rows with  

    //the values from the search result item.  

    for (int i = 0; i < items.Length; i++)  

    {  

      Item item = items[i];  

      dr = ds.Tables[0].NewRow();  

      dr["ProductName"] = item.ItemAttributes.Title;  

      dr["AuthorName"] = item.ItemAttributes.Author[0];  

      dr["Price"] = item.ItemAttributes.ListPrice;  

      dr["URL"] = item.DetailPageURL;  

      ds.Tables[0].Rows.Add(dr);  

    }  

    //add the dataset to the cache  

    Cache.Insert(PowerSearchString, ds, null,   

        DateTime.Now.AddHours(24), TimeSpan.Zero);  

  }  

  else  

  {  

    ds = (DataSet)Cache[PowerSearchString];  

  }  

//return the dataset  

return ds;  

}  

private ItemSearchResponse grabAmazonSearch(string PowerSearchString)  

{  

  ItemSearchResponse response;  

  AWSECommerceService aws = new AWSECommerceService();  

  ItemSearchRequest request = new ItemSearchRequest();  

  request.SearchIndex = "Books";  

  request.Power = PowerSearchString;  

  request.ResponseGroup = new string[] { "Small" };  

  request.Sort = "salesrank";  

  ItemSearchRequest[] requests = new ItemSearchRequest[] { request };  

  ItemSearch itemSearch = new ItemSearch();  

  itemSearch.AssociateTag = "myassociatetag-20";  

  itemSearch.SubscriptionId = "XXXXXXXXXXXXXXX";  

  itemSearch.Request = requests;  

  response = aws.ItemSearch(itemSearch);  

  return response;  

}
```

如果我们将一个数据网格放到一个 Web 表单上，我们只需两行代码就可以用亚马逊查询的结果填充它:

`Datagrid1.DataSource = AmazonToDataSet("title: " + TextBox1.Text);  
 Datagrid1.DataBind();`

这提供了以下输出:

![1419_image5](img/e98fcdc7c2b5e1e2f4ada1c5c4cfbd73.png)

##### 摘要

在您的网站上使用亚马逊网络服务可以为您的网站创造新的收入机会，并为您的用户提供新鲜的内容。充分利用它！

## 分享这篇文章