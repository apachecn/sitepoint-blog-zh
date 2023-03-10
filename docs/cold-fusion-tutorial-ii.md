# ColdFusion 教程第二部分

> 原文：<https://www.sitepoint.com/cold-fusion-tutorial-ii/>

****简介:**如果你现在正在读这篇文章，那么你应该已经读过这篇文章的第一部分，“ [ColdFusion 教程第一部分——数据库集成](http://www.webmasterbase.com/article.php?aid=310)”如果你还没有读过第一部分，我建议你读一读，否则你可能会对我将要讲的内容感到陌生。在本文的第二部分，我将介绍 ColdFusion (CF)编码技术。我将介绍的所有内容都可以在< CFQUERY >标记之外找到，在< CFQUERY >标记内的高级编码可以在第三部分“ColdFusion 教程第三部分-高级 SQL 和数据库集成”中找到**

##### 1.处理复选框

如果你尝试过我在第一部分中介绍的内容，你可能会在从复选框中获取输入时遇到问题。这是因为当复选框没有被选中时，没有值被提交，甚至没有空值，所以 CF 解释器最终会寻找一个不存在的值。幸运的是，有一个简单的方法可以解决这个问题。

**图一。`CFPARAM`** ` 
<CFPARAM NAME = "addtolist" DEFAULT = "no">`

通过将上面的代码行插入到表单处理页面中，您为解释器提供了一个可以修复问题的默认值。

##### 2.交替行颜色

通常，当显示数据库中的数据时，为了便于阅读，需要改变行的背景颜色。这很容易实现，只需要几行代码:

**图二。`Alternating Row Color`** ` 
<STYLE TYPE="text/css">  
<!--
.row0 {background-color: green;}  
.row1 {background-color: white;}  
-->  
</STYLE>  
<TABLE>  
<CFOUTPUT QUERY="QueryName">  
<CFSET class="row#Int(QueryName.CurrentRow MOD 2)#">  
<TR class = #class#>  
<TD>#Field1#</td>  
<TD>#Field2#</td>  
</TR>  
</CFOUTPUT>  
</TABLE>`

现在让我们检查代码。首先，你有一些 CSS 声明 class .row0 将有这样那样的背景颜色，class .row1 将有不同的颜色。接下来您会看到一个 CFOUTPUT 标记，您现在应该很熟悉了，接下来可能就不熟悉了。CFSET 标签用于给变量赋值。我们用它来定义一个名为 class 的变量，class 的值是通过计算函数 row#Int(QueryName。电流模式 2)#。该函数的第一部分只是文本“row ”,我们可以忽略它。然后我们看到一个#符号，它标志着 CF 表达式的开始。当传递一个值时，Int 函数返回一个整数，它使用的值是 QueryName。电流模式 2。这个值的意思是“当前行数除以 2 的余数是多少。”如您所知，任何东西除以 2 都会得到 1 或 0 作为余数，因此所有偶数行将产生余数 0，所有奇数行将产生余数 1。因此，对于偶数行，变量类将是“row0 ”,对于奇数行，变量类将是“row1 ”,这将允许您替换行颜色。

##### 3.CFIF，CFELSE，CFELSEIF

如果你熟悉任何编程语言，你应该熟悉条件代码背后的概念。我现在将讨论实现这一点的 CF 语法。每个<cfif>标签必须有一个匹配的</cfif>标签，<cfelse>和<cfelseif>标签是完全可选的。您可以根据需要使用任意多个<cfelseif>标签，但是每个<cfif>标签只能使用一个<cfelse>标签，并且<cfelse>标签必须放在最后。</cfelse></cfelse></cfif></cfelseif></cfelseif></cfelse>

**图三。`CFIF Example`**

`<CFIF Login IS "True">  
Welcome.  
<CFELSEIF Login IS "False">  
Sorry, we could not log you in.  
<CFELSE>  
The was a problem processing your request,
please try again or contact the server admin.  
</CFIF>`

比较 CFELSEIF 和 CFIF 标记中的值时，可以使用以下运算符:

**是**——登录是“真”
**不是**——登录不是“真”
**包含**——“KY，MI，CA，FL”包含状态
——T9 不包含——“MI，OH，CA”不包含状态
**大于**——年龄大于“13”
**小于**——年龄小于“62”

 **你也可以用 AND 或 or 来组合陈述，例如:年龄小于“20”，性别是“男性”。

##### 4.CFINCLUDE

要实现 SSI 风格，包括使用 CF，您应该使用<cfinclude>标签。</cfinclude>

**图四。`CFINCLUDE`**

`<CFINCLUDE TEMPLATE = "include.htm">`

我认为这很简单，不需要解释。然而，与 SSI 不同，您只能使用要包含的文件的相对路径。

##### 5.饼干

Cookie 是客户端计算机上的一个变量集，它们对于跟踪访问者、建立登录以及许多其他事情都很有用。要在 CF 中创建一个 cookie，可以使用<cfcookie>标签。然后使用代码从 cookie 中读取，比如:#COOKIE。名称#其中“名称”是 cookie 的名称。</cfcookie>

**图五。 `Cookie Syntax`**

`<CFCOOKIE NAME = "Name" Value = "Value" Expires = "Expiration Date">`

截止日期可以是一个明确的日期，如“81 年 3 月 9 日”，可以是相对天数“100”，也可以是“现在”或“永远”此外，除了上述内容之外，CFCOOKIE 标签还有一些可选属性。通过包含不带参数的单词 SECURE，可以指定需要使用 SSL 安全地发送 cookie，如果 SSL 不可用，则不发送 cookie。通过包含域(Domain = " webdevhq.comwebmasters-network.com ")您可以指定 cookie 适用的域，通过包含 PATH，您可以指定 cookie 适用的 URL 的子集，当使用 PATH 时，域是必需的。

结论在文章的这一部分，我已经介绍了不适用于<cfquery>标签的最常见的 CF 编码技术。在第三部分中，我将介绍直接影响查询的高级 SQL 和 CF 编码。</cfquery>** 

## **分享这篇文章**