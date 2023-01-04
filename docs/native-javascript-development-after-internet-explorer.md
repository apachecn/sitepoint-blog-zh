# Internet Explorer 之后的原生 JavaScript 开发

> 原文：<https://www.sitepoint.com/native-javascript-development-after-internet-explorer/>

![An illustration of a boy and girl with Chrome and Firefox logos playing with new technology at the park while an old man with an IE logo feeds ducks](img/28c365fc0efcd0d34dff5baeb2729b69.png)

欢迎大家来到本系列的第三部，也是最后一部，献给 *oldIE* 的退役，以及这个事件在前端开发领域带来的改变。到目前为止，我们已经讨论了可以安全丢弃的[过时技术](https://www.sitepoint.com/cleaning-house-after-internet-explorer/)和 [HTML5 和 CSS3 属性，这些属性现在已经在主流浏览器上获得了完全的原生支持](https://www.sitepoint.com/front-end-development-internet-explorer/)。今天，我们将重点关注原生 JavaScript 技术和其他任何不属于前面提到的类别的技术。

再一次，很多功劳归于 CanIUse.com，他被证明是一个无价的资源。我还要重申我上次的免责声明:

> 本文与是否放弃支持*老爹*的决定无关。你和你自己必须根据你的网站或应用程序的具体细节做出决定。

说了这么多，我们继续吧！

## 1.JavaScript APIs

在这一节中，我们将详细介绍 JavaScript 特性、API 和功能。他们都有什么共同点？它们中没有一个可以真正在 *oldIE* 上使用，要么需要使用各种 polyfills，要么它们的效果必须通过各种其他框架和库来实现(如果可以实现的话)。在当前的上下文中(IE11 + Edge)，它们在浏览器中内置了原生支持，这意味着它们可以开箱即用。

### Base64 编码和解码(btoa 和 atob)

Base64 是一个非常有用的网络工具。你们中的许多人可能已经用它在 CSS 中嵌入字体或图像。另一个常见的用途是处理通常会干扰传输协议的各种资源。一个很好的例子是[基本访问认证](https://en.wikipedia.org/wiki/Basic_access_authentication)，其中`username:password`对使用 Base64 打包，然后发送到服务器。对编码/解码操作的本机支持意味着它们可以变得更快。这里有一些资源可供您参考:

*   关于 MDN 的 [atob()](https://developer.mozilla.org/en-US/docs/Web/API/WindowBase64/atob) 和 [btoa()](https://developer.mozilla.org/en-US/docs/Web/API/WindowBase64/btoa) 文档
*   [Base64.js](https://github.com/davidchambers/Base64.js) polyfill

### Blob 构建

一个[二进制大对象](https://en.wikipedia.org/wiki/Binary_large_object)或**斑点**是在数据库管理系统中作为单个实体存储的原始数据的集合。它可以是音频剪辑或图像，以 Base64 格式存储。或者图像的集合。在许多情况下，Blob 字段用于结构不太严格的数据，如通过普通的表或表的模式表达的数据，如 JSON 对象。你们中的一些人可能记得 Blob 接口的祖先，即`BlobBuilder`。这种方法已经被否决了，强烈建议所有的 Blobs 操作都通过新的接口进行。

最重要的是，因为这个集合非常类似于一个文件，所以 Blob 对象的本地接口被用作`File()`接口的基础。因此，有一个很好的特性叫做“Blob URLs ”,它允许开发人员为 Blob 对象创建 URL，可以在任何可以使用文件的地方使用。考虑到这一点，现在原生支持覆盖了所有主流浏览器，这是非常值得赞赏的。

*   [MDN 上的斑点](https://developer.mozilla.org/en-US/docs/DOM/Blob)
*   [MDN 上的 BLOB URLs】](https://developer.mozilla.org/en/DOM/window.URL.createObjectURL)
*   [JavaScript blob 和文件接口介绍](http://qnimate.com/an-introduction-to-javascript-blobs-and-file-interface/)

### 渠道消息传递

通常，运行在不同浏览器环境中的两个脚本被禁止相互通信，以避免大量的安全隐患。有时候，这种交流不仅是需要的，而且是非常必要的。这就是通道消息 API 发挥作用的地方。这个接口允许我们的两个脚本通过双向管道相互通信。这就像给每个人一个对讲机设置在同一个频道。很整洁，不是吗？

*   开发工具上的 HTML5 网络消息简介。歌剧
*   [MDN 上的消息通道](https://developer.mozilla.org/en-US/docs/Web/API/MessageChannel)

### 常量和块级变量

`const`和`let`是 ES6 中定义数据的两种新方法。当`var`用全局或者函数范围定义变量时，新增加的部分有一个块级范围。这意味着用`const`和`let`创建的变量被限制在它们被定义的那对括号内。

虽然`let`定义了一个行为与经典变量相同的变量(范围除外)，但常量(`const`)是对某个值的只读引用。它不能被重新分配，不能被重新定义，也不能与同一范围内的任何其他变量或函数同名。唯一的例外是当常量是一个具有自己属性的对象时。这些属性不受保护，不会发生变化，其行为就像普通变量一样。

话虽如此，看看在代码中使用常量和块级变量的正确方法:

*   [MDN 上的常数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const)
*   [打开 MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let)
*   [准备 ECMAScript 6: let 和 const on SitePoint](https://www.sitepoint.com/es6-let-const/)
*   [ES6 let VS const 变量 by Wes Bos](http://wesbos.com/let-vs-const/)

### 控制台日志记录

大多数前端开发人员都会同意，当您的脚本没有正常运行时，web 控制台是手边最有用的工具之一。然而，就其本质而言，Internet Explorer 将其集成到代码中的速度相当慢，只有版本 10 才开始提供完全支持。现在，随着老古董的退役，没有什么能阻止我们充分利用这一特性。如果你需要更新你的知识或者发现使用控制台的新方法，看看下面的规格:

*   [MDN 上的控制台](https://developer.mozilla.org/en-US/docs/Web/API/Console)

### 跨产地资源共享

跨源资源共享(CORS)是一个 HTML5 API，它允许从自己的域之外请求资源。它描述了一组 HTTP 头，当授予特定权限时，这些头允许浏览器和服务器请求远程资源。以下资源是学习如何正确使用该功能的良好起点:

*   在 Dev 上使用跨源资源共享的 DOM 访问控制。歌剧
*   [MDN 上的 HTTP 访问控制(CORS)](https://developer.mozilla.org/en-US/docs/Web/HTTP/Access_control_CORS)
*   [在 SitePoint 上深入了解 CORS](https://www.sitepoint.com/an-in-depth-look-at-cors/)

### 网络加密 API

今天，安全性和隐私是任何应用程序最受欢迎的两个功能，这意味着良好(和快速)的加密技术真的很受欢迎。现在所有主流浏览器都一致支持 Web Cryptography API，只有 IE11(实现了旧版本的规范)和 Safari(需要`crypto.webkitSubtle`前缀)例外。幸运的是，一些特定的功能(比如随机值的生成)得到了更好的实现。因此，实现具有本机支持的加密元素比以往任何时候都更容易。以下是一些关于如何正确操作的指南:

*   [MDN 上的加密对象](https://developer.mozilla.org/en-US/docs/Web/API/Crypto)
*   [在 MDN](https://developer.mozilla.org/en-US/docs/Web/API/RandomSource/getRandomValues) 上踩油门
*   [GitHub 上传统浏览器的网络加密 API shim】](https://github.com/vibornoff/webcrypto-shim)

### 国际化

如今，互联网的普及意味着你网站的访问者可以来自世界各地。由于人们更信任他们熟悉的事物，所以用他们的语言和他们习惯的格式展示你的所有信息是一个好习惯。这就是你需要**国际化**(又称`i18n`)和**本土化**(或`L10n`)的地方。你觉得这听起来像胡言乱语吗？让我们引用奥雷利奥·德·罗萨在他的文章《如何在 JavaScript 中实现国际化(i18n)》中的话:

> **国际化**(也称为 i18n)是创造或改造产品和服务的过程，以便它们能够很容易地适应特定的当地语言和文化。**本地化**(也称为 L10n)是将国际化软件改编为特定地区或语言的过程。换句话说，国际化是调整您的软件以支持多种文化(货币格式、日期格式等)的过程，而本地化是实现一种或多种文化的过程。

浏览器支持比年初略有改善，Safari v10 在 9 月份加入了这一行列。听起来足够有趣？以下是一些帮助你走上正确道路的资源:

*   [MDN 上的国际化 API](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl)
*   [JavaScript 国际化 API–简单介绍](https://marcoscaceres.github.io/jsi18n/)
*   [如何在 JavaScript 中实现国际化(i18n)](https://www.sitepoint.com/how-to-implement-internationalization-i18n-in-javascript/)

### 处理媒体查询

响应式网站设计是当前高性能网站的标准，而使其成为可能的关键特征是媒体查询的存在。将 CSS 中的媒体查询引入 JavaScript，让开发人员在优化各种设备的内容时拥有更大的灵活性。一个很好的例子就是处理手机和平板电脑从纵向模式到横向模式的转换。虽然有一个 API 可以处理设备方向的[检测，但对大多数浏览器的支持是部分的，而只有微软 Edge 提供完全支持。这里有一些资源可以帮助您开始了解这个主题:](https://developer.mozilla.org/en-US/docs/Web/API/Detecting_device_orientation)

*   [在 MDN 上测试媒体查询](https://developer.mozilla.org/en-US/docs/Web/CSS/Media_Queries/Testing_media_queries)
*   [MDN 上的 window . match media](https://developer.mozilla.org/en-US/docs/Web/API/Window/matchMedia)
*   [如何在 SitePoint 上使用 JavaScript 进行媒体查询](https://www.sitepoint.com/javascript-media-queries/)

### 媒体源扩展

媒体源扩展(MSE)在不使用插件的情况下为视频和音频元素添加了额外的功能。这为您提供了自适应媒体流、实时流、视频拼接和视频编辑等功能。自 2013 年 9 月以来，YouTube 一直在其 HTML5 播放器中使用 MSE。浏览器支持也相当不错，只有 iOS Safari 和 Opera Mini 缺少这个功能。IE11 只有在 Windows 8+上使用时才有完全支持。不幸的是，IE11/Win7 用户无法从这项技术中受益。无论您只是好奇还是真的想开始使用这个 API，您都会发现以下资源非常有用:

*   [MDN 上的媒体源 API](https://developer.mozilla.org/en-US/docs/Web/API/MediaSource)
*   [MSDN 上的媒体源扩展](https://msdn.microsoft.com/en-us/library/dn594470%28v=vs.85%29.aspx)
*   [HTML5 媒体源扩展:在 Smashing Magazine 上将制作视频带到网络上](https://www.smashingmagazine.com/2016/04/html5-media-source-extensions-bringing-production-video-web/)

### 突变观察者

JavaScript 应用程序每天都在变得越来越复杂。作为开发人员，您需要控制页面上发生的变化，尤其是 DOM 树发生变化或“变异”的时候。对这种监控的需求并不新鲜，事实上已经有了解决方案——突变事件。这个接口的问题是，作为事件，它们都是同步的(被调用时被触发，可能会阻止其他事件触发)，并且必须被捕获或通过 DOM 冒泡。这反过来会触发其他事件，使 JavaScript 线程过载，在某些特殊情况下，会产生整个级联故障，导致浏览器崩溃。

因此，突变事件已被弃用，取而代之的是突变观察器。你可能会问，有什么区别？首先，也是最重要的，观察者是异步的。它们不会阻止您的脚本运行。它们不是在每一次突变时被解雇，而是在主活动完成后交付一批结果。更重要的是，您可以对观察器进行微调，以观察对节点的所有更改或特定类别的更改(比如只对子列表的更改或只对属性的更改等等)。从以下资源开始学习如何做到这一点:

*   [MDN 上的变异观察器](https://developer.mozilla.org/en-US/docs/Web/API/MutationObserver)
*   [了解突变观察者](https://dev.opera.com/articles/mutation-observers-tutorial/)
*   [在站点点进化出新的突变](https://www.sitepoint.com/evolving-a-new-mutation/)

### 页面可见性

标签浏览改变了我们与网络互动的方式。很多人同时打开几十个页面的情况并不少见。这些页面中的每一个都做它们自己的事情，运行它们的脚本，下载它们拥有的资源等等。尽管在给定时间只能有一个选项卡处于活动状态，但所有打开的页面都在消耗 CPU 时间和带宽。一些应用程序可能会定期通过连接发送和接收更新。然而，如果你在活动标签中没有那个应用程序，它需要在后台每 X 秒更新一次吗？似乎有点浪费，不是吗？尤其是在移动设备和数据计划上，每项资源都非常宝贵。

这就是页面可见性 API 发挥作用的地方。这个界面允许开发者知道他们的应用程序是在一个活跃的标签页还是在后台。让我们以我之前提到的应用程序更新为例。通过页面可见性 API，您可以检测应用程序何时在后台，而不是每 5 或 10 秒更新一次，而是每 60 秒甚至更短时间更新一次。一旦页面再次可见，您就可以切换回正常的更新速度。很酷，不是吗？

你还在等什么？请查看以下指南，并启动您的应用程序以获得页面可见性。你的用户会因此感谢你的:

*   [MDN 上的页面可见性 API](https://developer.mozilla.org/en-US/docs/Web/API/Page_Visibility_API)
*   [site point 页面可见性 API 介绍](https://www.sitepoint.com/introduction-to-page-visibility-api/)

### 页面转换事件

你有没有使用过这样的 web 表单，当你试图离开或关闭页面时，它弹出一条消息说你有未保存的数据？如今，在页面上更改设置、个人资料等是很常见的。页面里的脚本怎么知道你要离开？他们收听`pagehide`事件。

`pagehide`和它的伙伴`pageshow`是页面过渡事件的主角。上面我们已经看到了第一个主要是用来做什么的。`pageshow`的主要用途是确定一个页面是从缓存加载的还是直接从服务器加载的。这不是最常见的用法，但是，如果您需要其中一种功能，请查看下面的资源:

*   [MDN 上的页面显示](https://developer.mozilla.org/en-US/docs/Web/Events/pageshow)
*   [MDN 上的页面隐藏](https://developer.mozilla.org/en-US/docs/Web/Events/pagehide)

### requestAnimationFrame

网络动画已经走过了漫长的道路，从早期的`<marquee>`和`<blink>`，到动画 gif、jQuery 效果，再到现在的 CSS、SVG、canvas 和 WebGL 动画。所有这些方法中的一个不变点是需要控制动画的流动，并使其尽可能平滑。

最初的方法使用`setInterval`和`setTimeout`来控制动画的步骤。问题是结果并不可靠一致，动画通常很粗糙。这就是为什么一个新的界面被构思出来——`requestAnimationFrame`。这种方法的主要优点是，浏览器可以自由地将请求与自己的绘制周期相匹配，从而明显地平滑动画。这两种方法与对应的`cancelAnimationFrame`一起，构成了现代 JavaScript 动画的基础。

和往常一样，下面是一些帮助您开始掌握该功能的资源。

*   [MDN 上的 request animation frame](https://developer.mozilla.org/en-US/docs/Web/API/window/requestAnimationFrame)
*   [取消 MDN 上的 animation frame](https://developer.mozilla.org/en-US/docs/Web/API/Window/cancelAnimationFrame)
*   [在 SitePoint 上使用 requestAnimationFrame 的简单动画](https://www.sitepoint.com/simple-animations-using-requestanimationframe/)
*   [观看:使用 SitePoint 上的 requestAnimationFrame 进行表演](https://www.sitepoint.com/watch-performance-with-requestanimationframe/)

### 计时 API

在线表演是当今的话题，每个人都尽最大努力减少资源，优化脚本，并充分利用他们可以支配的所有工具。有两种主要的方法来解决这个问题:网络性能(站点和资源交付的速度)和用户性能(应用程序本身执行的速度)。

网络性能由两个 API 提供服务:`Navigation Timing`和`Resource Timing`。它们都提供了各种与网络性能相关的信息，如 DNS、CDN、请求和响应时间等。唯一的区别是`Navigation Timing`针对 HTML 页面本身，而`Resource Timing`处理所有其他资源(图像、脚本、媒体等)。)

在用户性能方面，我们有一个 API: `User Timing`。这个 API 处理两个主要概念，称为`Mark`(一个非常详细的时间戳)和`Measure`(两个标记之间的间隔)。通过修改这些概念，开发人员可以测量他们的代码运行速度，并确定需要优化的地方。不幸的是，Safari 仍然不支持这个 API，所以可能需要一个 polyfill。

在寻求确保你的网站或应用程序的最佳性能的过程中，掌握这些界面的使用成为一项至关重要的技能。这就是为什么我们给你一些材料开始学习:

*   导航计时
    *   [MDN 上的导航定时 API](https://developer.mozilla.org/en-US/docs/Web/API/Navigation_timing_API)
    *   [使用站点点上的导航定时 API 加载剖析页面](https://www.sitepoint.com/profiling-page-loads-with-the-navigation-timing-api/)
    *   [导航定时 API:如何在 SitePoint 上有效地分析页面负载](https://www.sitepoint.com/profiling-page-loads-with-the-navigation-timing-api-2/)
*   资源定时
    *   [MDN 上的资源定时 API](https://developer.mozilla.org/en-US/docs/Web/API/Resource_Timing_API)
    *   [使用谷歌开发者博客上的资源计时 API 测量网络性能](https://developers.googleblog.com/2013/12/measuring-network-performance-with.html)
    *   [site point 上的资源定时 API 介绍](https://www.sitepoint.com/introduction-resource-timing-api/)
*   用户计时
    *   [发现站点点上的用户计时 API](https://www.sitepoint.com/discovering-user-timing-api/)
    *   [html 5 rocks 上的用户计时 API](https://www.html5rocks.com/en/tutorials/webperformance/usertiming/)
    *   [user-timing-rum.js](https://gist.github.com/pmeenan/5902672) 和 [UserTiming.js](https://github.com/nicjansma/usertiming.js) 在 GitHub 上聚合填充

### 类型化数组

JavaScript 类型化数组是类似数组的对象，提供了一种访问原始二进制数据的机制。为了获得最大的灵活性和效率，实现分为两个概念:缓冲区(原始数据块)和视图(提供可以解释缓冲区数据的上下文)。有许多 web APIs 使用类型化数组，如 WebGL、Canvas 2D、XMLHttpRequest2、文件、媒体源或二进制 WebSockets。如果您的代码处理这样的技术，您可能会对下面的资源感兴趣:

*   [MDN 上的 JavaScript 类型化数组](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Typed_arrays)
*   [类型化数组:HTML5Rocks 上浏览器中的二进制数据](https://www.html5rocks.com/en/tutorials/webgl/typed_arrays/)

### websocket

我们前面谈到了通道消息传递，以及它如何使两个不同的脚本直接相互通信。WebSockets 与此类似，而且远不止于此。使用这个 API 可以在浏览器和 web 服务器之间创建一个持久的通信通道。

就像 HTTP 一样，WebSocket 协议有两个版本:不安全的(`ws://...`)和安全的(`wss://...`)。它也考虑到代理服务器和防火墙，通过打开隧道通过它们。事实上，WebSocket 连接从普通的 HTTP 连接开始，确保了与现有基础设施的兼容性。

WebSockets 是一项令人着迷的技术(他们甚至有一个专门的网站)，有很多东西需要学习。为了帮助您入门，这里有一些精选的资源:

*   [关于 WebSocket.org 的 web socket](http://websocket.org/aboutwebsocket.html)
*   [MDN 上的 web sockets](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API)
*   [site point 上 HTML5 WebSockets API 介绍](https://www.sitepoint.com/introduction-to-the-html5-websockets-api/)

### 网络工作者

默认情况下，所有 JavaScript 任务都运行在同一个线程中。这意味着一个页面中的所有脚本必须在处理时间内共享同一个队列。当处理器只有一个内核时，这很好也很简单。但现代 CPU 至少有双核，在某些型号上达到 4、6 或 8 个。如果一些任务可以转移到单独的线程中，由额外的可用内核来处理，这不是很好吗？这就是网络工作者被发明出来的原因。

使用 Web Workers API，开发人员可以将命名的脚本文件委托给在单独线程中运行的 worker。工作者只对创建它的脚本负责，通过消息进行双向通信，可以运行`XMLHttpRequest`调用，并且不与 DOM 或`window`对象的一些默认方法和属性进行交互。在异常类别中，我们可以提到`WebSockets`(可以将 WebSocket 连接的管理分配给一个工作器)或类似`IndexedDB`的数据存储机制。当主线程专注于运行整个应用程序时，没有什么比让您自己的爪牙处理次要任务更好的了。

要开始使用此功能(包括 web workers 可用的函数和类的列表)，请查看下面的资源:

*   [MDN 上的 Web Workers API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API)
*   [MDN 上可供网络工作者使用的功能和类](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Functions_and_classes_available_to_workers)
*   [site point 上 HTML5 Web Workers 的 JavaScript 线程](https://www.sitepoint.com/javascript-threading-html5-web-workers/)

### XMLHttpRequest 高级功能

`XMLHttpRequest`的采用预示着网络开发的新时代。现在，数据可以在浏览器和服务器之间交换，而不必重新加载整个页面。AJAX 是一种新的标准，它允许今天每个人都喜欢的单页应用程序的存在。

这样一项有用的技术将会进一步发展，这是很正常的。这就是 XHR 如何获得新的功能，如文件上传，传输进度信息或直接发送表单数据的能力。并且所有这些功能(IE11 或旧版本 Android 的情况下有小的例外)在 *oldIE* 退役后都在主流浏览器上得到支持。

有关更多详细信息，请随意阅读以下资源:

*   [MDN 上的表单数据](https://developer.mozilla.org/en-US/docs/Web/API/FormData)
*   [使用 SitePoint 上的 HTML5 FormData 接口实现更简单的 Ajax】](https://www.sitepoint.com/easier-ajax-html5-formdata-interface/)

## 2.杂项功能

现代 web 不仅仅是 HTML、CSS 和 JavaScript。有许多看不见的(和无名的)英雄在幕后辛勤工作，使我们的在线体验尽可能好。下面，我们将讨论几个这样的功能，就像上面的那些一样，不能在*老式*浏览器上使用(这些浏览器因其安全漏洞和缺乏对现代功能的支持而臭名昭著)。

### 使用“异步”和“延迟”的非阻塞 JavaScript 加载

每个 web 开发人员都知道脚本是“负载阻塞”的，会将整个页面扣为人质，直到它们完成加载。我们都记得在`</body>`之前加载 jQuery 的建议。这种方法在单页面应用程序中毫无用处，因为网站的所有行为都是由 JavaScript 驱动的。这让我们又回到了起点。

但事实是，在大多数情况下，你的网站或应用程序只需要它加载的全部 JavaScript 的一部分。剩下的将会被需要，或者他们正在做不影响 DOM 的事情。显而易见的方法是以正常的方式只加载关键的脚本，其余的以不会对应用程序产生负面影响的方式加载。事实上，有两种这样的装载方法。

第一个是使用`defer`属性，用来标记一个不会影响 DOM 的脚本，它应该在文档被解析后执行。在大多数情况下，这些脚本处理用户交互，使得它们以这种方式加载是安全的。第二个使用了`async`属性，并标记了一个脚本，该脚本在并行加载时会在下载后立即执行。但是不能保证加载顺序与执行顺序相同。

鉴于这两种观点带来的所有好处，它们正在成为提高网站和应用程序性能的重要工具。查看下面的参考资料，了解更多关于如何以及何时使用这种技术的信息:

*   [移除谷歌开发者的渲染屏蔽 JavaScript](https://developers.google.com/speed/docs/insights/BlockingJS)
*   [使用 HTML5 异步加载非阻塞 JavaScript，并在 SitePoint 上延迟](https://www.sitepoint.com/non-blocking-async-defer/)

### 内容安全政策

从一开始，web 上的安全性就建立在“同源”模型的基础上，这意味着只有来自同一个域的脚本才能与给定的页面进行交互。然而，随着时间的推移，我们不得不将第三方脚本集成到我们的页面中:来自 cdn 的 JavaScript 库，来自脸书、Google+或 Twitter 的社交媒体小部件以及其他类似的情况。这意味着我们打开了大门，允许“客串”剧本跑进我们隐喻的庭院。当恶意脚本也潜入其中并像其他脚本一样被执行时，问题就来了——这种攻击方法我们都知道是**跨站脚本**或 **XSS** 。

**内容安全政策**是对抗 **XSS** 的主要武器。该机制包含一组策略和指令，指定允许执行哪些脚本，从哪里加载资源，是否可以运行内联样式或脚本等等。 **CSP** 基于白名单，这意味着默认情况下所有内容都被拒绝，只有指定的资源可以访问。这意味着，当规则被微调时，即使恶意脚本被插入我们的站点，它也不会被执行。

这里有一些资源可以帮助您更好地理解这种机制:

*   [内容安全策略参考](https://content-security-policy.com/)
*   [通过 SitePoint 上的内容安全策略提高网络安全性](https://www.sitepoint.com/improving-web-security-with-the-content-security-policy/)
*   [html 5 rocks 内容安全策略介绍](https://www.html5rocks.com/en/tutorials/security/content-security-policy/)

### HTTP/2 协议

从一开始，Web 就运行在 HTTP 协议之上。然而，尽管第一个版本已经发生了巨大的变化，HTTP 却几乎没有变化。在现代网站和应用程序的复杂生态系统中，HTTP 可能是一个性能瓶颈。当然，有一些技术和实践可以优化这个过程，但是能做的也就这么多了。

这就是为什么该协议的第二次迭代，命名为`HTTP/2`，是基于谷歌的`SPDY`协议开发的。它于 2015 年 2 月获得批准，其规格于 2016 年 5 月作为 [RFC 7540](https://tools.ietf.org/html/rfc7540) 发布。到目前为止，主流浏览器只在加密连接上支持 HTTP/2，在可预见的未来，这种情况很可能会继续下去，以鼓励网站所有者转向 HTTPS。

采用 HTTP/2 并不是简单地改变一些配置设置。用于增强 HTTP 性能的一些最佳实践会对 HTTP/2 的性能产生影响。要了解您的网站是否为 HTTP/2 做好了准备，您可以参考以下资源:

*   [为 HTTP/2 做好准备:Smashing Magazine 的网页设计者和开发者指南](https://www.smashingmagazine.com/2016/02/getting-ready-for-http2/)
*   [HTTP/2 如何改变新遗迹博客上的 Web 性能最佳实践](https://blog.newrelic.com/2016/02/09/http2-best-practices-web-performance/)
*   [cloud flare 博客上面向 Web 开发人员的 HTTP/2](https://blog.cloudflare.com/http-2-for-web-developers/)

### 资源提示:预取

如今，Web 性能风靡一时，这是有充分理由的。正如在这个领域工作的每个人都知道的那样，资源下载占用了页面的大量加载时间。如果可以利用页面加载后的时间为后续步骤预加载资源，那不是很好吗？这正是资源提示的用途。

资源提示是一系列指令，告诉浏览器提前准备好以后需要的特定资源。该列表包含五个提示，如下所示:

*   dns 预取
*   再连接
*   预取
*   事先装好
*   更有趣

在这五个可能的选项中，目前唯一具有良好浏览器支持的是`prefetch`。这个提示告诉浏览器缓存用户在当前页面之后最有可能请求的文档。这限制了它对可以缓存的元素的使用。将它与其他类型的资源一起使用将不起作用。

如果您对这个主题感兴趣，下面是提供更多详细信息的资源列表:

*   [关于媒体上的资源提示的文章](https://medium.com/@luisvieira_gmr/html5-prefetch-1e54f6dda15d#.biofnez80)
*   [CSS 上的预取、预加载、预浏览-技巧](https://css-tricks.com/prefetching-preloading-prebrowsing/)
*   [key dn 博客上的资源提示](https://www.keycdn.com/blog/resource-hints/)

### 严格的运输安全

HTTPS 正在成为浏览的新标准，越来越多的网站只接受安全连接。正常的连接(在 HTTP 上)通常被重定向到 HTTPS 版本，一切照常进行。然而，这种方法容易受到“中间人”攻击，在这种攻击中，重定向发生在您想要的网站(通常是银行网站)的欺骗克隆上，以便窃取您的登录凭据。

这就是严格的传输安全头发挥作用的地方。首次使用 HTTPS 连接到所需网站时，标题会发送到浏览器。下次连接时，即使您只使用 HTTP 版本的 URL，浏览器也会直接转到 HTTPS 版本，而不经过重定向循环。因为 HTTP 上没有连接，所以前面描述的攻击不会发生。

有关严格传输安全标头的更多详细信息，请访问以下网站:

*   [MDN 上的 HTTP 严格传输安全](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Strict-Transport-Security)

### 设备像素比率

`Window.devicePixelRatio`是一个只读属性，返回当前显示设备上一个物理像素的(垂直)大小与一个 CSS 像素的大小之比。通过这种方式，开发人员可以检测高密度屏幕(像苹果的 Retina 显示屏或高端 Android 屏幕)。与媒体查询和匹配媒体(我们在上面讨论过)一起使用，该属性允许交付优化的资源以获得最佳可能的体验。

*   [MDN 上的 window . device pixel ratio](https://developer.mozilla.org/en-US/docs/Web/API/Window/devicePixelRatio)

### 网络视频文本轨道

Web 视频文本轨道(或 WebVTT)是一种用于标记多媒体资源文本标题的格式。它与 HTML5 `<track>`元素一起使用，能够以同步的方式呈现媒体资源(音频或视频)的字幕、翻译、说明或描述。这种文本信息的存在使得媒体资源更容易访问。

有关如何开始使用此功能的说明，请查看以下资源:

*   [WebVTT on MDN](https://developer.mozilla.org/en-US/docs/Web/API/Web_Video_Text_Tracks_Format)
*   [web vtt 和< track >在 Dev 上的介绍。歌剧](https://dev.opera.com/articles/an-introduction-to-webvtt-and-track/)
*   [html 5 rocks 上的 Track 元素入门](https://www.html5rocks.com/en/tutorials/track/basics/)

## 包装东西

现在，在这个以简单的智力练习开始的系列文章的结尾:“老歌已经过去了！让我们狂欢吧！*(…小时后…)* 现在怎么办？”。我们涵盖了广泛的主题，从不再需要的技术和实践到我们现在无需 polyfills 就可以自由完成的所有新内容，无论是 HTML、CSS 还是原生 JavaScript。我们甚至谈到了更广泛的话题，如性能优化和增强安全性。

你应该现在就开始重构你所有的代码吗？很可能不会。必须根据重构成本和技术债务成本之间的平衡来做出这样的决定。但是如果你正在开始一个新项目，无论如何，要为未来而不是过去而建。

## 分享这篇文章