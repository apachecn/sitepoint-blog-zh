# 构建一个 XML/XSLT 驱动的网站。网

> 原文：<https://www.sitepoint.com/xmlxslt-driven-website-net/>

***感兴趣。网？不要错过[网站。NET 功能指南](https://www.sitepoint.com/using-special-characters-online/) —这是一个极好的资源！***

今天，我们将使用 ASP.NET、C#、XML 和 XSLT 构建一个简单的动态网站。我假设你有 ASP.NET、C#、XML 和 XSLT 的工作知识。这里提供的站点源代码显示了实现的所有细节，包括错误处理。

##### 为什么是 XML 和 XSLT？

有几种不同的方法可以动态生成 HTML，它们通常允许同时编写表示和业务逻辑。虽然使用这些方法编写小型站点很快，但是随着站点的增长，缺乏分离使得现有代码的维护和重用变得困难。

XML/XSLT 方法自然地将表示层从底层业务逻辑中分离出来。这种方法有很多好处:

*   以快速可靠的方式更好地重用业务逻辑代码

*   创建多个可供选择的用户界面，以便为具有不同功能或需求的客户提供方便快捷的服务
*   用户定制，如联合品牌，自定义外观和感觉是可能的
*   更大的开发团队有专门的 UI 设计师和核心开发人员。UI 的分离减少了开发人员之间的依赖性，从而提高了生产率
*   甚至 UI 单元测试也更容易，因为重现特定测试只需要一个 XML 文档

但是，如果 UI/业务逻辑分离是所有这些好处的根源，为什么还要使用 XML 和 XSLT 呢？

把 XSLT 想象成 HTML，因为 XSLT 中的大部分代码都是 HTML。给定，有几个规则要遵守，HTML 必须是格式良好的，但这没有什么显著的不同。有免费的工具可以帮助你将 HTML 转换成 XSLT。W3C 网站上的 HTML Tidy 就是其中之一。

XML 呢？XML 可以表示任何类型的数据结构，并被用作 XSLT 转换的输入。大多数现代数据库(SQL Server、Oracle 等。)可以返回 XML 格式的查询结果。此外，数据库 API(ADO)也可以将其结果转换成 XML 格式。

##### 站点架构

我们将建立的网站的架构很简单。对于每个 HTTP 请求/响应，业务逻辑和数据管理层以 XML 的形式提供适当的数据。表示层为转换提供 XSLT 和不同的参数。XSLT 转换的结果是返回给用户的 HTML。

可重用的表示层支持皮肤和全球化。XSLT 文件存储在名为“UI”的本地目录中。XSLT 文件的每个“皮肤”集都存储在带有皮肤名称的子目录中。XSLT 文件的每个全球化集都存储在皮肤目录下带有地区 id 的子目录中。默认皮肤没有名称，全球化集也是如此。

下面是目录树的样子:

```
UI 

  skin1 

    lcid1 

    lcid2 

  skin2
```

UI 目录包含客户端最终渲染所需的所有 UI 文件，包括图像、CSS、java 脚本等等。

独立于应用程序且可重用的功能被隔离在 UIEngine.cs 文件中的 UIEngine 类中。

该类公开了三个属性:ApplicationBase、Skin 和 Lcid，以及方法 Transform、ExtractUIParameters 和 ExtractAction。ApplicationBase 包含 UI 目录的相对路径，Skin 包含请求的当前皮肤，Lcid 包含请求的区域设置。

Transform 方法使用指定的样式表转换 XML 数据，并将结果 HTML 写入响应对象。

```
Transform(HttpContext ctx, XPathDocument xmldoc,  

string stylesheet, int lcid, XsltArgumentList arg)
```

##### Web 应用程序库

默认情况下，应用程序库是从 Web 中提取的。Config，这是 Web 应用程序的配置文件。这是一个包含 Web 应用程序配置的 XML 文件。该文件包含站点的大量系统设置。有一个名为`<appSettings>`的自定义应用程序设置部分。该配置可通过系统的一部分 ConfigurationSettings 类进行访问。配置命名空间。代码非常简单:

```
<configuration>  

...  

   <appSettings>  

      <add key="ui.base" value="ui" />  

   </appSettings>  

</configuration>  

m_applicationBase = ConfigurationSettings.AppSettings["ui.base"];
```

UI 引擎使用应用程序库、皮肤、区域设置和 XSLT 文件名来确定 XSLT 文件的完整路径:

```
string path = m_applicationBase;  

// check for skin  

if(m_skin.Length > 0)  

{  

    path += m_skin + "\";  

}  

// check for globalization  

if(m_locale > 0)  

{  

    path += m_locale.ToString() + "\";  

}  

// add visualization parameters  

arg.AddParam("ui.path","",path);  

arg.AddParam("ui.skin","",m_skin);  

arg.AddParam("ui.locale","", m_locale.ToString());  

// add the stylesheet  

path += stylesheet;
```

为了切换皮肤，我们在查询字符串中设置皮肤的名称:

```
http://localhost/bookstore/title.aspx?skin=dark
```

因为 HTTP 是无状态协议，所以皮肤名称需要在不同的调用中保持不变。有几种不同的方法:会话变量、查询参数、cookies 和数据库持久性。

如果您的站点设计为使用会话，会话变量将是首选方法。将变量保存在服务器上可以减少页面的复杂性和来回传输的数据量。然而，由于会话管理开销，这样的站点不能像不使用会话的站点那样伸缩。

查询参数和 cookie 方法不需要服务器会话，它们的伸缩性更好，但它们也使站点设计变得复杂，如果用户关闭浏览器对 cookie 的支持，它们将受到影响。另一个问题是安全性:像信用卡号码这样的敏感数据不应该作为查询参数或 cookies 的一部分发送，因为如果不使用安全连接，数据就不会被加密。

由于示例站点不需要会话，参数也很少，所以我选择通过 URL 查询的参数传递状态，也就是说，skin=dark 会自动增加到每个 URL。这是在 XSL 文件中完成的。

```
<a href="title.aspx?skin={$ui.skin}&amp;lcid={$ui.locale}">...
```

可以构建一个显示可用皮肤或地区的配置页面。例如:

```
http://localhost/bookstore/title.aspx?skin=dark&lcid=1026
```

该 URL 将使用皮肤“深色”并本地化为保加利亚语(1026)按标题显示图书列表。请注意，只有 UI 是本地化的；数据本身不是。默认情况下，HTML 的编码是 UTF-8，它适用于各种语言。然而，有些浏览器不支持 UTF-8，特定于地区的编码可能会添加到 XSL 样式表中。

可视化参数作为参数添加到 XSLT 转换中。它们在 XSLT 中是可见的，可以用来查找作为 CSS 和图像的 HTML 元素。例如，我们将使用 ui.path 参数来查找 CSS 文件和图像:

```
<LINK REL="stylesheet" TYPE="text/css" HREF="{$ui.path}css/default.css" />  

<img src="{$ui.path}view.gif" alt="view book details" border="0" />
```

每个 XSLT 文件的附加定制参数可以通过 XsltArgumentList arg 添加，它是 Transform 方法的一部分。

一旦确定了相对路径，就将其转换为绝对路径并加载到 XslTransform 对象中:

```
XslTransform xslt = new XslTransform();  

xslt.Load(ctx.Server.MapPath(path));
```

XML 数据作为字符串传递并加载到 XPathDocument:

```
XPathDocument mydata = new XPathDocument(new   

XmlTextReader(xmldoc,XmlNodeType.Document,null));
```

执行转换，并将结果写入字符串:

```
StringWriter sw = new StringWriter();  

xslt.Transform(mydata,arg,sw);
```

在打开缓冲的情况下，字符串被写入响应对象:

```
ctx.Response.BufferOutput = true;  

ctx.Response.Write(sw.ToString());  

ctx.Response.End();
```

##### 创建网页

完成 UIEngine 后，接下来是创建 Web 页面。至少有两种不同的方法可以用来完成这项任务。默认设置是创建 Web Form aspx 页面，并在 Load 事件中处理呈现。新类是从 System 派生的。Web.UI.Page，加载事件由一个名为`Page_Load`的方法处理。在源代码中，您会找到这样一个页面的示例，author.aspx，尽管它被禁用且未被使用。

我使用的方法是实现`IHttpHandler`。虽然我必须手工编写(目前没有可用的向导)，但代码很简单，不需要多个源文件。此外，编译后的应用程序是单个 DLL:即没有页面(aspx)文件。

```
IHttpHandler interface as part of System.Web namespace and lets you define custom handling of HTTP requests. The interface has one property IsReusable, and one method ProcessRequest. The IsReusable property is set to "true" to allow the object to be reused instead of being created every time is needed. To support session state, IRequiresSessionState has to be implemented as well.
```

如何从浏览器调用这个处理程序？您需要将这个处理程序映射到传入的 URL。这是在 Web 中完成的。配置文件:

```
<system.web>   

...   

  <httpHandlers>   

    <add verb="*" path="*.aspx"    

    type="bookstore.UIHandler,bookstore" />   

  </httpHandlers>   

</system.web>
```

“verb”属性是指 HTTP 动词:GET POST 等。将值设置为'`*`'允许使用任何动词。“type”属性指定处理程序的类名，包括命名空间，第二部分是程序集名称。使用。Net 向导中，将程序集名称设置为项目的名称，在本例中为“书店”。

“路径”属性更有趣。用户可以指定一个特定的 URL，比如 book.aspx、通配符名称(`*.aspx`)或者甚至一个路径— `books/*.aspx`。使用网站名称，三种情况下的绝对 URL 如下所示:

```
http://localhost/bookstore/book.aspx   

http://localhost/bookstore/<anyname>.aspx   

http://localhost/bookstore/books/<anyname>.aspx
```

注意，在第三种情况下，服务器路径被认为是 books/books 和 HttpContext。Server.MapPath 会相应地映射路径。

使用扩展名 aspx 只是因为它已经注册到 Web 服务器。可以使用自定义扩展，但是 Web 服务器需要知道 ASP.NET 将处理这样的扩展。

##### API

该网站有一个简单的 API。一旦扩展被剥离，就有四种资源(或虚拟页面)或动作:

*   `author.aspx` —显示按作者排序的书籍列表
*   `title.aspx` —显示按标题排序的书籍列表
*   `genre.aspx` —显示按流派排序的书籍列表
*   `book.aspx` —显示所选图书的全部详细信息

每个 URL 都带有可选的 UI 参数'`skin`'和'`lcid`'。book.aspx 还需要带有图书 id 的“id”参数。

请求在方法`ProcessRequest`中处理。首先，提取 UI 参数并确定请求的动作:

```
eng.ExtractUIParameters(ctx);   

string action = eng.ExtractAction(ctx);
```

然后，对于每个动作，确定 XSLT 并从业务层逻辑中提取 XML 数据。在这个示例中，所有动作都使用相同的 XML 数据，这些数据在类`CBookData`中实现。

```
CBookData bd = new CBookData();   

mydata = bd.GetData(ctx);
```

动作“book”提取附加参数 id，并将其作为参数添加到 XSLT 转换中。其他情况下，比如参数，也可以传递给业务逻辑层。

```
string id = ctx.Request.QueryString["id"];
```

最后，执行转换，并在用户的浏览器中呈现 HTML 页面。

```
eng.Transform(ctx,mydata,stylesheet,arg);
```

错误由`UIEngine.DisplayError`方法处理，该方法以固定的 HTML 格式显示所有被捕获的异常消息。

```
catch(Exception err)   

{   

   UIEngine.UIEngine.DisplayError(ctx,err.Message);   

}
```

可以使用 XSLT 样式表以与主 UI 相同的方式实现错误页面。

##### 源代码和安装说明

源代码压缩在 code.zip 文件中，你可以从这里下载。有两个 C#文件 UIEngine.cs 和 UIHandler.cs 位于“UI”目录下，包含两个皮肤——默认和“深色”。“深色”皮肤也针对地区 1026 进行了本地化。数据文件“book.xml”位于子目录数据中。

1.  **使用 VC.NET 创建新项目**使用向导“Visual C#项目| ASP.NET Web 应用程序”。输入“书店”作为项目的名称。您可以选择不同的名称，但是您需要修改 UIHandler.cs 中的名称空间和 Web 中的 httpHandlers。以反映该名称。

*   **将 sourcecode.zip 解压到新项目的本地目录**(例如 C:Inetpubwwwrootbookstore)。项目目录中应该有两个文件夹，“UI”和“data”。*   使用“项目|添加现有项目”菜单添加到项目 UIEngine.cs 和 UIHandler.cs。*   可选地，删除由向导创建的 WebForm1.aspx。*   **打开并修改网页。配置文件:**

```
<configuration>   

...   

<system.web>   

...     

    <httpHandlers>   

  <add verb="*" path="*.aspx" type="bookstore.UIHandler,bookstore" />   

     </httpHandlers>   

 </system.web>   

<appSettings>   

          <add key="ui.base" value="ui" />   

</appSettings>   

</configuration>
```

*   **编译**项目。*   **显示列表。**在浏览器中输入 http://localhost/book store/author . aspx。应该会显示按作者排序的图书列表。

这个简单的网站收获了 ASP.NET 的功能，整个代码不到 300 行长，没有用户界面。代码很容易重用，XML/XSLT 的强大功能将帮助您轻松创建和维护动态网站。对于一个真实的站点，需要添加一个生产强度数据库。这意味着设计数据库模式和数据库查询，这对确保网站的良好性能和可伸缩性非常重要。那么就需要一个模块来与数据库通信。作为底层数据库库，ADO.NET 是一个不错的选择。它可以处理各种数据库，它是在。它可以将查询结果转换成 XML 格式。

祝你的网站好运！

*本文是 [SitePoint 的一部分。NET 功能指南](https://www.sitepoint.com/using-special-characters-online/) —对于有抱负和有经验的人来说是一个极好的资源。NET 开发人员。不要错过！*

## 分享这篇文章