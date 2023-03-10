# PHP 和 XML:解析 RSS 1.0 文章

> 原文：<https://www.sitepoint.com/php-xml-parsing-rss-1-0/>

XML 作为一种为系统间信息交换创建标准数据格式的手段，正在互联网上遍地开花，与它们的平台或技术无关。您可能已经知道，XML 允许您定义自己的定制标记语言，类似于 HTML，适合于您需要表示的任何数据。许多基于 XML 的标准标记语言已经被创建出来，以促进常见类型信息的交换。在本文中，我们将学习如何使用 PHP 读取 XML 文档，并将其中包含的数据显示为网页。我们将使用的示例是资源描述框架(RDF)站点摘要(RSS) 1.0 文档，尽管这里介绍的技术适用于任何希望在 PHP 脚本中解析 XML 数据的情况。

##### RSS 1.0 简介

RSS(以前代表 Netscape 开发的 Rich Site Summary，但现在指 RDF Site Summary，Netscape 技术的更新和 XML 兼容版本)是一种 XML 文档格式，旨在描述、总结和分发作为“通道”的网站内容。像[MoreOver.com](http://www.moreover.com/)和奥莱利的 [Meerkat](http://meerkat.oreillynet.com/) 这样的网站处理新闻和其他内容网站提供的 RSS 源，并提供综合头条新闻源服务。RSS 目前是由 [RSS-DEV 工作组](http://www.purl.org/rss/)开发的。

与大多数 XML 文档格式一样，只需查看一个示例文档，就可以很容易地了解文档的含义。SitePoint.com 在 https://www.sitepoint.com/rss.php 提供 RSS 格式的头版文章摘要。如果您使用的是 Internet Explorer 5 或更高版本，可以直接在浏览器中查看此 XML 文档的当前版本。对于其他人来说，这是在我写这篇文章的时候最新的 SitePoint.com RSS 文件:

`<?xml version="1.0" encoding="utf-8"?>
<rdf:RDF
xmlns:rdf="https://www.w3.org/1999/02/22-rdf-syntax-ns#"
>`

 <channel rdf:about="”https://www.sitepoint.com/rss.php”"><称号></称号>
<描述>掌握网络！</描述>
<链接>https://www.sitepoint.com/</链接></channel>

 <items><rdf:Seq>
<rdf:Li rdf:resource = " http://www .>
<rdf:Li rdf:resource = " http://www .>
< rdf:李 rdf:resource="http://www . ecommerce base . com/article/552 "/>
<rdf:李 rdf:resource = " http://www . ecommerce base . com/article/505 "/>
<rdf:李 rdf:resource = " http://www .>
< rdf:李 rdf:resource = " http://www . ecommerce base . com/article/508 "/>
</rdf:Seq>
</items>
</channel></items>

 <item rdf:about="”http://www.PromotionBase.com/article/551″"><标题>逃避搜索引擎缓存</标题>
<描述>你知道很多搜索引擎缓存你的页面吗？
虽然这种做法可以加快搜索速度，但用户可能看不到你的
最近的网站更新！拉尔夫向你展示如何阻止搜索引擎
缓存你的页面。</描述>
<链接>http://www.PromotionBase.com/article/551</链接>
</条目></item>

 <item rdf:about="”http://www.WebmasterBase.com/article/541″">< title >给 Fireworks 添加 JavaScript</title>
<描述>你的设计需要更多的 pizazz 吗？无需学习 JavaScript，为您的网站添加交互性！Matt 解释了如何在 Fireworks 中创建
JavaScript 效果，并详细探讨了该程序的
工具的使用。</描述>
<链接>http://www.WebmasterBase.com/article/541</链接>
</条目></item>

 <item rdf:about="”http://www.eCommerceBase.com/article/552″"><title>8 步电子邮件营销活动–第 2 部分< /title >
<描述>好了，你已经钓上了你的潜在客户，他们已经在你的邮件列表上
了。现在怎么办？你如何有效地沟通，并
把他们变成客户？杰森揭示一切…</描述>
<链接>http://www.eCommerceBase.com/article/552</链接>
</条目></item>

对书面网站合同的需求< /title >
<描述>如果你付钱给别人
设计、建造或维护你的网站，一份书面协议是必不可少的。伊凡向那些在网上工作的人解释合同的必要性
。</描述>
<链接>http://www.eCommerceBase.com/article/505</链接>
</条目>

 <item rdf:about="”http://www.PromotionBase.com/article/556″"><title>2001 年搜索引擎战略——会议报告< /title >
<描述> Sinewave Interactive 的 Gavin Appel 与 Matt 谈论
今年的搜索引擎战略会议。他概述了行业领袖的
讨论和预测。</描述>
<链接>http://www.PromotionBase.com/article/556</链接>
</条目></item>

 <item rdf:about="”http://www.eCommerceBase.com/article/508″"><标题>更好的电子商务调查问卷</标题>
<描述>现在就彻底改变你的电子商务战略吧！面对李提出的
棘手问题，他将指导你通过一个简单的过程
来优化你的电子商务战略。</描述>
<链接>http://www.eCommerceBase.com/article/508</链接>
</条目></item>

如您所见，该文件以一个`<channel>`标记开始，它包含 RSS 文件描述的站点的标题、描述和 URL，以及该频道当前包含的一个`<items>`列表。这个标签之后是出现在 SitePoint.com 首页的每篇文章的`<item>`标签。对于每一个，都提供了标题、描述和 URL。应该注意的是，这是一个基本的 RSS 文件——许多网站使用 RSS 格式的标准扩展来包含作者姓名、图像和其频道中项目的出版日期等内容，但是对于本文的目的来说，这个基本的 RSS 文件就足够了。

现在，由于大多数 Web 浏览器不能读取 XML 页面，而浏览器在默认情况下只能显示页面的代码(Internet Explorer 5+)或页面的文本部分(Netscape 6+)，所以如果您想将这个 RSS 文档显示给用户，就需要一些中间技术将它转换成可显示的内容。其他可能性包括读取文件并将标题存储到数据库中，或者如果特定关键字出现在新文章的描述中，则向订阅用户发送电子邮件。在任何情况下，您都需要能够读取 XML 的东西。在这个领域的众多可用选项中，本文将研究如何使用 PHP 来解析 XML 文档。

##### PHP 对 XML 的接受

编程语言读取 XML 文档有两种广泛使用的方法:**基于事件的 API**和**文档对象模型(DOM)API**。在后一类 API 中，XML 文档被完整地读入内存，然后可以通过一组函数进行操作，这些函数提供对内存中文档的面向对象模型(DOM)的访问。DOM APIs 一般被认为更强大；然而，它们有一个严重的缺点:它们不适合处理大型 XML 文档，这将占用太多的内存来构建文档模型。

PHP 使用基于事件的 API 来处理 XML。在这种模型中，XML 文档被从头到尾读入，每当遇到开始标签、结束标签或字符数据块时，就触发一个**事件**。这些事件中的每一个都导致程序员选择的函数被调用。因此，使用类似 PHP 的基于事件的 API 来读取 XML 文档，只需编写函数，对 PHP 在文档中移动时发生的事件做出适当的反应。

下面是用 PHP 设置事件处理函数和解析(读入)XML 文档的基本代码:

`// Create an XML parser
$xml_parser = xml_parser_create();`

 `//设置处理开始和结束标签的函数
XML _ Set _ element _ handler($ XML _ parser，" startElement "，" endElement ")；

//设置函数处理字符数据块
XML _ Set _ character _ data _ handler($ XML _ parser，" character data ")；

//打开 XML 文件读取
$ FP = fopen(" https://www . site point . com/rss . PHP "，" r")
或 die("读取 RSS 数据出错。");

//在$data = fread($fp，4096))
//用上面创建的 XML 解析器解析每个 4KB 的块
xml_parse($xml_parser，$data，feof($fp))
//处理解析中的错误
或 die(sprintf(" XML error:% s at line % d "，
XML _ error _ string(XML _ get _ error _ code($ XML _ parser))，
XML _ get _ current _ line _ number($ XML _ number

//关闭 XML 文件
fclose($ FP)；` 

`// Free up memory used by the XML parser
xml_parser_free($xml_parser);`

上面的每一行代码都有注释来解释它们的作用，但是让我们更详细地看看上面代码中使用的与 XML 相关的 PHP 函数:

*   **`xml_parser_create()`** 创建一个 XML 解析器。正如如果想与数据库交互，必须用 PHP 创建一个数据库连接一样，当想读入 XML 文件时，必须创建一个 XML 解析器。在上面的例子中，对解析器的引用存储在`$xml_parser`中。
*   **`xml_set_element_handler(*parser*, *startElementFunction*, *endElementFunction*)`** 该函数指定了 XML 解析器应该用来处理开始和结束标签所生成的事件的函数。在这种情况下，解析器存储在我们的`$xml_parser`变量中，而函数被称为`startElement`和`endElement`。这些函数将在 PHP 脚本的其他地方定义(我将在下面给出一个例子)。
*   **`xml_set_character_data_handler(*parser*, *characterDataFunction*)`** 该函数指定了 XML 解析器应该用来处理 XML 文档中标签之间出现的字符数据的函数。在上面的例子中，我们再次使用了我们的`$xml_parser`变量。我们选择处理字符数据的函数叫做`characterData`。
*   **`xml_parse(*parser*, *data*, *endOfDocument*)`** 该函数将 XML 文档的全部或部分内容发送给解析器进行处理。如果数据标志着 XML 文档的结束，那么`endOfDocument`参数应该被设置为`true`，或者如果在对`xml_parse`的后续调用中还会有更多的文档，那么`false`参数应该被设置为。这允许解析器正确地捕捉文档末尾未关闭的标签，依此类推。在我们的例子中，解析器再次是`$xml_parser`。用`fread`从文件中检索到的`$data`变量(最大为 4KB)作为要处理的数据传递，而`feof`函数用于确定 PHP 是否已经到达 XML 文件的末尾，从而提供所需的`endOfDocument`参数。如果在解析文档的过程中出现错误，我们会打印出错误消息以及出现错误的文件行，带有 **`xml_error_string`** 、 **`xml_get_error_code`** 和 **`xml_get_current_line_number`** ，如果您感兴趣的话，PHP 手册中会详细描述这些内容。
*   **`xml_parser_free(*parser*)`** 虽然在 PHP 脚本结束时所有的内存资源都被释放，但是如果您的脚本在解析 XML 数据后将执行其他潜在的内存密集型任务，您可能希望释放 XML 解析器使用的内存。这个函数销毁指定的 XML 解析器，从而释放它可能已经为解析分配的资源和内存。

有几个额外的函数可以让您处理 XML 解析过程中发生的一些更深奥的事件，但是这些在 PHP 手册中有很好的记录，所以如果您的特定应用程序需要它们，我将把它们留给您去阅读。对于我们的目的(读取一个 RSS 文件)，我们现在拥有了我们需要的一切。剩下的就是编写三个事件处理函数:`startElement`、`endElement`和`characterData`。

##### RSS 解析的事件处理程序

我们要写三个函数。这些函数中的每一个都必须有特定的参数。这些参数由 PHP 决定，因为是 PHP 的 XML 解析器调用它们。以下是您必须为这些功能定义的属性:

**T2`startElement($parser, $tagName, $attrs)`**

*   `$parser`将被传递一个对用于解析文档的 XML 解析器的引用。
*   `$tagName`是触发事件的开始标记名的全大写(PHP 手册称之为“大小写折叠”)版本。
*   `$attrs`是触发事件的标签中存在的属性的关联数组。例如，如果标签`<body bgcolor="#FFFFFF">`触发了事件，那么`$attrs['BGCOLOR']`的值就是`"#FFFFFF"`。注意，和标记名一样，属性名也是大小写混合的(全部大写)。

**T2`endElement($parser, $tagName)`**

*   `$parser`将被传递一个对用于解析文档的 XML 解析器的引用。
*   `$tagName`是触发事件的结束标记的大小写混合名称。

**T2`characterData($parser, $data)`**

*   `$parser`将被传递一个对用于解析文档的 XML 解析器的引用。
*   `$data`是出现在文档中 XML 标签之间的文本字符串。两个标签之间的文本不一定触发单个事件。分布在多行上的文本块将导致每行一个事件，每个事件被传递给该行的`$data`。

考虑到这一点，将 SitePoint 的 RSS 文件的 XML 数据转换成可查看的 HTML 文档的过程初看起来可能相当简单。但是，如果你停下来试着弄清楚这三个事件处理函数应该做什么，你会很快意识到这并不像看起来那么简单。对于那些在这个阶段感到迷茫的人，不要担心。看看这些将处理 SitePoint 的 RSS 文件(或者任何站点的 RSS 文件)的函数的定义，应该会有助于理解这些函数。

给您留下深刻印象的第一个复杂性是，`characterData`函数必须对出现在标签之间的文本做出反应，但是没有传递任何东西给函数来告诉它*哪些*标签包含正在处理的文本。因此，大多数 XML 解析脚本需要定义一组全局变量来跟踪一个事件处理函数接收到的信息，以供其他函数使用。

在我们的 RSS 文件中，我们需要的关于 SitePoint 封面文章的所有信息都包含在文档的`<item>`标签中。所以我们将定义的第一个全局变量是`$insideitem`，当输入一个`<item>`标签时，我们将它设置为`true`，当退出一个标签时，我们将它设置为`false`。我们还将定义其他四个变量，随着时间的推移，这些变量的用途将变得清晰:

`$insideitem = false;
$tag = "";
$title = "";
$description = "";
$link = "";`

先从`startElement`说起吧。每当遇到开始标记时，XML 解析器都会调用这个函数。由于我们只对`<item>`标签之间发生的事情感兴趣，我们将首先检查我们是否确实在`<item>`标签内:

`function startElement($parser, $tagName, $attrs) {
global $insideitem, $tag;
if ($insideitem) {`

请注意函数开头的全局语句，它表明该函数需要访问`$insideitem`和`$tag`全局变量。现在，如果`$insideitem`是`true`，这意味着我们要注意开始的标签，这样我们就知道如何处理它包含的字符数据，这将触发对下一个`characterData`的调用。因此，我们在全局变量`$tag`中记录标签的名称(`$tagName`):

`$tag = $tagName;`

另一方面，如果我们不在一个`<item>`标签内，那么我们可能感兴趣的唯一开始标签将是一个实际的`<item>`标签，在这种情况下，我们将把`$insideitem`设置为 true，以表明我们正在进入这些标签中的一个:

`} elseif ($tagName == "ITEM") {
$insideitem = true;
}
}`

请注意，我们正在检查`$tagName`是否是`"ITEM"`，因为标签名称都是大写的。

这样做是为了打开标签。解析 RSS 文档的下一步是处理出现在标签之间的*字符数据，这是我们的`characterData`函数的工作:*

`function characterData($parser, $data) {
global $insideitem, $tag, $title, $description, $link;`

这个函数需要访问我们所有的五个全局变量，我们很快就会看到。现在，和以前一样，我们唯一对 XML 文件中的字符数据感兴趣的时候是在一个`<item>`标记中，所以第一步还是检查情况是否如此:

`if ($insideitem) {`

现在，有三个不同的标签可以出现在我们感兴趣的<item>标签中:`<title>`、`<description>`和`<link>`。现在，由于我们希望在每篇文章的描述上方显示标题，并链接到在`<link>`标签中指定的 URL，我们不能简单地输出在 XML 文件中遇到的字符数据。相反，我们需要收集每个`<item>`标签的所有数据，然后一次性打印出来。我们的全局`$title`、`$description`和`$link`变量将用于此目的。我们将使用一个`switch`语句来确定我们正在处理哪个标签，并将`$data`存储在相应的变量中。回想一下，当前标签的名称存储在全局变量`$tag`中。</item>

`switch ($tag) {
case "TITLE":
$title .= $data;
break;
case "DESCRIPTION":
$description .= $data;
break;
case "LINK":
$link .= $data;
break;
}
}
}`

注意，我们*将* ( `.=`)的`$data`附加到正在讨论的变量上，而不是简单地赋值(`=`)，因为单个标签的内容可以作为几个连续的`characterData`事件接收。

一旦标签的字符数据被处理，下一个事件将调用我们的`endElement`函数来指示结束标签。在这个应用程序中，唯一需要我们在标签关闭后进行操作的标签是`<item>`标签。当遇到`</item>`标签时，我们将检索到该项目的所有`$title`、`$description`和`$link`数据，因此我们可以将其输出为 HTML:

`function endElement($parser, $tagName) {
global $insideitem, $tag, $title, $description, $link;
if ($tagName == "ITEM") {
printf("<p><b><a href='%s'>%s</a></b></p>",
trim($link),htmlspecialchars(trim($title)));
printf("<p>%s</p>",htmlspecialchars(trim($description)));`

如果您不习惯我在上面使用的更方便的`printf`函数，请随意使用`echo`语句。在这两种情况下，一旦输出了`<item>`的 URL、标题和描述，就可以清除全局变量，以便它们准备好接收文档中下一个`<item>`的字符数据:

`$title = "";
$description = "";
$link = "";`

然后最后将`$insideitem`设置为`false`,以向我们的其他函数表明我们不再位于`<item>`标签内。

`$insideitem = false;
}
}`

就是这样！要查看这个脚本的运行情况，[点击这里](http://www.webmasterbase.com/examples/phpxml/sitepointcover.php)。您还可以看到完整的[源代码](http://www.webmasterbase.com/examples/phpxml/sitepointcover.php.txt)(如果源代码没有显示为文本文件，请使用浏览器中的查看源代码命令)。

##### 另一个选项

当我提到使用全局变量时，听众中更有经验的程序员可能会退缩。有一个学派认为全局变量仅仅是懒惰编程的标志，事实上 PHP 的面向对象编程(OOP)特性提供了一个更好的选择。这是我们刚刚开发的脚本的另一个版本。我们不使用函数来处理 XML 文档事件，而是使用 PHP 对象的方法。这些方法必须共享的数据可以存储为对象的实例变量，从而消除了脚本中对全局变量的需求。

`class RSSParser {`

 `其中$ internal = false；
有$标签=’。
有$ title =’。
有$ description =’。
有$ link =’。

函数 startElement($parser，$tagName，$ attrs){
if($ this->inside item){
$ this->tag = $ tagName；
} else if($ tagName = = " ITEM "){
$ this->inside ITEM = true；
}
}

函数 endElement($parser，$ tagName){
if($ tagName = = " ITEM)){
printf("<p><b><a href = ' % s '>% s</a></b></p>"，
trim($this- > link)，htmlspecialchars(trim($ this->title))；
printf("<p>% s</p>"，
htmlspecialchars(trim($ this->description))；
$ this->title = " "；
$ this->description = " "；
$ this->link = " "；
$ this->inside item = false；
}
}

函数 characterData($parser，$ data){
if($ this->inside item){
switch($ this->tag){
case " TITLE ":
$ this->TITLE。= $ data
突围；
案例「描述」:
$本- >描述。= $ data
破；
案例“链接”:
$this- >链接。= $ data
破；
}
}
}
}` 

`$xml_parser = xml_parser_create();
$rss_parser = new RSSParser();
xml_set_object($xml_parser,&$rss_parser);
xml_set_element_handler($xml_parser, "startElement", "endElement");
xml_set_character_data_handler($xml_parser, "characterData");
$fp = fopen("https://www.sitepoint.com/rss.php","r")
or die("Error reading RSS data.");
while ($data = fread($fp, 4096))
xml_parse($xml_parser, $data, feof($fp))
or die(sprintf("XML error: %s at line %d",
xml_error_string(xml_get_error_code($xml_parser)),
xml_get_current_line_number($xml_parser)));
fclose($fp);
xml_parser_free($xml_parser);`

如果您熟悉 PHP 中的面向对象编程，那么唯一可能让您感到恐慌的一行代码如下:

`xml_set_object($xml_parser,&$rss_parser);`

由于`xml_set_element_handler`和`xml_set_character_data_handler`不能将对对象方法的引用作为事件处理程序的函数名(即`xml_set_element_handler($xml_parser,"$rss_parser->startElement",...`不起作用)，您需要一种方法来告诉 XML 解析器调用`$rss_parser`对象的方法，而不是基本函数。`xml_set_object`就是这么做的，带着解析器和对您希望它调用其方法的对象的引用(注意`&`)。

点击这里下载这个面向对象版本[的完整代码。](http://www.webmasterbase.com/examples/phpxml/sitepointcover-oo.php.txt)

##### 进一步阅读的摘要和资源

在本文中，我们学习了如何使用 PHP 解析 XML 文档(以 RSS 1.0 文档为例)。尽管它的基于事件的 XML 文档处理 API 可能需要一些思考来确定如何处理一个简单的 XML 文档结构，但是我们看到，在文档处理过程中考虑事件的发生有助于确定每个事件处理方法需要做什么。

在一个完全编码的示例中，我演示了如何解析 SitePoint 的 RSS 文件，该文件列出了任何给定时间关于 SitePoint.com 的封面文章。请随意修改这段代码，在您的站点上使用，以适合您站点的任何格式列出 SitePoint.com 当前的头条新闻！对于编程纯粹主义者，我提供了一个替代版本的代码，通过封装事件处理程序和它们必须在 PHP 类中共享的变量，避免了使用全局变量。再做一点工作，您就可以将所有的 XML 处理代码封装在该类中，并将该类放在一个 include 文件中，从而将您的文档中用于显示 SitePoint 标题的实际代码减少为两行:创建您的 RSSParser 对象，然后向它的一个方法传递 SitePoint 的 RSS 文件的 URL([https://www.sitepoint.com/rss.php](https://www.sitepoint.com/feed))。

有关 RSS 1.0 规范的更多信息，请访问 [RSS 工作组的网站](http://www.purl.org/rss/)。该站点还包含了一些链接，这些链接指向目前仍被网上许多站点使用的较旧的 RSS 0.9x 标准的信息，因为 RSS 1.0 还是相当新的。您会很高兴地知道，用 PHP 解析 RSS 0.9x 格式和 RSS 1.0 一样容易，甚至更容易。

要查看解析 RSS 0.91 文档所涉及的代码，请参阅 Mark Robards 的文章[用 PHP](http://www.wirelessdevnet.com/channels/wap/features/xmlcast_php.html) 解析 XML。这篇简短但内容丰富的文章演示了一种用 PHP 解析 XML 数据的技术，这种技术最大限度地减少了所需的全局变量(或者实例变量，如果您使用面向对象的方法的话)的数量。就我个人而言，我更喜欢可读的代码，而不是这些巧妙的技巧，但是这种技术可以大大简化非常复杂的 XML 文档的解析。

## 分享这篇文章