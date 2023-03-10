# 理解幻象

> 原文：<https://www.sitepoint.com/understanding-phantomjs/>

自从发布以来， [PhantomJS](http://phantomjs.org/) 已经成为许多 JavaScript 工程师工作流程中的主要部分。在[题为“Headless WebKit 和 PhantomJS”的文章](https://www.sitepoint.com/headless-webkit-and-phantomjs/)中，Colin Ihrig 介绍了“Headless WebKit”的概念，并写了一些关于 PhantomJS 的文字。GitHub 上有超过 11，000 颗星，PhantomJS 已经成为开发人员的首选工具，尤其是在处理代码测试时。然而，仍然有许多开发人员不得不避免在他们的项目中实现这个工具，因为他们不知道这个工具到底是什么。

为了帮助填补这一空白，在本文中，我将解释幻想曲的核心概念，并试图揭开一些似乎经常困扰开发人员的复杂性。一旦你读了这篇文章，你就会对什么是幻想曲以及为什么它被认为是一个如此强大的工具有深刻的理解。

## “一个无头的什么？”

在 PhantomJS 的网站上，这项技术的解释如下:

> PhantomJS 是一个可以用 JavaScript API 编写脚本的无头 WebKit。它对各种 web 标准提供快速的本地支持:DOM 处理、CSS 选择器、JSON、Canvas 和 SVG。

显然，你们中那些不熟悉幻想曲的人可能会发现看到一些外国术语有些困难。这种描述可能会让有抱负的开发人员不知所措，并让那些技术新手认为它只适合非常专业的开发人员。然而，我可以向你保证，这些概念很容易理解。PhantomJS 是一个只存在于脚本中的 web 浏览器。没有 GUI，而是一个可以用 JavaScript 自动完成不同过程的无头浏览器。

让我们来看看这个工具开箱即用带来的一些好处。在解释这个话题之前，我建议你在你的电脑上安装 PhantomJS，如果你还没有的话。它可以通过在您的 CLI 中运行以下命令，经由`npm`进行安装:

```
npm install phantomjs -g
```

安装后，您将可以使用`phantomjs`命令。

## 幻想曲核心概念

让我们深入了解其核心概念的细节。

### 页面自动化

PhantomJS 允许开发人员访问浏览器的 DOM API。毕竟，即使没有 GUI，PhantomJS 仍然是一个浏览器。开发人员可以编写针对特定页面进行评估的 JavaScript 代码。虽然这看起来不是很重要，但它允许我们自动与网页进行任何形式的交互，而无需打开浏览器(这一操作将为您节省大量时间)。这在使用 PhantomJS 运行测试时尤其有用，我们很快会看到更多相关内容。

现在，让我们看看这个项目网站上的例子。它展示了如何使用`evaluate()`函数从页面中返回不同的对象。在这种情况下，`evaluate()`被用来返回 ID 为`myagent`的元素的`textContext`属性。要开始这个例子，我们所要做的就是在命令行中运行一个名为`phantomjs userAgent.js`的文件，我们将在控制台中收到我们的结果。

```
//userAgent.js
var page = require('webpage').create();
console.log('The default user agent is ' + page.settings.userAgent);
page.settings.userAgent = 'SpecialAgent';
page.open('http://www.httpuseragent.org', function(status) {
  if (status !== 'success') {
    console.log('Unable to access network');
  } else {
    var ua = page.evaluate(function() {
      return document.getElementById('myagent').textContent;
    });
    console.log(ua);
  }
  phantom.exit();
});
```

### 屏幕捕获

通过利用 WebKit，PhantomJS 提供了在 web 页面上呈现任何内容并将其保存为图像的能力。因此，它可以用来自动捕捉网页截图的过程，开发人员可以分析，以确保一切看起来不错。这些图像可以保存为多种格式，如 PNG、JPEG、PDF 和 GIF。

[下面的代码摘自 PhantomJS 关于屏幕截图](http://phantomjs.org/screen-capture.html)的文档。通过在命令行中运行`phantomjs github.js`，下面的代码将呈现 GitHub 主页的 PNG 图像。

```
//github.js
var page = require('webpage').create();
page.open('http://github.com/', function() {
  page.render('github.png');
  phantom.exit();
});
```

PhantomJS 还允许开发人员调整这些截图的大小，并指定我们想要捕捉的确切区域。下面，我们可以看到一个这样的例子，这只是上面所示的`github.js`脚本的修改版本。

```
var page = require('webpage').create();
//viewportSize being the actual size of the headless browser
page.viewportSize = { width: 1024, height: 768 };
//the clipRect is the portion of the page you are taking a screenshot of
page.clipRect = { top: 0, left: 0, width: 1024, height: 768 };
//the rest of the code is the same as the previous example
page.open('http://example.com/', function() {
  page.render('github.png');
  phantom.exit();
});
```

## 测试

PhantomJS 帮助开发人员自动化运行测试的过程，而不需要任何种类的 GUI。PhantomJS 利用其 headless 浏览器来处理不同的单元测试，并使用命令行来告诉开发人员他们在哪里遇到了错误。

毫无疑问，PhantomJS 主要因其在测试中的使用而闻名；然而，值得注意的是，它不是一个测试框架。在开发中，PhantomJS 用于启动不同的测试框架，例如 [Karma](http://karma-runner.github.io/0.13/index.html) 。通过[访问关于无头测试](http://phantomjs.org/headless-testing.html)的文档页面，你可以看到已经构建了哪些框架来支持 PhantomJS，以及可以通过外部测试运行器访问的框架列表，例如 [PhantomJS Runner QUnit 插件](https://github.com/jonkemp/qunit-phantomjs-runner)。

幻想曲也用于持续集成系统。对于那些不熟悉持续集成过程的人来说，它提供了一种监控应用程序的方法。开发人员可以将 PhantomJS 与 CI 系统(例如 [Travis CI](http://docs.travis-ci.com/user/gui-and-headless-browsers/#Using-PhantomJS) )集成起来，以便在实际推出代码之前，*对添加到项目中的任何新代码进行测试。因此，开发人员能够在代码中出现问题时立即检测到问题，并修复它们，从而确保不会将损坏的代码推送到项目中。*

## 网络监视

PhantomJS 的另一个核心特性是监视网络连接的能力。如文档中所定义:

> 因为幻像允许对网络流量进行检查，所以它适合于对网络行为和性能进行各种分析。

这意味着幻像可以被编程来收集关于网页性能的不同数据。当与 PhantomJS 配对时， [YSlow](http://yslow.org/phantomjs/) 可以使用不同的格式(例如 TAP)输出这些测试的结果。当实现时，TAP 允许单元测试和测试工具之间的通信，在本例中是 PhantomJS。此外，PhantomJS 和 YSlow 在持续集成系统中使用 TAP 协议，并监控添加到项目中的新代码的性能。通过这种方式，开发人员可以在代码被推送之前得到任何性能退化的通知。

## 结论

希望现在你已经对幻想曲是什么、它是如何工作的以及它有多强大有了一个明确的理解。如果您对幻想曲和测试不熟悉，并且想了解更多关于这些主题的知识，这里有一个资源列表，您会发现对这个目的非常有用:

*   [幻想曲和幻想曲简介](https://www.youtube.com/watch?v=NBxd0VrIpXM)
*   [用虚拟图实现自动化](https://www.youtube.com/watch?v=omUoFGMxRPI)
*   [网页抓取&用幻想曲实现自动化& CasperJS](https://www.youtube.com/watch?v=Kefil5tCL9o)
*   [节点](https://www.youtube.com/watch?v=b7d3P5KGk28)上 jQuery、PhantomJS 的自动化

我希望你喜欢这篇文章。如果您有任何问题或疑问，欢迎在下面的部分发表评论。

## 分享这篇文章