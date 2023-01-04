# 对 ColdFusion 函数和运算符运行性能指标

> 原文：<https://www.sitepoint.com/running-performance-metrics-on-coldfusion-functions-and-operators/>

*…发帖人* [大卫·梅德洛克](https://www.sitepoint.com/forums/member.php?u=24385) *:*

您是否注意到开发人员在陈述各种操作符和函数与其他函数和操作符相比表现如何？例如，<cfif not="" variable="" eq="" variable2="">与<cfif variable="" neq="" variable2="">相比表现如何？真的有显著差异吗？</cfif></cfif>

在我最近的一篇关于查询的文章中，我使用了一个代码块，它输出一个表中查询的列，然后输出下面的值。在那个查询中，我使用了 Evaluate()函数来显示列的值。一位目光敏锐的读者向我指出，使用数组符号比使用 Evaluate 函数更好，如果可能的话，在任何情况下都应该避免使用 Evaluate 函数。下面是我使用的代码块:

 ``<cfloop list="#GetUsers.ColumnName#" index="column"><cfoutput><cfloop list="#GetUsers.ColumnName#" index="column">| #列# |
| #评估(列)# |</cfloop></cfoutput></cfloop>` 

建议是我应该使用 GetUsers，而不是使用 Evaluate(column)

. Now, I’d like to say a couple of things here. First of all, this was a simple demonstration and using Evaluate(column) versus GetUsersis not going to make any performance difference whatsoever when displaying a small amount of data. It’s merely personal preference.

现在，我一点也没有被这样的言论所冒犯。我喜欢看别人如何编写他们的应用程序，嘿，如果它能加速我的应用程序使用不同的方法，我洗耳恭听！但有时开发者会走极端。我决定做一些研究，看看使用 Evaluate(column)是否真的比 GetUsers 快

. I set up a very simple experiment to test this.

这个实验并不完全全面，我邀请每个人来尝试这个实验(在开发机器上，而不是在服务器上！)用不同的变量来看你会得到什么结果。

首先，这是我使用的代码:

 ``<cfscript>//这只是建立了测试查询
qry = query new(" col 1，col 2 ")；
for(I = 1；我 lte 10I = I+1){
QueryAddRow(qry，1)；
query set cell(qry，" col1 "，i * 2，qry。record count)；
query set cell(qry，" col2 "，i * 3，qry。record count)；
}
</cfscript>` 

 `<cfoutput>Entire Process Time: #endTest# (ms)
Total Looping Time: #TotalTime# (ms)
Average Looping Time: #AvgTime#</cfoutput>` 

我创建了一个 2 列 10 行的查询。然后，我循环查询 20，000 次。在查询循环中，我遍历了 qry。ColumnList 变量，并将名为 temp 的变量设置为当前行中当前列的值。

请注意，我运行了 20 次不同的测试，然后更改了:

收件人:

然后又运行了 20 次。我记录了 20，000 次迭代所用的总时间，每次迭代的平均时间，以及处理整个页面所用的总时间。所有的时间都以毫秒为单位。长话短说，使用数组表示法的平均计算时间是. 231 毫秒。(是的，那是一个很小的数字。)使用 Evaluate()的平均计算时间为. 228 毫秒。这是一个更小的数字。如果你看看数据，所有使用 Evaluate()的数字都变小了。所有其他代码保持完全相同。

在这些测试之间，我没有重启我的服务器，这可能会因为重新编译和初始化所有的东西而稍微改变结果。不过，我怀疑这对结果会有很大的影响。在实时环境中，您不需要在每次运行报告或加载表单时重启服务器。

我将在稍后的时间点运行更多的测试，看看我会得到什么结果。我将迭代次数增加到 100，000 次，并运行 50 次。(当我有时间的时候……每次运行大概需要 45 秒左右。)

我想我在这里的观点是，对于一个中小型应用程序，使用 Evaluate()不会在您的系统中造成巨大的瓶颈。是的，通常有其他方法可以绕过它，如果你不喜欢使用它，那就找其他方法绕过它。这只是使用 Evaluate()函数的一种情况。还有数不清的其他方法，在其他情况下，Evaluate()可能根本不会执行得很好。

以下是一些关于 CF 性能的链接，您可能会感兴趣:

[http://www.markme.com/cantrell/archives/003088.cfm](http://www.markme.com/cantrell/archives/003088.cfm)–CFSCRIPT 表演

[http://www . find articles . com/cf _ DLS/m0MLU/11 _ 4/94550933/P1/article . jhtml](http://www.findarticles.com/cf_dls/m0MLU/11_4/94550933/p1/article.jhtml)–CFIF 演出

[下载包含性能数据的 Excel 电子表格](http://www.kodefusion.com/samplecode/evalmetrics.xls)` 

## `分享这篇文章``