# JavaScript 历史 API 概述

> 原文：<https://www.sitepoint.com/an-overview-and-usage-of-javascript-history-api/>

现代 web 应用程序可以使用历史 API 访问用户的浏览历史。从 HTML5 开始，您还可以非常灵活地操作历史条目。本教程概述了 JavaScript 的历史 API，并解释了在设计现代 web 应用程序时如何使用这一特性。

# 控制历史

历史 API 允许开发人员添加、删除和替换历史条目，改变后退和前进按钮的行为。此外，您可以提取状态信息并使用它来操作文档的内容。所有这些都是使用`history`对象完成的——这是`window`的一个属性。

## 前后移动

`history`对象提供了两种有用的方法来循环浏览用户历史，`back()`和`forward()`。调用`history.back()`会让用户在浏览器历史中后退一步。这与点击后退按钮的效果相同。同样，调用`history.forward()`与按下浏览器的前进按钮有相同的效果。

## 移动到特定的历史点

`history`对象提供了另一个方法`go()`，它将用户带到一个特定的历史点。例如，如果你打电话给`history.go(-3)`，它会把用户带回三页。类似地，调用`history.go(3)`会让用户前进三页。分别调用`history.go(-1)`和`history.go(1)`与调用`history.back()`和`history.forward()`的效果相同。

**注意:** IE 允许开发者将 URL 传递给`go()`。但是，这不是标准的，应该避免。

## 计算历史记录条目的数量

通过访问`history`对象的 length 属性可以找到历史中的页数，如下所示。

```
alert(history.length);
```

# 操作历史记录条目

`history`对象提供了两种方法，`pushState()`和`replaceState()`，用于添加和替换历史条目。

## 使用`pushState()`

假设下面的代码在`http://localhost/index.html`被执行:

```
history.pushState({page:2},"Page 2","page2.html");
```

这将导致浏览器将当前页面的 URL 更改为`http://localhost/page2.html`。但是，它不会改变页面的内容或重新加载它。浏览器甚至不会检查`page2.html`是否存在。它只会在地址栏显示网址。

现在，假设你访问`http://www.sitepoint.com`并点击后退按钮。浏览器将加载先前添加到历史堆栈中的`http://localhost/page2.html`。当页面被加载时，它还会收到一个`popstate`事件。在上面的 JavaScript 代码中，我们将一个对象作为第一个参数传递给了`pushState()`(这就是所谓的`state`对象)。我们可以从`popstate`事件的`state`属性中检索这个对象，并使用它来操作文档的内容。

`pushState()`方法采用以下三个参数:

1.  状态对象–该对象与添加到堆栈中的新历史条目相关联。
2.  标题–新历史条目的标题。目前 Firefox 忽略了这个参数，但是将来可能会用到。
3.  URL–向用户显示的 URL。它可以是绝对的，也可以是相对的，但是 URL 应该与当前 URL 来源相同。否则，该方法将引发异常。

为了加强推送状态的概念，假设我们有三个页面:`index.html`、`page1.html`和`page2.html`。`index.html`的 HTML 内容如下所示。

```
<html>
  <head>
    <title>Demo Page</title>
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/1.9.0/jquery.min.js"></script>
  </head>
  <body>
    <input type="button" id="push" value="Push History"/>
  </body>
</html>
```

现在，以下 JavaScript 代码被添加到文档中:

```
$(document).ready(function(){
  $("#push").click(function(){
    history.pushState({page: 1},"Page 1","page1.html");
    history.pushState({page: 2},"Page 2","page2.html");
  });
});
```

以下 JavaScript 代码被添加到`page2.html`:

```
$(document).ready(function(){
  window.onpopstate=function(event){
  alert("location: "+document.location+" and state: "+event.state.page);
  }
});
```

如果您加载`http://localhost/index.html`并点击推送历史按钮，它将添加两个历史条目并导致浏览器显示 URL `http://localhost/page2.html`。但是，页面内容不会改变。如果你导航到其他页面，然后按下返回按钮，浏览器将加载`page2.html`。该页面还将接收一个`popstate`事件。这个事件的`state`属性将包含一个在用`history.pushState()`添加历史条目时使用的状态对象的副本。

如果您再次按下后退按钮，URL 将变为`http://localhost/page1.html`，并且`page2.html`将接收到另一个`popstate`事件。请注意，虽然 URL 被更改为`page1.html`，但显示的是`page2.html`的内容。

## 使用`replaceState()`

`history.replaceState()`的作用与`history.pushState()`相同，但它修改当前历史记录条目，而不是添加一个新条目。

# 实际用法

当你点击脸书相册中的一张特定照片时，你会看到网址发生了变化，一张图片显示出来。所有这些都不需要重新加载页面。

也可以看看 Chrome 网上商店。当你点击一个特定的应用程序时，该应用程序的所有细节都会显示在一个模态窗口中，并且页面的 URL 会发生变化。关键是每个应用都有自己的 URL，用户可以将它加入书签。

我们可以使用历史 API 实现类似的功能。在本例中，我们将创建一个简单的照片库。如果用户点击一张特定的照片，照片会以灯箱的形式打开。我们通过给每张照片赋予自己的 URL 来增强功能。当一张图片打开时，网页的 URL 被改变以反映当前打开的图片的地址。

## 第一步

我们首先创建一个简单的 HTML 页面，并向其中添加一些图像，如下所示。请注意，我已经包含了 prettyPhoto，这是一个用于创建 lightbox 效果的 jQuery 插件。

#### demo.html

```
<html>
  <head>
    <title>Demo Page</title>
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/1.9.0/jquery.min.js"></script>
    <script src="js/jquery.prettyPhoto.js" charset="utf-8"></script>
    <link rel="stylesheet" href="css/prettyPhoto.css"/>
    <script type="text/javascript" src="customurl.js" charset="utf-8"></script>
  </head>
  <body>
    <div align="center">
      <a href="/demos/history/pics/image1.jpg" rel="prettyPhoto" id="1"><img src="pics/image1.jpg" height="300"
      width="300"/></a>
      <a href="/demos/history/pics/image2.jpg" rel="prettyPhoto" id="2"><img src="pics/image2.jpg" height="300"
      width="300"/></a>
      <a href="/demos/history/pics/image3.jpg" rel="prettyPhoto" id="3"><img src="pics/image3.jpg" height="300"
      width="300"/></a>
      <a href="/demos/history/pics/image4.jpg" rel="prettyPhoto" id="4"><img src="pics/image4.jpg" height="300"
      width="300"/></a>
    </div>
  </body>
</html>
```

## 第二步

我们继续向页面添加一些 JavaScript。页面中包含的 customurl.js 的内容如下所示。首先，我们通过初始化 prettyPhoto 创建一个照片库。接下来，当用户点击一个链接时，我们获取图片编号，并根据该编号创建一个假的图片 URL。然后我们把它推到历史堆栈上。类似地，当用户关闭一个图像时，我们从堆栈中弹出当前的历史条目。结果，原来的网址回到地址栏。

```
$(document).ready(function(){
  $("a[rel^='prettyPhoto']").prettyPhoto({
    callback: function(){history.pushState("","","/demos/history/demo.html"); document.title="Demo Page";}
  });
  $("a").click(function(){
    var id=$(this).attr("id");
    var img="image"+id;
    var url="images/"+img+"/";
    history.pushState("","",url);
    document.title=img;
  });
  function getParameter(name){
    if(name=(new RegExp('[?&]'+encodeURIComponent(name)+'=([^&]*)')).exec(location.search))
      return decodeURIComponent(name[1]);
  }
  var image=getParameter("id");
  if(typeof image !='undefined'){
    var event=document.createEvent('MouseEvents');
    event.initEvent('click',true,true);
    document.getElementById(image).dispatchEvent(event);
  }
});
```

## 第三步

用户直接访问假图片网址怎么办？浏览器将发送 404 错误，指示未找到该页面。为了克服这个问题，我们创建了一个服务器端 PHP 脚本。该脚本从请求中获取图像编号，并将用户重定向到`demo.html`，将图像编号作为查询字符串参数追加。

在`demo.html`中的 JavaScript 代码中，我们定义了`getParameter()`函数，该函数搜索 URL 以找到图像编号。我们已经给了我们的图片库链接特定的`id`属性，代表图像编号。如果从 URL 中找到图像编号，我们将在特定链接上以编程方式创建一个`click`事件。因此，图像以灯箱效果显示。

你可以在这里找到完整的工作演示。

# 进一步阅读

你可以访问 [Mozilla 开发者网络](https://developer.mozilla.org/en-US/docs/DOM/Manipulating_the_browser_history)来了解更多关于历史 API 的信息。另外，查看 [`history`](https://developer.mozilla.org/en-US/docs/DOM/window.history) 对象以了解更多关于它的属性和方法。

## 分享这篇文章