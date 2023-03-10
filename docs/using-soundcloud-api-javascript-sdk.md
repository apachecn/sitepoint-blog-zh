# 通过 JavaScript SDK 使用 SoundCloud API

> 原文：<https://www.sitepoint.com/using-soundcloud-api-javascript-sdk/>

*这篇文章由[杰米·希尔兹](https://www.sitepoint.com/author/jshields)和[沃恩·安切塔](https://www.sitepoint.com/author/wancheta)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

SoundCloud 已经发布了一个 API，允许开发者获得几乎任何他们想要的数据。但它的用法可能会令人困惑，尤其是对初学者来说，因为截至目前，SoundCloud API [文档](https://developers.soundcloud.com/docs/api#client-side)和[示例](http://connect.soundcloud.com/examples/basic.html)使用不同版本的 SDK(软件开发工具包)。

API 和 SDK 的区别是什么？基本上，API 是一个 URL 集合，提供对来自 SoundCloud 服务器的数据的访问，SDK 是一个预先编写的库(或客户端),用于查询 API。要了解更多信息，请参见本讨论。

在本教程中，我们将学习如何访问 SoundCloud API，以及如何使用 SDK 简化这个过程。我们将逐步完成 SDK 的设置，然后编写 JavaScript 从 SoundCloud 获取数据、播放音频等等。

## 入门指南

了解 HTTP 和 API 的概念和工作方式会有所帮助。如果你想了解更多关于 API 的知识，我推荐这个短期课程:[API 简介](https://zapier.com/learn/apis/)。关于异步 JavaScript、承诺和回调的一些知识也会有所帮助。 [jQuery](https://jquery.com/) 在我们的代码示例中使用，所以了解一些基础知识不会有什么坏处。

要开始使用 JavaScript 查询 SoundCloud API，我们需要下载 SoundCloud 提供的 JavaScript SDK。如前所述，SDK 有两个不同的版本。

### 使用哪个版本的 SDK？

它们之间的主要区别在于，当向 API 发出异步请求时，它们如何返回数据。最新版本返回一个 Promise，另一个需要回调函数作为参数。

我注意到的一个问题是，对于文档使用的 SDK 版本，用户登录功能似乎有问题，因为弹出窗口不会自动关闭。

因此，为了简单起见，也因为它更稳定，我们将在整个教程的例子中使用旧版本。这个版本将需要异步请求的回调函数。

## 使用 SoundCloud API

### 设置一个基本的 HTML 文档

我们将创建一个基本的 HTML 页面，作为我们的主页。我们还将在这里包含 SDK，因此我们可以利用它的功能。

```
<!DOCTYPE html>
<html>
  <head>
    <title>Include SDK - Using SoundCloud API</title>
    <script src="//connect.soundcloud.com/sdk.js"></script>
  </head>
  <body></body>
</html> 
```

请注意，我们已经在页面中包含了直接来自 SoundCloud 服务器的 SDK。您也可以下载 SDK 并参考它，如下所示:

```
<script src="sdk.js"></script> 
```

要测试 SDK 是否正确加载到您的网页中:

*   在浏览器中打开页面(推荐使用 Chrome)。
*   在浏览器中打开开发者控制台(在 Chrome 中为 *Ctrl + Shift + J* )。
*   在控制台中，键入`SC`并按回车键。`SC`是一个由我们刚刚包含的 SDK 创建的 *Javascript 对象*。

如果出现一个`undefined`错误，则说明加载不正确。尝试刷新并确保 SDK 文件(`sdk.js`)的路径是正确的。

### 注册 SoundCloud 应用程序

要注册 SoundCloud 应用程序，您只需要一个 SoundCloud 帐户。如果你还没有，那就创建一个吧。通过注册一个应用程序，SoundCloud 服务器将能够验证我们的请求，因此其他任何人都不能代表我们提出请求。

*注意:如果我们不打算在我们的网站上使用用户登录功能，我们可以跳过这一步。这将在下一节解释。*

*   打开 [SoundCloud 应用页面](http://soundcloud.com/you/apps)。这里将列出我们已经创建的任何应用程序。请确定您已登录到您的 SoundCloud 帐户。*注:您不需要为此单独建立账户。您可以使用用于个人目的的相同帐户。*

*   点击*注册新应用*按钮。
    

*   给它起个名字，通过勾选复选框接受 SoundCloud 的[开发者政策](https://developers.soundcloud.com/docs/api/terms-of-use)。
    

*   点击大*注册*按钮，完成 app 注册。

成功注册后，我们将被重定向到我们新创建的应用程序的设置页面。在那里，我们将找到我们的应用程序的*客户端 ID* ，它将用于授权我们的请求。我们可以暂时离开*网站*和*回拨*字段。我们稍后会谈到这一点。

### 初始化客户端

通过“初始化客户端”，我们的意思是让客户端准备好在它自己和 SoundCloud API 之间交换数据。我们可以在我们之前创建的基本 HTML 文档中完成，或者在外部的*中完成。js* 文件。

这样做的 JavaScript 语法是:

```
SC.initialize({
  client_id: "CLIENT_ID",
  redirect_uri: "CALLBACK_URL"
}); 
```

让我们来分解一下:

*   注册我们的应用程序时会向我们提供`CLIENT_ID`。
*   `CALLBACK_URL`是到`callback.html`的 URL，这是一个 HTML 文件，在用户登录后被调用。我们将很快创建它。

现在，在初始化之后，我们准备好查询 SoundCloud API。让我们看一些例子，看看我们已经能做些什么。

## 例子

如果我们打开浏览器控制台并输入`SC.`，就会出现一个与`SC`对象相关的方法列表。`SC.get(uri, callback)`就是其中之一，用于向 API 发出 GET 请求。

### 获取曲目列表

要获得随机曲目的列表，我们可以像这样使用`SC.get()`:

```
SC.get("/tracks", function(response) {
  for (var i = 0; i < response.length; i++) {
    $("ul").append("<li>" + response[i].title + "</li>");
  }
}); 
```

看到笔[在](http://codepen.io/SitePoint/pen/ZQNgoe/) [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )列出曲目。