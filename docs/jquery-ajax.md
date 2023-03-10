# 带有 Ajax 和 PHP 基础的 jQuery

> 原文：<https://www.sitepoint.com/jquery-ajax/>

Ajax 提供了一种很好的界面处理方式，不再需要等待整个页面的加载。jQuery 有一套工具来简化应用。本指南将帮助你了解如何使用 jQuery 连接 PHP。

## **阿贾克斯解释道**

异步 JavaScript 和 XML (AJAX)是一个非常简单但有用的服务器端脚本。众所周知，它可以部分刷新页面，你不必刷新整个网页就可以看到其中一小部分的更新。现在很多网站都在用这个。

## **我们的目标是建立什么**

事实上，我们可以构建许多东西，但我们将专注于它的简单性，我们将构建基本的东西。

我们将创建一个文本字段，您可以在其中键入任何内容，它将通过 jQuery 发送到服务器。服务器将追加文本的一部分，并将其发送回 jQuery。

## **开始**

这段 HTML 代码非常简单。我们所需要的只是一个输入文本和一个带有 id 的 div，这样 jQuery 就可以在从服务器返回时输入文本。我们还需要 jQuery 库。

```
 <title>Ajax With Jquery</title>

<label for="txtValue">Enter a value :</label> 
<input type="text" value="" id="txtValue"> 

 **PHP:** 

```
"This is returned from PHP : ".$value));
?>
```

## 分享这篇文章

```