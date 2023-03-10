# 如何在 15 分钟内定制一个 WordPress 模板

> 原文：<https://www.sitepoint.com/how-to-tailor-a-wordpress-template-in-15-minutes/>

你知道我最喜欢的 WordPress 开发项目是什么吗？我可以从零开始建立自己的主题。我真的不喜欢不得不跳到一个先前修改过的主题，并试图弄清楚正在发生什么。

因此，让我向你展示我自己的快速修复皮肤的 WordPress 主题。我将介绍一些您可能会遇到的明显的调整，并向您展示如何快速加入战斗。

### 儿童主题或半身像

我在其他帖子中广泛地讨论了创建子主题的问题，但是这里有一个不太好的版本。在你的主题目录下创建一个文件夹，将下面的 CSS 保存为 style.css 并放入文件夹中:

[sourcecode language="css"]

/*
主题名称:二十二个孩子
模板:二十二个
*/

@导入 url("../twenty twentire/style . CSS ")；

[/sourcecode]

这个例子使用了 WordPress 中默认的 Twenty 主题，并把它变成了你自己的主题。如果您有自己的主题，只需替换模板标签和导入语句中的主题文件夹名称。

现在，进入“外观”>“主题”并选择你的孩子主题。这是最安全的构建代码的方式，坦白地说，在一张白纸上工作会少很多干扰。

### 步骤 2:修改标题图像

在 WP 3.5+和一些更老的主题中，你通常可以通过“外观”>“标题”或“外观”>“主题选项”对话框来上传网站标题。如果你的主题有这个，那么使用它比硬编码一个图形更好。

但是，对于那些需要硬编码图像的骑警来说，你通常可以在 header.php 文件中找到你要找的东西。2012 主题有一个关于如何膨胀一些简单代码的非常复杂的例子。所以，如果你能完成这些代码，你应该没问题:

[sourcecode language="php"]

如果(！空($header_image)):？>
< a href=" <？PHP echo ESC _ URL(home _ URL('/')；？> " > < img src=" <？PHP echo ESC _ URL($ header _ image)；？>" class = " header-image " width = "<？PHP echo get _ custom _ header()->width；？> " height=" <？PHP echo get _ custom _ header()->height；？>" alt = " "/></a>
<？php endif？>
[/源代码]

您可以将其修改为:

[sourcecode language="php"]

[![](http://www.yoursite.com/some-folder/image.jpg)](http://www.yoursite.com)

[/sourcecode]

根据需要调整宽度和高度。因为您有一个 header-image 类，所以您可以在其中添加一些额外的内容，如边框、最大宽度等。

### 步骤 3:导入 Web 字体

赋予网站个人风格的一个非常好的方法是使用独特的标题字体。我是 Google.com/fonts 选择的忠实粉丝，它们非常容易实现。

一个简单的例子是我最喜欢的字体之一——Simonetta。它有四种风格，所以你可以从中获得很多优势。如果您想要导入所有四个样式，您应该将它放在主题的 style.css 文件中。

注意:如果你有一个子主题，确保这个导入代码在你导入父主题的 style.css 之后

 *[sourcecode language="css"]

@导入 URL(http://fonts . Google APIs . com/CSS？family=Simonetta:400，900，400italic，900 italic)；

[/sourcecode]

现在，您可以为您的任何样式声明这种字体——从段落到标题，甚至是特定的类。要使这个网络字体适用于整个站点的 H1 标签，只需将它添加到 style.css 文件中:

[sourcecode language="css"]

h1 {
字体系列:' Simonetta '，草书；
字号:30px
}
[/源代码]

### 第四步:颜色，颜色，颜色！

另一个让网站有自己的外观的简单方法是改变配色方案。虽然你会听到各种关于可读性的争论(你应该注意它们)，但我会跳过它们，只说这一点:保持字体和背景的高对比度。句号。也就是说，让我们改变主题中的一些颜色，比如背景颜色和标题字体。

在进入 style.css 文档之前，看看你的主题是否有一个“外观”>“背景”或“外观”>“主题选项”，比如改变颜色的界面。如果您使用内置的背景颜色功能，那么处理这个问题和识别冲突就容易多了。如今大多数主题都有。

如果没有，在你的 CSS 中，你只需要在你的 body 元素中定义它:

[sourcecode language="css"]

正文{
背景色:红色！重要；
}
[/sourcecode]

请注意，我使用了！重要的代码，以覆盖任何其他功能，可能已经阻止我的代码。当处理一个子主题时，这种情况会发生，一个简单的解决方法就是抛出！代码后的重要语句。

你可能会遇到同样的字体颜色(以及其他样式)问题。

[sourcecode language="css"]

h2 {
颜色:红色！重要；
}

[/sourcecode]

现在，如果你看你的网站，没有看到任何变化，这可能是因为特定的标题可能有自己的类。例如，帖子上 20 个 12 H2 的默认设置是将标题包装在“条目标题”类中，该类也是一个链接。因此，您需要在 style.css 文件中对此进行自定义，如下所示:

[sourcecode language="css"]

。参赛作品-标题 a {
颜色:红色！重要；
}

[/sourcecode]

如果您想要更改的元素在某个具有 ID 的容器中，您可能需要像这样声明样式:

[sourcecode language="css"]

#entry-title a {
颜色:红色！重要；
}

[/sourcecode]

请注意，我在声明前使用了“#”符号，而不是句点。这告诉你的主题寻找带有“条目标题”ID 的东西，比如:

[sourcecode language="html"]

## [点标题](#)



[/sourcecode]

### 步骤 5:集成滑块/图像转盘

我想每个 WordPress 开发者都有他们最喜欢的滑块，但我的是 [Revolution 滑块](http://codecanyon.net/item/slider-revolution-responsive-wordpress-plugin/2751380)。它不是免费的，但所有的功能都值几块钱。让幻灯片看起来令人惊叹需要一些工作，但最终结果绝对令人惊叹。

许多高级主题都有一个内置的滑块，但是如果你需要一个免费的东西来开始，请查看 [Meta Slider](http://wordpress.org/plugins/ml-slider/) 。它有几个选项和几个不同的图像传送带内置在一个插件中。

无论你使用什么样的滑块或者转盘系统，将一个放入主页并添加一些图片都会对设计产生巨大的影响，并且只需要几分钟就可以实现。

### 包裹

通过自定义的标题图形、漂亮的字体，以及改变标题和背景等一些主要元素的配色方案，您也可以快速修改主题，为您可能正在使用的任何品牌定制外观。

你多久会被要求设计一个 WordPress 主题？你更喜欢从零开始，还是满足于建立一个子主题，让现有的 WordPress 设计成为你自己的？* 

## *分享这篇文章*