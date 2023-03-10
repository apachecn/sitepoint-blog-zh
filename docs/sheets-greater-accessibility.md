# 构建用户控制的样式表以提高可访问性

> 原文：<https://www.sitepoint.com/sheets-greater-accessibility/>

许多关于网页可访问性的讨论都是关于法律上失明的用户，他们需要像 JAWS 这样的文本阅读器。但是像美国盲人基金会这样的组织也支持有严重视力障碍的人。视力受损的访问者可以阅读网页，但可能需要高对比度、大字体的字体。

虽然有 180 万美国人在法律上是盲人，但另有 770 万人有严重的视力障碍，影响了他们的阅读能力。随着人口老龄化，这一数字预计到 2030 年将翻一番。

在本文中，我们将看到如何让访问者通过改变应用于页面的样式来生成页面的大型打印版本。我们使用的方法很简单，不需要对底层 HTML 代码进行大规模的修改。你可以不费吹灰之力提高网站的可访问性。

在这个过程中，我们还将学习三种不同的方法来改变你的样式:通过改变特定的元素 id，通过改变 CSS 选择器，以及通过交换整个样式表。希望这三种方法能够提高您对样式表的了解，并在本文之外有所应用。

##### 模型

AFB 网站为我们想要实现的目标提供了一个很好的例子。[这个页面](http://www.afb.org/myafb.asp)允许访问者设置网站中使用的字体，设置字体大小、颜色和背景对比度。然后，访问者的偏好被存储在一个 cookie 中，并应用于网站中的所有其他页面以及所有未来的访问。

当用户可以使用“视图-文本大小”菜单自己增加浏览器的默认字体大小时，为什么要这么麻烦呢？Internet Explorer 和其他浏览器提供的文本大小选项是有限的，只允许用户将字体大小增加 125%。为了支持有严重视觉障碍的访问者，我们需要更大的字体和增加对比度的能力。

AFB 网站使用动态服务器页面，因此通过服务器端代码更改页面样式。我们的方法将使用 JavaScript 和动态 HTML 来做同样的事情。

AFB 网站还为用户提供了几种字体大小和颜色选择。为了简单起见，我们将在本文中提供一个备选方案。但是，您可以轻松地扩展这里描述的方法，为您的访问者提供多种选择。

##### 我们的测试页面

首先，让我们创建一个我们想要改变其样式的简单页面:

```
<div id="test"> 

<h2>Headline</h2> 

<p>This is the content section of our document.</p> 

</div> 

<p><a href="javascript:;" onClick="changeStyles();">Change styles</a>
```

这个简单的页面定义了一个标题和一段文本，并在它们周围包装了一个 DIV 元素，使页面内容更容易用 JavaScript 操作。

它还定义了一个“更改样式”链接，该链接执行一个 JavaScript 函数来更改样式。我们有三种方法可以编写`changeStyles( )`函数:

1.  通过 ID 更改特定页面元素的样式
2.  通过 CSS 选择器改变所有元素的样式
3.  更改分配给我们页面的样式表

这些方法各有优缺点。

***通过 ID 改变元素***

更改单个页面元素的样式是一个简单的过程。我们首先访问与元素对应的 JavaScript 对象，然后更改各个属性的值。

我们的测试页面在页面内容周围包装了一个 DIV 元素，因此我们可以使用该元素的 ID 来查找适当的对象:

```
 1: <script language="javascript"> 

      2: ns4 = document.layers; 

      3: ie = document.all; 

      4: ns6 = document.getElementById && !document.all; 

      5: 

      6: function changeStyles (id) { 

      7:    if (ns4) { 

      8:       alert ("Sorry, but NS4 does not allow font changes."); 

      9:       return false; 

     10:    } 

     11:    else if (ie) { 

     12:       obj = document.all[id]; 

     13:    } 

     14:    else if (ns6) { 

     15:       obj = document.getElementById(id); 

     16:    } 

     17:    if (!obj) { 

     18:       alert("unrecognized ID"); 

     19:       return false; 

     20:    } 

     21: 

     22:    obj.style.color = "yellow"; 

     23:    obj.style.backgroundColor = "black"; 

     24:    obj.style.fontSize = "300%"; 

     25: 

     26:    return true; 

     27: } 

     28: 

     29: </script>
```

第 2-4 行确定使用的是哪个版本的 DOM，并设置一系列变量来存储这些信息。第 7-20 行使用适合浏览器的方法将 JavaScript 对象加载到一个名为 obj 的变量中。

注意，第 7-10 行只是发布了一个警告。这是必要的，因为 Netscape 版本 4 和更早的版本不允许我们在页面加载后改变字体样式。

第 22-24 行是我们脚本的核心，将文本颜色改为黄色，背景颜色改为黑色，并将字体大小增加 300%。

最后，请注意，`changeStyles( )`函数的这个实现将 id 作为输入参数，指示要更改其样式的元素的 ID。这需要我们改变调用函数的方式，如下所示。

```
<p><a href="javascript:;" onClick="changeStyles('test');">Change   

styles</a>

The advantage of this approach is that it's fairly straightforward. If you are familiar with Dynamic HTML, the method is easy to follow.  
但是这种方法也很脆弱。在实践中，将 DIV 元素包装在整个页面内容周围有点笨拙。更严重的是，在 Internet Explorer 中使用 DIV 元素会带来继承问题，其中包含页面标题的 H2 标签不会继承分配给 DIV 的字体大小。

因此，当我们只想更改页面中选定区域的样式时，这种方法最好在有限的范围内使用。

 ***通过选择器改变元素*** 

让用户控制文本显示的一个更灵活的方法是让他们在选择器级别改变样式。CSS 选择器是样式规则的左边部分——定义应用规则的 HTML 元素的部分。例如，P 标记是 CSS 规则中的选择器，如下所示。

```
P   { font-size: 12pt; font-family: Arial }
```

如果我们改变与任何给定选择器相关的样式，这将不费吹灰之力地改变我们文档的大部分。
下面的代码显示了为此而重写的脚本。

```
 1: <script>  

      2: ns4 = document.layers;  

      3: ie  = document.all;  

      4: ns6 = document.getElementById && !document.all;  

      5:  

      6: function changeStyle (selector) {  

      7:    if (ns4) {  

      8:       alert ("Sorry, but NS4 does not allow font changes.");  

      9:       return false;  

     10:    }  

     11:    else if (ie) {  

     12:       setNewStyle('P');  

     13:       setNewStyle('H2');  

     14:    }  

     15:    else if (ns6) {  

     16:       alert('Sorry, Netscape does not support this function.');  

     17:    }  

     18:  

     19:    return true;  

     20: }  

     21:  

     22: function setNewStyle(selector) {  

     23:    style = getStyleObj(selector);  

     24:    if (!style) return false;  

     25:  

     26:    style.color = "yellow";  

     27:    style.backgroundColor = "black";  

     28:    style.fontSize = "300%";  

     29: }  

     30:  

     31: function getStyleObj (selector) {  

     32:    for (x=0; x < document.styleSheets.length; x++) {  

     33:       var oStyleSheet = document.styleSheets[x];  

     34:       if (ie4) {  

     35:          for (y=0; y < oStyleSheet.rules.length; y++) {  

     36:             var oRule = oStyleSheet.rules[y];  

     37:             if (oRule.selectorText == selector) {  

     38:                return oRule.style;  

     39:            }  

     40:         }  

     41:       }  

     42:    }  

     43:    return false;  

     44: }  

     45:  

     46: </script> 
```

`changeStyle( )`函数再次基于正在使用的浏览器进行分支。第 12-13 行适用于 Internet Explorer，涉及对一个`setNewStyle( )`函数的两个独立调用:一个调用更改所有 P 元素，另一个调用更改所有 H2 元素。
第 22-29 行定义了`setNewStyle( )`函数。它首先调用另一个名为`getStyleObj( )`的函数来获取对应于选择器输入参数的 JavaScript 对象，然后设置元素的字体大小、颜色和背景颜色。
到目前为止，这个版本的脚本与我们早期的基于 ID 的方法没有太大的不同。然而，我们脚本的核心是第 31-44 行定义的函数`getStyleObj( )`。这段代码搜索与文档关联的所有样式表，检查每个样式表中定义的样式规则，并测试规则的 selectorText 属性是否与传递给函数的选择器输入参数匹配。该函数返回与我们的选择器标准匹配的第一个规则的样式对象。
注意，只有为给定的选择器定义了一个样式规则，`getStyleObject( )`才会起作用。如果不存在规则，那么`getStyleObj( )`将返回 false。因此，为了让这段代码在我们的测试页面中正常工作，我们需要添加一个 STYLE 元素来定义 P 和 H2 元素的规则，如下所示。

```
<style>  

P { font-size : 10pt; }  

H2 { font-size : 10pt; }  

</style>
```

至此，您可能已经注意到了这种方法的重大局限性:它只适用于 Internet Explorer。Netscape 版浏览器使用的 Gecko 渲染引擎中的一个错误使 selectorText 属性无法正常工作。这在 Gecko 的最新版本中得到了纠正，但还不足以支持 Netscape 7。
因此，虽然这种方法提供了很大的灵活性，但它可能最适合在 Internet Explorer 是标准浏览器的内部网中使用。

 ***改变样式表*** 

定制文本表示的最后一种方法是允许用户切换到另一个样式表。这取代了整个样式表，而不是改变单个页面元素的样式或特定的选择器。

虽然这种方法不像改变单个选择器那样灵活，但是它很容易设置，并且除了 Netscape Version 4 之外的所有浏览器都支持它。

要应用这种方法，首先创建两个外部样式表:一个默认样式表和一个大的打印样式表。

首先，将下面的代码放在一个名为 default.css 的文件中。

```
P { font-size : 10pt; }   

H2 { font-size : 10pt; }
```

接下来，将下面的代码放在一个名为 accessible.css 的文件中，这将是我们的大型打印样式表。

```
P,H2 { font-size : 200%; color : yellow; background-color : black; }
```

然后，我们将这些样式表与我们的文档联系起来，将这些代码放在页面的 HEAD 部分:

```
<link rel="stylesheet" type="text/css"    

href="default.css" id="default">   

<link rel="stylesheet" type="text/css"    

href="accessible.css" id="accessible">
```

当我们为文档定义两个或更多外部样式表时，我们为浏览器创建了一个潜在的 CSS 冲突，不同的浏览器将以不同的方式解决这个冲突。Internet Explorer 将优先使用列出的第一个样式表来解决这些冲突。基于 Gecko 的浏览器，如 Netscape 和 Mozilla，将倾向于最后列出的样式表。Netscape 还将允许用户通过它的“查看-使用样式”菜单来选择不同的样式表。

因为我们希望我们的页面在不同的浏览器中显示一致，所以我们需要停用 accessible.css 样式表，这样当页面加载时只有一个活动的样式表。为此，我们将以下脚本代码添加到页面的 HEAD 部分。

```
<script>   

ns4 = document.layers;   

if (!ns4) {   

   accessibleSheet = document.getElementById('accessible');   

   accessibleSheet.disabled = true;   

}   

</script>
```

这决定了是否正在使用 Netscape Version 4(在这种情况下，我们什么也不做),如果没有，就停用 accessible.css 样式表。

我们的下一步是编写`changeStyles( )`函数，以便它启用 accessible.css 并禁用 default.css。

```
function changeStyles() {   

   if (ns4) {   

alert ("Sorry, but NS4 does not allow us to change styles.");   

      return false;   

   }   

   else {   

      defaultSheet = document.getElementById('default');   

      defaultSheet.disabled = true;   

      newSheet = document.getElementById('accessible');   

      newSheet.disabled = false;   

   }   

   return true;   

}
```

##### 坚持到底

不管你用什么方法改变样式，你都希望你的改变是持久的。除非我们这样做，否则用户将不得不在网站的每个页面上选择大字体。

我们可以通过设置一个 cookie 来指示用户的风格偏好，从而使我们的更改持久化。Cookie 代码很复杂，因此对于本文，我们将使用从 [WebMonkey](http://hotwired.lycos.com/webmonkey/reference/javascript_code_library/wm_ckie_lib/?tw=reference&category=forms_data) 获得的现成 cookie 库。

下面的代码显示了如何将 cookie 代码与我们的样式表交换脚本集成在一起。支持 cookies 的更改以粗体显示。

```
 1: <html>   

      2: <head>   

      3: <link rel="stylesheet" type="text/css"    

href="default.css" id="default">   

      4: <link rel="stylesheet" type="text/css"    

href="accessible.css" id="accessible">   

      5: <script language="javascript" src="monkeylib.js"></script>   

      6:   

      7: <script>   

      8: ns4 = document.layers;   

      9:   

     10: if (!ns4) {   

     11:    **if (WM_readCookie('siteStyle') == 'accessible') {**   

     12:       **changeStyles();**   

     13:    **}**   

     14:    **else {**   

     15:       accessibleSheet = document.getElementById('accessible');   

     16:       accessibleSheet.disabled = true;   

     17:    **}**   

     18: }   

     19: </script>   

     20:   

     21: </head>   

     22: <body>   

     23: <script>   

     24:   

     25: function changeStyles () {   

     26:    if (ns4) {   

     27:       alert ("Sorry, NS4 does not let us    

to change styles.");   

     28:       return false;   

     29:    }   

     30:    else {   

     31:       defaultSheet = document.getElementById('default');   

     32:       defaultSheet.disabled = true;   

     33:   

     34:       newSheet = document.getElementById('accessible');   

     35:       newSheet.disabled = false;   

     36:   

     37:       **WM_setCookie('siteStyle','accessible',17520);**   

     38:    }   

     39:   

     40:    return true;   

     41: }   

     42:   

     43: </script>   

     44:   

     45: <h2>Headline</h2>   

     46: <p>This is the content section of our document.</p>   

     47:   

     48: <a href="javascript:;" onClick="changeStyles()">Set large    

     49: fonts</a>
```

第 37 行设置一个名为 siteStyle 的持久 cookie 来存储值“`accessible.`”。第 11-17 行检查这个 cookie 是否存在，如果已经设置为“`accessible`”，就调用`changeStyles( )`函数。

##### 应用它

不管你用什么方法让用户控制页面样式，记住普通的 CSS 优先规则仍然适用。如果您为页面元素定义内联样式，这些样式将覆盖用户选择的样式并破坏可访问性。

话虽如此，这些方法都不需要对底层 HTML 代码进行重大更改。事实上，大部分代码都是剪切粘贴友好的，这使得提高页面的可访问性变得很容易。当你可以做一些事情让你的网站更容易被访问，而不需要花费几个小时的努力，这是一件很棒的事情。

## 分享这篇文章

```