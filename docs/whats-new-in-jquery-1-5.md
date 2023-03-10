# jQuery 1.5 的新特性

> 原文：<https://www.sitepoint.com/whats-new-in-jquery-1-5/>

jQuery 团队今天[宣布](http://blog.jquery.com/2011/01/31/jquery-15-released/)如期发布了(几乎)每个人都喜欢的 JavaScript 库 1.5 版本。

## 埃阿斯

jQuery 1.5 中对代码库的最大改变是 Ajax 模块的完全重写。许多已经完成的工作对普通开发人员来说是透明的，但是有一些重要的部分值得注意。

首先，所有 Ajax 调用(通过`$.ajax()`方法)现在返回一个 [jqXHR](http://api.jquery.com/jQuery.ajax/#jqxhr) 对象，而不是浏览器本地的 XMLHttpRequest。新对象的主要功能是包装 XMLHttpRequest 的浏览器实现，为您提供一个良好的标准化界面。但除此之外，它还有另一个绝妙的锦囊妙计。jqXHR 对象实现了一个名为[延迟对象](http://api.jquery.com/category/deferred-object/)的新接口。延迟对象是一个很酷的构造，它比过去的`$.ajax()`更干净地处理回调队列。您可以向延迟对象添加任意数量的回调，它们都被放在一个整齐的队列中，在 Ajax 调用完成时执行。但是，如果您在调用已经完成之后添加更多的回调，该怎么办呢？没问题。jQuery 知道发生了什么，所以它会根据需要运行回调函数。

这意味着您进行 Ajax 调用，并得到一个 jqXHR 对象。您可以直接链接对该对象的回调，如下所示:

```
 var jqxhr = $.ajax({ url: "example.php" })
    .success(function() { alert("that went well"); })
    .error(function() { alert("ack! an error!"); });
```

您可以向请求中添加任意数量的每种类型的回调。更好的是，因为有了这个延迟的对象，您可以在任何时候向它添加更多的回调，可能是在代码的后面:

```
 jqxhr.error(function() { alert("did you hear me? there was an error!") }); 
```

如果在您分配额外的回调时请求已经返回了一个错误，那么您的额外代码仍然会运行。

这个新功能甚至在 Ajax 领域之外也很有用:因为 Deferred 是一个通用的实用程序对象，所以您可以在任何需要透明管理大量回调的代码中使用它。相当狡猾。关于使用延迟对象的更多信息，jQuery 博客上的公告文章推荐 Eric Hynds 的这篇文章。

Ajax API 的另一个主要新增功能是一组扩展基本功能的方法。插件开发人员会对这些特别感兴趣，但是几乎所有使用基本 Ajax 方法之外的方法的人都可以从进行全面定制的能力中获益。查看 API 文档中的[扩展 Ajax](http://api.jquery.com/extending-ajax/) 页面。

## 表演

jQuery 团队还设法显著提高了该库的几个 DOM 遍历方法的性能，即`prev()`、`next()`和`children()`。改进的范围从巨大到巨大，取决于使用的浏览器。举一个最显著的例子，大多数版本的 Chrome 已经从 jQuery 1.4 的每秒执行一千多点`children()`操作发展到了 jQuery 1.5 的大约 20，000 次。

公告博客帖子有性能比较图，所以如果你喜欢一些赏心悦目的东西(我们都喜欢)，一定要去看看。

## 错误修复

当然，那些只是大的变化。与任何开源库的主要版本一样，jQuery 1.5 包含了大量的错误修复，您可以在这里浏览[。](http://bugs.jquery.com/query?status=closed&resolution=fixed&milestone=1.5&group=resolution&order=priority)

## 开始吧。

所以你有它！又是一年，又是一个 jQuery。查看完整的发行说明，了解新特性的更详细描述，然后启动您选择的编辑器，开始在您的项目中使用它。编码快乐！

## 分享这篇文章