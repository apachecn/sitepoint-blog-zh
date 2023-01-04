# 哈姆勒简介

> 原文：<https://www.sitepoint.com/an-introduction-to-haml/>

Haml 是一种 X**H**TML**A**b 抽象 **M** arkup **L** 语言，根据[haml-lang.com](http://haml-lang.com/):

> 充当内联页面模板系统(如 PHP、ASP 和 ERB)的替代品

在 [Node.js](https://github.com/creationix/haml-js) 、 [PHP](http://phphaml.sourceforge.net/) 和[中，可以在很多 Ruby 框架中使用。网](http://code.google.com/p/nhaml/)。

这篇文章将向你介绍如何把你的 HTML 写成 Haml。因此，它不会包含太多的 Ruby 代码。但是如果你想更深入地了解 Haml 是如何在幕后工作的，请阅读 Xavier Shay 关于 Haml 的优秀代码探索文章:[Haml 入门](https://www.sitepoint.com/code-safari-getting-started-in-haml/)和 [Haml，编译完成](https://www.sitepoint.com/code-safari-haml-compiling-to-completion/)。

## 安装 Haml

要使用 Haml，你首先需要安装它。这应该不会比打开命令行并键入以下内容更难:

```
gem install haml
```

现在，让我们看看如何使用 Haml 编写一些 HTML。

## ！文档类型

默认情况下，Haml *通常*默认为 XHTML 文档类型。我说通常是因为如果你使用 Rails 3，那么 Haml 默认为 HTML5。您可以使用 [`:format`选项](http://haml-lang.com/docs/yardoc/file.HAML_REFERENCE.html#format-option)更改默认的 DOCTYPE，但是，由于本文处理的是独立的 Haml，我们将看看如何在我们的模板中手动编写 DOCTYPE:

*   HTML5 DOCTYPE

    ```
    !!! 5
    ```

    将产生:

    ```
    <!DOCTYPE html>
    ```

*   XHTML 严格文档类型

    ```
    !!! Strict
    ```

    这给了我们:

    ```
    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "https://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">
    ```

*   XML DOCTYPE

    ```
    !!! XML
    ```

    将输出:

    ```
    <?xml version="1.0" encoding="utf-8" ?>
    ```

## 标签和属性

现在我们已经处理了 DOCTYPE，让我们看看如何构造我们的 HTML。我们将开始一个 HTML 文档的基本模板，它在 Haml 中看起来像这样:

```
%html 
  %head 
    %title Our Awesome Haml Template 
  %body 
    Abstracting HTML since 2006
```

当上面的代码被编译后，我们将得到下面的 HTML 代码:

```
<html> 
  <head> 
    <title>Our Awesome Haml Template</title> 
  </head> 
  <body> 
    Abstracting HTML since 2006 </body> </html>
```

你可能已经注意到了:

*   Haml 使用空白和缩进来格式化 HTML。
*   标签以`%`为前缀。结束标签不是必需的。

到目前为止，一切顺利。但是我们需要在页面上添加更多的内容，以及更多的结构和语义:

```
%body 
  #container 
    %header 
      %h1 Our Awesome Haml Template 
    #main Abstracting HTML since 2006 
    %footer 
      %address Ian Oxley
```

这为我们提供了以下 HTML:

```
<body> 
  <div id="container"> 
    <header> 
      <h1>Our Awesome Haml Template</h1> 
    </header> 
    <div id="main"> 
      Abstracting HTML since 2006 
    </div> 
    <footer> 
      <address>Ian Oxley</address> 
    </footer> 
  </div> 
</body>
```

随着更多标签的加入，Haml 如何使用空格和缩进来构建 HTML 的结构将变得更加明显。但是你有没有注意到我们是如何把我们的`<div id="container">`和`<div id="main">`标签放到页面上的呢？

*   Haml 假设您正在输出一个`<div>`标签，除非您另外指定
*   `id`属性的指定方式与你在 CSS 中编写`id`规则的方式相同，即`#container`

您还可以像添加`id`属性一样向元素添加`class`属性:

```
%footer 
  %address 
    .hcard 
      .fn Ian Oxley 
      .adr 
        .locality Newcastle-upon-Tyne 
        .country-name England
```

如果您一直在关注，您可能已经猜到这将编译为:

```
<footer> 
  <address> 
    <div class="hcard"> 
      <div class="fn">Ian Oxley</div> 
      <div class="adr"> 
        <div class="locality">Newcastle-upon-Tyne</div> 
        <div class="country-name">England</div> 
      </div> 
    </div> 
  </address> 
</footer>
```

但是我们需要添加到标记中的其他属性呢？像`lang`、`src`、`rel`、`href`之类的？这很容易做到，使用两种方法之一:

*   您可以使用花括号，并用 Ruby hash 指定您的属性。例如:

    ```
    %img{ :src => "/path/to/image", :alt => "Description of image" }
    ```

*   或者您可以使用圆括号，并使用更类似 HTML 的 foo="bar "方法:

    ```
    %img ( src="/path/to/image", alt="Description of image")
    ```

这两者都将输出以下 HTML:

```
<img src="/path/to/image" alt="Description of image">
```

使用这种方法，我们可以轻松地将 CSS、JavaScript 和任何我们需要的元标签添加到模板中:

```
%meta{ :charset => "utf-8" } 
%link{ :rel => "stylesheet", :href => "/css/master.css" }
```

会给我们:

```
<meta charset="utf-8"> 
<link rel="stylesheet" href="/css/master.css">
```

并且:

```
%script{ :src => "/js/site.js" }
```

会给我们:

```
<script src="/js/site.js"></script>
```

## 评论

Haml 允许您在标记中添加三种类型的注释:

1.  HTML 注释
2.  条件注释
3.  Haml 注释

可以使用正斜杠“/”添加 HTML 注释，但是根据您放置它的位置，将会影响注释中包含的内容:

```
/ A forward slash at the start of a line wraps that line in a comment  
%blockquote  
  %p Roads? Where we're going we don't need roads

/  
  A forward slash at the start of a nested block wraps the whole block in a comment  
  %blockquote  
    %p Roads? Where we're going we don't need roads
```

编译后，我们的输出将是:

```
<!-- Only this line will be wrapped in a comment --> 
<blockquote> 
  <p>Roads? Where we're going we don't need roads</p> 
</blockquote> 

<!-- 
  Now the whole block will be commented out 
  <blockquote> 
    <p>Roads? Where we're going we don't need roads</p> 
  </blockquote> 
-->
```

通过将条件放在“/”后面的方括号中，可以添加条件注释:

```
/[if IE] %link { :rel => "stylesheet", :href => "/css/ie.css" }
```

这为我们提供了以下 HTML:

```
<!--[if IE]> <link href="/css/ie.css" rel="stylesheet"> <![endif]-->
```

Haml 注释是包含在模板文件中但不在最终输出中呈现的注释。您用-#指定它们，适用于 HTML 注释的规则在这里也适用:

```
%p The line below won't appear in the HTML 
-# The rest of this line is a comment 
%p The line above won't appear in the HTML, nor will the lines underneath 
-# 
  None of this nested text will appear 
  in our rendered output either
```

这会产生:

```
<p>The line below won't appear in the HTML</p> 
<p>The line above won't appear in the HTML, nor will the lines underneath</p>
```

## 添加一些 Ruby 代码

虽然这篇文章关注的是如何使用 Haml 编写 HTML，但是如果不简单介绍一下如何将一些 Ruby 代码放入模板中，我们就无法完成这篇文章。

要插入一些 Ruby 代码，只需在代码后面加上`'='`符号。例如:

```
%p= Time.now
```

代码将被评估并输出到包装在`中的标记中`

 `像这样的标签:

```
<p>Sat Aug 06 15:06:09 +0100 2011</p>
```

## 从命令行运行 Haml

试验 Haml 的一个好方法是从命令行运行它。只需将所有的 Haml 代码放在一个文件中，就可以将编译后的模板输出到另一个文件中，如下所示:

```
haml input.html.haml output.html
```

所以，这是一个快速浏览，当你写 HTML 的时候，如何使用 Haml。如果你还没有用过 Haml，希望它能鼓励你尝试一下。别忘了访问位于 http://haml-lang.com 的 Haml 网站，在那里你可以找到更多的信息，一个 T2 的完整的语言参考，甚至可以让 T4 试驾 Haml。

**更新:**我已经把文章中所有的 Haml 片段放到 GitHub 的一个要点中。随意叉开:【https://gist.github.com/1147666】T2

## 分享这篇文章`