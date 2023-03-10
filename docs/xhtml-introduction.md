# XHTML 简介

> 原文：<https://www.sitepoint.com/xhtml-introduction/>

我们所知道和喜爱的 HTML，由于许多原因变得相当松散和不受控制。如果你在不同的浏览器上勤奋地测试你的网页，却发现你精心制作的杰作在 IE5x 上看起来很棒，但在 Netscape 4x 上却变得难以辨认，那么欢迎加入这个俱乐部。

## 我们能做什么？

好吧，我们可以花所有的时间抱怨浏览器的一致性、专有标签和标准。或者，我们可以采取积极的态度，支持[万维网联盟的](http://www.w3c.org/)XHTML 的第一个建议:XHTML 1.0

这篇文章采用了一种“快速入门”的方法，旨在帮助 HTML 作者提高他们的技能，文章最后提供了更多详细信息的链接。

## XHTML 是什么？

根据 [W3C](https://www.w3.org/MarkUp/) :

<q>“XHTML 1.0 是 HTML 4.01 在 XML 中的重构，结合了 HTML 4 的强大和 XML 的强大。</q>

<q>“XHTML 1.0 是自 1997 年 HTML 4.0 发布以来，对 HTML 的第一次重大变革。它将 XML 的严谨性带到了网页中，并且是 W3C 创建标准的基石，这些标准可以在越来越多的浏览器平台上提供更丰富的网页，这些平台包括手机、电视、汽车、钱包大小的无线通信设备、信息亭和桌面。”</q>

听起来不错吧？然后继续读下去…

## 你从哪里开始？

如果您没有使用 XML 的经验，那么被它吓倒是情有可原的。但是如果你能用 HTML 编码你的页面，你会很高兴地知道学习 XHTML 会非常容易。它还将为您提供一个极好的 XML 入门教程。

本质上，XHTML 只是 HTML 4.01 的一个更严格的版本，当你标记你的页面时，有一些需要注意的事项。

## 三种口味可供选择！

如您所知，可扩展标记语言根本不是标记语言，而是一种通过使用文档类型定义或 DTD 来定义标记语言的方法。XHTML 就是这样一种语言，有三种不同的 dtd 可供选择。

*   [严格](https://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd)–不允许使用所有不推荐使用的标签和属性，例如`<font>`标签。
*   [Transitional](https://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd)——更加宽容，支持所有那些你每天最有可能使用的不推荐但受浏览器支持的标签。
*   [框架集](https://www.w3.org/TR/xhtml1/DTD/xhtml1-frameset.dtd)–与过渡 DTD 完全相同，但用框架属性替换了文档正文。

您可能希望使用过渡性 DTD，因为它为 XML 和 XHTML 的介绍提供了最宽松的环境。

## HTML 和 XHTML 的主要区别

该规范要求您的文档“格式良好”，这意味着您必须特别注意代码的某些方面。以下是你需要注意的要点。

***1。嵌套元素***

首先，你需要整理你对待页面元素的方式。XHTML 不允许不正确的嵌套，所以类似这样:

```
 <b><p>I'd probably have gotten away  with it too if it weren't for 
        you pesky W3C folks</b></p>
```

不会通过 W3C 的[验证服务](http://validator.w3.org/),但是

```
 <p><b>Buffy rules!</b></p>
```

…会很好的。这同样适用于所有的标记标签。

***2。**区分大小写*

在 XHTML 中，标签和它们的属性都是区分大小写的。简单而严格的规则是所有的标签和属性必须用小写字母书写。举个例子，

```
<A HREF="myPage.html">Some page</a>
```

会让你被 XHTML 验证器活活烤死，但是

```
<a href="wellFormed.html">Well formed page</a>
```

会完美运作。

## 结束标签

大多数 HTML 设计者忽略了某些元素的结束标记，例如。如果你不知道

甚至有结束标记，你并不孤单。以下是最有可能抓住你的标签:

```
<th> <tr> <td> <li>
```

## 图像和换行符怎么办？

好问题。这些元素是相似的，都需要一个结束标记。这就是 XML 的工作方式，当然 XHTML 也不例外，即使在 HTML 中没有结束标记。您可以通过在它的开始部分包含结束标记来处理这个问题。这里有一个例子:

```
 <p>XHTML is strict but not really hard</p>  
  <img src="somePic.gif" /><br />  
  <p>See what I mean?</p>  
  <hr />
```

诀窍是在结束标记前留一个空格，以免混淆非 XHTML 浏览器。

## 属性

当您处理属性时，有几件事情您应该知道。第一，所有的属性都必须用“双引号”括起来。

第二，对于那些在 HTML 中没有值的属性，比如`<ul compact>`，你必须指定一个值。是这样做的:

```
<ul compact="compact">
```

其他需要注意的属性包括:

```
ismap="ismap"  
declare="declare"  
nowrap="nowrap"  
compact="compact"  
noshade="noshade"  
checked="checked"
```

## 特殊字符

我不想这么说，但是这是 XHTML 变得有点麻烦的地方。以上大部分只是自律，养成良好的编码习惯，但这里有几个问题需要特别提一下。如果你没有准备好，他们几乎肯定会给你带来麻烦！

1.  XHTML 在处理 CSS 和 JavaScript 中的和&字符时可能会有点问题。XML 浏览器可能会删除您的注释，从而删除您的注释 CSS。使用外部样式表和 JS 脚本以确保万无一失(尽管到目前为止我还没有遇到问题，也没有在我自己的网站上这样做)。

3.  &符号在属性中也是一个问题。作为一个通用的经验法则，只需对&，字符使用相应的 HTML 实体，并确保您的[正确验证您的页面](http://validator.w3.org/)。

## 使用 id 而不是名称

name 属性现在已被弃用，取而代之的是新的首选属性 **id** 。虽然它是受支持的，但是如果你在一个**地图**标签上使用 name，你会得到警告。

## 一个简单的 XHTML 文档

好了，该做的和不该做的都说完了。如果您急于开始，这里有一个简单的 XHTML 文档可以帮助您开始。

```
<?xml version="1.0" encoding="UTF-8"?>  
<!DOCTYPE html PUBLIC  
          "-//W3C//DTD XHTML 1.0 Transitional//EN"  
          "https://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">  
<html xmlns="https://www.w3.org/1999/xhtml" xml:lang="en" lang="en">  
<head>  
<title>XHTML is easier than you thought!</title>  
</head>  
<body>  
<hr />  
<p>As long as you remember the rules and guidelines   
above<br />   
you'll soon be writing well formed documents.  
 No really, you will! </p>  
<hr />  
</body>  
</html>
```

文档顶部对声明的详细解释超出了本“快速入门”指南的预期范围(对大多数设计人员来说也是不必要的)，但是这里有一个简单的版本。

**第 1 行**告诉浏览器我们正在使用 XML 1.0，并将其编码为 8 位 Unicode。

第 2-4 行陈述了我们正在使用的 DTD，在本例中是过渡版本。

**第 5 行**在`<html>`标签中声明了 XHTML 名称空间和语言属性。

现在，您已经准备好开始编写格式良好、符合标准的 XHTML 页面了！你所需要做的就是使用上面的代码作为你的基本模板，并从一开始就养成良好的编码习惯。如果你发现你不能正确地验证你网站上的每一页，那么不要担心，这是一个相当困难的决定。只要你尽可能努力去验证，你就做得很好。

## 更多信息和资源

**官方消息来源**

XHTML 1.0:可扩展超文本标记语言

[W3C HTML 验证服务](http://validator.w3.org/)

[HTML 主页](https://www.w3.org/MarkUp/)

**教程和文章**

Mozilla 开发者网络上的 XHTML

设计师的 XHTML 路线图

[Webmonkey.com: XHTML 概述](http://hotwired.lycos.com/webmonkey/00/50/index2a.html?tw=authoring)

祝你好运！

## 分享这篇文章