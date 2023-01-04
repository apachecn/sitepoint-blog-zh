# Ajax/jQuery.getJSON 简单示例

> 原文：<https://www.sitepoint.com/ajaxjquery-getjson-simple-example/>

在本文中，我们将探讨 JSON 的重要性以及为什么我们应该在我们的应用程序中使用它。我们将看到 jQuery 为我们提供了一个非常好的[便利函数](https://api.jquery.com/jquery.getjson/)。

## JSON 是什么？

[JSON](https://www.json.org/) 代表**J**ava**S**script**O**object**N**旋转。它是一种独立于语言、基于文本的格式，通常用于在 web 应用程序中传输数据。在本文中，我们将研究如何使用 HTTP GET 请求加载 JSON 数据(我们也可以使用其他动词，比如 POST)。

为什么我们会选择 JSON 而不是 XML 呢？使用 JSON 的关键优势是效率。JSON 不那么冗长和混乱，导致更少的字节和更快的解析过程。这使我们能够处理更多以 JSON 形式而不是 XML 形式发送的消息。此外，JSON 有一个非常有效和自然的对象表示，导致像 [BSON](http://bsonspec.org/) 这样的格式，其中类似 JSON 的对象以二进制格式存储。

现在，让我们看看 jQuery 如何帮助我们从远程数据源加载 JSON 编码的数据。对于你们当中不耐烦的人来说，在文章的最后有一个演示。

## JSON jQuery 语法

如果您不需要太多额外的配置，那么`$.getJSON()`方法是直接使用 JSON 的便利助手。本质上，它归结为更一般的 [$。ajax()](http://api.jquery.com/jquery.ajax/) 助手，正确的选项被隐式使用。方法签名是:

```
$.getJSON(url, data, success); 
```

除了必需的 URL 参数，我们还可以传入两个可选参数。一个表示要发送到服务器的数据；另一个表示在成功响应的情况下触发的回调。

所以这三个参数对应于:

*   `url`参数，它是一个字符串，包含请求被发送到的 URL
*   可选的`data`参数，它可以是一个对象，也可以是一个随请求发送到服务器的字符串
*   可选的`success(data, textStatus, jqXHR)`参数，这是一个只有在请求成功时才执行的回调函数

在最简单的场景中，我们只关心返回的对象。在这种情况下，潜在的`success`回调如下所示:

```
function success(data) {
  // do something with data, which is an object
} 
```

如上所述，同样的请求可以通过更冗长的`$.ajax()`调用来触发。这里我们将使用:

```
$.ajax({
  dataType: 'json',
  url: url,
  data: data,
  success: success
}); 
```

让我们通过一个小演示来看看这一点。

## 示例应用程序

我们将启动一个服务于静态 JSON 文件的本地服务器。这个文件表示的对象将被我们的 JavaScript 代码获取和处理。出于演示的目的，我们将使用 Node.js 来提供服务器(尽管任何服务器都可以)。这意味着我们需要以下三样东西:

*   Node.js 的工作安装
*   节点程序包管理器(npm)
*   全球安装的[实时服务器包](https://www.npmjs.com/package/live-server)

前两点是平台相关的。要安装 Node，请前往该项目的[下载页面](https://nodejs.org/en/download/)并获取您系统的相关二进制文件。或者，您可以考虑使用版本管理器，如“[使用 nvm](https://www.sitepoint.com/quick-tip-multiple-versions-node-nvm/) 安装 Node.js 的多个版本”中所述。

npm 与 Node 捆绑在一起，所以不需要安装任何东西。但是，如果您需要任何帮助，请参考我们的教程“[NPM 初学者指南—节点包管理器](https://www.sitepoint.com/beginners-guide-node-package-manager/)”。

第三点可以通过从终端运行以下命令来实现:

```
npm install -g live-server 
```

如果您发现自己需要一个`sudo`前缀(-nix systems)或提升的命令提示符来执行这个全局安装，您应该考虑[改变全局包](https://www.sitepoint.com/beginners-guide-node-package-manager/#changing-the-location-of-global-packages)的位置。

满足这些要求后，我们可以在新文件夹中创建以下三个文件:

*   `main.js`，这是请求数据的 JavaScript 文件
*   `example.json`，这是示例 JSON 文件
*   `index.html`，这是调用 JavaScript 并显示数据的 HTML 页面

在命令提示符下，我们可以简单地在新文件夹中调用`live-server`。这将在一个新的浏览器标签中打开我们的演示，运行在 [http://localhost:8080](http://localhost:8080) 。

### 示例 JavaScript

下面的代码是完整的客户端逻辑。它等待`DOMContentLoaded` loaded 事件触发，然后获取对两个 DOM 元素的引用— `$showData`，其中我们将显示解析的响应，以及`$raw`，其中我们将显示完整的响应。

然后，我们将一个事件处理程序附加到 ID 为`get-data`的元素的`click`事件上。当点击这个元素时，在处理响应并在屏幕上显示之前，我们尝试使用`$.getJSON()`从服务器加载 JSON:

```
$(document).ready(() => {
  const $showData = $('#show-data');
  const $raw = $('pre');

  $('#get-data').on('click', (e) => {
    e.preventDefault();

    $showData.text('Loading the JSON file.');

    $.getJSON('example.json', (data) => {
      const markup = data.items
        .map(item => `<li>${item.key}: ${item.value}</li>`)
        .join('');

      const list = $('<ul />').html(markup);

      $showData.html(list);

      $raw.text(JSON.stringify(data, undefined, 2));
    });
  });
}); 
```

除了将部分对象转换为无序列表，整个对象也被[字符串化](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify)并显示在屏幕上。无序列表被添加到 ID 为`show-data`的`<div>`元素中，JSON 字符串是一个`<pre>`标签，因此它的格式很好。当然，对于我们的例子，数据是固定的，但是一般来说，任何类型的响应都是可能的。

注意，我们还为输出`<div>`设置了一些文本。如果我们为 JSON 检索插入一些(人为的)延迟(例如，在您浏览器的开发工具中)，我们将看到这实际上是在显示任何`$.getJSON`请求的结果之前执行的。原因很简单:默认情况下，`$.getJSON`是非阻塞的，也就是异步的。因此，回调将在稍后的某个(未知的)时间点执行。

提取源以获得关键信息会产生以下块:

```
$('#get-data').on('click', () => {
  $.getJSON('example.json', (data) => {
    console.log(data);
  });
}); 
```

这里，我们只在调试控制台中打印返回的对象之前连接链接来触发`$.getJSON`助手的启动。

### 示例 JSON

样本 JSON 文件比我们关心的子集要大得多。尽管如此，我们还是以展示大部分 JSON 语法的方式构建了这个示例。文件内容如下:

```
{
  "items": [
    {
      "key": "First",
      "value": 100
    },
    {
      "key": "Second",
      "value": false
    },
    {
      "key": "Last",
      "value": "Mixed"
    }
  ],
  "obj": {
    "number": 1.2345e-6,
    "enabled": true
  },
  "message": "Strings have to be in double-quotes."
} 
```

在示例 JavaScript 中，我们只处理与`items`键相关的数组。与普通的 JavaScript 不同，JSON 要求我们将键放在双引号中。此外，我们不能使用尾部逗号来指定对象或数组。然而，和普通的 JavaScript 数组一样，我们*被允许*插入不同类型的对象。

### 示例网页

我们已经看过了脚本和样本 JSON 文件。剩下的就是 web 页面，它提供了 JavaScript 文件用来触发和显示 JSON 文件的部分:

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>Request JSON Test</title>
  </head>
  <body>
    <a href="#" id="get-data">Get JSON data</a>
    <div id="show-data"></div>

    <script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>
    <script src="main.js"></script>
  </body>
</html> 
```

这里不多说了。我们使用来自官方网页的 jQuery 的缩小版。然后我们包括我们的脚本，它负责注入逻辑。

*注意:因为我们将 JavaScript 文件放在了正确的位置(就在结束的`</body>`标签之前)，所以不再需要使用`$(document).ready()`回调，因为在这一点上，文档已经准备好了。*

## 演示

这就是我们的结局。

[https://codesandbox.io/embed/goofy-architecture-f12ln?fontsize=14&hidenavigation=1&theme=dark&view=preview](https://codesandbox.io/embed/goofy-architecture-f12ln?fontsize=14&hidenavigation=1&theme=dark&view=preview)

## 更一般的方法

如前所述，`$.ajax`方法对于任何(不仅仅是 JSON 相关的)web 请求都是真实的。这个方法允许我们显式地设置所有我们关心的选项。如果我们希望这个调用并发运行，我们可以将`async`调整为`true`——也就是说，可能与其他代码同时运行。将其设置为`false`将会阻止其他代码在下载过程中运行:

```
$.ajax({
  type: 'GET',
  url: filename,
  data: data,
  async: false,
  beforeSend: (xhr) => {
    if (xhr && xhr.overrideMimeType) {
      xhr.overrideMimeType('application/json;charset=utf-8');
    }
  },
  dataType: 'json',
  success: (data) => {
    //Do stuff with the JSON data
  }
}); 
```

调用`overrideMimeType`方法(覆盖服务器返回的 MIME 类型)只是为了演示。通常，jQuery 足够智能，可以根据所使用的数据类型调整 MIME 类型。

在我们继续介绍 JSON 验证的概念之前，让我们先看一个更现实的例子。通常，我们不会请求一个静态的 JSON 文件，但是会加载 JSON，它是动态生成的(例如，作为调用一个 API 的结果)。JSON 的生成依赖于一些参数，这些参数必须事先提供:

```
const url = 'https://api.github.com/v1/...';
const data = {
  q: 'search',
  text: 'my text'
};

$.getJSON(url, data, (data, status) => {
  if (status === 200) {
    //Do stuff with the JSON data
  }
}); 
```

在这里，我们检查状态以确保结果确实是从一个成功的*请求返回的对象，而不是包含错误消息的对象。确切的状态代码取决于 API，但是对于大多数 GET 请求，状态代码 200 是常见的。*

数据以对象的形式提供，这就把创建查询字符串(或传输请求体)的任务留给了 jQuery。这是最好最可靠的选择。

## JSON 验证

我们不应该忘记验证我们的 JSON 数据！有一个名为 JSONLint 的在线 JSON 验证工具可以用来验证 JSON 文件。与 JavaScript 不同，JSON 非常严格，没有公差——例如，对于前面提到的尾随逗号或多种写法的键(带`/`，不带引号)。

因此，让我们讨论一些处理 JSON 时最常见的错误。

### 普通美元。getJSON 错误

*   `$.getJSON`通话中的无声故障。例如，如果使用了`jsoncallback=json1234`，而函数`json1234()`不存在，就会出现这种情况。在这样的情况下，`$.getJSON`会无声无息地出错。因此，我们应该始终使用`jsoncallback=?`让 jQuery 自动处理初始回调。
*   在最好的情况下，使用真正的 JSON(而不是 [JSONP](https://en.wikipedia.org/wiki/JSONP) )(要么通过与我们自己的服务器对话，要么通过 <abbr title="cross-origin resource sharing">CORS</abbr> )。这消除了使用 JSONP 可能引入的一系列错误。关键问题是:服务器/ API 支持 JSONP 吗？使用 JSONP 有什么限制吗？你可以在这里找到更多关于与 JSONP [合作的信息。](https://www.sitepoint.com/jsonp-examples/)
*   `Uncaught syntax error: Unexpected token`(Chrome 中)或者`invalid label`(Firefox 中)。后者可以通过将 JSON 数据传递给 JavaScript 回调来修复。然而，总的来说，这是 JSON 格式错误的一个强有力的标志。如上所述，考虑使用 [JSONLint](https://jsonlint.com/) 。

现在的大问题是:我们如何检测错误是否真的存在于传输的 JSON 中？

### 如何修复 JSON 错误

在开始任何与 JSON 相关的调试之前，应该包括三个要点:

*   我们必须确保服务器返回的 JSON 格式正确，使用了正确的 MIME 类型。
*   我们可以尝试使用[美元。获取](http://api.jquery.com/jquery.get/)而不是`$.getJSON`，因为我们的服务器可能会返回无效的 JSON。此外，如果`JSON.parse()`在返回的文本上失败，我们立即知道 JSON 是罪魁祸首。
*   我们可以通过将数据记录到控制台来检查返回的数据。这应该是进一步调查的输入。

然后应该用前面提到的 JSONLint 工具开始调试。

## 结论

JSON 是交换文本数据的事实上的标准格式。jQuery 的`$.getJSON()`方法给了我们一个很好的小助手来处理几乎所有涉及 JSON 格式数据请求的场景。在本文中，我们研究了这个方便的助手提供的一些方法和可能性。

如果你需要帮助在你的代码中实现 JSON 抓取(使用`$.getJSON()`或其他任何东西)，来访问我们的 [SitePoint 论坛](https://www.sitepoint.com/community/)。

## 分享这篇文章