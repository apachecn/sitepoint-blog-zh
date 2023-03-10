# 漂亮排版的原则

> 原文：<https://www.sitepoint.com/principles-beautiful-typography/>

这篇文章写于 2007 年，至今仍是我们最受欢迎的帖子之一。如果你渴望了解更多关于字体设计的知识，你可能会对这个[最近的播客](https://www.sitepoint.com/podcast-175-typography/)很感兴趣。

让我们面对它:所有网页设计的核心目的是沟通。无论我们谈论的是在线电子商务商店、财富 500 强公司的网站，还是社交网站的个人资料，排版都是至关重要的组成部分。对大多数人来说，排版只是简单地排列一组熟悉的形状来组成单词、句子和段落。只需在[键盘](https://www.sitepoint.com/best-mechanical-keyboards/)上敲击几下就能打字，这让我们忘记了这种媒介的创造性和艺术性。

有效定制网页排版有许多障碍——我们将在接下来的几页中解决这些问题，这些问题摘自我的 SitePoint 书籍[](https://www.sitepoint.com/books/design1/)*美丽网页设计的原则——但是文字的力量应该足以推动众所周知的信封。不服气？拿起一本杂志，打开一台电视机，或者在杂货店里散散步。毫无疑问，你会看到数百种创造性的、有效的活字用法。这是品牌的实质，是无声交流的关键，也是网页设计的重要部分。*

 *为了释放类型的潜力，我们必须首先了解它。诚然，这不是一项容易的任务。经过几个世纪的研究和实践，字体的微小细节及其周围的空间已经被仔细计算出来。在印刷术的早期，每一种字体的每一个字母都必须被雕刻在木头上或者用铅铸造，上墨，然后压制在纸上。这是一个手工制作的职业，需要对细节的严格关注。尽管这种做法的实用性早已被现代印刷方法超越，但许多学院和大学还是开设了凸版印刷课程，以便未来的平面设计师既能体会到在计算机上使用字体的好处，又能看到印刷探索的潜力。

我个人对字体设计的热爱是双重的。作为一名设计师，我喜欢在艺术方面与文字打交道。我喜欢不同字体提供的声音，以及像图 4.1 中那样的排版拼贴的表现力。毕竟构成排版的词根是*错别字*，意为印象或标记，*字形*，意为书写；印刷术字面上的意思是用文字制造印象。作为一名程序员，我也喜欢处理文字时所涉及的类似难题的解决问题的任务。字体和颜色的选择只是字体的冰山一角。事实上，在我们的文字工作中，需要做出的大多数决定都与字体和文本块周围的空间有关，而不是实际的文字本身。然而，选择合适的字体也是至关重要的一步。

## 警告:这个话题可能会上瘾！

学习字体设计一段时间后，你再也不会以同样的方式看广告牌、宣传册或书籍了。你可能会开始拍摄主题公园的乘车标志，而不是你的孩子。思考餐厅菜单上的主菜是在 Cantoria 还是 Meyer 2 中，可能比在主菜之间做出选择更有趣。对字体设计的研究吸引了很多人，并且永远不会让他们离开！你被警告了。

type 的历史和实现是一个已经占据了数百本书的主题。不过，在这一章中，我打算简单介绍一下印刷术的世界。首先，我将介绍一些将文字带到网络上的问题和解决方案。然后，我们将进入基本的字体术语，探索一些使用指南，并研究不同字体的特点。从我们对易读性问题的讨论，到在线使用动态标题的问题，我希望你会发现这一章是实用和有启发性的。

![A collage of found typography](img/98ad1cbaf9360ca67e23d7ee5aad1b87.png)

如果你喜欢你在这里看到的，并且想更深入一点，有很多在线社区可以学习和讨论印刷术。其中最受推崇的是 typophile.com。“打字爱好者”这个名字充分说明了这个社区的性质，如果你对字体有任何问题，这些论坛中可能会有人可以提供帮助。

##### 把文字带到网上

当涉及到网页和选择将在浏览器中显示的文本字体时，不管你安装了 5 种还是 5000 种字体；你必须考虑最小公分母。在 90 年代，一个短暂的趋势是，许多网站设计者将他们网站的文本设置为他们想要的任何晦涩的字体，并在网站上包括一个声明，如“这个网站在某种字体下看起来最好，单击此处下载它”，以及在他们的网站上发布实际的字体文件以供下载。这种方法不仅很可能违反了版权法，而且我们不应该期望网站访问者只是为了浏览我们的网站而不辞辛苦。

默认情况下，两种主要操作系统支持的字体系列数量非常少。图 4.2 中九种字体系列的列表通常被称为安全列表。安全列表的缺点是它没有为每个字体类别提供太多的多样性。如果你需要一个标准的无衬线字体，你必须在 Arial，Trebuchet MS 和 Verdana 之间选择。对于没有接触过很多字体的人来说，这似乎是一个合理的变化，但对于我们这些知道其他无衬线字体(如 Helvetica 新字体、Futura 字体和 Univers 字体)细微差别的人来说，使用一种安全的字体就像用螺丝刀钉钉子一样。

幸运的是，CSS 的`font-family`属性允许您按照偏好的顺序选择多种字体。如果第一种字体不可用，将使用第二种字体；如果第二种字体不可用，将使用第三种字体，依此类推。假设您希望您的部分标题有衬线字体。您认为最适合这项工作的字体是 Calisto MT，所以您首先指定它——为少数安装了它的人。

![The nine web-safe fonts](img/a73dcfa6e2cfc0096c89a4fd0a8cb848.png)

你的第二个选择是你的第一个后备计划，为此你选择了格鲁吉亚。如果用户没有安装 Calisto MT，他们将看到佐治亚州。尽管 Georgia 在安全列表中，但有些人可能没有安装它。Times New Roman 与之相当，所以您决定不妨将它作为下一个选项添加进来。为了完成优先列表，为了迎合没有安装这些字体的用户，您添加了 W3C 所谓的通用字体系列。通用字体系列有衬线字体、无衬线字体、草书字体(类似于手写体或手写字体)、幻想字体(或新奇字体)和等宽字体。到目前为止，您所有的字体选择都来自 serif 系列，所以这是您指定的通用系列。总而言之，您的字体系列声明应该是这样的:

```
font-family: 'Calisto MT', Georgia, 'Times New Roman', serif; 
```

*提示:带空格的字体*
*记住，任何包含空格的字体系列名称都必须用引号括起来，要么用单引号(`'`)要么用双引号(`"`)括起来。*

## 在图像中显示文本

现在，所有这些优先的字体规范都没问题，但是如果你真的想让你的访问者看到一些特定字体的文本呢？在这种情况下，最明显的解决方案是依靠图像来显示相关文本。这就是 Kevin Cornell 在设计他的投资组合网站 [Bearskinrug](http://www.bearskinrug.co.uk/) 时使用的方法，如图 4.3 所示。徽标、顶部导航和网站上的大部分文本都以图像的形式呈现。这种方法不仅给了 Kevin 控制字体显示的能力，还允许他将飞溅的水彩背景图形整合到文本的设计中。这个网站有许多文字图片…有时还有一只叫 Mojo 的袜子猴。

![Bearskinrug -- fonts presented as images](img/31b7713e57c4e457110d75e71e648189.png)

Kevin 在他的网站上为一些文本图形使用的技术对于不经常改变的静态文本很有效，但是如果您想要为周期性改变的文本使用非常特定的字体，比如新闻文章博客条目的标题，该怎么办呢？不断创建和上传新的文本图形将是一项单调的任务，即使对于一个专业使用图像编辑软件的设计师来说也是如此。如果你正在为一个不知道如何使用 Photoshop 或任何 HTML 的客户建立一个博客，你最好忘记这个选项。在这种情况下你能做什么？

## 用 sIFR 替换文本

目前，最受欢迎的文本替换选项是 sIFR(发音为“siffer”)，这是可伸缩因曼 Flash 替换的首字母缩写——一种允许设计师使用 Flash 和 JavaScript 将他们选择的字体应用到标题的技术。

![The sIFR Logo](img/c6219fd7a9155746c9426977742ea7c0.png)

sIFR 背后的基本前提是，Flash 电影能够嵌入字体，并以抗锯齿的美丽向大多数安装了 Flash 的 web 用户显示，JavaScript 能够用 Flash 电影替换指定的 HTML 对象。通过将这两个概念结合在一起，网页设计师和开发者肖恩·因曼创造了一种他称之为因曼闪存替代(IFR)的技术，这是 sIFR 的前身。IFR 是网页排版的革命性发展，但是它有一些小缺点。它最显著的缺点是只能用来替换单行文本。认识到这项技术的潜力，Mike Davidson 和 Mark Wubben，以及无数的支持者和测试者，共同开发了一个文档完善、易于实现的解决方案——sIFR，并作为开源软件发布给开发社区。

那些对 Flash 和 JavaScript 解决网络问题的方案持怀疑态度的人可以放心了，因为 sIFR 是一个完全可访问的解决方案，并且降级很好。如果用户没有安装 Flash，或者他们关闭了 JavaScript，他们将看到脚本和 Flash 本应替换的普通旧浏览器文本。如果你对这个解决方案总体上持怀疑态度，你会很高兴地听到，它有一个令人印象深刻且不断增长的高调支持者名单，包括 [ABC 新闻](http://abcnews.go.com/)和[耐克](http://www.nike.com/nikebiz/nikeconsidered/)。在网站上寻找 sIFR 文本的例子可能很困难，特别是当他们使用它来显示通常安装在许多计算机上的字体时。例如，美国广播公司新闻使用 sIFR 来显示未来的新闻标题，如图 4.4 所示。Futura 是默认为 OSX 用户安装的字体，但通过使用 sIFR，ABC 可以确保其大多数用户以相同的字体看到标题。

![ABC News, featuring Futura headlines powered by sIFR](img/4e3e7c2bf98dc48a683c4d38518452c0.png)

要下载 sIFR 的最新版本，并访问完整的安装说明，请访问[迈克·戴维森的 sIFR 页面](http://www.mikeindustries.com/sifr/)或[马克·沃本的 sIFR 维基](http://wiki.novemberborn.net/sifr/)。

## 显而易见的解决方案:使用安全字体

像 sIFR 这样的创新解决方案证明了设计师是多么渴望在他们的布局中使用特定的字体。那么，如果每个互联网用户实际上都安装了你想在你的网站上使用的字体呢？用那种字体显示站点的文本就像在 CSS 中设置`font-family`属性一样简单。不幸的是，普遍可用的字体数量非常有限。这正是设计师 Andrei Michael Herasimchuk 于 2006 年 8 月在他的网站上发布“致约翰·沃诺克的公开信”的原因。在他的信中，Andrei 要求 Adobe 的联合创始人说服该公司向公共领域发布 8 到 12 种核心字体。如果他们这样做了，苹果和微软肯定会在他们的操作系统中包含这些字体，让所有互联网用户都可以使用。虽然发布这些字体会剥夺 Adobe 未来的一些收入，但 Andrei 认为这将“大大有助于为设计师提供他们需要的工具，以实现互联网上的通信承诺。”很自然，这封信得到了设计界的大量草根支持，如果能看到它有所收获，那将是一件美妙的事情。

即使通用安装字体的安全列表永远不会扩展，也总会有办法在您的网站中使用非标准字体。因此，了解不同的可用字体非常重要。要做到这一点，你必须首先了解组成这些字体的各个字形的一些细节。

## 字体剖析

我在大学里上的一些设计课对字体的解剖和术语有很深的了解。许多人已经能识别衬线、上升线和下降线，但是有一节课，我们要背大约 100 个术语。虽然我在这里不会太残忍，但在我们开始讨论类型之前，了解一些基本的术语是很重要的。当然，我可以用一些非正式的词来描述字体，比如曲线，斜线，等等，但是这很快就会让人感到困惑。

看一下图 4.5，下面解释一下。

![The terminology of type](img/c476f87a23f1d7b36d6631429e8edebf.png)

**1)基线**

基线是大多数字符所在的假想水平线。在图 4.5 中，唯一挂在基线下方的字符是小写字母“q”

**2)瓶盖高度**

帽高或帽线是另一条假想线。这个标记了字体中所有大写字母的高度。请注意，字帽高度低于字体的最大高度。

**3)横杆**

在大写字母“A”和“H”中，连接两行的笔划称为横杠。不连接两条线的水平笔划，如小写字母“f”或“t”，称为**十字笔划**。

**4)衬线**

衬线是某些字体底部和顶部的最后一笔的名称。当我们谈到字体的区别时，我会更多地谈论衬线。

**5)平均线**

另一条标志小写字母顶部边缘的假想水平线是中线。与它听起来的方式相反，中线并不总是正好位于基线和帽檐高度的中间。

**6)碗**

字母的碗是在字体中包围负空间的圆形曲线。碗的例子可以在字母“D”、“o”和“g”中看到。

7)下降者

延伸到字体基线以下的小写字母“g”、“j”、“p”、“q”和“y”的下部称为下行字母。通常延伸到基线以下的唯一其他字符是某些字体中的旧式数字。这些类型的数字，从图 4.6 中可以看到乔治亚字体的例子，被认为与小写罗马数字融合得更好，当它们用在正文中时看起来特别好。

![Old-style numerals in the font Georgia](img/39b84d2ee25c6d3b586cb16532ac40ad.png)

**8)计数器**

字母内的负空格称为计数器。在某些字母中，如“A”、“o”和“P”，计数器是完全封闭的。像“G”、“u”和“c”这样的字母中的非封闭的负空格也称为计数器。

**9)阀杆**

字干是字体中主要的垂直或对角线笔划。这些包括字母“I”和“H”的垂直部分，以及字母“w”中的所有笔画

标题

这可能是我最喜欢的字体术语。Tittle 是小写字母“j”和“I”上面的点的名称。

**11)端子**

没有衬线的字干或笔画的末端称为结尾。甚至一些衬线字体的末端也有终端，如图 4.6 中的字母“c”所示。

**12)上升器**

大多数小写字母的顶部形成一条假想的线，称为中线。一些小写字母有一个 ascender，这是一个在**中线**之上的扩展。那些字母是" b "、" d "、" f "、" h "、" k "、" l "和" t "

**13)腿**

字母“K”、“R”和“Q”中较低的有角度的笔画称为腿。这些有时也被称为尾巴。

**14)结扎线**

您可能没有注意到，在图 4.5 中，单词“fix”的“f”和“I”实际上组合成了一个字符。这种字符组合称为连字。如图 4.7 所示，连字的存在是为了使某些字符之间的间距更美观。

![Example of a ligature](img/2dac1d236bf2bb3cae84a4aa3b3aab0f.png)

**15) x 高度**

x-height 正是您所期望的:字体中小写 x 的高度。本质上，x 高度是字体基线和中线之间的距离。虽然不是很实用，但是实际上可以在 CSS (ex)中使用 x 高度作为相对的度量单位。

## 文本间距

现在你知道了如何描述字母的各个部分，下一步就是能够描述和调整字母之间的间距。我之前提到过，很多排版决策都是基于间距的。对于印刷字体来说，这一直是正确的，随着 CSS 的出现，它也适用于 web 字体。不管我们谈论的是使用印刷字体还是网页字体，有两个方向可以控制间距——水平方向和垂直方向。

## 水平间隔

**字距调整**和**字距调整**是你在关于水平字母间距的对话中经常听到的两个术语。字距调整是调整单个字母之间的间距的过程。通常，当您在打字时，您会注意到成对的字母看起来靠得太近或离得太远。大多数字体都有一套确定特定字符间距的规则。例如，字母“Wa”之间的字距应该比“WV”之间的字距窄得多大多数时候，字体规则足以使文本可读。如果没有，您可以在您选择的图像创建软件中调整单个字母对。

![Kerning examples -- notice the spacing differences](img/cf4c272c42ce5769401c8640d915ac51.png)

对于网页中的文本，不可能逐字母调整字距。您可以做的是调整`letter-spacing` CSS 属性，这在印刷界被称为调整字体的跟踪。像字距调整一样，字距调整字体之间的水平间距，但适用于每个字母之间的间距。如果您想让文字看起来更开阔、更自然，请尝试在字距值中增加一两个像素。图 4.9 显示了间距效应的一个例子。网页中的文本通常很紧凑，如图 4.9 所示，所以在这里指定一个负值可能会降低文本的可读性。

CSS 中的另一个水平间距选项由`word-spacing`属性提供。该属性可以采用正或负的长度，或者关键字`normal`。正如您所料，这个属性会影响单词之间的空格数量。

![Letter spacing example](img/42f8d01722160bc0ac287edf6b5687e5.png)

## 垂直线间距离

在印刷设计语言中，文本行之间的垂直空间被称为**前导**(发音与“铺垫”押韵)。这个术语来自活版印刷的早期，当时空白的铅条被用来分隔金属活字的线条。当没有额外的间隔时，这些线条被称为“实心的”增加了垂直间距的文本更容易阅读，但是正如你在图 4.10 的第一段中看到的，文本行之间的默认间距非常小。在图 4.10 的第二段中，我们调整了 CSS `line-height`属性:

```
line-height: 1.5em; 
```

em 是一个 CSS 单位，它测量字体的大小，从字体的顶部高度到其最低下行线的底部。最初，em 等于大写字母 M 的宽度，这就是它的名字的由来。

![Leading example](img/d964cbf974aba4109406e294fb703f4d.png)

## 文本对齐

你有没有注意到你在书籍和杂志中看到的文本经常是沿着页面或栏目的左右两边对齐的？这种类型的文本对齐被称为**对齐**。当文本对齐时，字母和单词的间距会自动调整，以便每行文本都有一个单词或字母与文本区域的左右边缘对齐。许多印刷设计师会对任何超过两行且不太窄的文本块使用对齐文本。通过将`text-alignment`属性设置为`justify`，可以用 CSS 对 Web 进行同样的文本处理。不过，在你开始为整个互联网辩护之前，让我给你两条关于文字辩护的警告:

一条河流穿过这里。

偶尔，一行中较宽的间距产生的间隙会与下一行中的间隙排成一行，下一行，下一行…最后你会得到你要的类型的峡谷或河流，如图 4.11 所示。这可能会分散读者的注意力。印刷设计师可以做出调整来解决这类问题，但在网络上，很难预测也不可能解决。

**什么？是吗？你呢。说什么？**

狭窄的柱子使河流问题变得更加严重。单词通常会被左右页边隔开，或者延伸到整个列的宽度。大多数文字处理程序通过在必要的地方用连字符连接单词来解决这个问题。浏览器不能做这种自动断字，所以网页设计者应该避免在狭窄的空间里使用两端对齐的文本。

![Justification problems -- can you spot the three other rivers present in this lorem ipsum text?](img/b887db6ffcf4de5ee639ad65d3cb2b79.png)

如果您不想更改文本的文本对齐方式来调整，您的其他选项是左对齐、右对齐或居中对齐。当文本沿页面或栏的左边缘或右边缘居中或对齐时，字符和单词之间的间距保持不变。任何文本块都可能出现河流问题，但在居中或仅在一侧对齐的文本中，不太可能出现易读性问题。

如果你想看看一些 HTML 文本在应用了不同的前导、跟踪和对齐设置后会是什么样子，一个很好的工具是 Marko Dugonji 的 [Typetester](http://typetester.maratz.com/) ，如图 4.12 所示。

Typetester 为您提供了一个界面，您可以将任何可以想到的文本选项应用于三列文本；然后，您可以并排比较这些显示器。一旦你有了一些你喜欢的设置，你可以点击工具标签来获得创建效果的 CSS。

![Typetester, an online application that allows you to compare HTML type](img/8b45b81648598149747e4d8f54885e1a.png)

## 字体区别

每个人都知道什么是字体。这是一组以相似风格出现的字母，它们预装在你的计算机上，当你想让你的文本看起来不一样时，你可以改变它。默认情况下，Windows 电脑平均安装 40 多种字体，而 Mac 用户平均可以使用大约 100 种字体。这些字体中的许多被组合成**字体系列**，该系列中的每种字体代表该系列命名后的字体的不同变体。大多数字体系列包括常规字体以及*斜体*、*粗体*和*粗斜体*变体。一些字体根本没有变化，一些可能只有粗体或斜体，一些商业上可用的字体系列有数百种变化。

正如一些字体家族的所有成员都有大耳朵或异常长的小脚趾一样，每个字体家族都有自己独特的、可识别的特征。看看图 4.13 中字母“g”的字体之间存在的所有差异。

![Fourteen gs](img/ca5825a3653dd0136b9dbf88e6e3f838.png)

这些特征有助于我们对字体和字体族进行分类。大多数字体可以分为衬线字体和无衬线字体。除了这种区别，还有许多其他的方法可以对字体进行分类和分组。我更喜欢将字体分为六个简单的类别:衬线字体、无衬线字体、手写字体、等宽字体、新奇字体和丁巴字体。现在让我们来看一看每一个。

## Serif Fonts

历史学家认为衬线源于罗马石雕。关于这些装饰性笔画的原始目的有很多争论，但在最近的历史中，它们被证明可以通过提供一条水平参考线来增加大块文本的可读性。当大多数设计师试图选择衬线字体时，第一个想到的就是 Times New Roman。然而，有很多种衬线字体可供我们选择。为了帮助我们做这个决定，首先决定我们希望我们的文本有什么样的声音是一个好主意。

看看图 4.14 中的 Garamond 文本。Garamond 是一种旧式衬线字体。旧式衬线字体改编自意大利抄写员的笔触，可以通过粗细笔触之间的平滑过渡以及圆形衬线边缘来识别。当我看到一种老式的衬线字体时，我觉得它似乎有一种历史悠久、手工制作的魅力。同时，像 Garamond 这样的字体是极其通用的。它们还没有老到不能用于现代应用，但这不是它们的强项。

图 4.14 中的第二种字体是巴斯克维尔字体，一种**过渡衬线**字体。连接笔画末端和衬线的弯曲角度被称为**括号**。过渡衬线字体的括号是圆形的，但是衬线的边缘是方形的。90 度角和完美直线的简单添加赋予了这类字体更加现代和机械的声音。这类衬线字体被称为过渡字体，因为它提供了旧式和现代衬线字体之间的过渡。

![Serif categories](img/26d6f8c8cff1d1b7e136ba90540ebf49.png)

在图 4.15 中，字体迪多特是一种**现代衬线**字体。现代衬线字体在粗细笔画之间提供了大量的对比，并且它们的衬线通常完全没有括号。现代衬线字体是在工业革命期间引入的，作为过渡衬线风格的激进替代。今天，这些字体与优雅、精致和时尚联系在一起。它们代表永恒，而不是尖端的现代。

由于其精细的线条细节，现代衬线字体真的只适合在标题中使用。你可以在图 4.15 中看到的 Vogue 杂志的标志是现代衬线字体使用的经典例子。其他使用现代衬线字体作为报头的著名杂志包括 *Brides* magazine 和 *Harper's Bazaar* 。

《Vogue》杂志的标志一直使用意大利语迪多特，这有助于将字体和公司确立为时尚的标志。

![Vogue -- Didot font for timeless style](img/2aa46f8e5d61b66ffa4c660fcdd7889d.png)

在 19 世纪后期，随着广告、海报和传单变得越来越普遍，需要一种更大胆的现代衬线字体来吸引人们的注意力。就是在这个时候 **slab serif** 字体问世了。你可以在图 4.15 中看到像罗克韦尔这样的平板衬线字体，有一种力量和坚韧的工业声音。这些面孔被设计成从远处也非常容易辨认。

## 无衬线字体

当印刷工开始尝试平板衬线时，完全消除衬线的想法似乎是一个巨大的错误。衬线是一种传统，去掉它们是印刷阉割。最初的无衬线字体在 19 世纪非常令人厌恶，以至于被认为是怪诞的。不过，最终，人们开始对无衬线字体的想法感兴趣，到 20 世纪 20 年代，一些人认为有衬线字体将很快被淘汰。

虽然衬线字体仍被广泛使用，但无衬线字体类别的普及性和多功能性仍在继续增长。这些类型的字体有一个更干净，更现代的感觉。它们作为标题很突出，尤其是当放在衬线字体的正文附近时。这一直是印刷设计的标准做法，也是我在大学里学到的一个技巧。然而，在网络上，相反的情况已经成为事实，因为设计师使用无衬线字体作为正文，而不是衬线字体的标题。在图 4.16 中的 [Coudal Partners](http://www.coudal.com/) 的主页上可以看到这个趋势。

![Coudal Partners homepage -- sans serif body text with serif headlines](img/8316653df038e42184c80dfec5002d68.png)

在网络上使用无衬线正文的转变主要是由于老式显示器和笔记本电脑屏幕的局限性。这些年来，显示设备的图像质量有了很大提高，但是 serif 字体的笔画变化和微小细节会使它们在低分辨率显示器上的小尺寸下几乎不可读。解决方法是增加字体大小来弥补，或者使用细节较少的无衬线字体。如图 4.17 所示，无衬线字体在小尺寸时可读性更好。

不管如何使用，无衬线字体对于任何用途都非常清晰和实用。最常用的无衬线字体是 Arial 和 Verdana。这些字体系列中的每一个都存在于两个主要操作系统的默认字体集中，但是在设计界，这些字体系列以被过度使用和通用而闻名(在设计界，Arial 还被广泛认为是 Helvetica 的穷亲戚)。这使得它们非常适合正文文本，在正文文本中，无声易读是目标，但对于标题和艺术应用程序，通常需要更独特的感觉。有时候，一个更强的衬线字体，或者一个更杰出的无衬线字体，会达到目的，但是除了这两个类别之外，当然还有更多的选择。

![Georgia and Arial at large and small sizes](img/8556c9fca6fa1f5c237e66bf72375b11.png)

## 手写字体

在活字印刷系统发明之前，所有的文字都必须用手刻、刷或写。手写文本的缺点——尤其是我自己的——是实现字体、对齐和间距的统一会令人沮丧。由于这些挑战，手写文本可能非常难以阅读。然而，手写的奇妙之处在于，它是人类的象征，并赋予它所代表的文本以有形的个性。看看图 4.18 中的文字就知道了。每一行都代表了字体的个性。

手写字体提供了没有人为错误因素的个性。手写字体中的文字和对齐方式将保持一致，如果字体设计得好，间距也应该很好。与任何字体一样，您不能依赖网站访问者安装您选择的手写字体，因此要在 Web 上使用它们，您需要将手写文本转换为图像，或者使用某种替换技术，如 sIFR。

![Handwritten fonts](img/816cfe67edd9c7be555ffda7789180c2.png)

## 固定宽度字体

你可能已经注意到，在大多数字体中，每个字母占据不同的空间。例如，大写字母“W”占据了很大的面积，而字母“l”占据了很小的面积。为了用简单的文字说明这一点，猜猜下面哪个句子中的字符更多。

![Which of the following sentences has more characters?](img/050e253707144207eef6afc3761c74a1.png)

这是一个棘手的问题:它们实际上有相同数量的字符！那么为什么第一句出现的时间比第二句长那么多呢？对这种现象的解释是，大多数字体都是**成比例间隔**。与每种字体的每个字符相关联的规则不仅决定字符的宽度，还决定每个字符周围出现的空间量。再看一下这两个句子，这一次，以 Courier 字体显示:

![Those two sentences again](img/6c70b49d81760bd5014c270110037a31.png)

这两个句子现在看起来宽度相同的原因是 Courier 是一种**固定宽度的**或**等宽的**字体。这类字体有统一的间距，字体的设计使它们的宽度相似。固定宽度字体最初是围绕打字机的技术限制而设计的。因为早期的打字机不能在打“w”而不是“I”时将打印页移动不同的距离，所以为这些设备开发了专门的字体。这些字体必须保持可读性，尽管事实上每个字母的间距都是一样的。早期的计算机显示器也采用了固定宽度的字体(现在仍有许多这样做——见图 4.19 中的终端窗口显示),但没过多久，计算机就能够显示更加清晰的可变宽度(或比例)字体了。

那么，为什么固定宽度字体今天仍然存在呢？主要是为了程序员和会计的理智。当这些专业人员编写代码或将表格数据显示为文本时，字符逐行、逐列对齐是非常重要的。如果你正在读这本书，你可能已经熟悉了固定宽度的字体。正如我们已经看到的，它们被用于终端窗口，以及文本编辑器和计算器。

![A terminal window using fixed-width fonts](img/322ee58c1bceeedb3b7da14a00299aa6.png)

在 Web 上，以固定宽度的字体显示文本的标准方式是用`<pre>`和`</pre>`标签将其包装起来。`pre`是预格式化文本的缩写，除了显示固定宽度的字符，`pre`元素还保留了制表符、空格和换行符。这通常使得在网站上显示代码或表格数据就像从源代码中剪切和粘贴一样简单。我说通常是因为存在于预格式化文本框中的 HTML 标签是正常呈现的，所以如果你试图在你的代码中包含任何标签，你需要用 HTML 字符代码等效物`&lt;`替换任何`<`，用`&gt;`替换任何`>`。和其他 HTML 元素一样，`pre`可以用 CSS 来设计样式。通常，计划在页面上显示代码的 web 开发人员希望代码从常规文本中脱颖而出。使用 CSS，`<pre>`标签可以被赋予边框、背景处理、额外的空白或不同的文本处理，以帮助它突出。

![Energy BBS ASCII art by Carsten Cumbrowski](img/f7fda884739369fbe4251f8bdc5f2a89.png)

固定宽度字体的另一个有趣但过时的用途是在 ASCII 艺术的创作中。 **ASCII** (美国信息交换标准代码)是通信设备的原始英语字符编码之一，几年来，这个 7 位系统中的 95 个可打印字符是唯一出现在显示器上的图形。在互联网出现在军事和学术界之外之前，就有拨号公告板系统(BBSs)网络，其中许多以 ASCII 字符显示菜单和游戏图形。我在 BBS 时代的巅峰时期长大，喜欢看人们只用固定宽度的字体就能创造出“地下”图形。

虽然使用计算机程序可以从图像中创建更复杂的 ASCII 艺术，但在 20 世纪 80 年代末和 90 年代初创建的 ASCII 艺术是一个字符一个字符地组成的，并真正推动了媒体的极限。这种类型的艺术作品是计算机图形学历史上经常被忽略的一环。

## 新颖的字体

**新奇字体**，也被称为**展示**或**装饰**字体，代表了绝大多数可以在网上免费获得的字体。这一类别中的一些字体，例如图 4.21 中的字体，是流行的衬线字体或无衬线字体的修改版本，还有一些是完全另类的想法，与其说是字体，不如说是概念艺术。就其本质而言，这些字体不如传统字体清晰易读，但如果使用得当，它们可以为设计增添丰富的个性和才华。

![Five examples of novelty fonts](img/71001f1f64dd228e27225b74cde19ca3.png)

新颖的字体通常是标志设计和装饰性字体元素的良好开端。如果你看一下图 4.22 中斯堪的纳维亚设计公司 [Guilago](http://www.guilago.se/) 的网站截图，你会注意到两种不同的新颖字体。这些字体被赋予了一些边框和透视处理，形成了公司的 logo。

和所有的设计选择一样，在你使用一种新颖的字体之前，你应该考虑你的客户的需求和目标受众。大多数客户已经有了某种形式的品牌，选择一种奇怪或另类的新奇字体可能会损害公司的形象。即便如此，当你为网站设计主题时，最好保持开放的心态。可能是你正在工作的公司想要偏离它的公司形象。也许你的客户想要创造一些更“个性化”的东西惠普似乎就是这种情况，考虑到该公司在其[“电脑又是个人的了”广告活动](http://www.hp.com/personalagain/)中使用的字体，如图 4.23 所示。

![Novelty fonts were used to create the logo for Guilago](img/5c3dc9f3e713694bf081a21aa1eda42a.png)

![HP - The Computer is Personal Again](img/bd23344a79099245a299c514331a8fd6.png)

在惠普宣传活动中，创造性的、定制设计的新颖字体的使用给广告带来了非常独特的感觉。我将这种古怪的字体描述为一部分是大学信笺上的涂鸦，一部分是圣诞节前的噩梦，这无疑证实了这场运动的个人主题。

## Dingbat Fonts

当你在寻找可以融入网站设计的插图和艺术品时，一个不应该被忽视的资源是**丁巴**或**符号**字体。在印刷术的早期，丁字是用来分隔印刷文字和填充空白的装饰性字符。最初的丁巴特字体主要由花饰和常用符号组成。然而，随着数字字体革命，丁巴特字体的概念发生了根本的变化。现在，任何图形系列都可以被指定为丁巴特字体中的字符。

虽然从排版的角度来看，这些字体可能不值得使用，但作为辅助图形和图标，它们还是很有用的。由于字体由可缩放的矢量形状组成，因此可以将丁巴特字形设定为任何大小，而不会变得模糊。唯一的问题是，要使用这些字体，您必须知道在哪里可以找到您想要的字形。偶尔，我会记住一个丁巴特字体的箭头或符号，并在找到我想要的之前打出一半的字母表。不过，幸运的是，大多数 dingbat 字体都有一个主题，所以很容易记住字形是哪种字体，即使很难找到特定的字符。

当人们想到 dingbats 时，首先想到的是 Wingdings 和 Webdings，即预装在 Windows 中的 dingbats 字体。事实上，网上还有数百种其他的丁贝字体。图 4.24 给出了几个例子。

![A few examples of free dingbat fonts](img/0fa44309d83a03555a638bf982cb114c.png)

## 查找字体

我有几次提到你现在可以在网上找到字体，但是我没有给你任何资源！既然我已经解释了所有六种基本字体类别，我想是时候告诉你在哪里可以找到一些新的字体来添加到你的排版工具带中了。

## 免费和共享字体库

这些网站列出并分类了来自许多不同设计师的数千种免费和共享软件字体。这些图库中列出的一些设计师有他们自己的网站，通过这些网站他们出售他们设计的其他字体。如果你喜欢特定设计师创作的字体，一定要找到他们的其他作品。许多网站声称提供免费字体，但在我看来，这是三个最好的资源:

*   1001 字体，在[http://www.1001fonts.com/](http://www.1001fonts.com/)
*   达丰，在[http://www.dafont.com/](http://www.dafont.com/)
*   想要的字体，在[http://www.wantedfonts.com/](http://www.wantedfonts.com/)

## 待售字体

像上面提到的免费和共享库一样，这些网站推广来自许多不同设计师和铸造厂的字体。但与这些图库不同的是，这些网站都不免费提供字体。从这些公司之一购买字体系列的好处是，你不仅有一套完整的字符，而且购买的字体通常包括粗体、斜体、斜体和其他变体。

*   位于[http://www.fontshop.com/](http://www.fontshop.com/)的 FontShop
*   爱克发 Monotype，在[http://www.fonts.com/](http://www.fonts.com/)
*   转向，在[http://www.veer.com/products/type/](http://www.veer.com/products/type/)
*   我的丰姿，在[http://www.myfonts.com/](http://www.myfonts.com/)
*   国际字体公司，在 http://www.itcfonts.com/

## 个体艺术家和铸造厂

我最喜欢的许多当代字体来自少数艺术家和公司。这些网站中的大多数都有一些免费的字体，同时也提供一些出售的字体:

Brian Kent 的 AEnigma 字体，在 http://www.aenigmafonts.com/的 T1，T2 的 T3，T4 的 Brian 已经开发了数百种很棒的免费字体。我希望他的网站更容易浏览，但它仍然是一个很好的资源。

Brian J. Bonislawsky 教授的散光单眼印刷学院，在[http://www.astigmatic.com/](http://www.astigmatic.com/)
Bonislawsky 教授在每一个可以想象的类别中创造了许多了不起的字体。

Jess Latham 设计的蓝色乙烯基字体，在[http://www.bvfonts.com/](http://www.bvfonts.com/)
像许多字体设计师一样，Jess 开始把设计字体作为一种爱好。他的免费软件和付费字体是独一无二的，做得非常好。

彼得·布鲁恩的《喷泉》，在 http://www.fountain.nu/
[喷泉展示了来自世界各地大约 20 位不同设计师的一些最好的字体。该网站还提供非常好的免费字体。](http://www.fountain.nu/)

Ray Larabie 设计的 Larabie 字体，at[http://www.larabiefonts.com/](http://www.larabiefonts.com/)
Ray 是免费字体领域的摇滚明星。他的作品以大字符集和高质量而闻名。

爱德华多·累西腓(Eduardo Recife)在[http://www.misprintedtype.com/](http://www.misprintedtype.com/)
印刷错误爱德华多是一个饱经风霜、饱经风霜、不拘一格的字体面孔。他的作品无疑是独一无二的，也有些扭曲。

雅各布·菲舍尔在[http://www.pizzadude.dk/](http://www.pizzadude.dk/)
做的比萨，雅各布有一种公认的傻傻的、悠闲的风格，但自 1998 年以来已经制作了 500 多种手工字体。

## 选择正确的字体

即使你了解字体和字样类别的所有技术方面，并且可以使用世界上所有的字体，你仍然很难选择正确的字体。这是因为字体的选择在很大程度上是基于艺术许可和情感联想，就像它是基于技术问题一样。那么，我们从哪里开始？

为了开始你对完美字体的探索，你应该首先定义你试图在你的目标受众中唤起的感觉。你是想展示这个网站所代表的公司是时髦而年轻的，还是想描绘出一种坚定的智慧？你想创造一些他们自己的东西吗，比如夏威夷式宴会或墨西哥嘉年华，或者你想传达一种更正式的身份？通过问自己这些问题，并在情感层面上思考字体，你应该能够相当容易地决定给定的字体是否适合你的应用程序。如果你认为你不能回答这些关于特定字体的问题，那就自己编问题。事实是，在你的一生中，你可能已经看到了数十亿的字母和数百万的单词——你只是感觉到了一些情感联系，你可以在此基础上选择你的字体。回想一下你见过的商标、专辑封面、教科书和标牌。这些印刷元素如何影响你对它们所代表的实体的认知？

现在，让我们带着这个想法逆向工作，使用一个通用的实体，比如乔的餐馆。你为这个设计选择的字体将在潜在的用餐者对餐馆的态度和身份的认知中扮演重要的角色。看一下图 4.25，试着选择一些让你想到一家休闲的意大利小酒馆的字体。好了，现在选择字体，暗示一家提供五星级美食的大都市餐厅。一个俗气的码头酒吧怎么样？这些情况都没有正确的答案，但肯定有一些字体在每种情况下都不适用。首先，试着把范围缩小到几个好的候选人，然后试着再次精炼你的选择，直到你找到一个很好的。

![20 different fonts to make you want to eat at Joe's](img/4ea87e566b846c9dc154107b64b2d0e9.png)

记住，没有不好的字体，只有不合适的字体。虽然一种特定的字体可能不能用于一个目的，但这并不意味着它不能用于另一个目的。试着保持开放的心态，如果你能把范围缩小到几种可能性，试着问一个朋友或同事这样的问题“哪一个让你感觉更形容词化？”用你想要引起的感觉代替形容词。

最后，当你选择字体时，重要的是不要选择太多。根据经验，在网站设计中尽量不要使用超过四种不同的字体。此外，尽量避免在同一个项目中组合两种不同的衬线字体或两种不同的无衬线字体。

## 设置文本大小

文本的大小一直以来都是一个令人困惑的话题。在法国活字印刷术创始人佩雷·塞巴斯蒂安·特鲁切特介绍活字印刷术之前，活字印刷术的历史已经过去了 300 多年。尽管从那时起，磅就一直是版式的标准度量单位，但由于英语和法语度量单位之间的差异，这个“标准”单位的确切大小在历史上已经改变了几次。直到数字印刷术的兴起，官方才把点的尺寸定为 1/72 英寸。

![6 different fs](img/429b5223440fe2ca53dff5819b2d98ad.png)

虽然印刷世界中的字体大小是用这个绝对值来衡量的，但网络上的字体大小必须与观看者的显示器分辨率相关。CSS 中，像素(px)最小？最好呢？设置文本大小的相对单位。显示器分辨率是以像素为单位设置的，所有显示图形的尺寸也是如此，因此用像素控制文本大小也是有意义的。那么，为什么大家不用像素来设置网页文字大小呢？嗯，主要是因为 ie 6。大多数浏览器，包括 Internet Explorer，都有一个选项，允许用户改变网站上字体的整体显示大小，如图 4.26 所示。

![Text Size menu in Internet Explorer](img/90408c02c30ffe794c29e2fc54271c03.png)

这种控制为有视觉障碍的人和低分辨率显示器的用户大大提高了网站的可用性。在大多数浏览器中，文本大小控制工作正常，但在 Internet Explorer 6 中，**文本大小**菜单中的设置不会影响以像素为单位设置文本的网页。为什么不呢？因为 IE 的开发者认为像素是一个绝对的单位，不应该调整大小。Internet Explorer 第 7 版延续了拒绝以像素为单位调整文本大小的传统，尽管它确实提供了页面缩放功能，这对于一些用户来说可能是一个不错的选择。即便如此，也不建议以像素为单位设置字体大小。

注:em 又是什么？
*在本章前面关于对齐的讨论中，我给出了 ems 的简要定义。em 是一个相对的测量单位；一个 em 等于一个元素文本的垂直大小。*

设置字体大小的方法有很多，但是因为我考虑的是像素，所以我最喜欢的方法是在`body`元素上设置 10 个像素的字体大小，文档的其余部分使用 em 单位。大多数浏览器的默认字体大小是 16 像素。因为 em 与父元素的字体大小相关，所以一个 em 的默认大小也是 16 像素。因此，如果你希望页面的段落文本以 12 像素显示，你必须将段落的字体大小设置为 0.75em 如果你想要 35 像素的标题，你必须将它们设置为 2.19em。(如果你想知道我是如何得到这些数字的，我将我想要的像素大小除以 16，然后四舍五入到最接近的百分之一 em。)

我不知道你怎么样，但我不会心算十进制除法。我也不喜欢每次我想在 CSS 文件中设置字体大小时不得不拖出我的计算器。下面这个 62.5% `body`字体大小的把戏就这么来了。通过改变`body`元素的字体大小，一个 em 的值实质上变成了十个像素。这使得与使用基于 em 的字体大小相关的数学运算变成了移动小数点的简单问题。在这种情况下，12 个像素等于 1.2 ems，35 个像素等于 3.5 ems:

```
body {          
  font-size: 62.5%;          
}          
p {          
  font-size: 1.2em;          
}          
h1 {          
  font-size:3.5em;          
}
```

这种方法让我拥有了我作为设计师所需要的逐像素精度，让 Internet Explorer 6 用户能够根据需要调整文本大小，并让我与计算器保持安全距离。

## 使用标点符号和特殊字符

当你在任何相当现代的文字处理程序中键入文本时，即使你的键盘按键显示无处不在的 ASCII 双引号符号，当你点击它时，你会看到漂亮的“卷曲”标点符号开始和结束。这些特殊的引号在你的键盘上是找不到的，如图 4.27 中的键所示。但是文字处理程序明白，当你把一些东西放在引号中时，你想要漂亮的左右引号，它会用正确的字符替换你输入的字符。撇号也是如此。你在书或小册子上见过像键盘上那样的 ASCII 撇号吗？当然不是。我们通常在印刷材料中看到的是结束单引号。事实上，存在大量标准键盘上没有的字符，尽管这些字符出现在网页和印刷材料中。

![The single- and double-quote key that you see on an average keyboard](img/effdf651f2c355d9a68cf6667e50665d.png)

现在，对于使用文字处理器的人来说，这一切都很好。但是对于我们这些在 HTML 文档中输入文本的人来说，没有一个系统可以自动地用语法正确的对应字符替换键盘上的字符。根据您的网站使用的字符编码类型，当您将这些字符直接粘贴到 HTML 文档中时，您可能会在呈现的页面上看到一堆乱码。此外，在文本中包含 HTML 使用的字符，如`<`和`>`，会严重破坏您的页面，因为它们会导致 HTML 代码的开始或结束。

由于这些原因，产生了一系列特殊的代码或实体？我们将这些输入到 HTML 文档中，以产生正确的标点符号和我们可能需要的任何特殊字符。表 4.1 中的例子只是现有的许多 HTML 字符代码的一个例子。最左边的代码被称为**实体名**或**关键字**。例如，要在文档中生成版权符号，直接在 HTML 中输入`copy`;您将在呈现的页面中看到一个。这些实体中的每一个也有一个数字等价物；copy 的数字等价物是#169，它产生相同的符号。要获得更完整的代码列表和它们的替代实体编号，请查看 W3Schools 的 HTML 实体页面。

**表 4.1 HTML 字符实体引用示例列表**

| 实体 | 性格；角色；字母 | 描述 |
| < | < | 不到 |
| > | > | 大于 |
| & | & | &的记号名称 |
| ‘ | ' | 左单引号 |
| ’ | ' | 右单引号 |
| “ | " | 左双引号 |
| ” | " | 右双引号 |
| « | Â | 左尖括号 |
| » | Â | 直角引用 |
| ® | Â | 注册商标 |
| ™ | “你知道吗 | 商标 |
| © | Â | 版权 |
| ¢ | Â | 分 |
| £ | Â | 镑 |
| € | “” | 欧洲的 |
| ¥ | Â | 日元 |
| ¼ | -什么 | 四分之一 |
| ½ | â½ | 一半 |
| ¾ | Â | 四分之三 |

本章到此为止！要了解更多关于[漂亮网页设计的原则](https://www.sitepoint.com/books/design1/)，请查看[目录](https://www.sitepoint.com/books/design1/toc.php)和[读者评论](https://www.sitepoint.com/books/usercomment.php?p=design1)。

如果你喜欢读这篇文章，你会爱上[可学的](https://learnable.com/)；向大师们学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和互动在线课程，如 [Learn HTML5](https://learnable.com/courses/learn-html5-201) 。

## 分享这篇文章*