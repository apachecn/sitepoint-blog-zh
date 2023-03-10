# CSS 架构:代码清理的原则

> 原文：<https://www.sitepoint.com/css-architectures-principles-of-code-cleanup/>

大多数情况下，主要(和次要)网站的 CSS 是代码膨胀和冗余的教科书案例，代码行吹嘘不必要的使用限定符元素，不必要的长选择器链中过于具体的选择器，当其他选择器更合适时过度依赖派生选择器，以及不适当或不必要的使用！重要。我们都可以制定一些基本的、可靠的 CSS 编写实践来极大地提高任何组织的样式表的可维护性。然而，最终我们真正应该追求的是构建一个给定站点的样式表，以显著提高可伸缩性。

因此，我们的方法是双重的。首先，我们必须为编写干净有效的 CSS 建立一些基本的实践。第二，我们需要了解并制定日益流行的可伸缩 CSS 架构的核心方法，以将我们网站的样式表提升到另一个水平。

我将在本文和下一篇文章中探讨这两个主题，但是在我们开始使代码可伸缩之前，让我们先做一些 CSS 清理工作，从一些我最喜欢的技巧开始。

## 一些有用的技巧

向其他开发人员学习工作流技巧通常非常有用。以下是我个人最喜欢的几个。

### 目标样式

有一个方法来定位页面上的样式是个好主意。如果你没有使用像 [Internet Explorer 开发者工具栏](http://www.microsoft.com/en-us/download/details.aspx?id=18359)、 [Mozilla 的 Firebug](http://getfirebug.com/) 或 [Chrome 开发者工具](https://developers.google.com/chrome-developer-tools/)这样的工具，这种添加属性轮廓的老派方法可以帮助你更快地看到和使用你想要的元素:

```
.searchform > .searchsubmit {
width: 14%;
height: 25px;
background: transparent url(images/icon_magnify.png) 0 0 no-repeat;
outline: 1px solid red
}
```

使用该属性和值的基本原理是 outline [不会像 border 那样增加元素](https://reference.sitepoint.com/css/outline)的尺寸。甚至红色(或任何其他颜色名称)的使用也很重要。编码时，仅使用十六进制、rgb(a)或 hsl(a)颜色代码。然后，您知道当您看到一个颜色名称时，它只是为了进行故障诊断。请注意，IE8 和更低版本不支持 outline。

### 添加测试样式

测试和故障排除的另一个好方法是[缩进新的试用样式](http://coding.smashingmagazine.com/2008/05/02/improving-code-readability-with-css-styleguides/)。

```
.searchform > .searchsubmit {
width: 14%;
height: 25px;
background: transparent url(images/icon_magnify.png) 0 0 no-repeat;
  margin: -2px 0 0 0;
}
```

有了缩进，你肯定知道样式是临时的，以后很容易扫描和查找。当您决定保留该样式时，就像对其他永久样式一样缩进它。

### 禁用样式

有了添加新样式的好方法，这里有一个快速禁用它们的方法:通过在样式选择器或样式规则前面放置 x-来“X”出它们:

```
.social a {
-moz-transition: opacity 0.3s ease 0s;
x-display: block;
height: 35px;
opacity: 0.4;
}
```

这个方法比注释掉样式更快。它也很容易扫描和查找，如果你后来决定要保留它，你已经有了这种风格。

## CSS 清理和优化指南

既然我们已经掌握了这些技巧，让我们把注意力转向一些编写干净、优化的代码的经验法则——或者当你清理别人的 CSS 意大利面时。我们将从宏观到微观，首先探索如何提高 HTML 的可读性和语义，然后继续探讨如何更好地组织和减少样式声明的数量。

### 宏观优化

关注样式声明本身很容易，但是在创建选择器之前，必须使 HTML 和样式表本身对读者友好。

#### 提供样式表信息并指明结构

对于非常大的样式表，我喜欢使用目录。对于不熟悉给定样式表的开发人员来说，当他们需要找到一组样式时，知道哪些部分是什么或者跳到哪些名称非常有用。

在基础层面，我建议在样式表中插入开发者的信息(姓名等)和最后更新日期。这样，如果对文档中的内容有任何疑问，当前的开发人员知道该问谁。

```
/* stylesheet information for XyZ Corp
File created date: 09.15.2010
Last modified date: 06.04.2012
By: [name]
*/
```

此外，我建议放入一个初步的目录表，以便其他开发人员了解文档的结构和样式的不同部分。

```
/* Table of Contents
- Link Styles
- Other sitewide styles
- Actions
- Layout
- LOGO
- TOP NAV
- NAVBAR
- SUBNAV
*/
…
(later in the document…)
/* =Layout */ (etc.)
```

请注意，在样式表的节名之前包含等号(=)是有意的— [它充当一个标志，并且是一种更容易在文档中搜索的方式](http://stopdesign.com/archive/2005/05/03/css-tip-flags.html%29)。

#### 注释和嵌套

标注和嵌套标记有助于跟踪包含其他元素的元素的开始和结束，这使您能够更快地识别各个部分。

使用标记`<!-- /end #id or .class name -->`或`<!-- / #id or .class name -->`来注释 div 和其他以`<!-- #id or .class name -->`开头的主要布局元素。

嵌套看起来似乎是一个不必要的步骤，但是从视觉角度来看，它是有用的，并且通过清楚地指示视觉级别，还可以更容易地发现问题，如内联元素中的块级元素，以及元素的不正确关闭和重新打开，这些都可能造成重大的布局问题——验证通常无法帮助您轻松找到这些问题。

您可以在下面的示例中看到不同之处:

**在**之前

```
<body>

<div id="pagewrap">

<div id="header">

<h1>Website Title</h1>

<ul id="navigation">

<li><a href="#">Home</a></li>

<li><a href="#">About</a></li>

<li><a href="#">Contact</a></li>

</ul>

<div id="contentwrap">

<div id="maincontent">

<h2>Main Content Title</h2>

<p>Main content, which is so much more important than the secondary content that it makes one teary with emotion.</p>

</div>

<div id="secondarycontent">

<h3>Sidebar Title</h3>

<p>Sidebar content, which is not as important as the primary content (which is why it is in the sidebar)</p>

</div>

<div id="footer">

<p>standard copyright and footer information</p>

</div>

</body>
```

之后

```
<body>

<div id="pagewrap">

  <div id="header">

    <h1>Website Title</h1>

    <ul id="navigation">

    <li><a href="#">Home</a></li>

    <li><a href="#">About</a></li>

    <li><a href="#">Contact</a></li>

    </ul><!-- end #header -->

  <div>

  <div id="contentwrap">

    <div id="maincontent">

    <h2>Main Content Title</h2>

    <p>Main content, which is so much more important than the secondary content that it makes one teary with emotion.</p>

    </div><!-- end #maincontent -->

    <div id="secondarycontent">

    <h3>Sidebar Title</h3>

    <p>Sidebar content, which is not as important as the primary content (which is why it is in the sidebar)</p>

    </div><!-- end #secondarycontent -->

  </div><!-- end #contentwrap -->

  <div id="footer">

    <p>standard copyright and footer information</p>

  </div><!-- end #footer -->

</div><!-- end #pagewrap -->

</body>
```

您可以在“[创建有效的语义标记](http://msdn.microsoft.com/en-us/magazine/ee730276.aspx)”中找到组织样式表的更多信息和技巧

### 微优化

微优化减少了文件大小，加快了页面加载时间，并鼓励最佳实践。这里有一些方法可以在微观层面上改进你的 CSS。

#### 按字母顺序排列规则顺序

将 CSS 声明按字母顺序排列是为干净的代码和更少的问题做准备的好方法。为什么？因为您的样式声明将更容易定位。

**例一:**

```
.login { 
margin-top: 5px; 
line-height: 1.5em; 
padding-left: 5px; 
float: right; 
list-style-type: none; 
width: 80px; 
font-weight: bold; 
border-left: 1px solid #69824d; 
}
```

**例二:**

```
.login { 
border-left: 1px solid #69824d; 
float: right; 
font-weight: bold; 
line-height: 1.5em; 
list-style-type: none; 
margin-top: 5px; 
padding-left: 5px; 
width: 80px; 
}
```

#### 提高速度的效率

选择器的长链元素迫使浏览器不必要地搜索页面的 DOM 来匹配。消除元素限定符和抛弃后代选择器，支持更直接的选择器，有助于加快速度。

预优化:

```
div#wrapper div#maincontent div#sidebar {
background: #fff url(bg.png) repeat-x 0 0;
border: 1px solid #ff0;
font: normal 1.33em/1.33 Georgia, serif;
margin: 10px 20px;
padding: .1em;
}
```

后期优化:

```
#sidebar {
background: #fff url(bg.png) repeat-x 0 0;
border: 1px solid #ff0;
font: normal 1.33em/1.33 Georgia, serif;
margin: 10px 20px;
padding: .1em;
}
```

在“[优化以改善页面加载时间](http://msdn.microsoft.com/en-us/magazine/ff743754.aspx)”上阅读关于这个主题的更多信息

#### 接吻:保持简单和简短

在样式声明和选择器方面，越少越好。对于样式声明，请遵循以下规则:

*   尽可能使用速记属性(使用速记时请记住以下事项:速记属性语法；属性值顺序(如果有)；默认值和必需的属性值)
*   压缩值和单位
*   尽可能避免重复的属性

之前:

```
#sidebar {
background-color: #fff;
background-image: (bg.png);
background-position: 0 0;
background-repeat: repeat-x;
border-width: 1px;
border-style: solid;
border-color: #ffff00;
font-family: Georgia, serif;
font-size: 1.33em;
line-height: 1.33em;
font-weight: normal;
margin: 10px 20px 10px 20px;
padding: .1em;
}
```

之后:

```
#sidebar {
background: #fff url(bg.png) repeat-x 0 0;
border: 1px solid #ff0;
font: normal 1.33em/1.33 Georgia, serif;
margin: 10px 20px;
padding: .1em;
}
```

#### 精简代码

最后，删除多行和缩进也有助于保持你的网站[高效和快速](http://msdn.microsoft.com/en-us/magazine/ff743754.aspx)。在开发 CSS 时，建议使用多行、嵌套和缩进，但是一旦一个站点准备好上线，缩小 CSS 是最好的途径。压缩 CSS 的几个好工具是 [CSS 压缩器](http://www.csscompressor.com/)和 [CSS 驱动](http://www.cssdrive.com/index.php/main/csscompressoradvanced/)。

## 工具可以伸出援手，嗯…之手

在你编码的时候记住这些规则有助于防止你犯错误，这些错误可能会耗费你的时间并使你以后受挫。但是不要认为你必须自己完成所有的工作，因为有一些很好的工具可以帮助你清理代码。我觉得 [CleanCSS](http://www.cleancss.com/) 和 Code Beautifier 值得一查。当您学习集成我描述的一些方法时，使用这些工具进一步清理您的代码。** 

## **分享这篇文章**