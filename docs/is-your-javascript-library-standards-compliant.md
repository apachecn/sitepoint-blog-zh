# 你的 JavaScript 库符合标准吗？

> 原文：<https://www.sitepoint.com/is-your-javascript-library-standards-compliant/>

像 [jQuery](http://jquery.com/) 、 [Dojo](http://dojotoolkit.org/) 和 [YUI](http://developer.yahoo.com/yui/) 这样的 JavaScript 库可以为你做的事情之一就是在最新的 Web 标准被嵌入浏览器之前就为它们添加支持。但是有些图书馆是不是走得太远了？

对于 JavaScript 库的开发人员来说，有一种诱惑，即*扩展*标准的特性，并构建更好的东西！一个很好的例子是 CSS 选择器查询，它使 jQuery 出名，但现在在大多数 JavaScript 库中都可以使用。

CSS 查询提供了一种非常方便的方法，可以从 HTML 文档中获取符合特定标准的元素列表。例如，您可以编写一个脚本，在一个新窗口中打开所有带有属性`rel="external"`的超链接。仅使用所有主流浏览器都支持的 DOM API，检索链接列表的 JavaScript 代码相当麻烦:

```
var anchors = document.getElementsByTagName('a');
for (var i = 0; i < anchors.length; i++)
{
  var href = anchors[i].getAttribute('href');
  var rel = anchors[i].getAttribute('rel');
  if (href != null && href.length > 0 &&
      rel != null && /(^| )external( |$)/.test(rel))
  {
    // anchors[i] is a link with rel="external"
  }
}
```

这段代码首先获取文档中所有`a`元素的列表，然后检查列表中的每个元素，看它是否有一个`href`属性和一个包含单词`external`的`rel`属性。

CSS 查询允许您使用我们在编写样式表时使用的相同的紧凑 CSS 选择器语法来表达所有这些测试。下面是它使用 jQuery 的 CSS 查询 API 的样子:

```
var links = $('a[href][rel~=external]');
for (var i = 0; i < links.length; i++)
{
  // links[i] is a link with rel="external"
}
```

这个特性非常受欢迎，以至于很快被添加到每个 JavaScript 库中。使用 Dojo 的 API 也是如此:

```
var links =  dojo.query('a[href][rel~=external]');
for (var i = 0; i < links.length; i++)
{
  // links[i] is a link with rel="external"
}
```

当各种不同的库为了产生最快的 CSS 查询实现而竞争时，浏览器制造商在 W3C 聚集在一起，制定了一个浏览器可以本地实现的标准:[选择器 API](https://www.w3.org/TR/selectors-api/) 。

但是事情变得有趣了:有些库，比如 jQuery，在 CSS 规范提供的选择器的基础上增加了对一大堆额外的选择器的支持，而其他的，比如 Dojo，坚持只支持标准的 CSS 选择器。

显然，当浏览器支持选择器 API (Safari 和 IE8 beta 1 已经支持了！)，它们的本地实现将比 JavaScript 库运行得更快。但是选择器 API 只允许您使用浏览器支持的 CSS 选择器——不多也不少。支持额外的非标准选择器的库将无法提供这种性能提升。

Dojo 的亚历克斯·罗素在他最近发布的 Dojo 1.1 的[公告](http://alex.dojotoolkit.org/?p=663)中是这样说的:

> …通过将我们的查询语法保持在 CSS 所提供的范围内，我们避免了让自己陷入这样一种境地，即我们总是需要通过网络来提供这样一个查询引擎。迟早，`dojo.query()`会变成一个对`querySelectorAll`的调用，再加上一些返回数组的语法糖。更好的是，API 不会改变，你现在可以在支持它的浏览器上获得加速，完全知道将来事情只会变得更快更小。对关注 web 发展的工具包的投资已经为 Dojo 用户带来了回报。

那么，您使用的是哪一个 JavaScript 库呢？您是否检查过它的 API 与相应的内置浏览器功能的新兴标准相比如何呢？

<ins>根据评论中的反馈更新了上面的代码示例，使用`for`循环代替`for…in`循环。</ins>

## 分享这篇文章