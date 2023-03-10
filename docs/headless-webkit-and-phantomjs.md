# 无头 WebKit 和幻像

> 原文：<https://www.sitepoint.com/headless-webkit-and-phantomjs/>

如果你正在阅读这篇文章，你很可能知道什么是浏览器。现在去掉 GUI，你就有了所谓的无头浏览器。无头浏览器可以做和普通浏览器一样的事情，但是速度更快。它们非常适合以编程方式自动化和测试网页。现存的无头浏览器有很多，而 [PhantomJS](http://phantomjs.org/ "PhantomJS: Headless WebKit with JavaScript API") 是最好的。

PhantomJS 建立在 Chrome 和 Safari 背后的引擎 [WebKit](http://en.wikipedia.org/wiki/WebKit "WebKit - Wikipedia") 之上，它给你带来了强大的浏览器功能，而没有笨重的 GUI。开始使用 PhantomJS 很容易——只需[下载可执行文件](http://phantomjs.org/download.html "PhantomJS: Download and Install")。接下来，创建一个名为`hello.js`的文件，并添加以下几行。

```
console.log("Hello World!");
phantom.exit();
```

要执行该脚本，请运行如下所示的命令。注意，`phantomjs`可执行文件必须在您当前的目录中，或者在您环境的`PATH`中的某个地方。如果一切配置正确，PhantomJS 将把`Hello World!`打印到控制台，然后在调用`phantom.exit()`时终止。

```
phantomjs hello.js

```

## 使用网页

一旦 PhantomJS 启动并运行，您就可以开始自动化 Web 了。以下示例加载 Google 主页，然后将屏幕截图保存到文件中。第 1 行创建了一个 web 页面的新实例。在第 4 行，网页加载了`google.com`。一旦页面完成加载，就会执行`onLoadFinished()`回调函数。回调接收一个参数，`status`，它指示页面加载是否成功。加载页面的 URL 在`page.url`中。当页面包含重定向，并且您想知道您到达的确切位置时，此属性特别有用。使用页面的`render()`方法在第 8 行截取了一个截图。`render()`可以创建 PNG、GIF、JPEG 和 PDF 文件。

```
var page = require("webpage").create();
var homePage = "http://www.google.com/";

page.open(homePage);
page.onLoadFinished = function(status) {
  var url = page.url;

  console.log("Status:  " + status);
  console.log("Loaded:  " + url);
  page.render("google.png");
  phantom.exit();
};
```

### 页面设置

页面对象有许多设置，可以根据应用程序的需要进行定制。例如，如果您只对下载源代码感兴趣，您可以通过忽略图像文件并关闭 JavaScript 来加速您的应用程序。下面重写了前面的示例，以反映这些变化。更改后的设置显示在第 3 行和第 4 行。请注意，任何设置更改**必须在调用`open()`之前**发生。如果您查看这个例子的截图，您会注意到 Google 徽标图像丢失了，但是页面的其余部分是完整的。

```
var page = require("webpage").create();
var homePage = "http://www.google.com/";

page.settings.javascriptEnabled = false;
page.settings.loadImages = false;
page.open(homePage);
page.onLoadFinished = function(status) {
  var url = page.url;

  console.log("Status:  " + status);
  console.log("Loaded:  " + url);
  page.render("google.png");
  phantom.exit();
};
```

## 访问文件系统

到目前为止，我们的示例已经加载了页面并将截图保存为图像文件。虽然这无疑很酷，但许多应用程序更喜欢将源代码存储到文件系统中。PhantomJS 通过提供广泛的文件系统 API 使这成为可能。下面的例子使用`FileSystem`模块将`google.com`源代码写到一个文件中。首先，在第 2 行导入`FileSystem`模块。在第 6 行，打开输出文件进行写入。在第 7 行，使用`write()`方法将数据写入文件。实际的源代码可以通过页面的`content`属性获得。最后，文件被关闭，PhantomJS 被终止。

```
var page = require("webpage").create();
var fs = require("fs");
var homePage = "http://www.google.com/";

page.open(homePage);
page.onLoadFinished = function(status) {
  var file = fs.open("output.htm", "w");

  file.write(page.content);
  file.close();
  phantom.exit();
};
```

## 执行 JavaScript

PhantomJS 最强大的特性之一是通过 JavaScript 与页面交互的能力。这使得点击按钮和提交表单等任务的自动化变得极其容易。我们的下一个例子通过加载 Google 主页、输入查询，然后提交搜索表单来执行 Web 搜索。这个例子的开头应该看起来很熟悉。新的内容从第 8 行开始，我们确定哪个页面已经被加载。如果这是主页，则调用页面的`evaluate()`方法。`evaluate()`执行您在页面上下文中提供的代码。这实质上赋予了您与页面原始开发者相同的权力。多酷啊。

```
var page = require("webpage").create();
var homePage = "http://www.google.com/";

page.open(homePage);
page.onLoadFinished = function(status) {
  var url = page.url;

  console.log("Status:  " + status);
  console.log("Loaded:  " + url);

  if (url === homePage) {
    page.evaluate(function() {
      var searchBox = document.querySelector(".lst");
      var searchForm = document.querySelector("form");

      searchBox.value = "JSPro";
      searchForm.submit();
    });
  } else {
    page.render("results.png");
    phantom.exit();
  }
};
```

在`evaluate()`中，我们找到搜索框和表单。我们将搜索框的值设置为“JSPro”，然后提交表单。这将导致页面的`onLoadFinished()`方法再次被触发。然而，这次是搜索结果的截屏，PhantomJS 退出。

PhantomJS 还提供了两种方法，`includeJs()`和`injectJs()`，允许您向页面添加外部脚本文件。`includeJs()`用于包含可从页面访问的任何脚本文件。例如，您可以使用下面的代码在前面的示例中包含 [jQuery](http://jquery.com/ "jQuery: The Write Less, Do More, JavaScript Library") 。注意第 9 行对`includeJs()`的调用，以及`evaluate()`中的 jQuery 语法。

```
var page = require("webpage").create();
var homePage = "http://www.google.com/";

page.open(homePage);
page.onLoadFinished = function(status) {
  var url = page.url;

  console.log("Status:  " + status);
  console.log("Loaded:  " + url);

  if (url === homePage) {
    page.includeJs("https://code.jquery.com/jquery-1.8.3.min.js", function() {
      console.log("Loaded jQuery!");
      page.evaluate(function() {
        var searchBox = $(".lst");
        var searchForm = $("form");

        searchBox.val("JSPro");
        searchForm.submit();
      });
    });
  } else {
    page.render("results.png");
    phantom.exit();
  }
};
```

`injectJs()`方法类似于`includeJs()`。不同之处在于，注入的脚本文件不需要从页面对象访问。例如，这允许您从本地文件系统注入脚本。

## PhantomJS 和 Node.js

遗憾的是，PhantomJS 并没有与 Node.js 很好地集成。已经创建了一些试图从 Node.js 控制 PhantomJS 的项目，但它们都有点像组装的。现有项目使用[子流程](http://nodejs.org/api/child_process.html "Child Process Node.js v0.8.15 Manual")模块来产生幻象的实例。接下来，PhantomJS 加载一个特殊的 web 页面，该页面使用 [WebSockets](http://cjihrig.com/blog/how-to-use-websockets/ "How to Use WebSockets") 与 Node.js 通信，这可能并不理想，但它是有效的。

两个更受欢迎的幻象节点模块是[节点幻象](https://github.com/alexscheelmeyer/node-phantom "alexscheelmeyer/node-phantom")和[幻象节点](https://github.com/sgentle/phantomjs-node "sgentle/phantomjs-node")。我最近开始开发我自己的 PhantomJS 节点模块，名为[捉鬼敢死队](https://github.com/cjihrig/ghostbuster "cjihrig/ghostbuster")。Ghostbuster 类似于 node-phantom，但试图通过提供更强大的命令来减少回调嵌套。更少地调用 PhantomJS 也意味着更少的时间浪费在 WebSockets 通信上。另一个选择是 [zombie.js](https://github.com/assaf/zombie "assaf/zombie") ，一个基于 [jsdom](https://github.com/tmpvar/jsdom "tmpvar/jsdom") 构建的轻量级无头浏览器。Zombie 没有 PhantomJS 强大，但它是一个真正的 Node.js 模块。

## 结论

看完这篇文章，你应该对幻想曲有了基本的了解。PhantomJS 最棒的特性之一就是使用起来非常简单。如果你已经熟悉 JavaScript，学习曲线是最小的。PhantomJS 还支持本文中没有涉及的各种其他特性。一如既往，我鼓励你查看一下[文档](https://github.com/ariya/phantomjs/wiki/API-Reference "API Reference · ariya/phantomjs Wiki")。也有一些[的例子](https://github.com/ariya/phantomjs/tree/master/examples "phantomjs/examples at master · ariya/phantomjs")展示了幻像的所有荣耀！

## 分享这篇文章