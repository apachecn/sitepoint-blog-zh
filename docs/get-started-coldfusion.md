# 开始使用 ColdFusion

> 原文：<https://www.sitepoint.com/get-started-coldfusion/>

ColdFusion MX 是 Macromedia 开发的一款已经很棒的产品的最新版本，Macromedia 可以说是最大、最好的网络软件生产商。通常所说的 CFMX 是建立在 J2EE (Java 2 企业版)之上的，因此比以前版本的 ColdFusion 更健壮。如果你想知道是否应该升级你仍然躺在那里的旧版本 3.1，答案是响亮的“是！”

如果你刚刚开始，你可能有一堆问题。“什么是 ColdFusion？既然 Java 是建立在 J2EE 之上的，我还需要了解它吗？为什么我要用 CFMX 而不是 ASP 或者其他免费的脚本语言？”让我一次回答一个问题:

1.  First of all, ColdFusion MX is a fully functional application server. CFMX will work in conjunction with most popular Web server software available, including Apache, IIS and Deerfield Website. Think of it like this: let’s say you use IIS. In this scenario IIS will act as the batter in a baseball game. The pitcher is a Web user and ColdFusion is the catcher.

    在一个典型的“游戏”中，用户“抛出”一个对 html 页面的 http 请求，然后击球手(即 Web 服务器软件)将请求返回给用户。使用 CFMX，用户“推销”对 ColdFusion 文件的请求。Web 服务器后退一步，让 catcher (ColdFusion)接收 CFML 请求并将其传回给用户。我知道这种类比有点奇怪，但它通常有助于说明 CFMX 在图片中的位置。

    其次，ColdFusion 是一种强大的服务器端脚本语言。相信我，它和其他语言一样强大。这种语言由类似于 HTML 标记的标记和类似于 Visual Basic 中的函数组成。

2.  Third in this line of questions is “Why should I use CFMX instead of another free scripting language?”. I believe that Ben Forta answered that question with a bang in a recent issue of ColdFusion Developer’s Journal. The short version is “you get what you pay for” and CFMX, while it’s not cheap, does deliver a boat-load of features that would cost you many times the price of CFMX in another “free” language.
3.  使用 CFMX 需要懂 Java 吗？不，CFML 在你和繁琐的 Java 之间放了一种简单易用的语言。

好了，够了！让我们开始编码吧！

我已经说过 CFML 类似于 HTML，但是 HTML 和 CFML 有什么区别呢？简单。所有 ColdFusion 标签都以字母“CF”开头，允许您区分什么是静态 HTML，什么是 ColdFusion 代码。当 ColdFusion 读取 CFML 页面并遇到 ColdFusion 标签时，它会执行指定的任务，并将 HTML 返回给最终用户。CFML 如此强大的原因之一是它可以完美地与现有的 HTML 兼容。您可以将 CF 标签嵌入到静态 HTML 中，甚至在许多情况下使用 ColdFusion 为您编写 HTML 代码。让我们试一试。

##### 第一课:CFML 的简单艺术

让我们从最基本和最常用的 ColdFusion 标签开始:
`<CFOUTPUT></CFOUTPUT>`
“cf output”标签允许告诉 ColdFusion 服务器在这些标签之间“输出”或显示数据。`<CFOUTPUT>`本身是没有用的，但是如果你把它和双井号(##)结合起来，它就成了 CFML 语言结构的基础。完整的输出标记如下所示:

```
<CFOUTPUT>#VariableName#</CFOUTPUT>
```

注意:通常不能将 cfoutput 标签放在其他 cfoutput 标签中。如果这样做，您将收到一个错误，告诉您嵌套了无效的 cfoutput 标记。有一种方法和理由可以做到这一点，但它超出了这个简单教程的范围。

现在，让我们产生一个简单的输出。对于大多数编程语言来说。你似乎总是先学习一个“Hello World”脚本，所以让我们从这个开始:

1.  Make sure that you have [installed CFMX](http://www.macromedia.com/software/coldfusion/).
2.  Be sure that ColdFusion MX Service is running.
3.  Create a new folder named Lesson1 under the Webroot of the CFMX Server. C:CFUSIONMXWWWROOTLESSON1
4.  Open Cold Fusion Studio or another of your favorite text editors. I recommend ColdFusion Studio or Homesite for this function. You can also use Dreamweaver MX in code-view. This lesson will not refer to any of the visual CFML in that application.
5.  Create a new blank CFML page. It will look the same as a blank HTML document.
6.  Save this file into your new folder as INDEX.CFM
7.  In between the `<BODY></BODY>` tags, just as you would in HTML, enter the following code:

    ```
    <CFSET MyWorld = 'Hello World'> 

    <CFOUTPUT>#MyWorld#</CFOUTPUT>
    ```

8.  通过打开浏览器并输入您的 IP 地址(大多数情况下为 127.0.0.1)，然后输入端口 8500 和文件夹 Lesson1 来查看页面。应该是这样的:
    http://127 . 0 . 0 . 1:8500/lesson 1/

太好了！现在，让我们来分解这个代码示例:

***分解代码样本***

```
<CFSET MyWorld = 'Hello World'> creates a variable and value on the page you are viewing. In this specific case we've created a variable named "MyWorld" and assigned it the value "Hello World".  In ColdFusion, as in all programming languages, the variable is equal to its value.  So MyWorld will process as "Hello World".每当你需要在这个模板上使用这个变量时，你可以用上面的`CFOUTPUT`块“调用”这个变量。一旦离开这个页面，变量就失效了，必须用`CFSET`重置，或者通过查询字符串传递。

我选择的名字“我的世界”并不是一个神奇的名字...它没有任何神秘的编程属性。如果你想这样做，你可以把你的变量命名为“小丑鞋”。

使用我上面的`<CFSET>`标签，有一些你可能希望遵循的基本准则。

	1.  ColdFusion variable names are NOT case sensitive.2.  ColdFusion variables are typeless.  You do not need to declare whether a variable is a string, number, etc.3.  Consider typing your variable names in "title case" where every word starts with a capital letter, similar to JackPoe or ColdFusion.  This just makes it easier to read them.  Feel free to adopt any coding practice you see fit, however, you should pick one naming practice and stick to it from here on out.4.  *Never ever* use spaces or punctuation in your variable names... ever.5.  Your variable name should not start with a number.6.  让你的变量名容易记住并且简短(如果可能的话)。这使得它们更容易记忆，打字也更快。避免创建像“MyThirdVariableHere”这样的变量名。如果你不得不打几十次，它会很快变得很累。
因此，快速回顾并绘制一个典型的`<CFSET>`标签:

```
 <CFSET MyScript = 'Hello Papa Smurf'>  

    The tag being used  

      The variable name  

        The value you are assigning   

to the variable
```

现在让我们看看`<CFOUTPUT>`块:

```
 <CFOUTPUT>#MyScript#</CFOUTPUT>
```

您会注意到变量名用双井号或井号括起来:##。如果你玩类比，这些就像 ColdFusion 的“指纹”。ColdFusion 服务器“掸去”模板上的灰尘，只要发现这些“指纹”,它就会说“啊哈！我需要在这里做点什么。”
在这种情况下，服务器接收到一个显示名为“MyWorld”的变量值的请求。因此，它不会显示单词“MyWorld ”,而是显示变量的值，在本例中是“Hello World”。
让我们再做一个——这次我们将引入两个简单的函数。

```

*   `Now()` -该函数从服务器获取当前日期和时间，并以标准时间戳的方式对其进行格式化。
*   `DateFormat(date, 'mask')` -使用此函数，我们将覆盖用`Now()`创建的日期的默认格式，并告诉 ColdFusion 我们希望它如何格式化。

```
<CFOUTPUT>#DateFormat(Now(), 'MM/DD/YYYY')#</CFOUTPUT>
```

这行代码将获取当前日期，并将其重新格式化为类似 04/12/2003 的格式。最酷的是，你可以把这整团代码当作普通文本，就像这样:

```
Hello, Today Is <CFOUTPUT>#DateFormat(Now(),'MM/DD/YYYY')#</CFOUTPUT>.
```

以这种方式使用这段代码，您将看到:

```
Hello, Today is 04/12/2003
```

将此代码添加到索引中。CFM·佩奇并尝试一下。您还可以在这段代码周围添加一些字体标签或其他格式的 HTML，以达到您想要的效果。

关于`DateFormat()`函数的更多用法和格式化选项，请参考本文末尾的函数参考部分。

您会注意到我们在这个代码示例中嵌套了函数。

```
<CFOUTPUT>#DateFormat(Now(), 'MM/DD/YYYY')#</CFOUTPUT>
```

ColdFusion 允许您永远组合或“嵌套”函数。您可以组合的功能数量没有限制。

ColdFusion 的成功基于两点:

1.  Know your code. Spend the cash to buy a current CFML Language Reference book and study it. It really is interesting reading.
2.  练习。和任何事情一样，熟能生巧。

请注意，在某些情况下，您可能希望向用户显示一个井号。要做到这一点，你需要“逃离英镑符号”。这是通过将两个##放在一起，中间没有任何信息来实现的。ColdFusion 会将其解释为单个字符，而不是要处理的变量。

例如，如果您在表格周围使用了`<CFOUTPUT>`标记，并且在您的 HTML 标记中试图指定一个`BGCOLOR="#FFFFCC"`，ColdFusion 会将其视为一个进程的开始。为了正确地避免这种情况，你可以输入`BGCOLOR="##FFFFCC"`

##### 第 2 课:使用 ColdFusion 显示数据库内容

也许 ColdFusion 最大的特点是它与数据库交互的方式非常简单。CFMX 可以连接到你能想到的任何数据库… Oracle、MS SQLServer、MySQL(我个人最喜欢的)、MS Access、FileMaker、Sybase、Informix(我最不喜欢的！)还有更多！

要使用 CFMX 和数据库，您需要了解两件事。ODBC 和 SQL。

*   ODBC 代表开放式数据库连接，是 ColdFusion 连接到大多数标准数据库的基础。
*   SQL 或“See-Quill”是用于与数据库对话的基本语言。作为一名开发人员，这种简单的语言可以成就你，也可以毁掉你。我建议拿一本本·福塔的《10 分钟学会 SQL》。不过，本课假设您不了解 SQL，所以我将保持简单。

首先，我们需要一个数据库！为了节省时间，我创建了一个非常简单的数据库[，供你下载](https://www.sitepoint.com/examples/cfml/startingcfml.zip)并使用。它至少需要 Microsoft Access 2000，所以在继续之前请确保您已经安装了该软件。

现在，我们需要创建一个简单的 ODBC 连接。这将需要连接到 CFMX Administrator，它是一个控制面板，允许您与 ColdFusion 应用程序服务器进行交互。

1.  Browse to 127.0.0.1:8500/cfide/administrator
2.  Enter your password. You should know this… if you installed the server properly. If you forget it, look at [CFFAQ.com](http://www.cffaq.com) for details.
3.  In the left navigation, click the “Data Sources” link.
4.  In the Data Source Name field, enter “CFData” without the quotes.
5.  Open the dropdown list, select Microsoft Access and click ADD.
6.  Browse the server to the database, or type in the path to the file manually.
7.  Click SUBMIT.
8.  如果您做的一切都正确，CFMX 将显示一条消息，说明数据源已成功更新，并且您将在连接的数据源列表中的数据源旁边看到“OK”。

现在您已经有了一个 ODBC 连接，让我们来看看数据库本身。这个数据库尽可能简单，只包含一个保存少量记录的表。在现实世界中，这种情况很少发生。

如果您不熟悉关系数据库，Access 是一个很好的实践场所，但是，给你一个忠告:Access 并不是满足您的数据库需求的最佳解决方案。如果你正在寻找低流量和少量的数据更新，那么你就可以使用 Access。如果您正在针对成千上万条记录构建一个庞大的数据库，并且/或者预期会有很高的使用率，请考虑尝试 MySQL…如果您负担得起，请尝试 SQLServer 或 Oracle。

在这种情况下，我们的数据库在名为 INFO 的表中包含一些记录。如果你不知道什么是数据库表，就把它想象成一个保存所有数据的大电子表格。该表中的记录由存储在列中的数据组成。真的很简单。

该数据库包含以下列:

```
 NAME   

  ADDRESS   

  CITY   

  STATE   

  ZIP   

  PHONE   

  EMAIL   

  NOTES   

  ID
```

注意 ID 一栏是你的`PRIMARY KEY`。这是 Access 为数据库中的每条记录创建唯一标识符的标准方式。

现在，我们将使用 CFML 把一些数据吸到屏幕上。

1.  Create a new folder under the CFMX Webroot, named LESSON2.
2.  In your text editor, create a new file and save it as ALLDATA.CFM
3.  Now, in that file, add this code between the BODY tags:

    ```
    <CFQUERY DATASOURCE="CFdata" NAME="getall">   

    SELECT *   

    FROM Info   

    ORDER BY Name ASC   

    </CFQUERY>
    ```

4.  Then below this code, add the following HTML:

    ```
    <table align="center" border="1">   

    <CFOUTPUT QUERY="getall">   

    <tr>   

      <td>#CURRENTROW#</td>   

      <td>#NAME#</td>   

      <td>#ADDRESS#<BR>#CITY# #STATE#, #ZIP#</td>   

      <td>#PHONE#</td>   

      <td>#EMAIL#</td>   

      <td>#NOTES#</td>   

    </tr>   

    </CFOUTPUT>   

    </table>
    ```

5.  保存页面并在浏览器中查看。

它还不漂亮——但很有效！

让我们回顾一下这段代码的作用。

标签是 CFML 从数据库中检索数据的手段。我们指定的属性`DATASOURCE`和`NAME`，是 CFMX 所需要的。这个标签的`NAME`属性由你指定，可以是任何容易记住的单词。查询名称不能包含空格或标点符号。

在开始和结束的`CFQUERY`标记之间是 SQL 查询本身。这是*不是* CFML，这是 SQL，数据库语言。那么我们告诉数据库做什么呢？我们来详细看看这个。

*   `SELECT *`
    This tells the database to get records. The asterisk means *all* records. So In short, we’ve told the database to `SELECT ALL`.
*   `FROM Info`
    Well, now that the database has been told to get all records, it follows that we need to tell it where to get them from. This line tells the database the name of the `TABLE` from which we want to select the data. In this case, we want the data from our table, Info.
*   `ORDER BY Name ASC`
    这最后一行真的只是肉汁。它告诉数据库，既然我们已经从 Info 表中选择了所有数据，我们希望它按照列`NAME`按字母升序排列。如果你想按降序排列数据，你可以使用缩写`DESC`。

将 CFML 和 SQL 结合在一起的好处在于，您可以轻松地使用 CFML 来扩充您的查询，以准确地检索您需要的数据…这就引出了本课的最后一部分。

##### 第 3 课:使用 CFML 搜索数据库

在第二部分中，我们使用 CFML 将整个数据库转储到屏幕上。这对于小型数据库来说很好，但是在现实生活中很少有用，并且会导致一些严重的性能问题。最简单的解决方案是让用户能够搜索数据库，找到他们需要的东西。对 CFML 来说，这是小菜一碟。

1.  Create a new file inside the lesson2 folder named INDEX.CFM
2.  Between the BODY tags of this document, add an HTML form like so:

    ```
    <div align="center">    

      <form action="search.cfm" method="POST">    

      Search for:<input type="text" name="k">    

    <input type="submit" value="Find!">    

      </form>    

    </div>
    ```

3.  Now create another new page named SEARCH.CFM
4.  Between the `BODY` tags we need to deal with validating the submitted form. Add this code:

    ```
    <CFIF len(FORM.k) IS 0>    

      Oops!<br>    

      You need to enter some search criteria!<br>    

      <a href="javascript:history.back()">Go back</a>     

    and try again.    

      <CFELSE>    

    <CFQUERY DATASOURCE="CFdata" NAME="findem">    

    SELECT *    

    FROM Info    

    WHERE Name LIKE '%#FORM.k#%'    

    </CFQUERY>    

    <CFIF findem.recordcount IS 0>    

      Sorry!<br>    

      Your search for <CFOUTPUT>#FORM.k#</CFOUTPUT>     

    did not return any matches.<br>    

    <form action="search.cfm" method="POST">    

      Search again:<input type="text" name="k">    

    <input type="submit" value="Find!">    

      </form>    

    <CFELSE>    

    <!--- display your results --->    

    <table align="center" border="1">    

    <tr><td colspan="6" align="center">Your search returned     

    <cfoutput>#findem.recordcount#</cfoutput> matches.</td></tr>    

    <CFOUTPUT QUERY="findem">    

    <tr>    

      <td>#CURRENTROW#</td>    

      <td>#NAME#</td>    

      <td>#ADDRESS#<BR>#CITY# #STATE#, #ZIP#</td>    

      <td>#PHONE#</td>    

      <td>#EMAIL#</td>    

      <td>#NOTES#</td>    

    </tr>    

    </CFOUTPUT>    

    </table>    

    </CFIF>    

      </CFIF>

    ```

5.  保存页面并在浏览器中试用。

现在，让我们回顾一下代码。这次有很多，所以我们将把它分成几块。

索引。CFM·佩奇没有任何花招。这只是一个简单的 HTML 表单。我将文本字段命名为“k ”,代表“关键字”...我就是这么做的。

搜索。CFM 有很多你可能从未见过的 CFML。您看到的第一个新标签是`<CFIF>`。这是我们在 CFML 用来处理条件处理的标准方法。这个标签有几种变体。要了解全部细节，请查阅本文末尾的参考资料。基本上，这个特定的用途是阻止用户在没有输入任何搜索关键字的情况下提交搜索表单。

条件处理允许开发者说“如果天空是蓝色的，就这样做。否则做这个或这个”。这允许您捕获事件和可能的错误，并控制它们做您所需要的事情。

```
<CFIF len(FORM.k) IS 0>
```

上述标记的“`len(FORM.k) IS 0`”部分使用`LEN()`函数来确定提交的表单字段内容的长度。在这种情况下，如果表单字段的长度为零，我们将停止处理页面。

```
<CFELSE>
```

这个标签是“否则”条件。因此，如果用户提交正确的搜索标准，CFMX 将继续处理页面。

让我们仔细看看这个 SQL。前两行应该很熟悉，但最后一行可能是新的。

```
WHERE Name LIKE '%#FORM.k#%'
```

这是 SQL 中的`WHERE CLAUSE`。它用来告诉数据库你到底想要选择什么数据。基本语法是`WHERE ColumnName LIKE`‘某物’。这个“某物”，如果用引号括起来，一定是一个`STRING VALUE`——一个或几个单词。没有引号，你告诉数据库匹配一个数字。在这种情况下，我们让 ColdFusion 用用户提交的表单字段值填充空白。

通配符:与许多语言一样，SQL 允许您使用通配符来增强搜索能力。在上面的代码中，我们使用百分号来表示通配符。在百分号内，我们放置提交的表单变量“k”。以下是一些例子:

*   `%variable%`表示包含变量值
*   `variable%`表示以变量值开始，以任意值结束
*   `%variable`表示以变量值结束，以任何值开始。

我们需要检查的结果代码的下一部分是下一个`CFIF`块。这段代码检查查询的`RECORDCOUNT`,如果它不为零——意味着查询返回任何大于零的数字——页面将进行处理。接下来是一个说明找到多少匹配记录的部分。注意变量的调用方式:`FINDEM.RECORDCOUNT`。这使用了查询变量`RECORDCOUNT`，并且只显示一次。如果您将这段代码放在一个包含`QUERY`属性的`CFOUTPUT`中，这段代码将为找到的每条记录重复一次！一点用都没有！

每个 ColdFusion 查询返回三个自动变量。`CURRENTROW`是每条记录的实际编号。例如，如果您的查询返回 50 条记录，`CURRENTROW`会将它们编号为 1-50。`RECORDCOUNT`以数字形式返回查询找到的记录数量。`COLUMNLIST`返回搜索的表格中以逗号分隔的列列表。

所以你有它！冷聚变和 CFML 的基础知识。正如你所看到的，这需要一些工作，但 CFML 是一个简单而有效的方法来创建任何你能想到的 Web 应用程序。请继续关注 CF 上的更多内容！

##### 标签和函数引用

```
<CFOUTPUT></CFOUTPUT>

Displays the output of a database query, variable or other operation.

Syntax:

```
 <cfoutput      

  query = "query_name"     

  group = "query_column"     

  groupCaseSensitive = "Yes" or "No"     

  startRow = "start_row"     

  maxRows = "max_rows_output">     

</cfoutput> 

```
<CFSET>

Defines a ColdFusion variable. 语法:

```
<cfset variable_name = expression> 
```

`<CFQUERY></CFQUERY>` 
将查询或 SQL 语句传递给数据源。
语法:
 `<cfquery      

  name = "query_name"     

  dataSource = "ds_name"     

  dbtype = "query"     

  username = "username"     

  password = "password"     

  maxRows = "number"     

  blockFactor = "blocksize"     

  timeout = "seconds"     

  cachedAfter = "date"      

  cachedWithin = "timespan"      

  Either of the following:     

    debug = "Yes" or "No"     

  or:     

    debug     

  SQL statement(s) >     

</cfquery>` 
`<CFIF></CFIF>` 
在 CFML 中创建简单和复合条件语句。测试表达式、变量、函数返回值或字符串。可选地与`cfelse`和`cfelseif`标签一起使用。
语法:
 `<cfif expression>     

  HTML and CFML tags     

<cfelseif expression>     

  HTML and CFML tags      

<cfelse>     

  HTML and CFML tags     

</cfif>` 
`LEN()` 
决定一个字符串或二进制对象的长度。
语法:
 `Len(string or binary object)` 
`NOW()` 
获取运行 ColdFusion 服务器的计算机的当前日期和时间。返回值可以作为参数传递给日期函数。
语法:
 `Now()`
`DATEFORMAT()` 
格式化一个日期值。支持美国日期格式的日期。
语法:
 `DateFormat("date" [, "mask" ])` 
![1138_reference](img/75ded194784db5acc9b52b2cd4164289.png)
*标签规范摘自《ColdFusion MX 语言参考指南》，
版权所有 Macromedia，Inc.* 

```

```

```

## 分享这篇文章