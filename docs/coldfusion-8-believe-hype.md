# ColdFusion 8:相信宣传

> 原文：<https://www.sitepoint.com/coldfusion-8-believe-hype/>

ColdFusion 没有屈服于许多过早的关于它即将消亡的谣言，而是继续蓬勃发展，在其新所有者 Adobe 的支持下更是如此。随着 ColdFusion 8 的发布，Adobe 通过发布多年来功能最丰富的版本重申了其对 ColdFusion 的承诺。

无论你以前从未使用过 ColdFusion，你正在考虑重新使用它，或者你正在寻找一个升级的好理由，ColdFusion 8 绝对值得你考虑。事实上，ColdFusion 8 代表了一个重要的版本，它的新特性比我在一篇文章中所能涵盖的还要多。简而言之，现在是成为 ColdFusion 开发人员的好时机，在本文中，我们将看看我最喜欢的原因。

##### 速度改进

你说什么？速度不是特色？嗯，当改进像 ColdFusion 8 中那样引人注目时，它绝对可以被视为一项功能。ColdFusion 团队分析了 100 多个实际的客户应用程序，以找到并消除瓶颈。这带来了全面的改进，使服务器的速度比 ColdFusion 7.0.2 快 4.25 倍。事实上，一些特定的区域和标签比以前的版本快 39 倍。特别是，如果您正在使用 ColdFusion components (CFC)进行面向对象的开发，您会注意到一个显著的改进。

最好的部分是，除了安装 ColdFusion 8 之外，您不需要做任何事情来利用该功能。无需更改一行代码，您现有的 ColdFusion 应用程序将获得巨大的性能提升。所以，计划是这样的…

1.  向你的老板推销一个能显著提高网站性能的计划，预算四周的工作。

1.  购买并安装 ColdFusion 8。

1.  花四周时间提高你在魔兽世界的排名。

如果您还没有使用 ColdFusion 进行开发，请记住 ColdFusion 早已不再被指责为速度慢或不可扩展，这一点很重要。正如最近升级到 ColdFusion 8 的《经济学人》的一名开发人员所说，“……整个系统非常稳定。”

##### 服务器监视器

有时，在以前版本的 ColdFusion 中诊断应用程序性能问题时，感觉就像医生试图在没有 x 光机的情况下诊断内伤。你知道内部通常有问题，但是你没有办法看到(因此也没有办法处理)这个问题。好消息是，ColdFusion 8(企业版和开发者版)配有一台 x 光机……呃……一台服务器监视器，允许您窥视运行应用程序的 ColdFusion 服务器的内部。

ColdFusion Server Monitor 是一个 Flex 应用程序，允许您检查与服务器整体健康状况相关的各种统计数据。它为您提供了有关特定应用程序、会话、请求和查询的详细信息。例如，您可以获得关于慢速请求的报告，并深入到这些请求中，以查看每个请求的可变内存使用情况，以及请求中任何包含、自定义标记和组件的性能。当您试图在一个缓慢的请求中定位瓶颈时，这非常有帮助。在我看来，仅这项功能就将对开发人员今后如何构建和调试 ColdFusion 应用程序产生巨大影响。

此外，服务器监视器提供了可定制的警报，允许您在发生某些不良行为时收到通知，例如 JVM 内存不足或线程运行时间过长。您甚至可以自定义服务器监视器在此行为发生时实现的响应，将其配置为发送电子邮件、创建快照、终止运行时间超过特定时间范围的线程，甚至从您指定的 ColdFusion 组件中执行一组自定义代码。

##### Ajax 集成

随着 7.0.2 版本的发布，ColdFusion 增加了 Flex 集成，这使它成为使用 Adobe 的 Flex 构建富互联网应用程序(RIA)的首选语言。这对我们这些热爱 Flex 的人来说太棒了(就像我一样！).然而，如果您想在本地使用 ColdFusion 数据类型和 JavaScript，使用 Ajax 构建 RIA 需要集成第三方开源库。这也意味着你必须包含并学习一个或多个外部 JavaScript 库的语法，比如 [Ext](http://extjs.com) 、 [Spry](http://labs.adobe.com/technologies/spry/) 或[雅虎用户界面库(YUI)](http://developer.yahoo.com/yui/) 。有了 ColdFusion 8，这种情况就不复存在了。

ColdFusion 8 添加了一长串新的标记和功能，这些标记和功能是专门设计来允许您使用舒适和熟悉的标记和函数语法快速构建基于 Ajax 的富互联网应用程序的。事实上，我上面提到的库都内置在 ColdFusion 8 中，例如，只需使用 cfgrid 标记就可以使用 Ext 创建 Ajax 网格。您甚至可以将这个网格绑定到由 CFC 方法返回的数据集，只需将对 cffunction 的引用放入标记的 bind 属性中。ColdFusion 8 通过集成丰富的用户界面元素，确实使向您的应用程序添加一些浮华变得非常简单和直观。

在我看来，更好的是，ColdFusion 8 中的 Ajax 集成比简单地在应用程序中添加花哨的 UI 元素更深入。ColdFusion 添加了标签和函数，允许您轻松地在 JavaScript 之间来回移动数据。例如，使用`cfajaxproxy`标签，您可以使用 JavaScript 让 CFC 中的函数直接从您的页面中被调用。该特性甚至会自动将数据类型(如 ColdFusion 查询对象)转换为 JavaScript 块中可用的类型，如数组或 JSON 包。创建的代理还包含为异步方法调用设置回调处理程序的内置方法。所有这些让使用 Ajax 和 ColdFusion 感觉完全无缝。

下面的示例代码展示了如何使用`cfajaxproxy`标记来创建基于可在 JavaScript 中使用的组件的类:

```
<cfajaxproxy cfc="path.to.cfc" jsclassname="MyClass" />   <script>    function myFunction(id) {        var myClass = new MyClass();        myClass.setCallbackHandler(populateWindow);        myClass.getSomeData(id);    }    function populateWindow {        // this function can be used, for example, to populate the returned data into a javascript window generated with the cfwindow tag    } </script>
```

##### 调试器

很久以前，ColdFusion 有自己的 IDE，名为 ColdFusion Studio，其中包括一个 step 调试器。当这个工具离开这个世界时，ColdFusion 程序员成为了通过`cfdump` / `cfabort`方法进行调试的专家，这基本上意味着将变量值转储到屏幕上并中止请求。虽然这是有效的，但它有一些主要的缺点，主要的一个是用这种方式调试整个页面是一个非常缓慢和乏味的过程。尽管如此，许多像我一样的 ColdFusion 程序员已经习惯了枯燥的工作，忘记了我们错过了什么……好吧，我们不再没有严肃的调试器了！

ColdFusion 8 调试器内置于 Eclipse IDE 中，这是一个使用 Flex Builder 或 CFEclipse 的人都熟悉的工具。调试器允许您设置断点并调试在本地或远程 ColdFusion 实例上运行的请求。一旦遇到断点，您可以检查请求中包含的任何范围内的任何和所有变量的值。此外，您可以单步执行请求并观察某些表达式，以查找值设置中的不一致之处。我相信这一点，结合服务器监视器，不仅使调试过程更容易，而且使我们能够创建更有效和无错误的应用程序。我从来不写 bug！(*咳咳！—编辑。*)

##### 多线程

我把 ColdFusion 8 增加多线程功能比作被放射性蜘蛛咬了一口。它为开发者提供了巨大的权力，但我们都知道伴随着巨大权力而来的是什么…是的，通常是名誉和金钱，但在这种情况下，我指的是巨大的责任。额外的线程用于提高包含某种长期运行的进程的请求的性能，该进程被传递给一个线程。通常，线程可以是:

*   创建并在完成后重新加入当前请求线程
*   创造出来，完成后就被遗忘了
*   创建并无限期运行，根据需要交替休眠或工作

ColdFusion 使得用`cfthread`标签生成新线程变得非常容易。包含在`cfthread`标签中的任何代码都是异步执行的，当前请求存在于一个独立的代码执行流中。请求的其余部分可以继续处理，而不需要等待线程代码完成，除非它被指示这样做。任何有经验的程序员都会遇到这样的情况，页面请求不需要等待特定的进程运行后再继续，所以很明显这是一个非常有用的函数(尽管它容易被误用)。

为了举例说明这是如何工作的，下面的例子展示了一些循环遍历 RSS 提要 URL 数组并使用新的`cffeed`标签解析 RSS XML 的代码。它在一个单独的线程上处理每个提要，以便它们可以同时运行，从而减少处理页面所需的总时间。一旦线程完成，另一个`cfthread`标签用于将它们重新加入到当前线程中，在那里我们可以输出值。已经熟悉 ColdFusion 的人可能还会注意到，我正在使用新的隐式数组创建来构建我的 feed 数组:

```
<cfset feeds = ["http://www.remotesynthesis.com/blog/rss.cfm?mode=full","http://www.nytimes.com/services/xml/rss/nyt/HomePage.xml"] />   <cfset threads = "" />   <cfloop from="1" to="#arrayLen(feeds)#" index="i">      <cfset threads = listAppend(threads,"thread"&i) />      <cfthread action="run" name="thread#i#" feed="#feeds[i]#">          <cffeed source="#feed#" properties="thread.myProps" query="thread.myQuery" />      </cfthread>   </cfloop>   <cfthread action="join" name="#threads#" timeout="6000" />   <cfloop list="#threads#" index="thread">      <cfif structKeyExists(cfthread[thread],"myProps")>          <cfoutput>#cfthread[thread].myProps.title#<br/></cfoutput>      </cfif>   </cfloop>
```

##### 甚至更多…

正如我之前所说，ColdFusion 8 中有如此多的新功能，我不可能在这里详细介绍它们。但是，这里有一些关键特性可能会引起您对这个新版本的兴趣:

*   **更深入的 PDF 集成:**正如人们对 Adobe 产品的期望，ColdFusion 8 包含任何应用程序服务器中最复杂的 PDF 集成。它不仅仅是简单地即时创建 PDF，还向开发人员提供了创建 PDF 表单和接受 PDF 表单提交、合并 PDF 文件、添加和删除页面、创建页面缩略图等功能。
*   **RSS/Atom 集成:**如上所述，新的 cffeed 标签使得阅读和创建 RSS 或 Atom 提要变得更加容易。
*   **图像操作:** ColdFusion 8 最终包含了一长串用于创建和操作图像的标签和函数。
*   **Zip/Jar 文件操作:**创建、修改和解压两者。拉上拉链。jar 文件。
*   **按需演示:**构建像 Adobe Presenter 一样工作的即时演示。
*   **语言改进:**第 8 版引入了大量语法和通用语言改进，包括 JavaScript 风格的操作符、隐式数组和结构创建，以及将属性作为集合传递给标签。这些改进可以让您的代码写得更快，更容易阅读，并且不那么冗长。
*   **。Net 集成:**与。存在于本地或远程服务器上的. Net 程序集。
*   **Exchange 服务器集成:**通过 ColdFusion 标签以编程方式管理 Microsoft Exchange 服务器上的电子邮件、日历项目、联系人和任务。

##### 摘要

如您所见，ColdFusion 8 中包含了如此多的新功能，您最喜欢的五个功能可能与我的完全不同。如果你还没有尝试 ColdFusion 8，是什么阻止了你？毕竟开发者版是免费的。试一试！

***延伸阅读***

**性能**
[ColdFusion 8 性能简介](http://www.adobe.com/products/coldfusion/pdfs/cf8_performancebrief.pdf) (PDF，172K)

**服务器监控**
ColdFusion 8 服务器监控系列:

*   [第 1 部分:在开发中使用服务器监视器](http://www.adobe.com/devnet/coldfusion/articles/monitoring_pt1.html)
*   [第 2 部分:在生产中使用服务器显示器](http://www.adobe.com/devnet/coldfusion/articles/monitoring_pt2.html)
*   [第 3 部分:利用警报和快照进行自动化监控和请求管理](http://www.adobe.com/devnet/coldfusion/articles/monitoring_pt3.html)
*   [第 4 部分:多服务器监控、管理 API 监控等等](http://www.adobe.com/devnet/coldfusion/articles/monitoring_pt4.html)

**Ajax 集成**
[cold fusion 8 中的 Ajax 特性概述](http://www.adobe.com/devnet/coldfusion/articles/cfajax_overview.html)

**调试器**
[为 Eclipse 使用 ColdFusion 8 分步调试器](http://www.adobe.com/devnet/coldfusion/articles/debugger.html)

## 分享这篇文章