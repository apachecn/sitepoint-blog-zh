# Axios 初学者指南:一个方便的基于 Promise 的 HTTP 客户端

> 原文：<https://www.sitepoint.com/axios-beginner-guide/>

**Axios 是一个流行的、[基于 promise 的 HTTP 客户端](https://www.npmjs.com/package/axios)，它拥有一个易于使用的 API，可以在浏览器和 Node.js 中使用。**

发出获取或保存数据的 HTTP 请求是客户端 JavaScript 应用程序需要完成的最常见的任务之一。第三方库——尤其是 jQuery——长期以来一直是与更冗长的浏览器 API 交互的流行方式，并抽象出任何跨浏览器的差异。

随着人们不再使用 jQuery，转而使用改进的原生 DOM APIs，或者 React 和 Vue.js 这样的前端 UI 库，仅仅因为它的`$.ajax`功能而包含它已经没有什么意义了。

让我们看看如何开始在代码中使用 Axios，并了解一些有助于它在 JavaScript 开发人员中流行的特性。

## Axios 与获取

你可能知道，现代浏览器内置了更新的[获取 API](https://www.sitepoint.com/introduction-to-the-fetch-api/) ，那么为什么不直接使用它呢？这两者之间有几个不同之处，许多人认为这是 Axios 的优势。

其中一个区别是这两个库如何处理 HTTP 错误码。当使用 Fetch 时，如果服务器返回 4xx 或 5xx 系列错误，您的`catch()`回调将不会被触发，而是由开发人员检查响应状态代码来确定请求是否成功。另一方面，如果返回其中一个状态代码，Axios 将拒绝请求承诺。

另一个经常让不熟悉 API 的开发人员出错的小差异是，Fetch 在发出请求时不会自动将 cookies 发送回服务器。有必要显式地传递一个选项来包含它们。Axios 在这里支持你。

对一些人来说，一个可能成为阻碍的区别是上传/下载的进度更新。由于 Axios 是建立在旧的 XHR API 之上的，你可以为`onUploadProgress`和`onDownloadProgress`注册回调函数，以在你的应用程序的 UI 中显示完成的百分比。目前，Fetch 不支持这样做。

最后，Axios 可以在浏览器和 Node.js 中使用。这有助于在浏览器和后端之间共享 JavaScript 代码，或者在服务器端呈现前端应用程序。

*注意:节点有不同版本的 [Fetch API，但是在我看来，Axios 提供的其他特性给了它优势。](https://www.npmjs.com/package/isomorphic-fetch)*

## 安装

正如您所料，安装 Axios 最常见的方式是通过 npm 软件包管理器:

```
npm i axios 
```

并在需要的地方将它包含在代码中:

```
// ES2015 style import
import axios from 'axios';

// Node.js style require
const axios = require('axios'); 
```

如果您没有使用某种模块捆绑器(例如 webpack ),那么您总是可以通过传统方式从 CDN 拉入库:

```
<script src="https://unpkg.com/axios/dist/axios.min.js"></script> 
```

### 浏览器支持

Axios 可以在所有现代网络浏览器和 Internet Explorer 8+中运行。

## 提出请求

类似于 jQuery 的`$.ajax`函数，您可以通过向 Axios 传递一个 options 对象来发出任何类型的 HTTP 请求:

```
axios({
  method: 'post',
  url: '/login',
  data: {
    user: 'brunos',
    lastName: 'ilovenodejs'
  }
}); 
```

这里，我们告诉 Axios 我们想要使用哪种 HTTP 方法(例如 GET/POST/DELETE 等。)以及应该向哪个 URL 发出请求。

我们还提供了一些数据，以简单的键/值对 JavaScript 对象的形式随请求一起发送。默认情况下，Axios 会将其序列化为 JSON，并作为请求体发送。

### 请求选项

在提出请求时，你可以通过一大堆[附加选项](https://github.com/axios/axios#request-config)，但这里是最常见的:

*   如果你指定了一个基本 URL，它将被添加到你使用的任何相对 URL 的前面。
*   `headers`:作为头发送的键/值对的对象。
*   `params`:一个键/值对的对象，它将被序列化并作为查询字符串附加到 URL 上。
*   `responseType`:如果您希望得到 JSON 之外格式的响应，您可以将该属性设置为`arraybuffer`、`blob`、`document`、`text`或`stream`。
*   `auth`:传递带有`username`和`password`字段的对象将在请求中使用这些凭证进行 HTTP 基本验证。

### 便利方法

也像 jQuery 一样，有一些快捷的方法来执行不同类型的请求。

`get`、`delete`、`head`和`options`方法都有两个参数:一个 URL 和一个可选的配置对象。

```
axios.get('/products/5'); 
```

`post`、`put`和`patch`方法将一个数据对象作为第二个参数，将一个可选的配置对象作为第三个参数:

```
axios.post(
  '/products',
  { name: 'Waffle Iron', price: 21.50 },
  { options }
); 
```

## 收到回应

一旦您发出请求，Axios 将返回一个承诺，该承诺将解析为一个响应对象或一个错误对象。

```
axios.get('/product/9')
  .then(response => console.log(response))
  .catch(error => console.log(error)); 
```

### 响应对象

当请求成功时，您的`then()`回调将接收一个具有以下属性的响应对象:

*   `data`:服务器返回的有效载荷。默认情况下，Axios 需要 JSON，并将它解析成 JavaScript 对象。
*   `status`:服务器返回的 HTTP 代码。
*   `statusText`:服务器返回的 HTTP 状态消息。
*   `headers`:服务器发回的所有头。
*   `config`:原始请求配置。
*   `request`:实际的`XMLHttpRequest`对象(在浏览器中运行时)。

### 错误对象

如果请求有问题，承诺将被一个错误对象拒绝，该对象至少包含以下一些属性:

*   `message`:错误信息文本。
*   `response`:上一节所述的响应对象(如果收到)。
*   `request`:实际的`XMLHttpRequest`对象(在浏览器中运行时)。
*   `config`:原始请求配置。

## 转换和拦截器

Axios 提供了一些受 [Angular 的$http 库](https://docs.angularjs.org/api/ng/service/%24http)启发的简洁功能。尽管它们看起来相似，但它们的用例略有不同。

### 转换

Axios 允许您以两种配置选项的形式提供转换输出或输入数据的功能，您可以在发出请求时设置这两种配置选项:`transformRequest`和`transformResponse`。这两个属性都是数组，允许您将数据将通过的多个函数链接起来。

传递给`transformRequest`的任何函数都应用于 PUT、POST 和 PATCH 请求。它们接收请求数据和作为参数的 headers 对象，并且必须返回修改后的数据对象

```
const options = {
  transformRequest: [
    (data, headers) => {
      // do something with data
      return data;
    }
  ]
} 
```

函数可以以同样的方式添加到`transformResponse`中，但是只能用响应数据调用，并且在响应被传递给任何链接的`then()`回调之前。

那么我们可以用变换来做什么呢？一个潜在的用例是处理需要特定格式数据的 API 比如 XML 甚至 CSV。您可以设置一对转换，将传出和传入的数据转换成 API 要求的格式。

值得注意的是，Axios 的默认`transformRequest`和`transformResponse`函数在 JSON 之间来回转换数据，指定您自己的转换将覆盖这些。

### 截击机

虽然 transforms 允许您修改传出和传入的数据，但 Axios 也允许您添加名为[拦截器](https://github.com/axios/axios#interceptors)的函数。像转换一样，当发出请求或收到响应时，这些函数可以附加到 fire。

```
// Add a request interceptor
axios.interceptors.request.use((config) => {
    // Do something before request is sent
    return config;
  }, (error) => {
    // Do something with request error
    return Promise.reject(error);
  });

// Add a response interceptor
axios.interceptors.response.use((response) => {
    // Do something with response data
    return response;
  }, (error) => {
    // Do something with response error
    return Promise.reject(error);
  }); 
```

从上面的例子中您可能已经注意到，拦截器与转换有一些重要的不同。拦截器不只是接收数据或头，而是接收完整的请求配置或响应对象。

在创建拦截器时，您还可以选择提供一个错误处理函数，该函数允许您捕捉任何错误并适当地处理它们。

请求拦截器可以用来做一些事情，例如[从本地存储中检索令牌，并与所有请求一起发送](https://www.npmjs.com/package/axios-token-interceptor)，而响应拦截器可以用来[捕捉 401 响应，并重定向到登录页面](https://gist.github.com/yajra/5f5551649b20c8f668aec48549ef5c1f)进行授权。

## 第三方加载项

作为一个流行的库，Axios 受益于扩展其功能的第三方库生态系统。从拦截器到测试适配器再到记录器，有相当多的种类可供选择。这里有一些我认为你可能会觉得有用的:

*   [mock-adaptor](https://github.com/ctimmerm/axios-mock-adapter) :允许您轻松地模拟请求，以便于测试您的代码。
*   [cache-plugin](https://github.com/jin5354/axios-cache-plugin) :选择性缓存 GET 请求的包装器。
*   redux-axios-middleware :如果你是一个 redux 用户，这个中间件提供了一种简洁的方式，用简单的老动作来调度 Ajax 请求。

Axios GitHub repo 上提供了更多[插件和扩展](https://github.com/axios/axios/blob/master/ECOSYSTEM.md)的列表。

综上，Axios 有很多值得推荐的地方。它有一个简单明了的 API，带有有用的快捷方法，以前使用过 jQuery 的人都很熟悉。它的受欢迎程度，以及各种第三方插件的可用性，使它成为包含在您的应用程序中的可靠选择，无论是前端还是后端，或者两者兼有。

## 分享这篇文章