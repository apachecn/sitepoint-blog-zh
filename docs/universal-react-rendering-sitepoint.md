# 通用反应渲染:我们如何重建站点点

> 原文：<https://www.sitepoint.com/universal-react-rendering-sitepoint/>

《环球反应渲染:我们如何重建 Sitepoint.com》由[斯图尔特·米切尔](https://twitter.com/mrlagmer)、[马特·伯内特](https://www.sitepoint.com/author/mburnett)和[琼·尹](https://github.com/newjs)进行同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！

![Universal React rendering machine powering SitePoint scrolling boxes](img/fea1a1b3e8c2f172480ec326a1a74f0f.png)

SitePoint 有很多很棒的内容，不仅仅是文章，所以当我们最近重新设计 SitePoint 主页时，目标之一是更好地展示其他内容，如来自 [SitePoint Premium](https://www.sitepoint.com/premium/) 的书籍、课程和视频，以及来自我们[论坛](https://www.sitepoint.com/community/)的讨论。这提出了一个挑战，因为这意味着从我们的主要 WordPress 应用程序之外的多个来源提取数据。

在我们的开发团队中，我们非常喜欢使用 [React](https://facebook.github.io/react/) 。JavaScript 中的组件模型和“数据存储”模式对我们非常有用，我们发现它产生了我们喜欢的代码库。

我们希望使用这些熟悉的工具，但是向用户发送一个几乎空无一物的主页，然后让他们等待所有不同的数据源加载并呈现，这是不可接受的。所以我们知道我们想要什么:我们的 WordPress 站点提供用 React 编写的服务器呈现的内容。如果用户可以享受我们的网站，而不需要启用 JavaScript，或者万一有什么事情导致我们的 JavaScript 中断，那就太好了。

## 通用 React 渲染和 PHP

WordPress 是 PHP，React 是 JavaScript——那么我们如何在服务器端实现这两者呢？我们的解决方案是创建 Node.js 代理服务器。所有的请求都进入这个节点服务器，并直接传递到我们的 WordPress 站点。然后，代理检查返回的页面，并在将其发送到浏览器之前进行 React 呈现。结果不像是一个完整的服务器端应用程序，而更像是 WordPress 的基本页面，带有一系列可以在客户端或服务器上通过 JavaScript 渲染/升级的组件。

虽然这个想法听起来很简单，而且我们发现了大量通过 React 呈现完整页面的例子，但是我们发现关于如何升级现有响应的例子相对较少。我们为此选择的工具有:

*   [node-http-proxy](https://github.com/nodejitsu/node-http-proxy) ，Node.js 的全功能 http 代理
*   以及 [Harmon](https://github.com/No9/harmon) ，node-http-proxy 用[小号](https://github.com/substack/node-trumpet)修改远程网站响应的中间件

描述这个过程的最好方式是用一个例子。下面是一个简单的[演示应用](https://sitepoint-universal-rvztzenoxz.now.sh)。如果你访问 [/_src](https://sitepoint-universal-rvztzenoxz.now.sh/_src) 网址，你可以看到完整的源代码，或者查看 [GitHub repo](https://github.com/BradDenver/sitepoint-universal/commit/f6762fd2db33685c09e20d81d88eca07729d8da2) 。

*   是一个非常简单的 HTTP 服务器，它总是返回相同的基本 HTML
*   `server/proxy.js`实现`node-http-proxy`将所有请求转发到目标服务器
*   `server/basicHarmon.js`提供了一个快速中间件，使用 Harmon 用一些新的、更好的内容重写任何`<header></header>`标签
*   `server/express.js`创建使用代理和 Harmon 中间件的 Express 服务器

当你访问该应用程序时，你应该看到你得到的结果有着奇特的标题。

在这个阶段，我们只有一个替换`header`标签中文本的简单例子，但是在接下来的部分中，我将展示如何使用相同的方法来呈现我们的 React 组件。这种方法给了我们很大的灵活性。Node.js 应用不需要 WordPress 如何工作的知识。它可以很容易地应用在 Rails 应用程序或其他任何东西的前面。

## SitePoint.com 上的组件

正如我提到的，我们真的很喜欢使用 React，我们已经在 SitePoint 上使用它的方式对我们迁移到服务器端很有帮助。

如果你以前用过 React，那么下面的[代码](http://codepen.io/BradDenver/pen/LRLkzx?editors=1010#0)块应该看起来很熟悉。

```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <title>Hello React!</title>
    <script src="build/react.js"></script>
    <script src="build/react-dom.js"></script>
    <script src="https://unpkg.com/babel-core@5.8.38/browser.min.js"></script>
  </head>
  <body>
    <div id="example"></div>
    <script type="text/babel">
      ReactDOM.render(
        <h1>Hello, world!</h1>,
        document.getElementById('example')
      );
    </script>
  </body>
</html> 
```

这直接来自 React“入门”页面，展示了如何将单个组件呈现到页面中。我在例子中较少看到的是在一个页面中使用多个组件。为了使用多个组件，你的站点不必是一个完整的单页应用。您可以自由添加多个 React 挂载点，我们认为这是一个不可忽视的好模式。这里有一个简单的例子:

见 [CodePen](http://codepen.io) 上 BradDenver ( [@BradDenver](http://codepen.io/BradDenver) )的笔 [React Hello，Goodbye World](http://codepen.io/BradDenver/pen/WGOxpY/) 。