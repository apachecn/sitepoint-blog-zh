# 新手的诀窍

> 原文：<https://www.sitepoint.com/trick-for-the-newbies/>

好吧，这一招是为较新的 ColdFusion 程序员设计的，因为大多数有经验的程序员要么知道这一招，要么觉得承认自己不知道很傻。假设你有一个用户表，每个用户都被分配到一个特定的部门。
为了简单起见，我们假设部门 ID 存储在用户的表中，因为一个部门中只能有一个用户。
所以你可能会有一个类似这样的查询

 `<cfquery name="q_getUsers" data-source="#APPLICATION.datasource#">SELECT u.firstName, u.lastName, u.email, d.departmentName, d.departmentID
FROM users u
JOIN department d on d.departmentid = u.departmentid
ORDER BY u.lastname</cfquery>` 

这段 SQL 代码将返回您的表中所有用户的列表，这些用户将按姓氏排序。现在让我们假设当你刚开始的时候，只有几个员工和两个部门。因此，您可以对输出进行如下操作:

 ``<cfloop query="q_getUsers"><cfoutput></cfoutput></cfloop>

| 员工姓名 | 员工电子邮件 | 员工部门 |
| #姓氏#，#名字# | #电子邮件# | #部门名称# |`  `现在你可以想象一下，让用户可以点击一个列标题，如果你愿意的话，可以按那个列排序，但是我们现在跳过。现在快进 2 年，你的公司一直在飞速发展。您现在有 50 名员工和 5 个不同的部门。你那小小的员工列表应用程序已经没用了。你的老板说他喜欢让所有员工都在一页上，但他更希望看到他们按部门分组。你带着那个指引回到你的办公室。
现在，您的第一反应可能是在数据库中查询部门，然后遍历每个部门并获取相应的雇员。这是完全可以接受的，但是让我告诉你一个更快的方法，用更少的代码。
将您之前的查询改为如下所示

 `<cfquery name="q_getUsers" data-source="#APPLICATION.datasource#">SELECT u.firstName, u.lastName, u.email, d.departmentName, d.departmentID
FROM users u
JOIN department d on d.departmentid = u.departmentid
ORDER BY d.departmentName</cfquery>` 

现在，您会注意到我们所做的只是更改了 order by 子句。这在以后变得很重要。现在是有趣的部分！让我们按照老板想要的方式展示它。看看下面这段代码。

 `| #部门名称# |
| #姓氏#，#名字# | #电子邮件# |`  `那么它是如何工作的呢？关键是 order by 子句，以及 cfoutput 标记的位置。首先，你会注意到我们去掉了 cfloop 标记，代之以带有查询和组属性的 cfoutput 标记。
现在你可能知道也可能不知道当你做一个

ColdFusion 服务器将循环查询结果，并输出结果集中每一行的开始和结束标记之间的代码。考虑到这种逻辑，您可能会想，我们将得到一个难看的输出，每个雇员前面都有重复的部门名称。没有 ColdFusion！您将在表的后面看到额外的 cfoutput 标记对(在雇员数据行的 HTML 代码的开头和结尾)。
ColdFusion 将对这些标记外部的所有内容执行一次，内部的所有内容将对每一行重复执行，但它将只输出在该部门有雇员的行。
因此，客观地说，ColdFusion 服务器将执行这段代码一次

 ``然后，它将为每个雇员记录循环这个代码，确保它只输出那些属于上面显示的部门的雇员

`然后它会输出以下代码:`

| #部门名称# |
| #姓氏#，#名字# | #电子邮件# |

 ``来完成这个过程。简言之，ColdFusion 服务器将为您执行双重循环。
这里需要注意的是，cfoutput 标记中的 **group=""** 值必须与 SQL 语句中的 order by 子句相同。

这难道不比你自己做所有的循环更容易吗？

## ``分享这篇文章``