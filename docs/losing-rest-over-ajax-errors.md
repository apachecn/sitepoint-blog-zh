# 因为 Ajax 错误而失去休息？

> 原文：<https://www.sitepoint.com/losing-rest-over-ajax-errors/>

我经常看到 Ajax 这样处理响应的例子:(伪代码用于演示目的)

```
 xhr.onreadystatechange = function() 
{
  if ( xhr.readyState == 4 )
  {
    if (xhr.status == 200) 
    {
      // Process returned data (eg: Parse XML).

      // Check status of result depending on custom/ad-hoc error detection.
      //  -- most commonly a 0 for fail, or 1 for pass

      // Deal with/report error, or take some other action based upon returned data.
    }
  }
} 
```

上面的方法是可行的，但是随着应用程序的增长，您需要提供有用的错误报告(和错误避免！)的增加，良好的布尔型错误检查将很快变得难以使用。我现在可以看到了…

1.  <cite>开发者 1</cite> : <q>错误码 7 又是什么意思？</q>
2.  <cite>开发者 2</cite> : <q>嗯，等等，我肯定我们把它记在某个地方了……</q>

不要害怕，有一个*更聪明的选择，你每次加载浏览器时都可以依赖这个选择——[HTTP 状态码](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)(如果一想到阅读另一个 RFC 就让你变得呆滞无神，那就看看马克·皮尔格林[的幽默删节列表](http://diveintomark.org/archives/2006/12/07/rest-for-toddlers "REST for toddlers"))。*

以前面的例子为例，我为一些状态代码添加了一个`switch`块，这些状态代码在处理对 JavaScript HTTP 请求的响应时最有用:

```
 xhr.onreadystatechange = function() 
{
  if ( xhr.readyState == 4 )
  {
    switch ( xhr.status )
    {
      case 200: // OK!
        /* 
         * If the request was to create a new resource 
         * (such as post an item to the database)
         * You could instead return a status code of '201 Created'
         */  
      break;

      case 304: // Not Modified
        /* 
         * This would be used when your Ajax widget is 
         * checking for updated content,
         * such as the Twitter interface.
         */   
      break;

      case 400: // Bad Request  
        /* 
         * A bit like a safety net for requests by your JS interface
         * that aren't supported on the server.
         * "Your browser made a request that the server cannot understand"
         */
      break;

      case 409: // Conflict 
        /* 
         * Perhaps your JavaScript request attempted to 
         * update a Database record 
         * but failed due to a conflict 
         * (eg: a field that must be unique)
         */
      break;

      case 503: // Service Unavailable
        /* 
         * A resource that this request relies upon
         * is currently unavailable 
         * (eg: a file is locked by another process)
         */
      break;    
    }
  }
} 
```

所以下一次您准备在 XML 响应中使用`<status>1</status>`或类似的代码时，请深入研究 HTTP 状态代码。这可能是休息的第一步，这当然是件好事。

## 分享这篇文章