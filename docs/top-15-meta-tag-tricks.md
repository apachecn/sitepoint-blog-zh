# 实用网页设计——15 大 META 标签技巧

> 原文：<https://www.sitepoint.com/top-15-meta-tag-tricks/>

**当您编写一个网络文档时，如果有必要，您可以不用元标签。您只需将`<HEAD>`部分留空(标题除外)，直接进入内容，如下所示:**

```
<HTML> 

<HEAD> 

<TITLE>My Magnificent Web Page</TITLE> 

</HEAD> 

<BODY>
```

…然后从那里开始。文档会很好地显示出来，老板会对你创建的精彩的 Web 文档惊叹不已。那么，为什么还要担心元标签呢？毕竟，他们不会出现什么的。

好吧，让我们来看看。

根据 [Webopedia](http://www.webopedia.com/) ，一个 META 标签:

<q>*"提供关于一个网页的信息。与普通的 HTML 标签不同，META 标签不影响页面的显示方式。相反，它们提供的信息包括谁创建了这个页面，多久更新一次，这个页面是关于什么的，哪些关键字代表了这个页面的内容。许多搜索引擎在建立索引时都会用到这些信息。*</q>

而根据[环球网财团](https://www.w3.org/)，

<q>*“元数据是机器可以理解的网络信息。”*</q>

幸运的是，我们不需要了解元标签的来龙去脉就可以在页面中使用它们。

注意 META 标签是“单例”；它们不需要使用像`</META>`这样的结束标签。它们必须在你的`<HEAD></HEAD>`区域内才能发挥作用。

##### 关键词和描述

最重要的元标签让搜索引擎在索引你的页面时有所依靠。执行此功能的两个 META 标记是关键字和描述标记。关键字标签提供了搜索引擎用户输入的关键字，以便找到像您这样的页面。该页面的 META 标记可能如下所示:

```
<META NAME="keywords" CONTENT="META tags, Practical Web  

Design, HTML, tips, tricks, SitePoint, Michael Tuck">
```

(注意:正如一个警惕的 [SitePoint Forums](http://www.sitepointforurms.com) 成员所指出的，如果你希望遵守严格的 XHTML 标准，你的标签应该是这样的:

```
<meta name="keywords" content="META tags, Practical Web  

Design, HTML, tips, tricks, SitePoint, Michael Tuck"/>
```

请注意，属性名称定义了正在使用的元标签的种类，而内容属性给出了描述页面的关键字和短语的列表。还要注意引号是如何使用的。

你应该投入一些精力来决定你的页面将会使用什么样的关键词。搜索词典，与朋友或同事一起集思广益，做出明智的决定。把最有用的词放在最前面。这些关键词将在很大程度上决定你的网站在搜索引擎结果列表中的位置。

DESCRIPTION 子标签非常相似，除了不是列出关键词和短语，而是提供页面的简短描述。搜索引擎会用这个描述来描述页面，所以你应该花些时间和心思来为你的网页设计一个完美的描述。该页面可能有一个描述标签，类似于:

```
<META NAME="description" CONTENT="Column One of SitePoint's  

Practical Web Design series; a discussion of META tags  

and how they're used in Web documents.  Written by  

Michael Tuck and published by SitePoint.com, your  

source for Web design information.">
```

描述标签是放置组织口号或其他吸引读者注意力的宣传性或描述性短语的绝佳位置。请记住，大多数搜索引擎只会使用前 150 个字符左右。首先关注页面的内容，而不是你组织的口号。

警告:大多数搜索引擎都有禁止 META 标签“垃圾邮件”的规则，这意味着你不应该试图通过一遍又一遍地重复相同的关键字或短语来淹没搜索引擎。最好的方法是保持你的关键词和描述简洁明了，避免重复。

这里有一些尝试:为什么不右键单击这个页面，选择 View Source，并查看 SitePoint 选择在本文的 HTML 代码中构建哪些元标记？这个页面不是我编写的，是 SitePoint 大师们编写的，我敢打赌，他们的元关键字和元描述标签与我上面引用的例子有很大不同——更适合 SitePoint contt，可能比我扔掉的那些更好。

值得注意的是，正如几个 SitePoint 论坛成员所做的那样，四个最受欢迎的搜索引擎——[谷歌](http://www.google.com)、 [AltaVista](http://www.altavista.com) 、 [FAST](http://www.fast.com) 和[Inktomi](http://www.inktomi.com)——只有一个，Inktomi，仍然使用 META KEYWORDS 标签索引网页。有些人，包括[搜索引擎观察](http://www.searchenginewatch.com)的丹尼·沙利文，建议我们不要费心为搜索引擎写元标签(尽管反过来说，可能不值得花力气把它们从现有页面中删除)。

另一方面，值得注意的是，Inktomi 引擎支持许多小型搜索引擎，其他搜索引擎如 [Teoma](http://www.teoma.com) 可能仍在使用 META 标签来帮助索引它们的页面。许多内部网和其他附属网站搜索也使用元标签。Inktomi 本身仍然是一股不可忽视的力量。所以决定权在你。

值得花费时间和精力来创建元关键字和描述标签吗？答案取决于你的网站的受众以及访问该页面所使用的搜索引擎和策略。有一点是肯定的，把他们包括进来没有坏处。如果你想覆盖所有的基础，继续添加它们。只是要意识到它们可能不值得你花费时间和精力去制作它们。

##### 刷新和重定向

这里有一个小标签，你会发现很有用。REFRESH 子标记可以用于将页面访问者发送到一个完全不同的页面，或者定期刷新页面内容——比方说，每 30 秒或更长时间。

一个例子可能是一个小软件公司，我们的酷软件，开始通过一个免费的地理城市网页营销其产品。该公司出售足够的软件来购买自己的产品。COM 网页可行，所以他们这样做。现在，不用登录到:

http://www.geocities.com/OurCoolSoftware/,

用户将登录到:

http://www.OurCoolSoftware.com/

但是，那些已经知道并使用旧的 Geocities 地址，并将其加入书签的用户怎么办呢？在将站点内容转移到新的 URL 后，为旧页面编写一个 META REFRESH 标记。这会强制旧页面自动将用户“重定向”到新页面，延迟时间以秒为单位，如下所示:

```
<META HTTP-EQUIV="refresh" CONTENT="10;  

url=http://www.ourcoolsoftware.com">
```

写一点文字来提醒冲浪者他们正被带到一个新的页面，瞧！你已经把你的站点访问者重新定向到新的页面，没有一点混乱或忙乱:

我们已经迁移到一个更大更好的网站！你将在 10 秒后被传送到我们的新地点。当你到达那里时，别忘了给我们的新页面加书签！

大约 10 秒钟后，你的网站访问者就来到了你很酷的新住处，*而不需要知道新的网址*。你已经为他们做了重定向。如果你想在 2 秒、30 秒或其他时间内将它们发送到新页面，只需将标签内容部分的数字更改为你喜欢的值。

就我个人而言，我觉得 10 秒钟是个不错的选择——足够让读者阅读你的文本，让他们知道发生了什么，并为新网站添加书签，但也不至于让他们不耐烦。有些人对 2-3 秒的重定向感到恼火——如果出于某种原因，他们不想访问新页面，他们就没有机会点击后退按钮。此外，如果他们没有意识到自己被重定向，他们可能会怀疑自己是否被劫持到了一个他们不想去的网站——也许是色情网站，或者是威胁他们计算机安全的网站。对于站点访问者来说，最好是在事情发生之前就知道发生了什么，让他们有时间决定是否允许重定向。此外，这是常见的礼貌，这年头，礼貌很重要。

注意:在极少数情况下，REFRESH 标签不起作用，原因很神秘，我不会在这里讨论。因此，您可以添加这样一行:

<q>*如果您的浏览器过一会儿没有将您带到我们的新网站，请单击下面的链接转到我们的新网站。到了那里，别忘了给我们加书签！【www.ourcoolsoftware.com/】*</q>

 *当然，普通版本的 REFRESH 标记也有它的用处——它的思想是以选定的时间间隔自动更新页面。这对于以股票市场信息、新闻源、广告内容或任何在几分钟内变化的信息为特色的页面非常有用。这是标签:

```
<META HTTP-EQUIV="refresh" CONTENT="600">
```

标签中的 600 表示自动页面重新加载之间的秒数，在本例中转换为重新加载之间的 10 分钟。

REFRESH 标签的另一个更有创意的用法是带访问者“游览”一系列内容。假设你想带你的游客看一组你参观乌鲁鲁(艾尔斯岩)的照片。您可以为每张照片及其附带的文本创建单独的 HTML 页面，并使用刷新标签将访问者从一页带到另一页，而无需他们触摸鼠标。如果你决定尝试这样做，确保给每张图片足够的时间来加载，也给访问者足够的时间来阅读文本。你必须判断多少时间是足够的——时间太少，他们会觉得被强行从一页拖到下一页，时间太多会让他们失去耐心。

顺便说一下，不用担心标签属性 HTTP-EQUIV 的含义。您可以使用属性，而不必知道它给出了“名称/值”对的名称，也不必知道它告诉服务器将该对视为 MIME(多用途 Internet 邮件扩展)文档头的一部分。看，你不知道这些是不是更开心？

##### 让你的网页远离其他网站的框架

有些网站很不礼貌，让其他页面出现在自己的框架集中。有时这是设计使然，有时是因为糟糕的编码。您可以通过包含以下标签使您的页面脱离另一个站点的框架(确保它出现在您站点的每个页面中):

```
<META HTTP-EQUIV="Window-target" CONTENT="_top">t
```

##### 其他元标签用法

如果你认为有必要，这里有一些其他的元标签供你使用:

***元作者***

标识页面的作者。

```
<META NAME="Author" CONTENT="Michael Tuck">
```

***元日期***

标识页面的创建日期。

```
<META NAME="Date" CONTENT="November 17, 2002">
```

***元版权***

设置页面的版权日期。

```
<META NAME="Copyright" CONTENT="2000-2003 SitePoint,    

Inc. All rights reserved.">
```

***到期***

如果你想让你的页面“过期”，下面是如何设置它:

```
<META HTTP-EQUIV="expires" CONTENT=   

"thu, 31 DEC 2002 00:04:00 EST">
```

在现实世界中，这意味着访问者的浏览器将使用页面的缓存版本，如果可用的话，直到指定的日期和时间，此时页面将在下次访问时从服务器重新加载。要强制访问者的浏览器在每次访问时从服务器重新加载您的页面(即总是使用最新的内容)，请使用以下内容:

```
<META HTTP-EQUIV="expires" CONTENT="0">
```

***元缓存控制***

这个标签防止你的页面被服务器或浏览器自动缓存。例如，AOL 通常在其服务器上存储缓存页面，这意味着 AOL 用户可以查看页面的缓存(即旧)副本，而看不到更新的版本。虽然缓存页面可以加快加载速度，但是如果您的页面经常更新，我建议您使用 no-cache 标记来确保您的访问者总是获得最新版本的页面:

```
<META HTTP-EQUIV="Cache-Control" CONTENT ="no-cache">
```

以下标记专门用于 Netscape 浏览器:

```
<META HTTP-EQUIV="Pragma" CONTENT="no-cache">
```

***元内容类型***

这指定了页面的字符内容。虽然大多数普通的 HTML 页面不需要这样，但是任何使用 XHTML 的页面都需要这样。

```
< META HTTP-EQUIV="Content-Type" CONTENT="text/html;    

charset=ISO-8859-1>
```

上面的标签相当通用，可以用于大多数网页。还有许多其他字符编码值可用。ISO-8859-1 是默认值。

***元内容-风格-类型***

指定默认级联样式表语言。下面的标签是一个相对普通的例子:

```
<META HTTP-EQUIV="Content-Style-Type" CONTENT="text/css">
```

***元机器人***

“机器人”是搜索引擎用来索引网站页面的程序。大多数网站都可以将所有的网页单独编入索引；这些站点可以使用以下标签:

```
<META NAME="robots" CONTENT="all">
```

但是你还有其他选择:

```
<META NAME="robots" CONTENT="noindex,nofollow"> 

(the page is not added to the search engine at all; another way to write this is <META NAME="robots" CONTENT="none">)

<META NAME="robots" CONTENT="noindex,follow"> 

(while the home page is not added to the search engine, the robot follows the links)

<META NAME="robots" CONTENT="index,nofollow"> 

(the home page only is indexed)

<META NAME="robots" CONTENT="index,follow>
```

默认值是最后一个值:“index，follow”内容值。这使得搜索引擎对索引(主页)页面进行分类，并跟踪所有链接。如果这是你想要的，没有必要包含任何机器人标签。

***目标【峰】***

[万维网联盟](https://www.w3.org/)提供了一个名为 [PICS](https://www.w3.org/PICS) 的站点标记协议，代表互联网内容规范平台。许多网页过滤软件包使用图片信息来决定哪些网站是家庭友好的，哪些不是。整个想法可能会变得相当复杂，所以我不会给你任何“通用”的东西，我建议你访问图片网站，自己决定如何进行。或者浏览[【ICRA】(互联网内容评级协会)](http://www.icra.org/)网站，了解如何为你的网站内容“评级”。

***元生成器***

许多 HTML 编辑器(如 PageMill、Front Page 和 Dreamweaver)会自动添加此标签:

```
<META NAME="generator" CONTENT="program name and version">
```

***【元智能解析】***

“智能解析”是一种隐秘的、通常不受欢迎的微软发明，还没有被广泛使用。如果在微软的任何应用程序或操作系统中使用，它将迫使你网站中选择的单词或短语链接到微软选择的其他网站——也就是说，点击智能标签词“汽车”的用户将被迅速带到，比如说，丰田的网站。举例来说，如果这发生在通用汽车的页面上，当这引起他或她的注意时，高管层的某个人会不高兴。

尽管智能分析不在当前版本的 XP 或 Internet Explorer 6 中，但它是 Office XP 的一项功能(默认情况下是关闭的),可能会出现在未来版本的 XP、IE 和其他 Microsoft 产品中。令人不安的是，它类似于 TopText，一种令人讨厌的众所周知的间谍软件(在我的 SitePoint 年 9 月 20 日的文章 [AdWare 和 Under-ware:权威指南](http://www.ecommercebase.com/article/888)中有更多关于间谍软件的内容)。一般来说，这种事情是一个坏主意，可能是非法的，而且肯定是侵扰性的和不受欢迎的。

在我写这篇文章的时候(2002 年 11 月)，微软已经与 25 家公司签订了合同，包括 ESPN、C|Net、MSNBC、联邦快递和其他公司，使用智能标记技术链接到任何可能被智能标记侵入的 Web 文档中选定的单词和短语。如果你像我一样，只有当你想链接到 ESPN 或联邦快递时，你才会链接到它们，并且不允许微软在没有你的同意下在你的页面上添加链接。使用此标记将智能标记排除在页面之外，请记住，此 META 标记必须出现在站点的每个页面上:

```
<META NAME="MSSmartTagsPreventParsing" CONTENT="true">
```

##### 让 MetaGen 帮你做吧

马特·密茨凯维奇的元页面自动为你的页面生成基本的元标签。SitePoint 向导的另一个好资源——试一试吧！

##### 参考书目和进一步阅读

关于元数据的 10 个问题

[http://builder . CNET . com/web building/pages/Authoring/Metadata/ss01 . html](http://builder.cnet.com/webbuilding/pages/Authoring/Metadata/ss01.html)

另一个列表:智能标记

[http://www.alistapart.com/stories/smarttags/](http://www.alistapart.com/stories/smarttags/)

高级 HTML:表格、框架和 META 标签

[http://www . rochester . edu/ATS/Documentation/web help/advanced . html](http://www.rochester.edu/ats/Documentation/WebHelp/advanced.html)

关于元标签你需要知道的一切

[http://www.webmasterbase.com/article.php?aid=31](http://www.webmasterbase.com/article.php?aid=31)

HTML、META、REL 和 REV 标签

[http://vancouver-webpages.com/META/index.shtml](http://vancouver-webpages.com/META/index.shtml)

HTML 标签库:标签

[http://html.about.com/library/tags/bl_meta.htm](http://html.about.com/library/tags/bl_meta.htm)

META FAQS

[http://vancouver-webpages.com/META/FAQ.html](http://vancouver-webpages.com/META/FAQ.html)

搜索引擎的元标记

[http://www.wdvl.com/Search/Meta/Tag.html](http://www.wdvl.com/Search/Meta/Tag.html)

Meta Tags

[http://html.about.com/cs/metatags/index.htm](http://html.about.com/cs/metatags/index.htm)

元标签资源

[http://www.ohlone.cc.ca.us/org/webcenter/metatags.html](http://www.ohlone.cc.ca.us/org/webcenter/metatags.html)

元刷新标签

[http://html.about.com/library/weekly/aa080300a.htm](http://html.about.com/library/weekly/aa080300a.htm)

Microsoft 剪辑 Windows XP 智能标记

[http://news.com.com/2100-1001-269167.html](http://news.com.com/2100-1001-269167.html)

Microsoft Office 电子服务-智能标记

[http://www.officesmarttags.com](http://www.officesmarttags.com)

新的视窗 XP 功能可以重新编辑他人的网站

[http://ptech.wsj.com/archive/ptech-20010607.html](http://ptech.wsj.com/archive/ptech-20010607.html)

重访元标签

[http://www.searchenginewatch.com/sereport/02/12-meta.html](http://www.searchenginewatch.com/sereport/02/12-meta.html)

SitePoint 论坛报第 112 期

[https://www . site point . com/newsletter/archive/newsletter 112 . PHP](https://www.sitepoint.com/newsletters/)

SitePoint 网站管理员论坛:实用网页设计-15 大 META 标签技巧

[http://www.sitepointforums.com/showthread.php?threadid=86842](http://www.sitepointforums.com/showthread.php?threadid=86842)

网络视频

[http://www.webopedia.com/](http://www.webopedia.com/)

万维网联盟

[https://www.w3.org/](https://www.w3.org/)

## 分享这篇文章*