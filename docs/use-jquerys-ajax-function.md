# 如何对异步 HTTP 请求使用 jQuery 的 ajax()函数

> 原文：<https://www.sitepoint.com/use-jquerys-ajax-function/>

Ajax 是一种允许开发人员在不需要刷新页面的情况下发出异步 HTTP 请求的技术。为了使这个过程不像纯 JavaScript 那样繁琐，开发人员已经使用 jQuery 库很多年了。在我的文章[jQuery 简写 Ajax 方法的介绍](https://www.sitepoint.com/introduction-jquery-shorthand-ajax-methods)中，我讨论了一些 jQuery 最常用的 Ajax 简写方法:`$.get()`、`$.post()`和`$.load()`。它们是用几行代码发出 Ajax 请求的便捷方法。

有时，我们需要更多地控制我们想要进行的 Ajax 调用。例如，我们希望指定在 Ajax 调用失败的情况下应该发生什么，或者我们需要执行 Ajax 请求，但只有在一定时间内检索到结果时才需要它。在这种情况下，我们可以依赖 jQuery 提供的另一个函数`$.ajax()`，这也是本教程的主题。

## `$.ajax()`功能

jQuery [`$.ajax()`](https://api.jquery.com/jQuery.ajax/) 函数用于执行一个异步 HTTP 请求。它是很久以前添加到库中的，从版本 1.0 就存在了。`$.ajax()`函数是前面提到的文章中讨论的每个函数使用预设配置在后台调用的函数。该函数的签名如下所示:

```
$.ajax(url[, settings])
$.ajax([settings]) 
```

`url`参数是一个字符串，包含 Ajax 调用要到达的 URL，而`settings`是一个对象文字，包含 Ajax 请求的配置。

在其第一种形式中，该函数使用`url`参数和`settings`中指定的选项执行 Ajax 请求。在第二种形式中，URL 在`settings`参数中指定，或者可以省略，在这种情况下，请求是针对当前页面的。

该函数接受的选项列表很长，将在下一节中描述，所以我将尽量缩短描述。万一你想深入研究它们的含义，可以参考`$.ajax()`的官方文档。

### `settings`参数

你可以指定许多不同的选项来满足你的需求。在下面的列表中，您可以找到它们的名称以及按字母顺序排列的描述:

*   `accepts`:在请求头中发送的内容类型，告诉服务器它将接受什么样的响应作为回报。
*   `async`:将该选项设置为`false`以执行同步请求。
*   `beforeSend`:一个预请求回调函数，可以用来在发送之前修改`jqXHR`对象。
*   `cache`:将此选项设置为`false`，以强制浏览器不缓存请求的页面。
*   `complete`:请求结束时(执行`success`和`error`回调后)要调用的函数。
*   `contents`:一个对象，它决定了库将如何解析响应。
*   `contentType`:发送给服务器的数据的内容类型。
*   `context`:一个对象，用作所有 Ajax 相关回调的上下文(`this`)。
*   `converters`:包含数据类型到数据类型转换器的对象。
*   `crossDomain`:将该属性设置为`true`，强制同一域上的跨域请求(如 JSONP)。
*   `data`:执行 Ajax 请求时发送给服务器的数据。
*   `dataFilter`:用于处理 XMLHttpRequest 的原始响应数据的函数。
*   `dataType`:期望从服务器返回的数据类型。
*   `error`:请求失败时调用的函数。
*   `global`:是否为该请求触发全局 Ajax 事件处理程序。
*   `headers`:发送给服务器的附加头的对象。
*   `ifModified`:如果您希望仅在自上次请求后响应发生变化时强制请求成功，请将此选项设置为`true`。
*   `isLocal`:如果您想强制 jQuery 将当前环境识别为“本地”，请将此选项设置为`true`。
*   `jsonp`:在 JSONP 请求中覆盖回调函数名的字符串。
*   `jsonpCallback`:指定 JSONP 请求的回调函数名。
*   `mimeType`:指定 mime 类型以覆盖 XHR mime 类型的字符串。
*   【XMLHttpRequest 响应 HTTP 访问认证请求时使用的密码。
*   `processData`:如果不希望传递给`data`选项的数据(如果还不是字符串)被处理并转换成查询字符串，将该选项设置为`false`。
*   `scriptAttrs`:定义一个带有附加属性的对象，用于“脚本”或“jsonp”请求。
*   `scriptCharset`:在请求中使用的脚本标签上设置 charset 属性，但仅在使用“script”传输时适用。
*   `statusCode`:数值型 HTTP 代码和函数的对象，当响应有相应的代码时调用。
*   `success`:请求成功时调用的函数。
*   `timeout`:指定请求超时(以毫秒为单位)的数字。
*   `traditional`:如果您希望使用 param 序列化的传统样式，请将此项设置为`true`。
*   `type`:请求的类型，可以是“POST”或“GET”。
*   `url`:包含请求发送到的 URL 的字符串。
*   `username`:在响应 HTTP 访问认证请求时与 XMLHttpRequest 一起使用的用户名。
*   `xhr`:创建 XMLHttpRequest 对象的回调。
*   `xhrFields`:设置在原生 XHR 对象上的对象。

这是一个很长的列表，不是吗？嗯，作为一名开发人员，我想你可能在第五或第六个元素时就停止阅读这个列表了，但这没关系。下一节将会更加令人兴奋，因为我们将把`$.ajax()`函数和其中的一些选项付诸实施。

## 把所有的放在一起

在这一节中，我们将看到这个函数和它的一些选项。

### `$.ajax()`的第一个例子

我们将从一个简单的演示开始，它再现了我们在上一篇文章中开发的相同代码，但是这一次我们将采用`$.ajax()`。为了方便起见，我所说的代码如下所示:

```
$('#main-menu a').on('click', function(event) {
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

更新这个代码片段以使用`$.ajax()`函数，我们获得如下所示的代码:

```
$('#main-menu a').on('click', function(event) {
  event.preventDefault();

  $.ajax(this.href, {
    success: function(data) {
      $('#main').html($(data).find('#main *'));
      $('#notification-bar').text('The page has been successfully loaded');
},
    error: function() {
      $('#notification-bar').text('An error occurred');
    }
  });
}); 
```

这里你可以看到我使用了函数的第一种形式。我已经将发送请求的 URL 指定为第一个参数，然后将 settings 对象指定为第二个参数。后者只利用了上一节中讨论的几个属性中的两个属性——`success`和`error`——来分别指定在请求成功或失败的情况下做什么。

### 使用`$.ajax()`从 Joind.in 中检索对话

我要讨论的第二个例子创建一个 JSONP 请求，从一个名为 [Joind.in](http://joind.in) 的服务中检索一些信息。后者是一个网站，活动参与者可以在这里留下对活动及其会议的反馈。具体来说，我将创建一段代码，使用`$.ajax()`函数，从 PHP 开发人员的角度检索我的演讲*现代前端的标题和描述。*

实现这一目标的代码如下:

```
$.ajax({
  url: 'http://api.joind.in/v2.1/talks/10889',
  data: {
    format: 'json'
  },
  error: function() {
    $('#info').html('<p>An error has occurred</p>');
  },
  dataType: 'jsonp',
  success: function(data) {
    var $title = $('<h1>').text(data.talks[0].talk_title);
    var $description =  $('<p>').text(data.talks[0].talk_description);
    $('#info')
    .append($title)
    .append($description);
  },
  type: 'GET'
}); 
```

在上面的代码片段中，我使用了上面列出的几个属性。首先，您可以看到我使用了第二种形式的`$.ajax()`，它允许我将请求发送到的 URL 指定为对象文字的属性(`url`)。因为 Joind.in 的 API 接受 JSONP 请求，所以在上面的代码中，我通过指定`dataType`属性来设置我想要使用的请求类型。然后，我使用`data`属性来定义我想要从服务器获取的格式类型，这是 API 所要求的。然而，后者需要这个数据作为 GET 请求的查询字符串的一部分，因此我也将`type`属性设置`GET`指定为它的值。最后，我编写了一个`error`回调函数在出错时显示一条消息，一个`success`回调函数在成功时显示演讲的标题和描述。

下面是这段代码的一个现场演示:

[//jsfiddle.net/2tqja09e/2577/embedded/](//jsfiddle.net/2tqja09e/2577/embedded/)

## 结论

在本教程中，我讨论了 jQuery 提供的最强大的 Ajax 函数，`$.ajax()`。它允许您在执行 Ajax 请求时对如何将请求发送到服务器以及如何处理响应进行大量控制。感谢这个函数，万一没有合适的速记函数，您就有了满足所有项目需求的工具。

为了更好地理解这个函数的潜力，我鼓励您使用代码示例，并尝试修改代码以使用由`settings`参数接受的一些其他选项。

如果你想了解更多关于 JavaScript 的知识，请访问 [SitePoint Premium](https://www.sitepoint.com/premium/library) 查看我们的 JavaScript 书目。玩得开心！

## 分享这篇文章