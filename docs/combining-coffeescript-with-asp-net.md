# 将 CoffeeScript 与 ASP.NET 相结合

> 原文：<https://www.sitepoint.com/combining-coffeescript-with-asp-net/>

大家都知道 CoffeeScript 有多酷。如果没有，下面是电梯演讲。CoffeeScript 是一种编译成 JavaScript 的小型语言。无论您是刚开始学习 JavaScript，还是经验丰富的老手，CoffeeScript 都会让您的生活更加轻松。您仍然需要知道如何使用 CSS，但是 CoffeeScript 允许您编写一次 CSS，并在多个地方使用它。这是我很久以来一直想要的东西，现在它在这里。

## 先决条件

这篇文章并不是一个关于如何编写 CoffeeScript 的完整教程，但是它将集中在如何将 CoffeeScript 与 ASP.NET 一起使用。首先，你需要 CoffeeScript 和 Visual Studio 2010。有几种方法可以在您的机器上安装并运行 CoffeeScript。一种方法是将 CoffeeScript 安装为 Node.js 实用程序。然而，安装 Node.js 超出了本文的范围，因为我将重点关注在 Visual Studio 中运行 CoffeeScript。

在 Visual Studio 中运行 CoffeeScript 之前，知道可以将它作为独立的网页运行是很重要的。你所需要做的就是引用 [CoffeeScript JavaScript 文件](http://cdnjs.cloudflare.com/ajax/libs/coffee-script/1.1.2/coffee-script.min.js)。下面显示了一个简单的 CoffeeScript 示例。用这个 [jsFiddle](https://jsfiddle.net/nm9XV/) 可以看到例子的结果。

```
<!doctype html>
<html>
<head>
  <title>Coffee in Browser</title>
  <script type="text/javascript" src="https://ajax.googleapis.com/ajax/libs/jquery/1.4.4/jquery.min.js"></script>
  <script type="text/coffeescript"> $ -> $('body').css 'background-color', '#ff0000' </script>
  <script type="text/javascript" src="http://cdnjs.cloudflare.com/ajax/libs/coffee-script/1.1.2/coffee-script.min.js"></script>
</head>
<body>
  <h1>Hello from CoffeeScript!!</h1>
</body>
</html>
```

## 在 Visual Studio 中使用 CoffeeScript

多亏了 NuGet，有一些优秀的包可以让 CoffeeScript 与 Visual Studio 的集成变得轻松。将 CoffeeScript 开发提升到一个新的水平，有一个伟大的新西兰公司叫做 [Mindscape](http://www.mindscapehq.com/) ，他们有一个产品叫做 [Web Workbench](http://www.mindscapehq.com/products/web-workbench) 。Web Workbench 集成到 Visual Studio 中，当您处理 CoffeeScript 文件时，它为您提供了编译时错误检查和智能感知。有免费版和专业版，需要一次性付费。专业版会在您保存正在处理的文件时缩小 JavaScript。

安装 Web Workbench 后，打开 Visual Studio 并启动一个新的网站项目。打开*新文件对话框*，将显示新的 CoffeeScript 模板，如下所示。

![](img/06e12612ca3375bd3aaa52319c4dbf49.png)

添加一个名为 coffeedemo.coffee 的. coffee 文件。现在不要担心语法。我将在接下来的系列文章中讨论这个问题。

```
display = -> alert 'I am running from CoffeeScript!' window.onload = display()
```

接下来，保存文件。您将看到两个新的 JavaScript 文件 coffeedemo.js 和 coffeedemo.min.js 被添加到您的项目中，如下图所示。每次保存 CoffeeScript 文件时，Web Workbench 都会编译并创建这些缩小和美化的版本。

![](img/cdd3a2257b7419cf8fdcf99760b2340a.png)

最后一步是在 web 页面中添加对 JavaScript 文件的引用。为此，添加以下脚本标记。如果您现在运行这个项目，您会看到一个对话框说“我正在从 CoffeeScript 运行！”

```
<script type="text/javascript" src="scripts/coffeedemo.min.js"></script>
```

## 结论

CoffeeScript 是一种很好的学习语言。学习一种编译成 JavaScript 的新语言令人耳目一新，老实说，这种语言可能比我编写的 JavaScript 更好。在接下来的几周里，我将重点关注一些关于 CoffeeScript 语法的文章。

## 分享这篇文章