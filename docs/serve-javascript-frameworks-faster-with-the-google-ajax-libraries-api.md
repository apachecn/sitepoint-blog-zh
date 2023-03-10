# 使用 Google AJAX Libraries API 更快地服务于 JavaScript 框架

> 原文：<https://www.sitepoint.com/serve-javascript-frameworks-faster-with-the-google-ajax-libraries-api/>

谷歌[宣布](http://googleajaxsearchapi.blogspot.com/2008/05/speed-up-access-to-your-favorite.html)他们的[谷歌 AJAX API](http://code.google.com/apis/ajax/documentation/)服务的扩展:T4 AJAX 库 API。他们收集了最常见的 JavaScript 库，并在他们的内容交付网络上提供。

对 JavaScript 框架库的一个常见批评是，从所有使用它们的站点下载相同的代码，浪费带宽并在浏览器的本地缓存中创建重复的文件。谷歌的举措是为了减轻这种负担，并创造一种 JavaScript 的香格里拉，所有的框架都在一起，有效地利用它们的时间，并可能唱歌。如果有比唱歌更好的方式来毁掉一部像样的[空难求生电影](http://en.wikipedia.org/wiki/Lost_Horizon_%28film%29)，我还没看过！弗兰克·卡普拉对此负有很大责任。

无论如何，如果有足够多的网站使用 Google CDN 作为他们的 JavaScript 库，浏览器就有更好的机会缓存它的本地副本，避免再次下载。每个人都获得了性能优势！。

使用该服务很简单。你可以像这样通过 URI 直接引用这些库:

```
<script type="text/javascript" src="https://ajax.googleapis.com/ajax/libs/prototype/1.6.0.2/prototype.js">
</script>
```

或者，你可以这样使用谷歌的 API:

```
<script type="text/javascript" src="http://www.google.com/jsapi"></script>
<script type="text/javascript">
  google.load("prototype", "1.6.0.2");
</script>
```

第一个参数是库的名称，第二个参数是所需的版本。

通过网络传输的 JavaScript 在浏览器支持时被压缩(124K 的原型库被压缩到大约 30K ),并带有适当的缓存头。该服务的一个出色特性是您可以控制版本。上面的例子下载了一个非常具体的 Prototype 版本:`1.6.0.2`。但是您可以指定任何增量级别，它将提供最新的版本。例如，如果你指定版本`1.6`，你将得到最新的`1.6`增量版本，无论它是什么。如果您指定版本`1`，也会发生同样的情况，如果您没有指定任何版本，您将获得最新的可用版本。

然而，在做一些测试时，我注意到一些更有趣的事情,`Cache-Control`头值被调整以适应请求。指定特定的增量版本时，头值如下所示:

`Cache-Control: public, max-age=31536000`

这将通知任何关心缓存控制头的缓存机制，内容可以安全地缓存长达一年。当指定一个更通用的版本时，您会得到:

`Cache-Control: public, max-age=3600, must-revalidate, proxy-revalidate`

这通知缓存应该每小时检查内容的变化，以确保提供最新版本。

文档只提到了与`google.load` API 调用相关的版本控制，但是所有下面的直接 URIs 似乎都工作并交付了原型 1.6.0.2:

*   `[https://ajax.googleapis.com/ajax/libs/prototype/1.6.0.2/prototype.js](https://ajax.googleapis.com/ajax/libs/prototype/1.6.0.2/prototype.js)`
*   `[https://ajax.googleapis.com/ajax/libs/prototype/1.6/prototype.js](https://ajax.googleapis.com/ajax/libs/prototype/1.6/prototype.js)`
*   `[https://ajax.googleapis.com/ajax/libs/prototype/1/prototype.js](https://ajax.googleapis.com/ajax/libs/prototype/1/prototype.js)`

目前谷歌主机:

*   [jQuery](http://code.google.com/apis/ajaxlibs/documentation/index.html#jquery)
*   [原型](http://code.google.com/apis/ajaxlibs/documentation/index.html#prototype)
*   [script.aculo.us](http://code.google.com/apis/ajaxlibs/documentation/index.html#script_aculo_us)
*   [MooTools](http://code.google.com/apis/ajaxlibs/documentation/index.html#mootools)
*   [道场](http://code.google.com/apis/ajaxlibs/documentation/index.html#dojo)

其中一些库也有缩小版。

YouTube 上有一个[短片](http://www.youtube.com/watch?v=4F4Jb1ssEvI)解释了这一切是如何工作的，但它似乎有点过时了；用于直接访问 JavaScript 文件的 URI 与文档不匹配，并且不起作用。

在宣布 Ajaxian 上的 API 时，Don 还说了一些关于这种服务的未来的有趣的事情:

> 如果我们看到良好的使用，我们可以与浏览器供应商合作，自动发布这些库。然后，如果他们看到我们使用的 URL，他们可以从本地系统自动加载库，甚至是特殊的 JIT 库。因此，根本没有网络点击！

尽管有一些脾气暴躁的老守财奴的咆哮，JavaScript 框架库的使用只是在增加和改进。我记得在过去的 DHTML 时代，每个可用的脚本都有自己版本的`addEvent`方法；这么多浪费的重复代码。通用框架的出现停止了重复，并提供了有用的抽象层次。我认为谷歌的新服务是朝着正确方向迈出的一大步。

## 分享这篇文章