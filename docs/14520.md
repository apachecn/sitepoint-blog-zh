# 查询的查询

> 原文：<https://www.sitepoint.com/query-queries/>

**除了 [CFCs](https://www.sitepoint.com/article/components-introduction) ，ColdFusion 我最喜欢的一个方面是查询的查询，这是在 ColdFusion 5.x 中引入的**

什么是查询中的查询？根据 [Macromedia Live 文档](http://livedocs.macromedia.com/coldfusion/6.1/htmldocs/using_re.htm)，

从记录集中检索数据的查询称为查询中的查询。生成记录集后，您可以通过使用查询的查询与其结果进行交互，就像它们是数据库表一样。

对我来说，查询中的查询是重新查询现有记录集的能力。例如，假设您有一个显示系统中所有用户的界面。您可能使用了类似于`select * from users order by lastName`的查询，然后以某种方式将它们输出到您的 HTML 中。现在，假设您在输出的顶部列出了字母表，这样用户就可以单击“J”，例如，查看姓氏以“J”开头的每个人。您必须重新查询数据库，并使用类似于`select * from users where lastName like 'j%' order by lastName`的东西。

这样做的缺点是，您现在已经向数据库发送了两个查询，占用了更多的带宽。对于单个请求来说，这不算什么，但是您能想象在一个每天有数千次点击的站点上使用这些查询吗？很快，您将需要升级硬件或网络连接来处理额外的查询和负载。

##### 闪电装载

现在，我们再来看看这个问题。不过，这一次，我们将使用 ColdFusion 和查询的查询功能来减轻负载并增加脚本的功能。让我们假设我们的客户已经请求了一个报告功能，该功能将向他显示以下内容:

*   注册用户总数
*   男性用户总数
*   女性用户总数
*   18 岁或以上男性用户总数
*   18 岁或以上女性用户总数

我们假设我们的表包含以下列:

*   `fname`
*   `lname`
*   `gender`
*   `age`
*   `email`

我们将在所有查询中使用这个表，因为这只是一个简短的教程，所以我们不会详细讨论所使用的数据类型或 SQL 查询的语义。换句话说，我假设您可以创建表，编写基本的 SQL 代码，并理解基本的 SQL 理论。

##### 要求 1:注册用户总数

查看我们的需求，客户端想要的第一项是注册用户的总数。我们的 SQL 将如下所示:

```
<cfquery name="allUsers" datasource="#APPLICATION.dbSource#"> 

  select fname, lname, gender, age, email from users  

</cfquery>
```

***这里有一个小提示:*** 当我建立一个应用程序时，我通常将我的数据库连接名存储在我的 Application.cfm 文件中，并为它分配一些应用程序级别的变量。请随意使其符合您的编码需求和风格。

现在，上面的查询将获取数据库中的所有用户，并返回名为`allUsers`的记录集。您会注意到我没有执行`Select COUNT(*) as count from users`查询，而是选择了选择所有的列并将这些数据放入记录集中。我选择这种方法，因为它允许我们进一步扩展代码，如果将来客户想要详细的列表或报告。如果我们只是运行 count 查询，我们必须对未来的报告进行重大修改，因为实际的用户数据丢失了。

我们有一个包含所有用户的记录集。现在，我们需要输出这些信息来满足客户的请求。我们需要生成一个报告，告诉客户端系统中的用户总数。

系统用户总数:`<cfoutput>#allUsers.RecordCount#</cfoutput><br />`

当放入 ColdFusion HTML(.cfml 或者。cfm)页面，上面的代码将生成一行文本，向我们的客户端显示系统中当前用户的总数。这将显示为由 ColdFusion 服务器自动计算并生成的数值，作为变量`RecordCount`。

##### 要求 2:系统中男性和女性用户总数

现在，我们需要生成系统中男性和女性用户总数的输出。

因为我们已经有了一个包含所有当前用户的记录集，所以我们将编写一个查询来生成输出的其余部分。下面用粗体显示了新代码。

```
<!--- grab all users ---> 

<cfquery name="allUsers" datasource="#APPLICATION.dbSource#"> 

  select fname, lname, gender, age, email from users  

</cfquery> 

**<!--- grab all the men ---> 

<cfquery name="allMales" dbtype="query"> 

  select * from allUsers where gender = 'male' 

</cfquery> 

<!--- grab all the women ---> 

<cfquery name="allFemales" dbtype="query"> 

  select * from allUsers where gender = 'female' 

</cfquery>**
```

上面的代码是我们查询的前两个查询。要执行查询的查询，您必须有一个有效的记录集进行查询。对于这个应用程序，有效的记录集是`allUsers`查询/记录集。

现在，我们用最新的代码更新输出，同样以粗体显示。

```
Total users in system: <cfoutput>#allUsers.RecordCount#</cfoutput><br /> 

**Total male users in system: <cfoutput>#allMales.RecordCount#</cfoutput><br /> 

Total female users in system: <cfoutput>#allFemales.RecordCount#</cfoutput><br />**
```

我们需要进一步查询`allMales`和`allFemales`记录集，这样我们就可以报告 18 岁以上用户的总数。

##### 要求 3:18 岁以上男女用户总数

我们将 SQL 代码部分更改为如下所示(新代码以粗体显示):

```
<!--- grab all users ---> 

<cfquery name="allUsers" datasource="#APPLICATION.dbSource#"> 

  select fname, lname, gender, age, email from users  

</cfquery> 

<!--- grab all the men ---> 

<cfquery name="allMales" dbtype="query"> 

  select * from allUsers where gender = 'male' 

</cfquery> 

**<!--- grab all the men at or over age of 18 ---> 

<cfquery name="allMales18" dbtype="query"> 

  select * from allMales where age >= 18 

</cfquery>** 

<!--- grab all the women ---> 

<cfquery name="allFemales" dbtype="query"> 

  select * from allUsers where gender = 'female' 

</cfquery> 

**<!--- grab all the women at over age of 18 ---> 

<cfquery name="allFemales18" dbtype="query"> 

  select * from allFemales where age >= 18 

</cfquery>**
```

现在更新您的输出代码，如下所示:

```
Total users in system: <cfoutput>#allUsers.RecordCount#</cfoutput><br /> 

Total male users in system: <cfoutput>#allMales.RecordCount#</cfoutput><br /> 

**Total male users in system at or over age 18: /<cfoutput>#allMales18.RecordCount#</cfoutput><br />** 

Total female users in system: <cfoutput>#allFemales.RecordCount#</cfoutput><br /> 

**Total female users in system at or over age 18: <cfoutput>#allFemales18.RecordCount#</cfoutput><br />**
```

让我们花一些时间来深入研究这段代码。您注意到的第一件事是，我们的查询中的查询没有指定数据源，而是指定了数据库类型查询(`dbtype="query"`)。这告诉 ColdFusion 引擎我们想要重新查询现有的记录集，从而创建一个查询的查询。您还会注意到，在我们的 select 语句中，我们没有引用表用户。相反，我们引用了我们的原始查询，在查询的前两个查询中，它是`allUsers`。请注意，我们在 18 岁及以上的搜索中没有指定性别。这个条件最初是在我们的`allMales`和`allFemales`查询中规定的，所以不需要再次应用。

##### 较轻的负荷

这就是了——对查询的查询世界的一个非常基本但强大的介绍。

我相信你想知道这如何减轻你的负担。如果没有一个充满用户的数据库，很难衡量性能的提高。但是，如果您停下来比较一下服务器必须对我们的查询代码执行什么操作，以及如果我们对这些统计数据中的每一个执行一个新的数据库查询，它将完成的处理，那么性能的提高是不可避免的。是的，ColdFusion 必须在第一次查询中花费更多的时间和内存从数据库中检索完整的用户列表，但是，作为回报，它可以通过简单地处理内存中的记录集来动态地计算统计数据，而不是返回数据库，这总是一个代价高昂的过程。

##### 应用查询的查询

什么时候应该使用查询中的查询？依我拙见，当您可以在不妨碍应用程序的编码和设计的情况下限制数据库服务器的连接数时，应该使用查询的查询。我不建议你冲出去运行 10 个`select * from Table`语句，在整个应用程序中使用查询的查询。请记住，您创建的结果集必须存储在某个地方。大多数时候，它们存储在内存中(无论是 RAM 还是虚拟内存),如果您没有正确规划，您的服务器可能会因为内存资源不足而变慢甚至停止。

与任何项目一样，最好先规划好整个系统，然后在寻求提高性能、安全性和整体稳定性的方法时，再回头对其进行多次优化。正是在这个细化阶段，您可以精确定位查询对应用程序和系统资源有益的可能领域。我希望这篇文章能够激励您检查代码，并了解如何在项目中实现查询功能。如果你需要更多的帮助，请随时给我留言，或者在 T2 网站论坛发帖。

## 分享这篇文章