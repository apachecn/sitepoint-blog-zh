# 使用微数据向机器人开放你的数据

> 原文：<https://www.sitepoint.com/open-data-bots-using-microdata/>

![htmlcss2thumb](img/d1b531ecd770df998ea32dbc2cf0f23c.png)

以下是我们的书的摘录，由 Alexis Goldstein、Louis Lazaris 和 Estelle Weyl 撰写的《真实世界的 CSS3，第二版。世界各地的商店都有出售，或者你可以在这里买到电子书。

微数据是另一种迅速获得采用和支持的技术，但与 WAI-ARIA 不同，它在技术上是 HTML5 的一部分。虽然仍处于开发早期，但这里值得一提的是[微数据规范，](https://www.w3.org/TR/microdata/)因为该技术提供了对文档可读性和语义未来的一瞥。

在规范中，**微数据**被定义为一种机制，它“允许机器可读的数据以一种易于编写的方式嵌入到 HTML 文档中，并具有明确的解析模型。”

使用微数据，页面作者可以给 HTML 元素添加特定的标签，对它们进行注释，以便它们可以被机器或机器人读取。这是通过定制的词汇表来完成的。例如，您可能希望脚本或其他第三方服务能够访问您的页面，并以某种方式与页面上的特定元素进行交互。使用微数据，您可以扩展现有的语义元素(比如`article`和`figure`)，以允许这些服务对带注释的内容进行专门的访问。

这可能会让人感到困惑，所以让我们考虑一个真实的例子。假设你的网站包括电影评论。您可以在一个`article`元素中包含每个评论，并为您的评论标上一定数量的星或百分比分数。但是当一台机器出现时，比如谷歌的搜索蜘蛛，它无法知道你内容的哪一部分是真正的评论——它看到的只是页面上的一堆文本。

为什么机器会想知道你对一部电影的看法？值得考虑的是，谷歌已经开始在其搜索结果页面中显示更丰富的信息，以便为搜索者提供不仅仅是文本匹配的查询。它通过使用微数据或其他类似技术读取这些网站页面中编码的评论信息来做到这一点。电影评论信息的示例如下所示。

![rich_snippet](img/8dc49ebc52e146918b884314b2b981c5.png)

通过使用微数据，您可以准确地指定页面的哪些部分对应于评论、人物、事件等——所有这些都是软件应用程序可以理解和使用的一致词汇表。

## HTML5 的语义还不够吗？

HTML5 规范现在包含了许多新元素，以支持更具表现力的标记。但是在 HTML 中添加只被少数人使用的元素会适得其反。这将使语言膨胀，使其功能从各个角度来看都不可维护——无论是规范作者、浏览器供应商还是标准团体。

另一方面，微数据允许开发人员在特定情况下使用定制词汇表(现有的或他们自己的)——使用 HTML5 的语义元素是不可能的。因此，现有的 HTML 元素和 HTML5 中添加的新元素被保留为一种语义基线，而开发人员可以根据自己的需求创建特定的注释。

## 微数据语法

微数据处理现有的格式良好的 HTML 内容，并通过名称-值对(也称为**属性**)添加到文档中。微数据禁止你创建新元素；相反，它为您提供了添加自定义属性的选项，这些属性扩展了现有元素的语义。

这里有一个简单的例子:

```
<aside itemscope> 
  <h1 itemprop="name">John Doe</h1> 
  <p><img src="https://www.sitepoint.com/bio-photo.jpg" alt="John Doe" itemprop="photo">&lt/p>
  <p><a href="http://www.sitepoint.com" itemprop="url">Author’s website</a></p>
</aside>
```

在上面的例子中，我们在一个`aside`元素中放置了一个普通的作者简历。您会注意到的第一个奇怪之处是布尔属性`itemscope`。这将`aside`元素标识为定义微数据词汇表范围的容器。属性`itemscope`的存在定义了规范所指的**项**。每个项目都由一组名称-值对来表征。

定义词汇表范围的能力允许我们在一个页面上定义多个词汇表。在这个例子中，`aside`元素中的所有名称-值对都是单个微数据词汇表的一部分。

在`itemscope`属性之后，下一个感兴趣的项目是`itemprop`属性，它的值是`"name"`。此时，解释脚本如何从这些属性中获取信息，以及我们所说的“名称-值对”是什么，可能是一个好主意

## 了解名称-值对

名称是借助于`itemprop`属性定义的属性。在我们的例子中，第一个属性名恰好是一个名为`name`的属性。在这个范围内有两个额外的属性名:`photo`和`url`。

给定属性的值有不同的定义，这取决于声明该属性的元素。对于大多数元素，值取自其文本内容；例如，我们示例中的`name`属性将从开始和结束`h1`标记之间的文本内容中获取其值。其他元素区别对待。

`photo`属性的值来自图像的`src`属性，因此该值由指向作者照片的 URL 组成。虽然在具有文本内容的元素(即短语“作者的网站”)上定义了`url`属性，但是它并不使用该文本内容来确定其值；相反，它从`href`属性中获取它的值。

其他不使用相关文本内容来定义微数据值的元素包括`meta`、`iframe`、`object`、`audio`、`link`和`time`。关于从文本内容以外的地方获取值的元素的完整列表，请参见微数据规范的[值部分。](https://www.w3.org/TR/microdata/#values)

## 微数据名称空间

到目前为止，我们所描述的对于不打算重用的微数据来说是可以接受的——但是这有点不切实际。正如我们所讨论的，当第三方脚本和页面作者可以访问我们的名称-值对并为它们找到有益的用途时，微数据的真正力量就释放出来了。

为了实现这一点，每个条目必须通过`itemtype`属性定义一个类型。请记住，微数据上下文中的项目是设置了`itemscope`属性的元素。该元素中的每个元素和名称-值对都是该项的一部分。因此，`itemtype`属性的值定义了该项目的词汇表的名称空间。让我们在示例中添加一个`itemtype`:

```
<aside itemscope itemtype="http://schema.org/Person">
  <h1 itemprop="name">John Doe</h1>
  <p><img src="https://www.sitepoint.com/bio-photo.jpg" alt="John Doe" itemprop="photo"></p>
  <p><a href="http://www.sitepoint.com" itemprop="url">Author’s website</a></p>
</aside>
```

在我们的项目中，我们使用“http://schema . org/Person”URL，它来自[Schema.org，](http://schema.org/)几个主要搜索引擎支持的合作项目。这个网站包含许多微数据词汇表，包括组织、个人、评论、事件等等。

## 进一步阅读

这个关于微数据的简要介绍并没有恰当地描述这个主题，但是我们希望它能够让您体验到使用这种技术扩展文档语义的可能性。

这是一个非常广泛的话题，需要在这个来源之外阅读和研究。考虑到这一点，如果你想更深入地了解微数据提供的可能性，可以查看以下几个链接:

*   [“扩展 html 5—微数据”](http://html5doctor.com/microdata/)在 HTML5 Doctor 上

*   [W3C 微数据规范](https://www.w3.org/TR/microdata/)

*   [Mark Pilgrim 对微数据的出色概述](http://diveintohtml5.info/extensibility.html)

*   [谷歌丰富的片段帮了大忙](http://www.google.com/support/webmasters/bin/answer.py?hl=en&answer=99170)

*   [“使用模式提高搜索结果中的内容可见性”](https://www.sitepoint.com/using-schemas-improve-content-visibility-search-results/)在 SitePoint 上

## 分享这篇文章