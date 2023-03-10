# 实例化 Ajax 的最好方法？

> 原文：<https://www.sitepoint.com/the-best-way-to-instantiate-ajax/>

Ajax 已经存在了一段时间，它的开发实践已经相当成熟。像大多数程序员一样，我选定了一个实例化模式，基本如下:

```
function AjaxRequest()
{
   var request = null;

   if(typeof window.XMLHttpRequest != "undefined")
   {
      request = new XMLHttpRequest();
   }
   else if(typeof window.ActiveXObject != "undefined")
   {
      try
      {
         request = new ActiveXObject("Microsoft.XMLHTTP");
      }
      catch(err) { request = null; }
   }

   return request;
}
```

代码首先测试大多数现代浏览器使用的本机对象，然后测试支持 <abbr title="Internet Explorer 6">IE6</abbr> 的`ActiveX`实现。你会在互联网上找到这样的代码。

*(有些人测试多个版本的`XMLHTTP`，试图选择最新的。在我看来，完全没有必要实例化`MSXML`的高级版本，除非你特别需要它们的高级特性，但这与本文无关。)*

不久前，我正在编写一个名为 [CSSUtilities](http://www.brothercake.com/site/resources/scripts/cssutilities/) 的开发和测试工具——一个 JavaScript <abbr title="Application Programming Interface">API</abbr> ，它提供了查询 <abbr title="Cascading Style Sheets">CSS</abbr> 样式表的方法。我需要 Ajax 加载样式表的能力；但更具体地说，它必须能够处理**本地文件以及网络文件**，这是现代浏览器*所支持的*(尽管有所不同——这种请求通常返回`0`的 <abbr title="HyperText Transfer Protocol">HTTP</abbr> 状态码，并且不提供任何 <abbr title="HyperText Transfer Protocol">HTTP</abbr> 头，这是有意义的，因为它们不是真正的 <abbr title="HyperText Transfer Protocol">HTTP</abbr> 请求)。

但是在开发这个功能的时候，我发现了一件不幸的事情——在 <abbr title="Internet Explorer 8">IE8</abbr> **中可用的原生`XMLHttpRequest`对象对于本地文件**根本不起作用。更糟糕的是，对象*确实*成功实例化了，但是试图使用它会触发一个安全错误— `"Access is denied"`。

这尤其令人恼火，因为这意味着我不能将它用于本地文件(因此，我的工具不能用于将站点开发为本地页面的人，尽管这不是一种很好的工作方式，但许多人都这样做了)。但更令人沮丧的是，这意味着**实例化模式没有机会退回到 ActiveX 实现**，因为本地对象*确实*实例化了，只是不起作用。

我的解决方案？简单— **先实例化`ActiveX`***。因此，如果正在使用的 <abbr title="Internet Explorer">IE</abbr> 版本同时支持*和*本地版本，那么`ActiveX`版本是首选；如果正在使用本地文件，`ActiveX`版本将会工作(如果安全设置允许的话，这是默认的)；如果远程文件正在使用中并且本地 <abbr title="XMLHttpRequest">XHR</abbr> 被禁用，则`ActiveX`版本仍将工作，或者如果`ActiveX`被禁用并且本地 <abbr title="XMLHttpRequest">XHR</abbr> 被启用，则本地版本将工作。喀普拉。*

```
function AjaxRequest()
{
   var request = null;

   if(typeof window.ActiveXObject != "undefined")
   {
      try
      {
         request = new ActiveXObject("Microsoft.XMLHTTP");
      }
      catch(err) { request = null; }
   }
   if(request === null && typeof window.XMLHttpRequest != "undefined")
   {
      try
      {
         request = new XMLHttpRequest();
      }
      catch(err) { request = null; }
   }

   return request;
}
```

当然，如果`ActiveX`被禁用并且本地文件正在被使用，就不可能实例化一个请求；但事实已经如此，所以我们没有损失什么。

围绕第一次尝试的异常处理非常重要——因此可以记录失败并取消引用，然后下一个条件有机会运行(指定为第二个`if`，而不是`else if`，在这些情况下不会运行)。围绕第二次尝试的异常处理只是以防万一，所以没有内部错误返回给用户的机会。

所有这些的要点是*绝对不是*试图规避安全或用户控制——用户仍然完全控制本机和`ActiveX`实现(分别通过高级互联网选项和安全设置)。重点是给用户(也可能是开发新手)机会，当他们的浏览器选项没有给他们完全的控制权时，允许他们*希望*运行的代码运行。

最终，这种模式*扩展了*用户控制，让所有相关人员更容易让他们的作品无条纹闪耀！

那么，你是怎么想的呢——你已经得出类似的结论了吗，或者你能想象这种模式可能会产生问题的情况吗？也许你已经采用了不同于这里讨论的任何东西的你自己的使用模式？

*缩略图鸣谢:[nickweeleroz](http://www.flickr.com/photos/nickwheeleroz/2166114756/)** 

## *分享这篇文章*