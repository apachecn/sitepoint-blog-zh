# ColdFusion 组件-简介

> 原文：<https://www.sitepoint.com/components-introduction/>

Macromedia 的 Coldfusion MX 最棒也可能是最受期待的功能之一是 ColdFusion Components (CFCs)。在其最基本的形式中，CFC 是存储在中央文件中的代码和数据的集合。在过去，您可能已经创建了一个包含所有函数的文件，您可以简单地将它包含在一个文档中，以使所有函数对您可用。这是氯氟化碳的一个非常简单的应用，但是，正如我们将要看到的，这并不是全部。

CFCs 代表 Macromedia 将 ColdFusion 带向面向对象编程(OOP)语言的一种尝试。ColdFusion 绝不是一种 OOP 语言，但部分由于 CFCs，它确实拥有一些使 OOP 语言如此流行的属性。例如，ColdFusion 使开发人员能够扩展一段代码，以包含另一段代码中的项目；它还让我们能够跨各种系统和应用程序移植和重用代码。

那么 CFC 是如何让 ColdFusion 更接近 OOP 的，故事的其余部分是什么？

CFC 不是普通的函数包含页。当您创建一个 CFC 时，您可以获得比创建一个包含许多函数的包含页面更多的功能。这些特性和优点到底是什么？它们包括:

*   **安全性**–您可以根据 CF 设置中的用户角色和设置，限制对 CFC 及其方法的访问。
*   **速度**CFC 经过编译，因此执行速度更快。在构建您的站点时请记住这一点，因为第一次调用 CFC 时，它会被编译并返回输出；在向公众发布之前，你可能想浏览一下你的网站。还要记住，每次您对代码进行修改并上传时，CFC 都必须重新编译——但是没有必要担心，因为服务器会为您处理所有这些。
*   **可扩展性**CFC 可以扩展，并且可以与其他 CFC 共享方法，就像 Java 和 PHP 类一样。CFC 也可以使用 SOAP 协议或 URL 调用从外部调用，这允许您将它们提供给其他程序员和其他语言。
*   **可重用性**—如果您对 CFC 进行了适当的编码，并且注意到了可扩展性的含义，那么您的 CFC 可以在服务器之间和脚本之间共享和移动。
*   **本地可重用性**-一旦在页面上调用了 CFC 方法，它通常可以在整个页面上重用。这个经验法则也有一些例外，但是代码重用的潜力意味着您不必在同一个页面上多次调用该函数。
*   **文档**-CFC 可以使用 cfcomponent 和 cffunction 标记中的提示属性生成自己的文档。要查看此功能的工作情况，只需打开一个 CFC，例如，在 Web 浏览器中(注意，您必须使用 RDS 或管理员登录才能查看页面)。服务器将生成一个记录 myCFC 的页面。GA _ googleFillSlot(" Articles _ 6 _ 300 x250 ")；
*   **前瞻性思维**CFC 代表 ColdFusion 的未来；我的观点是，我们现在应该接受它们，并把它们推到极限，这样 Macromedia 的优秀员工就会继续改进它们。

在 ColdFusion MX 出现之前，您可能已经创建了自定义标记或用户定义函数(UDF ),或者找到了开发可重用代码的其他方法。CFC 可以帮助您实现这一目标，同时提供此处列出的额外好处。

##### 术语

在我们进入简单的 CFC 之前，我们需要理解一些关键术语。

首先，CFC 被称为组件，或 ColdFusion 组件，您可以将其视为容器。现在，一个空容器在你装满它之前是没有价值的。我们用方法填充组件，这些方法由`<cffunction>`标签定义。

一旦我们有了一个组件并用方法填充它，我们就可以使用`<cfinvoke>`标签调用特定的方法，这些方法可以在组件中找到。

##### 简单的氯氟化碳

现在你已经对流程和术语有了一个大致的了解，让我们将这些知识运用到工作中，这样你就能真正掌握 CFC 的概念。

我们需要做的第一件事是创建一个新文件，并用. cfc 扩展名保存它。将该文件称为 users.cfc 我们将使用它来存储我们所有的用户方法。我还建议您将所有的 cfc 存储在一个名为 components，CFC 的中央文件夹中——随便您喜欢什么名字。

现在，在您喜欢的文本编辑器中打开该文件，并键入以下内容:

```
<cfcomponent displayname="Users CFC" hint="This is the CFC for my user stuff">  

  <!--- This function gets all the users and their  data from the DB --->  

  <cffunction name="get_all_users" hint="Gets all users in the database" returntype="query">  

    <cfquery name="get_em" datasource=#APPLICATION.db_source#>  

      select * from from users  

    </cfquery>  

    <cfreturn get_em>  

  </cffunction>  

</cfcomponent>
```

保存您的文件，这样您就不会丢失工作，让我们一行一行地检查代码。

`<cfcomponent displayname="user cfc" hint="This is the CFC for my user stuff">`

第一行告诉 CF 服务器这是一个 Coldfusion 组件。`displayname`是生成文档时显示的名称；`hint`包含关于 CFC 整体的描述性文本。

```
 <!--- This function gets all the users and their  data from the DB --->  

  <cffunction name="get_all_users" hint="Gets all users in the database" returntype="query" output="false">
```

接下来我们看到一个描述 CFC 工作的注释。在注释之后是一个带有属性的`name`、`hint`、`returntype`和`output setting`的`cffunction`标签。`name`显示被调用函数的名称。`hint`显示关于此功能的描述性文字。`returntype`告诉 CF 服务器这个函数将返回哪种数据。最后，`output`告诉服务器这个函数是像在`<cfoutput>`标签中还是在`<cfsilent>`标签中一样工作。基本上，我们使用`output`设置来定义我们是希望函数处理`cffunction` ( `output="true"`)中的 cfoutput 标签，还是希望这些标签被抑制(`output="false"`)。

`returntype`你有几个选择，一定要挑一个适合你功能的。这些类型是不言自明的:

*   任何的
*   排列
*   二进制的
*   布尔代数学体系的
*   数字的
*   日期
*   全局唯一标识符
*   询问
*   线
*   结构体
*   Uuid
*   变量名称
*   空的

除此之外，我们还可以定义`access`和`role`属性来帮助控制谁有权访问函数及其输出。`roles`属性与`cflogin`标签协同工作。我们可以定义`roles`等。，带有`cflogin`标记；这些角色决定了用户可以调用和不可以调用的具体方法。我们可以用一整篇文章来讨论这个主题，但简而言之，当您的应用程序利用 ColdFusion 的内置身份验证时，角色对于安全性非常重要。如果您想了解这些主题的更多信息，请查看 [ColdFusion 文档和 Macromedia 网站](http://livedocs.macromedia.com/cfmxdocs/CFML_Reference/contents.htm)。

```
 <cfquery name="get_em" datasource=#APPLICATION.db_source#>  

      select * from from users  

    </cfquery>
```

下一段代码代表我们将对数据库运行的实际查询。您会注意到我使用了变量`#APPLICATION.db_source#`而不是命名一个特定的数据源。这是一个个人代码首选项，所以您可以使用`cfset`标记将该值存储在 application.cfml 中，或者按照 CF Administrator 中的设置指定数据源名称。

```
 <cfreturn get_em>
```

接下来，我们必须将查询返回到调用该函数的页面。因为我们希望将整个查询返回到调用页面，并且因为我们设置了`returntype="query"`，所以我们插入查询的名称，因为它出现在`cfquery`标记的 name 属性中。

对于`cfreturn`标签要记住的一点是，它一次只返回一个值。因此，如果您希望或需要返回多个值，您必须将它们放在一个数组或结构中，然后返回该数组或结构。

```
 </cffunction>  

</cfcomponent>
```

最后，我们关闭函数和组件标签。

##### 使用并致电您的 CFC

所以你有它！您已经构建了第一个组件和函数。但是现在你需要知道如何使用和调用它。为此，我们需要创建一个新页面，并将其保存为 users.cfml 或 users.cfm。一旦该页面打开、保存并准备就绪，请在页面顶部的任何 HTML 代码之前插入以下行:

```
<cfinvoke component="cfc.users" method="get_all_users" returnvariable="u"></cfinvoke>
```

上面一行是我们的`cfinvoke`标签，用于调用组件中的方法。这个问题的分解相当简单。`component="cfc.users"`告诉服务器您想要获取什么组件，以及在哪里可以找到它。当我将我的 cfc 保存在一个名为“CFC”的文件夹中时，我必须使用点符号来告诉服务器位置，以及我想要哪个文件。如果您将您的目录命名为“组件”，您的代码应该是:`component="components.users"`。如果您有一个更复杂的目录结构，比如/components/users/general/，您的代码将类似于下面这样:`component="compontents.users.general.users"`

下一部分，`method="get_all_users"`，告诉服务器我们想要使用哪种方法。最后，我们需要指定如何引用返回的数据。在这种情况下，我们选择了一个简单的“u”，如 user，我们用`returnvariable="u"`指定了它。

接下来，我们希望创建以下 HTML 和 ColdFusion 代码来输出我们的数据:

```
<table width="100%"  border="1" cellspacing="0" cellpadding="3">   

  <tr>   

    <td>Users Name</td>   

    <td>Users Login</td>   

    <td>Users Email</td>   

    <td>Users Status</td>   

  </tr>   

<cfoutput query="u">   

  <tr>   

    <td>#name#</td>   

    <td>#login#</td>   

    <td>#email#</td>   

    <td>#status#</td>   

  </tr>   

</cfoutput>   

</table>
```

这将输出一个漂亮的小表格，表格中的每一列都有一行标题，表格中每个相应的行/用户都有一个新行。

你会注意到我们使用了`<cfoutput query="u">`标签。这允许我们在查询中快速输出数据，而不必使用循环。CF 服务器将知道它需要执行 cfoutput 标记及其包含的代码，直到查询到达输出的末尾。

所以你有它！您已经成功地创建了一个组件和一个函数，在一个单独的页面中调用了该方法，并输出了数据。

我希望这篇文章能帮助你更好地理解 CFCs，并激发你学习更多的兴趣。这绝不是对 CFC 的完整概述，有许多主题已经暗示过，但在这里没有展开。我建议您阅读 Macromedia 网站或您的服务器上的文档，并查看[Macromedia cold fusion DevNet](http://www.macromedia.com/devnet/mx/coldfusion/)上一些很棒的 CF 和 [CFC 专用文章](http://www.macromedia.com/devnet/mx/coldfusion/cfcs.html/)。另外，如果你觉得有必要，可以在这里给我留言或评论，告诉我你的问题或对未来文章的想法。

## 分享这篇文章