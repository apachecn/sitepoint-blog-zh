# Nokogiri 基础:从网络中提取 HTML

> 原文：<https://www.sitepoint.com/nokogiri-fundamentals-extract-html-web/>

![Vector code  maintenance icon](img/b5967560db08095fbeb3ba0c65a9a1bf.png)

大多数人在没有首先掌握一些基础知识的情况下试图学习 Nokogiri 时会感到非常困惑。这是有原因的。试图学习 Nokogiri 而不学习使其工作的东西就像试图学习 Word 的功能而不知道如何打字一样。到本文结束时，您将能够轻松地访问一个网站并从中提取任何数据。

Nokogiri 是最受欢迎的红宝石之一，在出版时有超过 3700 万次下载。相比之下，Rails 目前有[4700 万次下载](https://rubygems.org/gems/rails)。我看到这个后的第一个想法是:“哇，如果这个宝石这么受欢迎，肯定有一些关于它的相当全面的教程，对不对？”如果你在谷歌中输入[“rails 教程”](https://www.google.com/search?&q= "rails+tutorial")，你会得到超过 28 万个结果。搜索[“noko giri 教程”](https://www.google.com/search?&q= "nokogiri+tutorial")会给你…不到 3000 个结果(好的一面是，这篇文章发布后，这个数字应该会变大！).

## Nokogiri 是一个解析器…什么？

更糟糕的是，大多数教程混淆而不是澄清事情。例如，在描述 Nokogiri 时，大多数文章都将其描述为“解析器”。大多数人对什么是解析器没有明确的定义。要回答这个问题，看一下 [this StackOverflow answer](http://stackoverflow.com/a/2933218) 在这里它被描述为“将某种数据(通常是字符串)转变为另一种数据(通常是数据结构)的东西”。这让事情变得更清楚了。

## 为什么这么多人用 Nokogiri？

像 Nokogiri 这样的解析器最常见的用途是从结构化文档中提取数据。对您至关重要的数据。示例:

*   价格比较网站上的价格列表。
*   来自搜索引擎的搜索结果链接。
*   来自问答网站的答案列表。

在回答为什么要使用 Nokogiri 之前，让我们采取一种更加手动的方法。早在 2009 年，SitePoint 主页[的底部有一个“主题”标签云](http://web.archive.org/web/20090220003702/https://www.sitepoint.com/):

![nokogiri1](img/977c92c1b3369444004b3bf74fb55981.png)

比方说，我想让你写一个程序来抓取云中的所有主题(比如 Flash、Php、架构等等)。你会怎么做？如果我让你描述一下你的程序在一个非常高的层次上要采取的步骤，它可能会从两件事开始:

1.  下载 HTML 文件的内容(内容将是一些文本数据)
2.  从步骤 1 中获得的内容中提取相关数据。

在 Ruby 中，您可以通过多种方式完成步骤 1:

*   使用`open-uri`将 HTML 内容存储在一个字符串变量中(后面提供了一个这样做的例子)
*   使用`watir-webdriver`导航到网页并获取它的 HTML(这里提供了一个[简短解释](http://stackoverflow.com/questions/9302737/ruby-watir-to-get-html-of-a-page)

第二步比较棘手。获得页面的实际 HTML 内容后，如何提取相关数据，比如“主题”项？您当然可以使用正则表达式(毕竟，HTML 内容只是文本)。但这是一个非常非常糟糕的想法。HTML 文本不同于普通文本(普通文本的一个例子是你正在阅读的单词)，有更好的方法从中提取相关数据。

## 你的大部分时间将花在寻找模式上

在从网页中提取数据的过程中，你实际上花在与 Nokogiri 互动上的时间是最少的。你的大部分时间将花在寻找从网页中选择数据的方法上。这和正则表达式的原理是一样的。你花了大部分时间计算出实际的表达式来选择你想要的文本。使用像 Nokogiri 这样的解析器，而不是文本，你的大部分时间将花在找出选择 *HTML 元素*的方法上(从技术上来说，你在选择节点，有 3 种类型的节点:元素、属性和文本节点，然而，大多数时间你将编写表达式来选择元素节点，因为 Nokogiri 允许你事后容易地获得属性/文本节点信息。[如果你不熟悉节点，这是一个很棒的教程](http://www.w3schools.com/jsref/dom_obj_all.asp)。

大多数网站的 HTML 代码都有可预测的结构，这是你的优势。你所需要做的就是*用 xPath/CSS 表达式描述*这个结构(我们将在后面讨论)，你的大部分工作就完成了。可以把 xPath/CSS 表达式看作 HTML 的正则表达式。

我们以 SitePoint 标签云为例。如果你使用 Chrome inspector 之类的工具分析 HTML 代码(右键单击标签云中的任何内容，然后按“检查元素”)，你会注意到标签云本身是一个大的`ul`元素，其`class`属性设置为`tagcloud`。在列表中，单个标签名存储在`li`元素中，在`li`元素中直接有`a`元素，我们想要获取的数据作为文本存储在`a`元素中。

你当然可以写一个复杂、庞大的正则表达式来描述这些数据，但是正如我们所看到的，这是一个糟糕的想法。Nokogiri 主要支持两种方式来描述 HTML 元素:

## xPath 表达式

xPath 的大多数定义都很隐晦。基本上，xPath 是一种语言，**在文档中定位**元素。文档可以是 HTML 或 XML(大多数 xPath 教程在示例中使用 XML 文档)，只要整个文档是 HTML/XML 的构建块*元素*的结构化集合，就没有关系。

之前，我们用简单的英语描述了定位标签云项目的位置。我们提到它们被直接存储在`li`元素内的`a`元素中，而`li`元素本身又在一个具有`class`属性`tagcloud`的`ul`元素内。让我们把它翻译成 xPath:

```
//ul[@class='tagcloud']/li/a
```

迷茫？让我们一步步来分析一切:

```
// - Start anywhere in the HTML document
ul - get a <li>
[@class="tagcloud"] - where the class attribute equals to tagcloud
/li/a - get a <li> element with <a> child.
```

我的建议是总是以//而不是单个/开始 xPath 表达式，因为单个斜杠将从文档的根开始。使用双斜杠，您将搜索整个文档。如果我们在上面的例子中使用了一个斜杠，我们需要这样做:

```
/html/body/p/ul ..and so on
```

至少花 2 个小时练习 xPath。如果有人在我开始使用 Nokogiri 的时候告诉我这些，我会省去很多麻烦！

## 如何练习 xPath

学习 xPath 最有效的方法是在一个环境中实践它，这个环境将为您提供关于 xPath 表达式是否有效的*即时反馈*(就像正则表达式的 [Rubular](http://rubular.com) )。

我的建议是为 Chrome 安装 [xPath Helper](https://chrome.google.com/webstore/detail/xpath-helper/hgimnogjllphhhkhlmebbmlgjoejdpjl?hl=en) ，在自己喜欢的网站上使用。转到我们在本文中使用的 Archive.org[网站，键入上面的表达式，看看您会得到什么(匹配项有黄色背景):](http://web.archive.org/web/20090220003702/https://www.sitepoint.com/)

![WwEuv8u](img/c452aeb21822941f8974aa4be0e631e4.png)

[这也是在特定 HTML 内容上练习 xPath 的一个很好的资源](http://staff.washington.edu/tabrooks/343INFO/greaseMonkey/practiceXPath.htm)。关于帮助您入门的优秀教程，请参见:

*   w3cSchools xPath 教程(不要被他们使用 XML 而不是 HTML 的事实所迷惑，这两种类型的文档的基本结构是相同的)。

*   [xPath 备忘单](https://www.simple-talk.com/dotnet/.net-framework/xpath,-css,-dom-and-selenium-the-rosetta-stone/)(它还包含关于如何用 CSS 选择器选择元素的注释)。

我怎么强调都不为过:**如果你想和 Nokogiri** 一起工作，至少花几个小时练习 xPath。学习 Nokogiri 而不首先学习如何使用 xPath 之类的东西选择 HTML 元素就像学习 Word 而不知道如何打字一样！编写您自己的 xPath 表达式，从您喜欢的网站中选择您想要的数据。有些浏览器可以选择为您生成 xPath 表达式，但是它们使 xPath 表达式变得更加复杂。将您自己的表达式输入到 xPath helper 中，检查它们是否匹配。

## CSS 选择器

如果你曾经做过 CSS，你会知道 CSS 规则由一个选择器(在这里你给出规则应用于什么元素的指令)和一个声明组成。您可以单独使用选择器部分来选择 Nokogiri 中的任何 HTML 元素。一个像 [SelectorGadget](https://chrome.google.com/webstore/detail/selectorgadget/mhjhnkcfbdhnjickkkdbjoemdmbfginb?hl=en) 这样的好的扩展为你输入的选择器提供即时反馈，并且是你学习它们的最佳选择。对于我们的标记云示例，这里是等价的 CSS 选择器表达式，它将选择与前面的 xPath 表达式相同的元素:

```
ul.tagcloud  > li >  a
```

如果你对 CSS 选择器一无所知，你可能也不知道 CSS。前往 [w3c schools](http://www.w3schools.com/css/) 并开始学习(在你完成非常基础的主题后，选择器会在开始时介绍)。

让我们编写第一个使用选择器的 Nokogiri 程序。

## 你的第一个 Nokogiri 程序

该程序将输出我们的 SitePoint 标签云中的所有标签名称:

```
require 'nokogiri'
require 'open-uri'

html_data = open('http://web.archive.org/web/20090220003702/https://www.sitepoint.com/').read
nokogiri_object = Nokogiri::HTML(html_data)
tagcloud_elements = nokogiri_object.xpath("//ul[@class='tagcloud']/li/a")

tagcloud_elements.each do |tagcloud_element|
  puts tagcloud_element.text
end
```

我故意把这个程序做得大一点，跳过了一些语法上的修饰，使它更容易解释。

这条线是一切的起点:

```
nokogiri_object = Nokogiri::HTML(html_data)
```

Nokogiri 需要一些内容来创建一个对象。在我们的例子中，内容来自从外部网站获取的 HTML 数据。但不一定非要这样！假设我保存了一份 HTML 文件的本地副本(你可以[在这里](https://www.dropbox.com/s/l9bwidboz5ab3mi/index.html?dl=0)下载)并把它放在和你的 Ruby 程序相同的文件夹里。然后可以用下面的代码替换声明了`html_data`变量的数据:

```
html_data = File.read('index.html')
```

现在，运行程序。一切都会按计划进行。Nokogiri 需要一些数据来开始，不管这些数据是来自远程位置还是您的硬盘。你会经常看到这样的东西:

```
nokogiri_object = Nokogiri::HTML(open('http://web.archive.org/web/20090220003702/https://www.sitepoint.com/'))
```

这是一个简单的版本，在一行中有几件事情在进行。数据被抓取并立即加载到 Nokogiri。如果使用这个，就不需要声明`html_data`的那一行了。

以下部分:

```
Nokogiri::HTML(html_data)
```

创建新的 Nokogiri 对象。你不能在 Photoshop 中编辑图片，除非你先在程序中打开它，对吗？想象一下用同样的方式创建一个 Nokogiri 对象:你不能用 HTML 做任何事情，除非你先把它“载入”Nokogiri。

一旦我们有了 Nokogiri 对象，我们就可以对它调用方法。怎么会？通过指示它在哪里找到我们需要的 HTML 元素(就像在记事本中键入一个特定的单词来找到它的所有出现，我们对 xPath/CSS 选择器做同样的事情)。这是你将花费大部分时间的地方，计算出“正确的”选择器等等。

我早期犯的一个大错误是想出一个 xPath/CSS 表达式，然后直接输入到 Nokogiri 中。大多数时候，我的表达式是错误的，没有选择任何内容，导致意外的 Ruby 错误。避免这种情况的方法是首先使用类似上面推荐的 Chrome 扩展测试 xPath/CSS 表达式，看看它们是否匹配。

下一行是我们要求 Nokogiri 对象给出所提供表达式的 HTML 元素匹配(如果有的话)的地方:

```
nokogiri_object.xpath("//ul[@class='tagcloud']/li/a")
```

如果您想要使用 CSS 版本，请将其替换为:

```
nokogiri_object.css("ul.tagcloud > li > a") # The white space in the CSS selector is optional
```

太好了！我们现在有了一组存储为数组的标记 cloud_elements。它们被存储为一个`NodeSet`，这是一个节点数组。我们之前讨论过节点，我的建议是现在把它们看作 HTML 元素，因为 99%的 xPath/CSS 表达式都会选择 HTML 元素。有多种选择文本/属性信息的方法，但是我更愿意将这种逻辑放入 Nokogiri 而不是 xPath/CSS 中。下一段代码显示了这一点。

将元素存储在数组中后，循环获取其中的文本。这就是最后一部分发生的事情:

```
tagcloud_elements.each do |tagcloud_element|
  puts tagcloud_element.text
end
```

假设需求发生了变化，我们想要获取属性`href`的值，而不是文本。为此，将`tagcloud_element.text`改为`tagcloud_element['href']`或`tagcloud_element.attribute('href')`。这就是你获得任何属性值的方法。用任何其他属性(`id`、`class`等等)替换`href`，以获得该属性的值。如果您试图获取一个不存在于元素中的属性，那么结果将是`nil`。

## 有用的 Nokogiri 片段

我花了 2 个多小时在 GitHub 上分析使用 Nokogiri 的代码，90%的时间都是相同的模式:人们创建一个新的 Nokogiri 对象，试图使用特定的 xPath/CSS 模式获取 HTML 元素，然后获取这些元素的文本属性。我们已经涵盖了所有这些内容。这里有一些其他有用的东西，一旦你学会了基础知识，你可以用 Nokogiri 做。我们将使用我在本文中使用的相同的标签云示例。

```
nokogiri_object.at_xpath("//ul[@class='tagcloud']/li/a")
```

用`at_xpath`(或`at_css`)代替`xpath` / `css`只会得到第一场比赛。如果没有找到匹配，则返回`nil`。

```
tagcloud_elements.each do |tagcloud_element|
  puts tagcloud_element.to_html
end
```

使用`to_html`方法获取匹配的 HTML 元素的 HTML。

```
tagcloud_elements.each do |tagcloud_element|
  puts tagcloud_element.parent
  puts tagcloud_element.children
  puts tagcloud_element.next_sibling
  puts tagcloud_element.previous_sibling
end
```

这些是在匹配的[文档对象模型](https://www.youtube.com/watch?v=-0ZcldkGlt8)中导航的方法。

现在你已经准备好开始使用 Nokogiri 了，没有大多数初学者都有的头痛问题！如果你有任何问题，欢迎在下面评论。

## 分享这篇文章