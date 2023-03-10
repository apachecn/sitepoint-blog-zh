# 带有 jQuery 的简单 Ajax

> 原文：<https://www.sitepoint.com/ajax-jquery/>

*这篇文章写于 2011 年，至今仍是我们最受欢迎的帖子之一。如果你渴望了解更多关于 jQuery 的知识，你可能会对这篇关于 jQuery 1.9 的文章很感兴趣。*

Ajax 正在改变 web 应用程序，为它们提供了桌面之外前所未闻的响应能力。但是在大肆宣传的背后，Ajax 并没有什么特别之处——HTML、JavaScript 和 XML 都不是什么新东西，在本教程中，我将向您展示如何在 jQuery 的帮助下进一步简化向应用程序添加 Ajax 的过程。

## Ajax 是什么？

您可能以前听说过 Ajax，或者至少使用过基于 Ajax 的应用程序，例如 Gmail。很简单，Ajax 是一种通过 JavaScript 异步处理外部数据的技术，无需重新加载整个页面。SitePoint 为 Ajax 提供了一个很好的介绍。杰西·詹姆斯·加勒特在本文中创造了这个术语。

不幸的是，关于进入 Ajax 世界的实用方法的深入教程很少。更糟糕的是，Ajax 使用的`XMLHttpRequest`类对于初学 web 开发的人来说并不容易使用。幸运的是，许多 JavaScript 库提供了一种更简单的方法。今天我将向您展示 jQuery——其中一个库——如何让您轻松地将 Ajax 添加到您的应用程序中。

## jQuery 是什么？

jQuery 是另一个成熟的 JavaScript 库，它提供了一些其他 JavaScript 库没有的特性。不可否认，它并不像其他一些产品那样轻量级:jQuery 有 19kb，而 moo.fx 只有 3kb。你可以在 JavaScript 库世界杯上阅读更多关于 jQuery 的内容，比较其他几个提供类似功能的 JavaScript 库。

## 假定的知识

要完成本教程，您需要一些基本的 JavaScript 知识。如果你懂一些 C 风格的语言，你很快就会掌握 JavaScript 的窍门。想象一下大括号、函数声明和每行末尾可选的分号(尽管在 jQuery 中它们不是可选的)。如果你热衷于开始学习 JavaScript，请看[这本为程序员设计的优秀、简洁的 JavaScript 教程](http://www.wdvl.com/Authoring/JavaScript/Tutorial/)。此外，因为我们正在讨论 web 应用程序，所以需要 HTML 的基础知识。

## jQuery 101

让我们快速浏览一下 jQuery 的简介。为了能够在您的页面中使用它，您首先需要下载这个库。在撰写本文时，您可以[下载最新版本](http://jquery.com/src/) — 1.8。jQuery 的方法论很简单:找东西，做东西。我们使用 jQuery 函数(别名为`$()`)从文档中选择元素(通过 DOM)。这个方便的函数就像一样，只是它不仅仅支持 id，还支持 CSS 选择器和一些 XPath 选择器；并且，它可以返回一个元素数组，而不是返回一个元素。好吧，也许对`$()`更好的描述是，它就像服用了类固醇的`document.getElementById()`。

然后，我们使用函数对我们的选择执行操作。例如，要追加文本“Hello World！”对于所有带有类`'foo'`的 div，然后将颜色设置为红色，我们将使用以下代码:

```
$("div.foo").append("Hello World!").css("color","red");
```

轻松点。通常，该任务需要两行代码，如下所示:

```
$("div.foo").append("Hello World!"); 
$("div.foo").css("color","red");
```

//

jQuery 的可链接方法允许我们编写比其他 JavaScript 库更紧凑的代码。jQuery 中有些函数不需要对象，因为它们独立工作，许多 Ajax 函数都属于这一类。比如我们很快就会用到的 post 函数，就是通过键入`$.post(parameters)`来调用的。要了解更多 jQuery 函数，请查看 visualjquery.com 的在线文档。

## 示例 1–我们的第一个 Ajax 应用程序

例如，我们将制作一个交互式概念生成器。基本上，这涉及到我们从列表中随机选择两个术语，然后将它们组合起来创建一个短语。在这个练习中，我们将使用 web 2.0 术语(“Mashup”、“Folksonomy”、“Media”等等)，通常我们会从平面文件中获取这些术语。为了避免您下载 JavaScript 中的每一个组合(或至少每一个元素)，我们将在服务器端动态生成它，并使用 jQuery 为客户端获取它。jQuery 与普通的 JavaScript 完美地集成在一起，所以您会发现将它集成到您的代码中是一件很容易的事情。

***【PHP】***

为了简单起见，我们将使用下面的基本代码来创建我们的概念生成器。不要担心它是如何工作的，只要看看它做了什么:它输出一个随机的报价。注意，这段代码不输出 XML，它只输出原始文本:

```
<?php 
header("Cache-Control: no-cache"); 
// Ideally, you'd put these in a text file or a database.  
// Put an entry on each line of 'a.txt' and use $prefixes = file("a.txt"); 
// You can do the same with a separate file for $suffixes. 
$prefixes = array('Mashup','2.0','Tagging','Folksonomy'); 
$suffixes = array('Web','Push','Media','GUI'); 
// This selects a random element of each array on the fly 
echo $prefixes[rand(0,count($prefixes)-1)] . " is the new "  
   . $suffixes[rand(0,count($prefixes)-1)]; 
// Example output: Tagging is the new Media 
?>
```

这里，我使用了`Cache-Control`头响应，因为 Internet Explorer 习惯于缓存具有相同 URL 的页面，即使页面之间的内容不同。显然，这违背了我们脚本的目的——在每次装载时生成一个新的报价。我们可以使用 jQuery 在 URL 中包含一个随机数，然后将其丢弃，但是在服务器端解决这个缓存问题比在客户端更容易。

***客户端代码(HTML)***

 *让我们开始为前端创建 HTML，然后将 Ajax 融入其中。我们在页面上需要的只是一个按钮，用户可以单击它来请求另一个报价，以及一个 div，一旦我们从服务器收到报价，我们就将报价放入其中。我们将使用 jQuery 来选择这个 div 并将引用加载到其中，我们将通过 div 的`id`来引用它。如果我们愿意，我们可以使用 jQuery 在类的帮助下将报价加载到多个元素中，但是现在我们只需要一个 id。让我们将此作为我们的 body 元素的内容:

```
<input type="submit" id="generate" value="Generate!"> 
<div id="quote"></div>
```

我们可以将引号本身放在 div 中。通常，我们会有一个很长的按钮事件`onSubmit`(id 为`'generate'`的输入)。有时，我们会有一个调用 JavaScript 函数的`onSubmit`事件处理器。但是使用 jQuery，我们甚至不需要接触 HTML——我们可以轻松地将行为(事件处理程序)与结构(页面 HTML)分开。

***客户端代码(jQuery)***

 *是时候使用 jQuery 将我们的后端和前端结合在一起了。我前面提到过，我们可以用 jQuery 从 DOM 中选择元素。首先，我们必须选择按钮，并为其分配一个`onClick`事件处理程序。在该事件的代码中，我们可以选择 div 并将脚本内容加载到其中。下面是 click 事件处理程序的语法:

```
$("element expression").click(function(){ 
  // Code goes here 
});
```

您可能已经知道，如果我们要在 CSS 中选择这个元素，`#`会识别出我们是在使用元素的`id`属性进行选择。您可以对 jQuery 使用完全相同的语法。因此，要选择 id 为`'generate'`的按钮(我们在上面已经给出了)，我们可以使用元素表达式`#generate`。另外，请注意，该语法将我们的事件处理程序定义为事件本身中的匿名函数。

如果你想了解更多，Mark Wubben 的 JavaScript 术语页面对匿名函数做了很好的解释。

我们将使用 jQuery 的高级 Ajax 函数之一，`load()`。让我们假设我们的生成器脚本保存为`script.php`。让我们借助`load()`功能将它与我们的客户端集成起来:

```
$("#generate").click(function(){ 
  $("#quote").load("script.php"); 
});
```

就这样:三行代码，我们就有了全功能的 Ajax 随机报价生成器！嗯，差不多了。

JavaScript 的问题在于，不在函数中的代码会在渲染过程中浏览器到达时立即执行，而不是在页面完成渲染后执行。因此，此代码将尝试附加到尚未加载的元素。通常，我们会使用`window.onload`来处理这个问题。然而，这种方法的局限性是一旦所有东西都加载完毕——图像和所有东西——就调用`window.onload`。我们对等待那些图像不感兴趣——它只是我们想要访问的 DOM。

幸运的是，jQuery 有`$(document).ready()`，顾名思义，它在 DOM 准备好被操作时执行。

***完整代码***

下面是完整的代码，包括`$(document).ready`包装器和一些基本的 HTML 和 CSS:

```
<html> 
<head> 
  <title>Ajax with jQuery Example</title> 
  <script type="text/JavaScript" src="jquery.js"></script> 
  <script type="text/JavaScript"> 
  $(document).ready(function(){ 
    $("#generate").click(function(){ 
      $("#quote p").load("script.php"); 
    }); 
  }); 
  </script> 
<style type="text/css"> 
    #wrapper { 
      width: 240px; 
      height: 80px; 
      margin: auto; 
      padding: 10px; 
      margin-top: 10px; 
      border: 1px solid black; 
      text-align: center; 
    } 
  </style> 
</head> 
<body> 
  <div id="wrapper"> 
    <div id="quote"><p> </p></div> 
    <input type="submit" id="generate" value="Generate!"> 
  </div> 
</body> 
</html>
```

这段代码也包含在[这个可下载的 zip 文件](https://www.sitepoint.com/examples/jquery/ajax-jquery-demo.zip)中。记住，这段代码假设 jQuery 库已经保存为与 PHP 脚本和 HTML 前端相同的文件夹中的`jquery.js`。现在您已经熟悉了 jQuery，让我们继续学习更复杂的内容:表单元素和 XML 处理。这才是真正的 Ajax！

**Go to page:** [1](https://sitepoint.com/ajax-jquery) | [2](https://sitepoint.com/ajax-jquery-2/) | [3](https://sitepoint.com/ajax-jquery-3/)**

## **分享这篇文章**