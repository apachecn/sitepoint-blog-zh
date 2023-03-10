# XMLHttpRequest vs . Fetch API:2019 年 Ajax 最适合做什么？

> 原文：<https://www.sitepoint.com/xmlhttprequest-vs-the-fetch-api-whats-best-for-ajax-in-2019/>

**权衡是否使用 XMLHttpRequest vs Fetch 及其对 Ajax 的现代版？我们比较了两种选择的利弊。**

2019 年 3 月庆祝阿贾克斯 20 周年。算是吧。第一个实现`XMLHttpRequest`在 1999 年作为 IE5.0 ActiveX 组件*(不要问)*发布。在那之前，有很多方法可以在不刷新整个页面的情况下从服务器上获取数据，但是它们通常依赖于笨拙的技术，比如`<script>`注入或者第三方插件。微软开发了`XMLHttpRequest` primary 来替代他们的 Outlook 电子邮件客户端。

直到 2006 年，它还不是一个 web 标准，但是它已经在大多数浏览器中实现了。它在 Gmail (2004 年)和 Google Maps (2005 年)中的采用导致了 Jesse James Garrett 在 2005 年的文章[AJAX:Web 应用的新方法](https://adaptivepath.org/ideas/ajax-new-approach-web-applications/)。这个新术语明确了开发者的关注点。

## Ajax 到 AJAX

AJAX 是异步 JavaScript 和 XML 的助记符。“异步”是肯定的，但是:

1.  JavaScript 是可能的，尽管 VBScript 和 Flash 是选项
2.  有效载荷不一定是 XML，尽管当时很流行。任何数据格式都可以使用，如今，JSON 通常是首选。

我们现在使用“Ajax”作为任何客户端进程的通用术语，它从服务器获取数据并动态更新 DOM，而无需刷新整个页面。Ajax 是大多数 web 应用程序和单页面应用程序(spa)的核心技术。

## 极限 XMLHttpRequest

以下 JavaScript 代码显示了使用 [`XMLHttpRequest`](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) (通常简称为 XHR)对`http://domain/service`的基本 HTTP GET 请求:

```
let xhr = new XMLHttpRequest();
xhr.open('GET', 'http://domain/service');

// request state change event
xhr.onreadystatechange = function() {

  // request completed?
  if (xhr.readyState !== 4) return;

  if (xhr.status === 200) {
    // request successful - show response
    console.log(xhr.responseText);
  }
  else {
    // request error
    console.log('HTTP error', xhr.status, xhr.statusText);
  }
};

// start request
xhr.send(); 
```

[`XMLHttpRequest`对象](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest)有许多其他选项、事件和响应属性。例如，可以设置和检测以毫秒为单位的超时:

```
// set timeout
xhr.timeout = 3000; // 3 seconds
xhr.ontimeout = () => console.log('timeout', xhr.responseURL); 
```

一个`progress`事件可以报告长时间运行的文件上传:

```
// upload progress
xhr.upload.onprogress = p => {
  console.log( Math.round((p.loaded / p.total) * 100) + '%') ;
} 
```

选项的数量令人困惑，早期的`XMLHttpRequest`实现有一些跨浏览器的不一致。出于这个原因，大多数库和框架都提供了 Ajax 包装函数来处理复杂性，例如 [`jQuery.ajax()`](http://api.jquery.com/jquery.ajax/) 方法:

```
// jQuery Ajax
$.ajax('http://domain/service')
  .done(data => console.log(data))
  .fail((xhr, status) => console.log('error:', status)); 
```

## 快进提取

[获取 API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) 是`XMLHttpRequest`的现代替代品。通用的[头](https://developer.mozilla.org/en-US/docs/Web/API/Headers)、[请求](https://developer.mozilla.org/en-US/docs/Web/API/Request)和[响应](https://developer.mozilla.org/en-US/docs/Web/API/Response)接口提供了一致性，同时承诺允许更容易的链接和异步/等待，而无需回调。上面的 XHR 例子可以转换成更简单的基于 Fetch 的代码，甚至可以解析返回的 JSON:

```
fetch(
    'http://domain/service',
    { method: 'GET' }
  )
  .then( response => response.json() )
  .then( json => console.log(json) )
  .catch( error => console.error('error:', error) ); 
```

Fetch 干净、优雅、更简单易懂，在 [PWA 服务人员](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API)中大量使用。*你为什么不用它来代替古老的 XMLHttpRequest？*

不幸的是，web 开发从来都不是那么清晰的。Fetch 还不能完全替代 Ajax 技术…

### 浏览器支持

Fetch API[得到了相当好的支持](https://caniuse.com/#search=fetch)，但是它在所有版本的 Internet Explorer 中都将失败。使用 2017 年以前版本的 Chrome、Firefox 和 Safari 的人也可能会遇到问题。这些用户可能在你的用户群中只占很小一部分……也可能是大客户。*在你开始编码之前一定要检查！*

此外，Fetch API 比成熟的 XHR 对象更新，并且接收更多正在进行的更改。这些更新不太可能破坏代码，但预计未来几年将进行一些维护工作。

### 默认无烹饪

与`XMLHttpRequest`不同，并非所有 Fetch 的实现都会发送 cookies，因此您的应用程序的认证可能会失败。这个问题可以通过改变第二个参数中传递的[初始化选项](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)来解决，例如

```
fetch(
    'http://domain/service',
    {
      method: 'GET',
      credentials: 'same-origin'
    }
  ) 
```

### 错误不会被拒绝

令人惊讶的是，像`404 Page Not Found`或`500 Internal Server Error`这样的 HTTP 错误不会导致 Fetch Promise 被拒绝；这个`.catch()`是绝对不会跑的。它通常会在`response.ok`状态设置为假的情况下解决。

只有当请求无法完成时，才会发生拒绝，例如网络故障。这使得错误捕获的实现更加复杂。

### 不支持超时

Fetch 不支持超时，只要浏览器选择，请求就会继续。需要更多的代码来将提取包装在另一个承诺中，例如

```
// fetch with a timeout
function fetchTimeout(url, init, timeout = 3000) {
  return new Promise((resolve, reject) => {
    fetch(url, init)
      .then(resolve)
      .catch(reject);
    setTimeout(reject, timeout);
  }
} 
```

…或者使用 [`Promise.race()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/race) 来解析何时先完成获取或超时，例如

```
Promise.race([
  fetch('http://url', { method: 'GET' }),
  new Promise(resolve => setTimeout(resolve, 3000))
])
  .then(response => console.log(response)) 
```

### 中止提取

用`xhr.abort()`结束 XHR 请求是很容易的，如果有必要，用`xhr.onabort`函数检测这样的事件。

中止一个获取在几年内是不可能的，但是现在在实现了 [AbortController API](https://developer.mozilla.org/en-US/docs/Web/API/AbortController) 的浏览器中支持了。这触发了一个信号，该信号可以被传递给获取启动对象:

```
const controller = new AbortController();

fetch(
  'http://domain/service',
  {
    method: 'GET'
    signal: controller.signal
  })
  .then( response => response.json() )
  .then( json => console.log(json) )
  .catch( error => console.error('Error:', error) ); 
```

通过调用`controller.abort();`可以中止提取。Promise 拒绝了，所以调用了`.catch()`函数。

### 没有进展

在撰写本文时，Fetch 还不支持进度事件。因此，无法报告文件上传或类似大型表单提交的状态。

## XMLHttpRequest vs Fetch API？

最终，选择权在你… *除非你的应用程序有 IE 客户端要求上传进度条*。

对于更简单的 Ajax 调用来说，`XMLHttpRequest`是更低级、更复杂的，您将需要包装器函数。不幸的是，一旦您开始考虑超时、调用中止和错误捕获的复杂性，so 将获取。

你可以选择 [Fetch polyfill](https://github.github.io/fetch/) 结合 [Promise polyfill](https://www.npmjs.com/package/promise-polyfill) ，这样就可以在 IE 中编写 Fetch 代码。然而，XHR 被用作退路；并非每个选项都能按预期工作，例如，无论设置如何，都会发送 cookies。

Fetch 是未来。然而，该 API 相对较新，它不提供所有的 XHR 功能，而且有些选项很麻烦。以后几年慎用。

## 分享这篇文章