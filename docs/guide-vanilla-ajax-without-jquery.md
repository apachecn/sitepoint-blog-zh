# 没有 jQuery 的普通 Ajax 指南

> 原文：<https://www.sitepoint.com/guide-vanilla-ajax-without-jquery/>

异步 JavaScript 和 XML 的缩写， [Ajax](https://en.wikipedia.org/wiki/Ajax_%28programming%29) 是一种进行部分页面更新的机制。它使您能够用来自服务器的数据更新页面的各个部分，同时避免了完全刷新的需要。以这种方式进行部分更新可以有效地创建流畅的用户体验，并可以减少服务器上的负载。

这是一个基本 Ajax 请求的剖析:

```
var xhr = new XMLHttpRequest();
xhr.open('GET', 'send-ajax-data.php');
xhr.send(null); 
```

这里，我们创建了一个必需类的实例，向服务器发出 HTTP 请求。然后我们调用它的`open`方法，将 HTTP 请求方法指定为第一个参数，将我们请求的页面的 URL 指定为第二个参数。最后，我们调用它的`send`方法，将 null 作为参数传递。如果发送请求(这里我们使用 GET)，这个参数应该包含我们希望随请求一起发送的任何数据。

这就是我们如何处理来自服务器的响应:

```
xhr.onreadystatechange = function () {
  var DONE = 4; // readyState 4 means the request is done.
  var OK = 200; // status 200 is a successful return.
  if (xhr.readyState === DONE) {
    if (xhr.status === OK) {
      console.log(xhr.responseText); // 'This is the returned text.'
    } else {
      console.log('Error: ' + xhr.status); // An error occurred during the request.
    }
  }
}; 
```

`onreadystatechange`是异步的，这意味着它可以随时被调用。这些类型的函数是回调函数——一旦某个处理完成，就会调用回调函数。在这种情况下，处理发生在服务器上。

对于那些希望学习更多 Ajax 基础知识的人来说，MDN network 有一个很好的指南。

## 到 jQuery 还是不到 jQuery？

所以，好消息是上面的代码可以在所有最新的主流浏览器上运行。坏消息是，这很复杂。呸！我已经在渴望一个优雅的解决方案。

使用 jQuery，可以将整个代码片段压缩为:

```
$.ajax({
  url: 'send-ajax-data.php',
})
.done(function(res) {
  console.log(res);
})
.fail(function(err) {
  console.log('Error: ' + err.status);
}); 
```

这很好。事实上，对于很多人来说，包括你的朋友，jQuery 已经成为 Ajax 的事实标准。但是，你知道吗？不一定非要这样。jQuery 的存在是为了避开丑陋的 DOM API。但是，真的*丑吗？还是无法理解？*

在本文的剩余部分，我将研究普通 JavaScript 中对 Ajax API 的改进。完整的规范可以在 W3C 上找到。这个规范给我留下深刻印象的是它的名字。它不再是“XMLHttpRequest Level 2”，而是“XMLHttpRequest Level 1”——这是 2011 年两个规范合并的结果。展望未来，从标准的角度来看，它将被视为一个单一的实体，生活标准将被称为 [XMLHttpRequest](https://xhr.spec.whatwg.org/) 。这表明社区致力于坚持一个标准，这对于想要摆脱 jQuery 的开发人员来说是个好消息。

所以让我们开始吧…

## 设置

对于本文，我在后端使用了 [Node.js](https://nodejs.org/) 。是的，浏览器和服务器上会有 JavaScript。Node.js 后端是精益的，我鼓励你在 [GitHub](https://github.com/sitepoint-editors/VanillaAjaxNojQuery) 上下载完整的演示并跟随。以下是服务器上的主要内容:

```
// app.js
var app = http.createServer(function (req, res) {
  if (req.url.indexOf('/scripts/') >= 0) {
    render(req.url.slice(1), 'application/javascript', httpHandler);
  } else if (req.headers['x-requested-with'] === 'XMLHttpRequest') {
    // Send Ajax response
  } else {
    render('views/index.html', 'text/html', httpHandler);
  }
}); 
```

这将检查请求 URL，以确定应用程序应该如何响应。如果请求来自于`scripts`目录，那么适当的文件将使用`application/javascript`的内容类型。否则，如果请求的`x-requested-with`头被设置为`XMLHttpRequest`，那么我们知道我们正在处理一个 Ajax 请求，我们可以适当地响应。如果不是这两种情况，文件`views/index.html`送达。

当我们深入研究来自服务器的 Ajax 响应时，我将展开注释掉的部分。在 Node.js 中，我不得不使用`render`和`httpHandler`做一些繁重的工作:

```
// app.js
function render(path, contentType, fn) {
  fs.readFile(__dirname + '/' + path, 'utf-8', function (err, str) {
    fn(err, str, contentType);
  });
}
var httpHandler = function (err, str, contentType) {
  if (err) {
    res.writeHead(500, {'Content-Type': 'text/plain'});
    res.end('An error has occured: ' + err.message);
  } else {
    res.writeHead(200, {'Content-Type': contentType});
    res.end(str);
  }
}; 
```

`render`函数异步读取所请求文件的内容。它被传递一个对`httpHandler`函数的引用，然后作为回调函数执行。`httpHandler`函数检查错误对象的存在(例如，如果请求的文件无法打开，就会出现错误对象)。如果一切都好，那么它就用适当的 HTTP 状态代码和内容类型提供文件内容。

### 测试 API

像任何声音后端 API 一样，让我们编写一些单元测试来确保它能够工作。对于这些测试，我呼吁[超级测试](https://www.npmjs.com/package/supertest)和[摩卡](https://www.npmjs.com/package/mocha)的帮助:

```
// test/app.request.js
it('responds with html', function (done) {
  request(app)
    .get('/')
    .expect('Content-Type', /html/)
    .expect(200, done);
});
it('responds with javascript', function (done) {
  request(app)
    .get('/scripts/index.js')
    .expect('Content-Type', /javascript/)
    .expect(200, done);
});
it('responds with json', function (done) {
  request(app)
    .get('/')
    .set('X-Requested-With', 'XMLHttpRequest')
    .expect('Content-Type', /json/)
    .expect(200, done);
}); 
```

这些确保我们的应用程序用正确的内容类型和 HTTP 状态代码来响应不同的请求。一旦安装了依赖项，就可以使用`npm test`从命令中运行这些测试。

### 界面

现在，让我们来看看我们用 HTML 构建的用户界面:

```
// views/index.html
<h1>Vanilla Ajax without jQuery</h1>
<button id="retrieve" data-url="/">Retrieve</button>
<p id="results"></p> 
```

HTML 看起来漂亮整洁。如您所见，所有令人兴奋的事情都发生在 JavaScript 中。

## onreadonly 与 onload

如果你翻阅任何一本典型的 Ajax 书籍，你可能会发现`onreadystate`无处不在。这个回调函数带有完整的嵌套 if 和许多模糊的东西，让人很难马上记住。让我们把`onreadystate`和`onload`事件放在一起。

```
(function () {
  var retrieve = document.getElementById('retrieve'),
    results = document.getElementById('results'),
    toReadyStateDescription = function (state) {
      switch (state) {
      case 0:
        return 'UNSENT';
      case 1:
        return 'OPENED';
      case 2:
        return 'HEADERS_RECEIVED';
      case 3:
        return 'LOADING';
      case 4:
        return 'DONE';
      default:
        return '';
      }
    };
  retrieve.addEventListener('click', function (e) {
    var oReq = new XMLHttpRequest();
    oReq.onload = function () {
      console.log('Inside the onload event');
    };
    oReq.onreadystatechange = function () {
      console.log('Inside the onreadystatechange event with readyState: ' +
        toReadyStateDescription(oReq.readyState));
    };
    oReq.open('GET', e.target.dataset.url, true);
    oReq.send();
  });
}()); 
```

这是控制台中的输出:

![Screenshot of console output generated by onreadystate vs onload](img/a58a6849fa5bf8403a26f5f23465487c.png)

`onreadystate`事件到处都是。它会在每个请求开始时、结束时触发，有时只是因为它真的喜欢被触发。但是根据规范，只有当请求*成功*时，才会触发`onload`事件。因此，`onload`事件是一个现代 API，你可以在几秒钟内很好地利用它。`onreadystate`事件是向后兼容的。但是，`onload`事件应该是你选择的工具。`onload`事件看起来像 jQuery 上的`success`回调，不是吗？

是时候把 5 磅的哑铃放在一边，开始手臂弯曲了。

## 设置请求标题

jQuery 将请求头隐藏起来，这样您的后端技术就知道这是一个 Ajax 请求。一般来说，后端不关心 GET 请求来自哪里，只要它发送正确的响应。当您希望用相同的 web API 支持 Ajax 和 HTML 时，这很方便。因此，让我们看看如何在普通 Ajax 中设置请求头:

```
var oReq = new XMLHttpRequest();
oReq.open('GET', e.target.dataset.url, true);
oReq.setRequestHeader('X-Requested-With', 'XMLHttpRequest');
oReq.send(); 
```

这样，我们可以在 Node.js 中进行检查:

```
if (req.headers['x-requested-with'] === 'XMLHttpRequest') {
  res.writeHead(200, {'Content-Type': 'application/json'});
  res.end(JSON.stringify({message: 'Hello World!'}));
} 
```

如您所见，vanilla Ajax 是一种灵活的现代前端 API。有很多想法可以使用请求头，其中之一就是版本控制。例如，假设我想支持这个 web API 的多个版本。当我不想破坏 URL，而是提供一种机制让客户端可以选择他们想要的版本时，这很有用。我们可以这样设置请求头:

```
oReq.setRequestHeader('x-vanillaAjaxWithoutjQuery-version', '1.0'); 
```

在后端，尝试:

```
if (req.headers['x-requested-with'] === 'XMLHttpRequest' &&
    req.headers['x-vanillaajaxwithoutjquery-version'] === '1.0') {
  // Send Ajax response
} 
```

Node.js 提供了一个`headers`对象，可以用来检查请求头。唯一的诀窍是它以小写形式读取它们。

我们在最后冲刺阶段，一点汗都没出！您可能想知道，关于 Ajax 还有什么需要了解的？来几个巧妙的技巧怎么样？

## 响应类型

您可能想知道为什么`responseText`包含服务器响应，而我所处理的只是普通的旧 JSON。原来，是因为我没有设置正确的`reponseType`。这个 Ajax 属性非常适合告诉前端 API 期望从服务器得到什么类型的响应。所以，让我们好好利用这一点:

```
var oReq = new XMLHttpRequest();
oReq.onload = function (e) {
    results.innerHTML = e.target.response.message;
};
oReq.open('GET', e.target.dataset.url, true);
oReq.responseType = 'json';
oReq.send(); 
```

太棒了，我可以告诉 API 会发生什么，而不是发送回我必须解析成 JSON 的纯文本。几乎所有最新的主流浏览器都有这个功能。当然，jQuery 会自动完成这种类型的转换。但是，现在我们有了一种用普通 JavaScript 来做同样事情的便捷方式，这不是很好吗？普通 Ajax 支持许多其他响应类型，包括 XML。

遗憾的是，在 Internet Explorer 中，这个故事并不那么精彩。截至 IE 11，该团队尚未添加对 [xhr.responseType = 'json'](https://connect.microsoft.com/IE/feedback/details/794808) 的支持。这个功能将在[微软 Edge](http://caniuse.com/#feat=xhr2) 上实现。但是，在撰写本文时，这个 bug 已经存在将近两年了。我猜微软的人一直在努力修改浏览器。让我们希望微软 Edge，也就是 Spartan 项目，能够兑现它的承诺。

唉，如果你必须绕过这个 IE 问题:

```
oReq.onload = function (e) {
    var xhr = e.target;
    if (xhr.responseType === 'json') {
        results.innerHTML = xhr.response.message;
    } else {
        results.innerHTML = JSON.parse(xhr.responseText).message;
    }
}; 
```

## 缓存破坏

人们容易忘记的一个浏览器特性是缓存 Ajax 请求的能力。例如，默认情况下，Internet Explorer 会这样做。我曾经花了几个小时试图弄清楚为什么我的 Ajax 会因为这个而无法工作。幸运的是，默认情况下，jQuery 会破坏浏览器缓存。嗯，在普通的 Ajax 中也可以，而且非常简单:

```
var bustCache = '?' + new Date().getTime();
oReq.open('GET', e.target.dataset.url + bustCache, true); 
```

根据 [jQuery 文档](http://api.jquery.com/jQuery.ajax/#jQuery-ajax-settings)，它所做的只是在请求的末尾附加一个时间戳查询字符串。这使得请求变得有些独特，并使浏览器缓存崩溃。您可以看到当您发出 HTTP Ajax 请求时是什么样子:

![Console output showing cache busting in action](img/5a775726ec835e6d22a29902b70da4ba.png)

Tada！一切都没有戏剧性。

## 结论

我希望你喜欢 300 磅的台式压力机香草阿贾克斯曾经是。曾几何时，阿贾克斯是一头可怕的野兽，但现在不是了。事实上，我们已经涵盖了 Ajax 的所有基础知识，没有 jQuery 的束缚。

我将留给您一个简洁的 Ajax 调用方法:

```
var oReq = new XMLHttpRequest();
oReq.onload = function (e) {
    results.innerHTML = e.target.response.message;
};
oReq.open('GET', e.target.dataset.url + '?' + new Date().getTime(), true);
oReq.responseType = 'json';
oReq.send(); 
```

这是回应的样子:

![Screenshot of Ajax response](img/a2a5bb32d9af3de0c725dd54a66a8a10.png)

别忘了，你可以在 [GitHub](https://github.com/sitepoint-editors/VanillaAjaxNojQuery) 上找到整个演示。我很高兴在评论中听到你对 Ajax 和 jQuery 的看法。

## 分享这篇文章