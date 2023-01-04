# 浏览器的 JavaScript HTTP 库的比较

> 原文：<https://www.sitepoint.com/comparison-javascript-http-libraries/>

Ajax 请求在现代网站中被大量使用。除了使用内置的方式来执行 Ajax 请求，使用一个`XMLHttpRequest`实例，许多开发人员使用 jQuery 的方法来执行相同的任务。

在本文中，我们将看看两个可供选择的库，它们可以让您实现相同的目标。特别是，我将介绍 [superagent](https://github.com/visionmedia/superagent) 和 [axios](https://github.com/mzabriskie/axios) 。为了比较它们，我们将对一个虚拟 HTTP 服务执行几个请求。

## 图书馆简介

一个`XMLHttpRequest`实例可以执行同步或异步请求。JavaScript 是单线程的，所以同步请求会阻塞其他操作的执行。因此，我们在实践中从不使用它们。本文中涉及的库 axios 和 superagent 总是执行异步请求。由于浏览器在后台执行请求时，您的代码会一直运行，因此您无法立即获得响应。你必须提供一个函数在服务器响应准备好时调用，通常称为*回调*，它将处理响应。

为了处理这个过程，axios 返回一个[承诺](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)。相反，superagent 的 API 不遵循任何标准。所以，关于这个特性，我们可以说 axios 比 superagent 更好。这样做的好处是，您可以将 axios 返回的承诺与其他库的承诺或您使用内置 JavaScript 对象创建的承诺结合起来。如果你需要对承诺的概述，你可以阅读[这篇教程](https://www.sitepoint.com/overview-javascript-promises/)。

另一方面，superagent 更加知名，它周围有一个小型插件生态系统(例如， [superagent prefix](https://github.com/johntron/superagent-prefix) 为所有 URL 添加前缀)。

当您希望使用基本的 POST/PUT/GET 功能与 API 通信时，Axios 和 superagent 是最合适的。例如，它们不支持像现代浏览器中的`XMLHttpRequest`那样监控上传进度。它们的主要优点是，您可以通过链接几个函数调用来配置和发送请求。

## 如何安装库

`XMLHttpRequest`不需要安装。从 Internet Explorer 8 开始，所有现代浏览器都实现了`XHMLHttpRequest`。superagent 作为一个 [npm 模块](http://www.npmjs.com)和一个[组件模块](https://github.com/componentjs/component)分发。要使用 npm 安装 superagent，您需要先安装它。npm 附带了标准的 [Node.js](https://node.js.org) 或 [io.js](https://iojs.org/) 安装程序，所以如果你已经安装了 Node.js/io.js，你应该准备好了，或者你应该安装你喜欢的任何一个。接下来，您需要使用客户端打包解决方案，如 [browserify](http://browserify.org/) 将`superagent`包含在您的文件中。如果你对它没有任何经验，SitePoint 发布了一个[教程，讨论 browserify](https://www.sitepoint.com/getting-started-browserify/) 的特性。

axios 作为 npm 模块、AMD 模块以及导出全局`axios`对象的普通 JavaScript 文件分发。你可以从 [axios Githib 库](https://github.com/mzabriskie/axios)下载它们。

## 示例 API

我们的示例 API 是一个面包店的订单管理服务。通过执行如下所示的 GET 请求，客户端可以按日期搜索订单:

```
/orders?start=2015-03-23&end=2015-03-24
```

客户还可以通过执行 POST 请求来创建新订单:

```
/orders
```

在 GET 请求的情况下，服务器返回的数据和网站发送的用于创建新订单的数据都是使用 JSON 格式提供的。例如，如果当前日期是 5 月 4 日，客户需要 3 个巧克力蛋糕和 5 个柠檬蛋糕在 3 月 10 日交货，您可以发送如下的 JSON 对象:

```
{
  "chocolate": "3",
  "lemon": "5",
  "delivery": "2015-03-10",
  "placed": "2015-03-04"
}
```

我们的目标是创建一个新订单，并检索在特定日期范围内的订单。

## 创建新秩序

要创建订单，我们需要指定:

1.  请求方法(POST)
2.  请求 URL ( `/orders`)
3.  请求体(JSON 格式的订单细节)
4.  请求正文内容类型(`application/json`)

指定正文内容类型是一个很好的实践:服务器可以根据请求头确定如何解析正文。

我们为每个配置参数配置一个具有专用方法的 superagent 请求，然后调用`end()`发送请求。`end()`函数将回调作为参数来处理响应。如果第一个回调参数为 the，则发生错误。让我们看一个例子:

```
var request = require('superagent');

request.post('/orders/')
  .send({'chocolate': 2, 'placed': '2015-04-26'})
  .type('application/json')
  .accept('json')
  .end(function(err, res) {
    if (err) {
      console.log('Error!');
    } else {
      console.log(res.body);
    }
  });
```

axios 将 URL 作为第一个参数，将请求体作为第二个参数，将所有剩余的配置作为第三个参数。为了处理响应，您可以指定两个回调:一个用于成功情况(我将把它传递给`then`)，一个用于错误情况(我将把它传递给`catch`)。下面显示了一个使用示例:

```
axios.post(
  '/orders/',
  {
    chocolate: 2,
	placed: '2015-04-26'
  },
  {
    headers:{
      'Content-type': 'application/json',
      'Accept': 'application/json'
    }
  }
)
  .then(function(response) {
    console.log(response.data);
  })
  .catch(function(response) {
    console.log('Error!)
  });
```

为了使用`XMLHttpRequest`达到同样的目的，我们在`open()`函数中配置 HTTP 方法和 URL。然后，我们将一个响应回调附加到`onload`处理程序，并调用`send`来发送请求。下面显示了一个使用示例:

```
var xhr = new XMLHttpRequest();

// The last parameter must be set to true to make an asynchronous request
xhr.open('POST', '/orders/', true);

xhr.setRequestHeader('Content-type', 'application/json');
xhr.setRequestHeader('Accept', 'application/json');
xhr.onload = function() {
  if (xhr.status >= 200 && xhr.status < 300) {
    console.log(xhr.response);
  } else {
    console.log('Error !');
  }
};
xhr.send();
```

浏览器将响应数据附加到`XMLHttpRequest`对象本身。要知道是否发生了错误，我们必须分析响应状态，并以我们喜欢的方式管理它。在本例中，我记录了在成功状态下返回的响应(从 200 到 299)；字符串`'Error !'`否则。

## 基于日期范围检索订单

为了检索某个日期范围内的订单，我们将设置一个`start`和一个`end`查询参数。superagent `query()`函数接受一个包含查询参数及其值的对象:

```
request.get('/orders')
  .query({start: '2015-04-22', end: '2015-04-29'})
  .accept('json')
  .end(function(err, res) {
    if (err) {
      /* Handle error */
    }  else {
      /* Handle response */
	}
});
```

axios 使用配置对象中的`params`键:

```
axios.get(
  '/orders',
  {
      headers: {
        'Accept': 'application/json'
      },
      params: {
        start: '2015-04-22',
		end: '2015-04-29'
      }
  }
);
```

查询字符串不应包含某些字符，尤其是`&`、`=`和`?`。axios 和 superagent 都注意为您转义查询参数。

使用`XMLHttpRequest`，您需要在查询字符串的键值对上使用内置的`encodeURIComponent()`函数来手动转义组件。下面你可以找到一个如何做到这一点的例子，即使它对于这个特殊的例子来说是多余的:

```
var xhr = new XMLHttpRequest();
xhr.open('GET', '/orders?start=' +
		encodeURIComponent('2015-04-22') + '&end=' +
		encodeURIComponent('2015-04-29'), true);
```

## 推荐

虽然 axios 使用了 promises，但代码看起来与 superagent 几乎相同。使用 superagent，您可以通过专用方法设置内容类型，但使用 axios 时，您需要小心避免标题名称中的输入错误。

superagent 和 axios 会根据内容类型自动反序列化请求正文。您可以通过在`XMLHttpRequest`实例上设置`responseType`属性来实现相同的目标。它们还将所有非 200 响应作为错误处理，而`XMLHttpRequest`通过允许您管理状态代码并相应地采取行动，提供了更大的灵活性。

superagent 和 axios 也可以在服务器上运行。然而，superagent 拥有我认为最令人愉快的 API。但如果你想要承诺，axios 是你最好的选择。最后一点，如果你能自己处理浏览器的差异，或者只是针对现代浏览器，`XMLHttpRequest`就可以了。

## 结论

在本文中，我们已经看到了如何使用 raw `XMLHttpRequest`、`axios`和`superagent`与简单的 REST API 进行通信。这三种解决方案提供了相同的功能，但各有特点。如果你使用现代浏览器，你可以依靠`XMLHttpRequest`。如果您希望避免处理旧版本的 Internet Explorer 并在服务器上使用相同的 API，那么`superagent`和`axios`非常方便。

如果您想使用本文中显示的代码，可以访问我创建的的 [GitHub 库。](https://github.com/sitepoint-editors/http-browser-comparison)

## 分享这篇文章