# Fetch API 简介

> 原文：<https://www.sitepoint.com/introduction-to-the-fetch-api/>

**在本文中，我们将了解新的 Fetch API 是什么样子的，它解决了什么问题，以及使用`fetch()`函数在 web 页面中检索远程数据的最实用的方法。**

多年来， [XMLHttpRequest](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) 一直是 web 开发人员值得信赖的伙伴。不管是直接的还是间接的，`XMLHttpRequest`已经实现了 Ajax 和一种全新的交互体验，从 Gmail 到脸书。

然而，`XMLHttpRequest`正在慢慢被[获取 API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) 所取代。两者都可以用来发出网络请求，但是 Fetch API 是基于承诺的，这使得语法更干净、更简洁，并且有助于避免[回调地狱](http://callbackhell.com/)。

## 获取 API

Fetch API 提供了一个在`window`对象上定义的`fetch()`方法，您可以使用它来执行请求。这个方法返回一个[承诺](https://www.sitepoint.com/overview-javascript-promises/)，您可以用它来检索请求的响应。

`fetch`方法只有一个强制参数，即您希望获取的资源的 URL。一个非常基本的例子如下所示。这从 Reddit 上的 [r/javascript 获取了前五个帖子:](https://www.reddit.com/r/javascript)

```
fetch('https://www.reddit.com/r/javascript/top/.json?limit=5')
.then(res => console.log(res)); 
```

如果您在浏览器的控制台中检查响应，您应该看到一个具有几个属性的`Response`对象:

```
{
  body: ReadableStream
  bodyUsed: false
  headers: Headers {}
  ok: true
  redirected: false
  status: 200
  statusText: ""
  type: "cors"
  url: "https://www.reddit.com/top/.json?count=5"
} 
```

看起来请求成功了，但是我们的前五个帖子在哪里呢？让我们找出答案。

## 正在加载 JSON

我们不能阻止用户界面等待请求完成。这就是为什么`fetch()`返回一个`Promise`，一个代表未来结果的对象。在上面的例子中，我们使用`then`方法来等待服务器的响应并将其记录到控制台。

现在让我们看看一旦请求完成，如何从响应中提取 JSON 有效负载:

```
fetch('https://www.reddit.com/r/javascript/top/.json?limit=5')
.then(res => res.json())
.then(json => console.log(json)); 
```

我们通过调用`fetch()`开始请求。当承诺实现时，它返回一个`Response`对象，该对象公开了一个`json`方法。在第一个`then()`中，我们可以调用这个`json`方法以 JSON 的形式返回响应体。

然而，`json`方法也返回一个承诺，这意味着在 JSON 响应被记录到控制台之前，我们需要链接另一个`then()`。

而`json()`为什么要回心转意呢？因为 HTTP 允许您将内容逐块地传输到客户机，所以即使浏览器收到来自服务器的响应，内容体也可能还没有全部到达！

### 异步…等待

`.then()`语法很好，但 2018 年处理承诺的一种更简洁的方式是使用`async … await`——es 2017 引入的一种新语法。使用`async Ú await`意味着我们可以将一个函数标记为`async`，然后等待承诺用`await`关键字完成，并以普通对象的形式访问结果。所有现代浏览器(不是 IE 或 Opera Mini)和 Node.js 7.6+都支持异步函数。

下面是上面的例子使用`async … await`后的样子(稍微扩展了一下):

```
async function fetchTopFive(sub) {
  const URL = `https://www.reddit.com/r/${sub}/top/.json?limit=5`;
  const fetchResult = fetch(URL)
  const response = await fetchResult;
  const jsonData = await response.json();
  console.log(jsonData);
}

fetchTopFive('javascript'); 
```

没什么变化。除了我们已经创建了一个`async`函数，并向其传递 subreddit 的名称，我们现在正在等待调用`fetch()`的结果，然后再次使用`await`从响应中检索 JSON。

这是基本的工作流程，但是涉及远程服务的事情并不总是一帆风顺。

## 处理错误

假设我们向服务器请求一个不存在的资源或者一个需要授权的资源。使用`fetch()`，您必须处理正常流程中的应用程序级错误，比如 404 响应。正如我们之前看到的，`fetch()`返回一个具有`ok`属性的`Response`对象。如果`response.ok`为`true`，响应状态码在 200 范围内:

```
async function fetchTopFive(sub) {
  const URL = `http://httpstat.us/404`; // Will return a 404
  const fetchResult = fetch(URL)
  const response = await fetchResult;
  if (response.ok) {
    const jsonData = await response.json();
    console.log(jsonData);
  } else {
    throw Error(response.statusText);
  }
}

fetchTopFive('javascript'); 
```

来自服务器的响应代码的含义因 API 而异，通常检查`response.ok`可能还不够。例如，即使您的 API 密钥无效，一些 API 也会返回 200 响应。请务必阅读 API 文档！

要处理网络故障，使用`try … catch`块:

```
async function fetchTopFive(sub) {
  const URL = `https://www.reddit.com/r/${sub}/top/.json?limit=5`;
  try {
    const fetchResult = fetch(URL)
    const response = await fetchResult;
    const jsonData = await response.json();
    console.log(jsonData);
  } catch(e){
    throw Error(e);
  }
}

fetchTopFive('javvascript'); // Notice the incorrect spelling 
```

只有当网络出错时，`catch`块中的代码才会运行。

你已经学习了提出请求和阅读回复的基本知识。现在让我们进一步定制请求。

## 更改请求方法和标题

看着上面的例子，你可能想知道为什么不能使用一个现有的 XMLHttpRequest 包装器。原因是 fetch API 为您提供的不仅仅是`fetch()`方法。

虽然您必须使用同一个`XMLHttpRequest`实例来执行请求和检索响应，但是 fetch API 允许您显式地配置请求对象。

例如，如果你需要改变`fetch()`发出请求的方式(例如配置请求方法)，你可以将一个`Request`对象传递给`fetch()`函数。`Request`构造函数的第一个参数是请求 URL，第二个参数是配置请求的选项对象:

```
async function fetchTopFive(sub) {
  const URL = `https://www.reddit.com/r/${sub}/top/.json?limit=5`;
  try {
    const fetchResult = fetch(
      new Request(URL, { method: 'GET', cache: 'reload' })
    );
    const response = await fetchResult;
    const jsonData = await response.json();
    console.log(jsonData);
  } catch(e){
    throw Error(e);
  }
}

fetchTopFive('javascript'); 
```

这里，我们指定了请求方法，并要求它永远不要缓存响应。

您可以通过将一个`Headers`对象分配给请求`headers`字段来更改请求头。以下是如何只通过`'Accept'`头请求 JSON 内容:

```
const headers = new Headers();
headers.append('Accept', 'application/json');
const request = new Request(URL, { method: 'GET', cache: 'reload', headers: headers }); 
```

您可以从一个旧的请求中创建一个新的请求，为不同的用例进行调整。例如，您可以从对同一资源的 GET 请求创建 POST 请求。这里有一个例子:

```
const postReq = new Request(request, { method: 'POST' }); 
```

您也可以访问响应头，但是要记住它们是只读值。

```
fetch(request).then(response => console.log(response.headers)); 
```

`Request`和`Response`紧跟 HTTP 规范；如果您曾经使用过服务器端语言，您应该能够识别它们。如果你有兴趣了解更多，你可以在 MDN 的[获取 API 页面上阅读所有关于它们的内容。](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)

## 将这一切结合在一起

为了完善本文，这里有一个可运行的例子，演示了如何从一个特定的子编辑中获取前五篇文章，并在一个列表中显示它们的详细信息。

在 [CodePen](https://codepen.io) 上通过 SitePoint ( [@SitePoint](https://codepen.io/SitePoint) )查看 Pen [获取 API 演示](https://codepen.io/SitePoint/pen/jzOopo/)。