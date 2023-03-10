# 学习 HTML 和 CSS:绝对初学者指南

> 原文：<https://www.sitepoint.com/html-css-beginners-guide-3/>

## 第二章。你的第一个网页

一位智者曾经说过，千里之行始于足下。在这一章中，你将在你的网站启蒙之旅中迈出隐喻的第一步:你将创建你的第一个网页。到本章结束时，你已经复制了第一页，形成了一个多页网站的开端。

## 很高兴见到你，XHTML

在这本书的序言中，我们简要地谈到了 XHTML 是什么。在本章中，我们将学习 XHTML 的基础知识，定期在浏览器中预览我们的进度，并逐步积累我们对各种 XHTML 元素的知识(元素是 XHTML 的基本构建块)。元素告诉 web 浏览器页面中的特定项目是什么:段落、标题、引用等等。这些元素包含了浏览器需要的所有信息，我们很快就会看到。

## 网页剖析

在序言中，我们说过学习 XHTML 就像上驾驶课。更进一步，把一个网页想象成你正在学习驾驶的汽车。有些东西是开车过程中必不可少的；其他的仅仅是时尚物品。

要驾驶汽车，你需要有轮子(包括方向盘)和一个坐的地方。汽车还必须有某种可以固定车身的底盘。汽车需要一台发动机来提供动力，车身也是如此，你的(不重要，但漂亮的)装饰可以安装在车身上。少了任何东西，你所拥有的只是一堆有吸引力的东西——但毫无用处！—备件。

就像汽车一样，你的网页也需要一个底盘:一个基本的结构，其他的一切都可以建立在这个基础上。但是这个假想的底盘是什么样子的呢？最好的方法是卷起袖子，形象地说，然后仔细看看在外表特征下发生了什么。

## 查看信号源

学习构建网页的一大好处是，我们都有能力查看他人网页的源代码。通过简单地看一眼别人的网页是如何构建的，你可以学到很多东西…但是你怎么做呢？

虽然每个浏览器使用的术语略有不同，但不同浏览器让我们查看网页代码的方式差异很小，因此不需要为每个浏览器详细说明这个过程。以下是在 IE 中查看网页源代码的技巧:

*   在浏览器中打开一个页面，例如 Web 标准项目的主页。Web 标准项目(WaSP)是一个宣传正确构建网站的好处的组织，所以你可以很有信心他们已经做对了。
*   将光标放在页面上的某个位置(而不是图像上)，然后右键单击(在 Mac 上按住 Ctrl 键单击)。您应该会看到一个类似如下所示的上下文菜单。

![Selecting the View Source command after right-clicking on a web page (as seen in Internet Explorer a](img/ea35101eca40404aaa9e25a9c2e2a897.png)

*   选择**查看源代码**(或者对于 Firefox 选择**查看页面源代码**)，一个新窗口将会出现，显示页面的所有底层标记。

在这一点上，我们不打算分析你正在看的标记，但这是从一开始就知道的真正有用的技巧之一。

### 注意:小心你信任的人！

大多数网页不使用最佳实践技术，所以避免查看页面的来源，除非本书中提到的有问题的网站是一个很好的例子。

## 网页的基本要求

正如我们已经讨论过的，在任何网页中都有一些基本的必备品。如果您浏览一下刚才尝试查看源代码时出现的标记，就会看到所有这些内容:

*   a doctype
*   一个标签
*   标签
*   `<title>`标签
*   标签

这些需求构成了网页的基本框架。这是你的汽车底盘，有一些未上漆的车身，但没有轮子或座位。汽车发烧友会称之为一个项目——一个坚实的基础，需要一点额外的工作才能变成有用的东西。网页也是如此。当这些需求被组合在一个基本的网页中时，它们看起来是这样的:

" https://www . w3 . org/TR/XHTML 1/DTD/XHTML 1-strict . DTD ">
<html>
<head>
<title>无标题文档</title>
<meta http-equiv = " Content-Type "
Content = " text/html；charset = utf-8 "/>
</head>
<body>
</body>
</html>

那些有鹰眼的人可能也发现了上面标记中的`<meta>`标签。我知道我还没有提到这一点；我们很快就会知道的。尽管< meta >标签不是 web 页面框架需求的一部分，但它有许多有用的用途，尤其是提供关于 web 页面的支持信息。

上面的标记是你在这里看到的最基本的网页。它实际上不包含任何有价值的内容(至少对于在浏览器中查看它的人来说是这样)，但是理解这个标记的含义是至关重要的。让我们再深入一点。

**Go to page:** [1](https://sitepoint.com/html-css-beginners-guide) | [2](https://sitepoint.com/html-css-beginners-guide-2/) | [3](https://sitepoint.com/html-css-beginners-guide-3/) | [4](https://sitepoint.com/html-css-beginners-guide-4/) | [5](https://sitepoint.com/html-css-beginners-guide-5/) | [6](https://sitepoint.com/html-css-beginners-guide-6/) | [7](https://sitepoint.com/html-css-beginners-guide-7/) | [8](https://sitepoint.com/html-css-beginners-guide-8/) | [9](https://sitepoint.com/html-css-beginners-guide-9/) | [10](https://sitepoint.com/html-css-beginners-guide-10/) | [11](https://sitepoint.com/html-css-beginners-guide-11/) | [12](https://sitepoint.com/html-css-beginners-guide-12/) | [13](https://sitepoint.com/html-css-beginners-guide-13/) | [14](https://sitepoint.com/html-css-beginners-guide-14/) | [15](https://sitepoint.com/html-css-beginners-guide-15/) | [16](https://sitepoint.com/html-css-beginners-guide-16/) | [17](https://sitepoint.com/html-css-beginners-guide-17/) | [18](https://sitepoint.com/html-css-beginners-guide-18/) | [19](https://sitepoint.com/html-css-beginners-guide-19/)

## 分享这篇文章