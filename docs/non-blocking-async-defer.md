# 用 HTML5 异步加载非阻塞 JavaScript 并延迟

> 原文：<https://www.sitepoint.com/non-blocking-async-defer/>

加载 JavaScript 是最大的性能瓶颈之一。在正常情况下，`script`标签会导致浏览器暂停渲染、加载文件并运行代码。浏览器无法执行其他有用的工作，因为您的 JavaScript 可以写入页面、修改现有元素或重定向到另一个 URL。出于这个原因，将`script`标签放在 HTML 的底部，就在< /body >之前是一个很好的做法。浏览器可能会有一两秒钟没有响应，但这并不明显，因为主要内容已经加载。然而，即使是这种解决方案也不足以满足当今数兆字节的客户端应用程序的需求。在极端情况下，有必要使用`script`标签注入或 Ajax 技术加载大型代码库。这可以防止阻塞，但需要额外的代码和严格的测试来确保脚本在所有浏览器中以正确的顺序运行。

## `defer`属性

`defer`属性向浏览器做出郑重承诺。它声明您的 JavaScript 不包含任何`document.write`或 DOM 修改内容:

```
<script src="file.js" defer></script>
```

浏览器将开始并行下载 file.js 和其他延迟脚本，而不会停止页面处理。`defer`在 12 年前的 Internet Explorer 版中实现！从 3.5 版本开始，Firefox 也提供了这种功能。虽然所有延迟脚本都保证按顺序运行，但很难确定什么时候会发生。理论上，它应该发生在 DOM 完全加载之后，DOMContentLoaded 事件之前不久。实际上，这取决于操作系统和浏览器，脚本是否被缓存，以及其他脚本当时在做什么。

## `async`属性

`async`已在 HTML5 中引入:

```
<script src="file.js" async></script>
```

`async`与`defer`相同，除了脚本在下载后第一时间执行(可以添加一个可选的`onload`属性来运行特定的功能)。您不能保证脚本会按顺序执行，但是它们会在窗口`onload`事件触发时加载。Firefox 3.6、Opera 10.5 和最新的 WebKit 版本都支持`async`，所以它应该会出现在 Chrome 和 Safari 的下一个版本中。IE9 还不支持`async`，但是 IE 团队可以很容易地将它添加为`defer`的别名。你可以同时使用`async`和`defer`来支持所有的浏览器——甚至是 IE4。也许在几个月内，我们最终会有一个本地的、非阻塞的 JavaScript 加载方法，可以在所有浏览器中工作。

**Note:** Opera’s Delayed Script Execution

Opera 提供了一个实验性的延迟脚本执行工具，可以在 about:config 中启用。它记得页面上加载了一个`async`脚本，所以可以使用`document.write`或修改 DOM。该功能将立即使阻止页面加载的小工具和广告受益。让我们希望微软、Mozilla 和 WebKit 跟随 Opera 的脚步。

## 分享这篇文章