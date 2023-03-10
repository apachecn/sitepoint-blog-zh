# Firefox 3 中的跨站点 Ajax

> 原文：<https://www.sitepoint.com/cross-site-ajax-in-firefox-3/>

John Resig】发布了一个很好的总结(包括演示代码)，讲述了如何实现跨站点 XMLHttpRequest 调用，这是一个目前由 Firefox 3 的 beta 2 版本实现的功能。

> 简而言之，有两种技术可以用来获得想要的跨站点请求结果:为内容指定一个特殊的访问控制头，或者在 XML 中包含一个访问控制处理指令。

特别令人兴奋的是利用这一特性所需的代码。例如，要从一个远程域请求一个 HTML 文件，您可能需要做以下事情(当然，您需要先[下载 Firefox 3](http://en-us.www.mozilla.com/en-US/firefox/3.0b2/releasenotes/) ):

```
var xhr = new XMLHttpRequest();
xhr.onreadystatechange = function() {
  if ( xhr.readyState == 4 ) {
    if ( xhr.status == 200 ) {
      document.body.innerHTML = "And the winner is... " + xhr.responseText;
    } else {
      document.body.innerHTML = "ERROR";
    }
  }
};
xhr.open("GET", "https://www.sitepoint.com/example.php", true);
xhr.send(null);
```

眼熟吗？除了在 open 函数的 URL 参数中包含域之外，这段代码与您可能已经在进行的标准 Ajax 调用完全相同。

当然，跨站点 Ajax 请求是否是一件坏事是一个争论，毫无疑问，这个争论将持续数年，直到有足够多的浏览器支持该功能，使其真正有用。一旦我们达到这一点，我敢打赌，一个全新的 mashups、应用和其他服务的世界将会开放(是的，不理解它的人无疑会用它做一些愚蠢的事情，就像他们在几年前 Ajax 成为新热点时所做的那样)。

阅读 Mozilla 开发中心的[官方文档](http://developer.mozilla.org/en/docs/Cross-Site_XMLHttpRequest)以获取更多信息(如果你在那里，也许可以查看文档以了解一些[即将推出的其他特性](http://developer.mozilla.org/en/docs/Firefox_3_for_developers))。

## 分享这篇文章