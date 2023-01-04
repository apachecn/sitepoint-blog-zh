# ColdFusion 教程:第一部分文章

> 原文：<https://www.sitepoint.com/cold-fusion-tutorial/>

****本教程系列最初写于 2001 年，可能已经过时。从那以后，我们发布了其他更新的 ColdFusion 教程。这里有一些更新的教程，你可能想看看:****

 ***   [五星！向您的 ColdFusion 应用程序添加评级小部件](https://www.sitepoint.com/article/rating-widget-coldfusion)
*   [变矮:用 ColdFusion 修剪脂肪](https://www.sitepoint.com/article/get-shorty-coldfusion)
*   [ColdFusion 9 为您服务:利用空气进行图像处理](https://www.sitepoint.com/article/image-manipulation-coldfusion-9-air)
*   ColdFusion 9 有什么新功能？

##### 简介:

当你需要为一个网站使用数据库时，这一天终将到来。使用数据库极大地增强了您添加和删除内容的能力，并且总的来说它需要更少的设置工作。

以这个网站为例。由于它的许多文章没有使用数据库，所以必须为每一篇文章制作单独的网页。当然，你可以用 SSI 实现自动化，但是你仍然需要为每篇文章创建一个单独的页面。

有了数据库，这就不同了，你可以简单地制作一个页面来查看你所有的文章。您只需在链接中传递一个变量，比如 ID=2，并告诉数据库为商品编号 2 提供服务。如何做到这一点将在这篇文章中解释。

有 3 种主要的编程语言用于访问数据库。PHP，通常出现在 unix/linux 平台上 ASP，是微软的一个创造，主要出现在 NT 平台上；cold fusion(CF)，也主要出现在 NT 平台上。

本文将向您展示如何使用 ColdFusion 从网站访问数据库，并使用它来显示信息。

##### 关于 ColdFusion

ColdFusion 是由 Allaire Corporation 作为商业产品开发的。作为一个商业产品，它有一些不同于其他服务器端脚本语言的特性。第一个主要区别是 ColdFusion 脚本不必像其他脚本语言一样显示源代码，它可以运行脚本的加密版本。这允许个人出售他们自己的脚本而不用担心盗版，然而它的副作用是阻止了学习型网站管理员使用的免费开源 CF 脚本的流动。第二个主要区别是 ColdFusion 需要钱。服务器端脚本语言与解释器一起工作。取决于要提供的文件名的扩展名。php。asp 或者。cfm，服务器将首先把文件发送给一个解释器，解释器动态地生成你的页面。所以要使用这些语言，你需要在你的服务器上安装它们。PHP 是免费的，ASP 是免费的，但是 CF 是要花钱的，这可能就是它增长缓慢的原因。然而，大多数 NT 主机包已经包含 ColdFusion，所以对于没有自己服务器的人来说，这个价格差异不会有太大影响。

至于语言本身，我以前用过 ASP 和 PHP，我认为 ColdFusion 是最容易学习和使用的。

##### 关于数据库

数据库是任何 CF 网站的后端。出于本教程的目的，我们将使用 Microsoft Access。Microsoft Access 是一个关系数据库，这意味着您可以将表彼此关联起来。现在，本文假设您知道如何操作数据库，并且熟悉字段、行和表等基本术语。

当从一个网站访问一个关系数据库时，不管它是 Oracle、Microsoft 还是 MySQL，也不管你使用的是 CF、PHP 还是 ASP，你都会使用一种叫做 SQL 的语言。SQL 是网站前端和后端之间的桥梁。在本文中，您将了解如何构建和使用基本的 SQL 语句，这些语句可以应用于任何服务器端脚本语言，而不仅仅是 CF。

要让 CF 访问数据库，您需要在服务器上设置一个 DSN。为此，首先上传数据库文件，然后告诉您的服务器管理员，您需要使用 Microsoft Access 驱动程序用 XXXX 名称设置 DSN，并告诉他们数据库文件的位置。

##### 基本冷聚变

首先，用. cfm 扩展名命名所有 CF 文件。ColdFusion 很像 HTML，因为它使用标签来定义许多函数，这可能使它更容易学习。请看下面的例子:

图一。Form.html
`<form method = "post" action = "name.cfm">
<input type = "text" size = "20" name = "name">
<input type = "submit" value = "Submit">
</form>`

图二。name . cfm


现在让我们来检查一下。在 name.cfm 中，您看到的第一个标签是一个<cfoutput>标签。这个标签可以放在网页的任何地方，也可以放在 HTML 标签的中间，如果告诉 CF 解释器你要显示一个变量，这没关系。变量本身包含在#中，这是 CF 标记变量的方式。在某些情况下，当你在网站上使用#的时候，比如声明颜色的时候，这会导致一个问题。这很容易解决，你可以简单地将<cfoutput>标签直接移动到变量的两侧，如下例所示。</cfoutput></cfoutput>

图 3。name . cfm


或者你可以简单地把一个双##，当你需要使用一个英镑符号的另一个目的。最后我们看到了标签，就像 HTML 一样，大多数 CF 标签都需要一个结束标签。

##### 基本 SQL

现在你已经知道了一些基本的 CF，你需要学习一些基本的 SQL。SQL 是一种非常简单的语言。请看下面的陈述:

图 4。基本 SQL
`SELECT Name Height Weight Sex
FROM Friends`

现在让我们来检查一下。假设您有一个包含所有朋友的数据库，您可以将表命名为 friends，并将表单字段命名为 name、Height、Weight 和 Sex。这条 SQL 语句要做的是返回你的朋友列表，以及他们的身高、体重和性别。现在看下一条语句:

图 5。Where 语句
`SELECT Name Height Weight
FROM Friends
WHERE Sex LIKE 'Male'`

现在让我们来检查一下。这个语句会返回你所有的男性朋友。请注意以 WHERE 开头的行，这被称为条件表达式。LIKE 这个词是一个搜索子句。其他搜索条款见表 1。

##### 表 1。搜索条款

**=** 等于姓= '史密斯'
**< >** 不等于名
**< >** '金'<小于年龄<' 20 '
**<=**=小于或等于年龄< = '20'
**>** 大于年龄> '20'
**> 测试 FavMovie 是否存在
**In** 测试值列表 LastName IN ('Smith '，' Jones')
**Is Null** 测试空值 fav movie Is Null
**Is Not Null**测试值 fav movie Is Not Null
**Like**测试模式 fav movie Like“brave heart”**

此外，您可以为这些条件表达式包含一个通配符。例如，如果您希望所有朋友的名字都以“A”开头，您可以使用以下示例:

图 6。通配符
`SELECT FirstName, LastName
FROM Friends
WHERE FirstName LIKE 'A%'`

##### 基本数据库设置

创建数据库时，有些事情你应该知道。首先，不要在你的字段名中包含空格。这样以后会出问题。第二，不要使用保留关键字命名字段名称，如“文本”或“日期”,这也会导致问题。最后，您必须正确指定字段数据类型。下面是数据类型列表，以及通常用来输入数据的内容。

##### 表二。数据类型

**Text** 255 个字符限制–单选，选择，文本，密码
NumberNumber Only–单选，选择，文本，密码
Date/Time 日期或时间(mm/DD/YY)–单选，选择，文本
Yes/NoTrue 或 False 值–复选框
**AutoNumber** 自动对行进行编号–None
**货币**货币格式–单选，选择，文本

上面的桌子上有一些笔记。首先，自动编号数据类型非常适合给文章编号。每次添加新文章时，它都会简单地为其生成一个 ID，该 ID 可用于各种目的，因为每篇文章都有一个唯一的 ID。第二点要注意的是，memo 数据类型是有限制的，但是它太大了，你可能永远也不会达到它。

##### ColdFusion 数据库集成

现在你知道了创建一个数据库集成网站所需的基础知识，是时候把所有东西放在一起了。创建一个名为 Friends 的数据库，并在其中创建一个名为 Friends 的表。创建以下字段和数据类型:

名字–文本
姓氏–文本
年龄–号码
朋友 id–自动号码
性别–文本
收藏电影–文本
收藏颜色–文本
汽车–文本
评论–备忘录

然后为若干人填写适当的值。将它上传到您的服务器，并告诉您的服务器管理员使用 Microsoft Access 驱动程序为它创建一个名为“Friends”的 DSN。

现在我们有了数据库，让我们为它做一个网站。首先，让我们制作一个页面，让人们搜索数据库。

图 7。Search.htm
`<form method = "post" action = "results.cfm">
<h1>Search For Friends</h1>
<select name = "Sex">
<option value = "male">Male
<option value = "female">Female
</select>
<input type = "Submit" value = "Submit">
</form>`

我们有了简单的搜索页面，现在我们需要结果页面:

图 8。Results.cfm
`<CFQUERY DATASOURCE = "Friends" NAME = "MAIN">
SELECT FirstName, LastName, Age, FriendID
FROM Friends
Where Sex LIKE '#sex#'
</CFQUERY>`

<q><div align = " center ">
<b>你找到了以下好友</b><br>
<table border = " 1 ">
<tr>
<TD>
名
</TD>
TD<TD>
姓
< /td ></q>

<q><tr>
<TD>
#名#
</TD>
<TD>
#姓#
</TD>
<TD>
#年龄#
</TD>
<TD>
<a href = " info . cfm？点击这里！</a>
</TD>
</tr></q>

</表>
</格>

现在让我们检查一下上面的内容。如您所见，要执行 ColdFusion 查询，您只需用 SQL 语句填充 CFQUERY 标记，就可以开始了。SQL 语句的任何部分都可以用从上一页传递来的变量替换，就像我对性别变量所做的那样。然后请注意，CFOUTPUT 标签有一个查询参数，这告诉它从一个查询中提取信息，有时您会在一个页面上使用多个查询，这告诉它使用哪个查询。

然后你会注意到我只需要在 CFOUTPUT 标记之间包含一个表格行，这是因为 ColdFusion 将为查询返回的每一行复制一次 CFOUTPUT 标记中的内容。最后，请注意“更多信息”栏中的内容。这是到另一个页面的链接，该页面也将 FriendID 作为参数传递。现在让我们看看那一页。

图 9。info.cfm
`<CFQUERY DATASOURCE = "Friends" NAME = "INFO">
SELECT *
FROM Friends
WHERE FriendID = '#ID#'
</CFQUERY>`

<q><div align = " center ">
<cf output QUERY = " INFO ">
<h1>好友信息为#名字# #姓氏#</h1>
<P>
<b>性别:#性别#</b><br>
<b>年龄:#年龄# < /b</q>

# paragraph format(Comments)#
</cf output>
</div>

现在让我们检查一下上面的内容。在 Select 语句中，我使用*是因为我想选择所有的字段。然后在 where 语句中，我使用了在 URL 中传递的 ID 变量。这次对于输出，我没有把它放在一个表中，因为每个 ID 都是惟一的，而且我知道只返回一行。然后我像以前一样列出变量，只有一个例外。对于注释变量，我包含了 ParagraphFormat 函数。这个函数的作用是识别段落之间的 2 个空行，并在那里插入一个

标签，这样它就可以正确地显示在页面上。

##### 结论

现在，您应该能够创建一个完全集成了数据库的 ColdFusion 网站。请继续关注我的下一篇文章，它将涵盖高级中间编码技术和更高级的 SQL。** 

## **分享这篇文章**