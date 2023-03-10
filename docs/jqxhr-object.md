# jqXHR 对象是什么？

> 原文：<https://www.sitepoint.com/jqxhr-object/>

jQuery.ajax()函数基本上只是一个大的 jqXHR 对象(本质上是一个假的 javaScript xhr 对象)。

*   jqXHR (jQuery XMLHttpRequest)替换了浏览器本机 XMLHttpRequest 对象。
*   jQuery 用超集 API 包装浏览器本机 XMLHttpRequest 对象。
*   jQuery XMLHttpRequest (jqXHR)对象由＄返回。ajax()函数。
*   jqXHR 对象尽可能模拟本地 XHR 功能。

## 那么它是做什么的呢？…

*   它处理 HTTP 请求头(Last-Modified、etag、Content-Type、MIME 类型等)。
*   它处理请求的回调(包括承诺回调。done()，。失败()等等…)
*   它处理为请求设置的任何预过滤器。
*   它处理为请求设置的任何超时。
*   它处理任何跨域调用(包括 jsonp)。

在 jQuery 源代码中，它甚至被注释为假 xhr

```
// Fake xhr
jqXHR = {

  ...

}
```

## jqZHR 实现了 Promise 接口

$返回的 jqXHR 对象。ajax()实现 Promise 接口。对象具有承诺的所有属性、方法和行为。 **[阅读更多关于](http://api.jquery.com/deferred.promise/)** 。

## jqXHR 向后兼容 XMLHttpRequest

为了向后兼容 XMLHttpRequest，jqXHR 对象将公开以下属性和方法:readyState、status、statusText、responseXML 和/或 responseText(分别带有 XML 和/或文本响应)、getAllResponseHeaders()、getResponseHeader()、abort()和 setRequestHeader()。由于 success、error、complete 和 statusCode 涵盖了所有需求，jqXHR 不提供任何对 onreadystatechange 的支持。

```
// Attach deferreds
deferred.promise( jqXHR ).complete = completeDeferred.add;
jqXHR.success = jqXHR.done;
jqXHR.error = jqXHR.fail;
```

## XHR 申请的背景资料

> XMLHttpRequest (XHR)是 web 浏览器脚本语言(如 JavaScript)中可用的 API。它用于将 HTTP 或 HTTPS 请求直接发送到 web 服务器，并将服务器响应数据直接加载回脚本。

*   XMLHttpRequest 是一个 JavaScript 对象，由微软设计，被 Mozilla、Apple 和 Google 采用。
*   不管它的名字是什么，XMLHttpRequest 可以用来检索任何类型的数据，而不仅仅是 XML，它支持除 HTTP 之外的协议(包括 file 和 ftp)。
*   XMLHttpRequest 受制于浏览器的同源策略，因为出于安全原因，只有当请求被发送到为原始网页提供服务的同一服务器时，请求才会成功。
*   XMLHttpRequest 对象背后的概念最初是由 Microsoft 为 Microsoft Exchange Server 2000 开发 Outlook Web Access 的开发人员创建的。

## 进一步阅读

*   [仔细查看 jqxhr 对象](http://php.refulz.com/a-close-look-at-jqxhr-object-of-jquery/#ixzz2Oo0oblX2)
*   sharpkit . jquery
*   [最新的 jQuery 源代码](https://code.jquery.com/jquery-latest.js)
*   [维基百科 XMLHttpRequest](http://en.wikipedia.org/wiki/XMLHttpRequest)
*   [Mozilla XMLHttpRequest](https://developer.mozilla.org/en-US/docs/DOM/XMLHttpRequest)

## 分享这篇文章