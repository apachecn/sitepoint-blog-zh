# ColdFusion MX 的内容联合

> 原文：<https://www.sitepoint.com/content-syndication-coldfusion/>

内容联合已经成为向网站访问者提供内容的一种非常流行的方式，同时也为许多人提供了一种在一个地方收集信息以便快速、方便地查看的方式。

例如，如果您从 Macromedia 获得了您的 [ColdFusion 新闻](http://www.macromedia.com/devnet/resources/macromedia_resources.rdf)，您的[技术文章](https://www.sitepoint.com/add-semantic-richness-to-your-markup-with-rdf-ease/)。从 SitePoint，你的[数字安全新闻](http://news.com.com/2547-1009_3-0-5.xml)从 CNET，你可能想创建一个单一的网页，让你一次看到所有的新闻。在这篇文章中，我们将讨论如何快速做到这一点(我的意思是非常快！)并在 ColdFusion MX 中轻松完成。

值得注意的是，因为 RSS 是一种基于 XML 的技术，所以您必须使用 ColdFusion MX 才能理解本文中的示例。有很多方法可以在旧版本中使用 XML，但是它们过于复杂，并且不总是像 MX 的内置功能那样稳定。

##### 什么是 RSS？

首先，我们来说说什么是 RSS。我不会在这里给你完整的历史，但是我会提供一些非常重要的信息的链接，帮助你开始了解 RSS 背后的概念。简而言之，RSS (Real Simple Syndication 或 RDF Site Summary)是一种在网站之间共享内容的干净、直接的方法。RSS 可以在几个方面为您服务:

1.  RSS 可以让您保持网站上的新闻项目更新，而不必手动更改或更新新闻。如果你经营一个健康相关的网站，你可能想把健康相关的新闻放在网站上。在这种情况下，您可以使用 [MedicineNet 的 RSS 提要](http://www.medicinenet.com/rss/article.htm)来更新新闻。

3.  您可以创建自己的 RSS 源，让网站访问者能够整合您的内容。这可以增加你在同类网站中的知名度，增加你网站的流量，帮助你建立一个可识别的名字。

5.  您可以使用 RSS 来创建自己的个人主页，以跟踪您通常需要访问几个网站才能获得的新闻项目。这也让您能够操纵信息的呈现方式和更新频率。

RSS 提要的可能性是无限的。不过，在我们继续深入之前，我将列出一些您可能想在深入研究本文其余部分之前阅读的参考资料。

*   离开你的 RSS！
*   [RSS 网站简介参考](http://www.webreference.com/authoring/languages/xml/rss/intro/)
*   XML.com——什么是 RSS？
*   [RSS 2.0 规范(科技@哈佛法律)](http://blogs.law.harvard.edu/tech/rss)
*   [RSS 1.0 规范](http://web.resource.org/rss/1.0/)

##### 检索和缓存 RSS 源

在我关于 HTTP 的文章中，我讨论了如何使用 HTTP 检索 RSS 提要。概括一下，我们将继续讨论如何检索 RSS 提要并缓存它以备后用。首先，我们将使用一个非常简单的 HTTP 调用来检索提要。作为我们的例子，我们将在 SitePoint 为我的博客使用 [RSS 提要](https://www.sitepoint.com/blog/)。为了检索博客提要，我们将使用如下所示的 HTTP 调用:

```
<cfhttp url="https://www.sitepoint.com/blog.rdf?blogid=7" method="get" timeout="5" path="C:temp" file="dave_blog.rdf" />
```

这个标签从 SitePoint 检索 RSS 提要，并将其存储在一个名为`"dave_blog.rdf"`的文件中。记住这一点很重要，因为我们指定了路径和文件，所以在`cfhttp`结构中没有`FileContent`变量。这意味着我们必须将文件读入一个变量:

```
<cffile action="read" file="C:tempdave_blog.rdf" variable="FileContent">
```

现在我们有了一个名为`FileContent`的变量，它包含提要的 RDF 文档。我们最终将解析这个 XML 文档并输出信息。但是，在此之前，我们先来讨论一下缓存。

如果你要把我的博客放在你的网站上，而你每天有 1000 个访问者，那么很有可能，你每天要对 SitePoint 进行 1000 次 HTTP 调用。为了防止他们的服务器过载，并加速这个提要在您自己的站点上的显示，您应该在您的服务器上缓存这个文件。这就是为什么我们指定了保存提要的路径和文件名。我在想，我想每 24 小时更新一次 feed，这样我就能确保获得新鲜的内容，但我不会经常点击 SitePoint。下面是完整的代码(`cache.cfm`)，我将根据需要获取提要并将其缓存到一个文件中:

```
<cfset SavePath = ExpandPath("./cache.cfm")> 

<cfset SavePath = Replace(SavePath, "cache.cfm", "blog.rdf")> 

<cfdirectory action="list" directory="#GetDirectoryFromPath(SavePath)#" name="blogRdf" filter="blog.rdf"> 

<cfif not blogRdf.RecordCount eq 0 and DateCompare(blogRdf.DateLastModified, DateAdd("h", -24, Now())) eq -1> 

  <cffile action="delete" file="#SavePath#"> 

</cfif> 

<cfif FileExists(SavePath) eq 0> 

  <cfhttp url="https://www.sitepoint.com/blog.rdf?blogid=7" method="get" timeout="10" path="#GetDirectoryFromPath(SavePath)#" file="#GetFileFromPath(SavePath)#"></cfhttp> 

</cfif>   

<cffile action="read" file="#SavePath#" variable="FileContent">
```

这是相当大的一段代码，所以我将为您一行一行地分解它。真的很简单。

1.  我创建了`SavePath`变量，它只是当前模板文件的路径。

3.  我将当前模板的名称替换为我想要存储提要的文件的名称，`"blog.rdf"`。

5.  我得到了存储提要的目录列表。

7.  如果文件存在于目录中，并且超过 24 小时，我会删除它。

9.  如果该文件不存在(记住，它每 24 小时被删除一次)，我就执行一个 HTTP 调用来检索它，并将它存储在文件中。

11.  我读入文件，并将其存储为`"FileContent"`以便稍后解析。

这就是我们的检索和缓存机制所要做的全部工作。那很容易，不是吗？剩下的也很容易。

##### 解析和显示 RSS 提要

如果您查看 RSS 提要的原始 XML 输出，您将能够看到它的结构包含一个名为“rdf:RDF”的根节点。然后，我们有一个“频道”节点，带有标题、链接、描述和图像。然后我们有一个“items”节点，它包含一个“li”节点的数组(“rdf:li”)。我们有一个“image”节点，它包含关于 SitePoint 徽标的信息，包括图像的标题、到博客的链接(进入锚标签的 href)以及图像本身的 URL。然后，我们有一个“item”节点数组，每个节点都包含一个标题、一个到博客文章的直接链接、一个描述和日期。请注意，日期位于“dc”命名空间中，ColdFusion 对这种格式并不太友好。我还将向您展示一种显示日期的强力方法。

你要做的第一件事是决定你想展示什么以及如何展示。我将有一个单独的页面，显示博客的标题和描述，以及到它的链接和 SitePoint 徽标。然后我会显示每个博客条目——标题会链接到 SitePoint 网站上的条目，在它下面会出现描述和日期。下面是我用来做这件事的代码:

```
<cfset xmlDoc = XMLParse(filecontent)> 

<cfoutput> 

  <img src="#xmlDoc.rdf.image.url.xmlText#" align="left"> 

  Source Title: #xmlDoc.rdf.channel.title.xmlText#<br> 

  Link: <a href="#xmlDoc.rdf.channel.link.xmlText#">#xmlDoc.rdf.channel.link.xmlText#</a><br /> 

  Description: #xmlDoc.rdf.channel.description.xmlText# 

  <hr /> 

  <cfloop from="1" to="#ArrayLen(xmlDoc.rdf.item)#" index="i"> 

    <cfset dt = Replace(xmlDoc.rdf.item[i].date.xmlText, "T", " ")> 

    <cfset dt = Replace(dt, "Z", " ")> 

    <p> 

    <a href="#xmlDoc.rdf.item[i].link.xmlText#">#xmlDoc.rdf.item[i].title.xmlText#</a><br /> 

    #xmlDoc.rdf.item[i].description.xmlText#<br /> 

    <em>(Posted on #DateFormat(dt, "mm/dd/yyyy")#)</em> 

    </p> 

  </cfloop> 

</cfoutput>
```

我将向您展示我们用来解析和显示提要的代码。首先，我们使用`XMLParse(string)`函数来解析文档。注意，这也是一个验证解析器，所以如果您向它传递无效的 XML，它将会阻塞。当您使用这个函数时，它创建一个 XML 文档对象。本质上，这是一系列相互嵌套的结构和数组。

接下来，我们将打开我们的`cfoutput`，开始显示信息。我的图像源将是包含在`xmlDoc.rdf.image.url.xmlText`中的值。如果您仔细观察，您会发现您可以简单地通过沿着文档树“走”到您需要的值来访问值。我们从`xmlDoc`变量开始，然后转到`rdf`的根节点。我们的图像信息在`url`节点下的`image`节点中。该值包含在`url`节点的`xmlText`元素中。

作为题外话，我应该解释如何在 XML 文档中访问属性。让我们访问图像节点的“about”属性。下面是它的路径:`xmlDoc.rdf.image.xmlAttributes.about`。要访问属性，请参考附加到存储属性的节点的`xmlAttributes`结构；然后，只需访问属性名。

您可以应用上面的逻辑来显示博客的标题、描述和链接。然后，我们要展示商品。为此，我将简单地从 1 开始循环到在`xmlDoc.rdf.item`找到的数组的长度。当我遍历它时，我将访问`item[i].variableName`变量来显示所需的信息。请注意，您必须完全限定值的路径，否则 ColdFusion 将不知道在哪里查找变量。

那也很简单，对吧？我们现在总共有大约 37 行代码，用来显示博客提要本身。您的任务是修改代码，使其更漂亮，在一页上只显示 x 行，并允许过早刷新缓存(在 24 小时结束之前)。

在我继续之前，让我谈谈日期。此日期不是以 ColdFusion 理解的格式显示的。因此，当我们的脚本试图使用`DateFormat(date, mask)`函数格式化它时，它会崩溃。要解决这个问题，你只需要用空格替换“T”和“Z ”,就像我上面做的那样。然后，您可以随意显示它。

##### 为您的网站创建 RSS 源

如果您运行一个内容站点，并且想要将您的内容整合到其他站点，最简单的方法就是创建一个 RSS 提要。我将使用 0.91 版创建自己的内容提要。如果你愿意，你可以像 SitePoint 一样使用 RSS 1.0。只需做一些小的修改，使文件符合标准。

我提供内容提要的方法是，我将有一个每 24 小时运行一次的计划任务。(如果您有一个非常动态且不断变化的站点，您可能希望减少这个时间间隔。)这个计划任务将创建 RSS 提要并将其写入服务器上的一个文件，然后所有人都可以轻松地访问该文件。这是一个非常简单的过程，代码如下所示:

```
<cfquery name="GetArticles" datasource="#dsn#"> 

   SELECT TOP 5 Title, Description, ID 

   FROM Articles 

   ORDER BY ID DESC 

</cfquery> 

<cfxml variable="rssFeed"> 

   <rss version="0.91"> 

      <channel> 

         <title>My Content Site â€“ Recent Articles</title> 

         <link>http://www.example.com/</link> 

         <description>The best place to get content.</description> 

         <image> 

            <title>Example.com</title> 

            <url>http://www.example.com/logo.gif</url> 

            <link>http://www.example.com/</link> 

         </image> 

         <cfoutput query="GetArticles"> 

            <cfset Desc = REReplaceNoCase(Description, "<[^>]*>", "", "ALL")> 

            <cfset Desc = Replace(Desc, "&nbsp;", " ", "ALL")> 

            <item> 

               <title>#Title#</title> 

               <link>http://www.index.com/article/index.cfm?ArticleID=#ID#</link> 

               <description>#Desc#</description> 

            </item> 

         </cfoutput> 

      </channel> 

   </rss> 

</cfxml> 

<cffile action="write" file="#ExpandPath("./myArticles.xml")#" output="#ToString(rssFeed)#">
```

此时不要关心查询或数据库结构。只需知道我从数据库中获得的信息是 RSS 提要中使用的标题、描述和链接(在本例中是链接的 ID)。

该文件中下一个值得注意的，也是最重要的事情是使用了`cfxml`。这个标签用于创建一个 XML 文档，该文档稍后将被转换为一个字符串并写入一个文件。注意，我们指定了`variable`属性，它将是保存 XML 文档的 ColdFusion 变量。变量名是*而不是*顶层节点！不要让那愚弄你。接下来，我们简单地插入我们希望提供给用户的 RSS 节点，包括标题、链接、URL 和图像信息。然后我们循环查询，添加每个带有标题、链接和描述的`item`节点。

最后，我们使用`ToString(string)`函数将在`cfxml`中创建的 XML 文档转换成可以写入文件的字符串。运行模板，然后将浏览器指向“./myArticles.xml "文件放在与模板相同的目录中。(只要可以通过 Web 访问，您就可以将它移动到服务器上的任何位置。否则，没有任何好处。)

对于创建 RSS 提要的另一种方法，您可能会对 Pablo Varando 的教程感兴趣。这是一种同样有效的不同方法。请注意，它也已经在 BlueDragon 上进行了测试。我的例子没有，但是 BlueDragon 服务器支持我使用的所有标记和方法，所以，从技术上讲，它应该工作得很好。

##### 包裹

我们现在已经看到了如何检索和显示 RSS/RDF 提要。我们使用的例子是 RSS 1.0，但是使用 RSS 0.91 的原理是一样的。我们还看到了如何创建自己的 RSS 提要供用户访问，以便他们可以在自己的站点上显示我们的内容。

现在，我将在我的文章中引入一些新的东西:作业。这里有一些任务，你可以完成来提高你在这方面的技能。如果你在这些作业上需要帮助，请在下面或 SitePoint 论坛上自由讨论。我通常可以提供帮助，在论坛上，有许多非常熟练的 ColdFusion 开发人员，他们随时提供他们的建议和观点。

1.  修改 RSS 阅读器以使用 RSS 0.91。

3.  创建一个自定义标记，该标记接受 RSS 提要的 URL，然后检索、缓存、解析并显示它。将其设置为接受 RSS 0.91 或 RSS 1.0。

5.  修改上面的 RSS 创建脚本来创建 RSS 1.0 提要。

7.  为你的站点创建一个 RSS 源(如果你有一个基于内容或新闻的站点)。

RSS 在网络上还有许多其他的使用方式。事实上，我敢肯定，你想得越多，你就会发现越多的想法。欢迎使用下面的讨论框与我们分享这些想法。我希望在不久的将来看到许多 ColdFusion 开发人员使用 RSS 提要。直到下一次，保持低温！

## 分享这篇文章