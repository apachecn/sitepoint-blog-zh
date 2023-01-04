# jQuery 简写 Ajax 方法介绍

> 原文：<https://www.sitepoint.com/introduction-jquery-shorthand-ajax-methods/>

如果你从未听说过 Ajax 这个术语，请举手。我敢打赌，你们几乎所有人都放下了手臂，靠近他们的身体。Ajax 最初代表异步 JavaScript 和 XML，是最常用的客户端方法之一，有助于创建异步网站和网络应用。

使用原始 JavaScript 执行 Ajax 调用当然是可能的，但是处理代码的所有不同部分可能是一件痛苦的事情。如果你必须支持像 Internet Explorer 6 这样的史前浏览器，那就更是如此。

幸运的是， [jQuery 为我们提供了一套处理这些问题的方法](http://api.jquery.com/jquery.ajax/)，让我们可以专注于我们希望用代码完成的任务。jQuery 提供了一个名为`$.ajax()`的主要方法，它是高度可配置的，可以满足您的任何需求。它还提供了一组[速记方法](http://api.jquery.com/category/ajax/shorthand-methods/)，之所以称为速记，是因为它们只是带有预设配置的`$.ajax()`方法的包装器，每个方法都服务于一个目的。

除了`$.ajax()`方法之外，所有方法都有一个共同的特性:它们不操作一组 DOM 元素，而是直接从 jQuery 对象调用。所以，不要说这样的话:

```
$('p').ajax(...);
```

它选择页面中的所有段落，然后调用`ajax()`方法，我们将编写:

```
$.ajax(...);
```

在本文中，我们将讨论三种最常用的 jQuery 速记方法:`load()`、`$.post()`和`$.get()`。

## `load()`

我们要讨论的第一个方法是`load()`。它使我们能够从服务器加载数据，并将返回的数据(通常是 HTML 代码)放入与选择匹配的元素中。在看到它运行之前，让我们看看它的签名:

```
load(url[, data][, callback])
```

每个参数的含义如下所述:

*   `url`:一个字符串，指定要向其发送请求的资源的 URL
*   `data`:可选字符串，应该是格式良好的查询字符串或必须作为请求参数传递的对象。如果传递一个字符串，请求方法将是 GET，如果传递一个对象，请求方法将是 POST。如果省略此参数，则使用 GET 方法；
*   `callback`:Ajax 请求完成后调用的可选回调函数。这个函数最多接受三个参数:响应文本、请求的状态字符串和 jqXHR 实例。在回调中，上下文(`this`)被设置为集合中的每个元素，一次一个。

这个是不是看起来很难用？我们来看一个具体的例子。

假设您的网站中有一个 ID 为`main`的元素，它代表主要内容。我们想要做的是异步加载主菜单中的链接所引用的页面的主要内容，理想情况下将`main-menu`作为其 ID。我们希望只检索这个元素内部的内容，因为布局的其他部分不会改变，所以不需要加载它们。

这种方法旨在增强功能，因为如果访问网站的用户禁用了 JavaScript，他们仍然可以使用通常的同步机制浏览网站。我们希望实现这个功能，因为它可以提高网站的性能。在这个例子中，我们假设菜单中的所有链接都是内部链接。

使用 jQuery 和`load()`方法，我们可以用下面的代码完成这个任务:

```
$('#main-menu a').click(function(event) {
      // Prevents the default behavior which is
      // to load the page synchronously
      event.preventDefault();

      // Load the HTML code referenced by this.href
      // into the element having ID of #main
      $('#main').load(this.href);
   });
```

但是等等！你能看出这段代码有什么问题吗？你们中的一些人可能会注意到，这段代码检索被引用页面的所有 HTML 代码，而不仅仅是主要内容。执行这段代码的结果类似于有两个镜子，一个在另一个的前面:您看到镜子里的镜子里的镜子，等等。我们真正想要的是只加载目标页面的*主*内容。

为了解决这个问题，jQuery 允许我们在指定的 URL 之后立即添加一个选择器。使用`load()`方法的这个特性，我们可以将前面的代码重写为:

```
$('#main-menu a').click(function(event) {
      event.preventDefault();

      $('#main').load(this.href + ' #main *');
   });
```

这次我们检索页面，但是我们过滤 HTML 代码，只注入 ID 为`main`的元素的后代。我们包含了通用选择器(` * ` ),因为我们希望避免在`#main`元素中包含`#main`元素；我们只想要`#main`内部的需求，而不是`#main`本身。

这个例子很好，但是它只显示了一个可用参数的使用。让我们看看更多的代码！

### 通过`load()`使用回调

回调参数可用于通知用户操作已完成。让我们最后一次更新我们之前的例子，使用这个。

这次我们假设有一个 ID 为`notification-bar`的元素，它将被用作通知栏。

```
$('#main-menu a').click(function(event) {
      event.preventDefault();

      $('#main').load(this.href + ' #main *', function(responseText, status) {
         if (status === 'success') {
            $('#notification-bar').text('The page has been successfully loaded');
         } else {
            $('#notification-bar').text('An error occurred');
         }
      });
   });
```

下面是这段代码的一个现场演示，对演示做了一些小的改进:

参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )的 load() 的 Pen [jQuery Ajax 示例。](http://codepen.io/SitePoint/pen/CwesD/)