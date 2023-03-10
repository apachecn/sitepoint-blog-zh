# WebAssembly:解决 Web 上的性能问题

> 原文：<https://www.sitepoint.com/webassembly-solving-performance-problems/>

在现代 JavaScript 中，目标通常是找到各种方法来优化浏览器的性能。有时候，web 应用程序需要高性能，并希望浏览器能够跟上。

由于引擎处理语言的方式，传统的 JavaScript 有性能限制。作为页面的一部分呈现的解释型(甚至是 JIT 编译型)语言只能获得这么多——即使是从最强大的硬件上。

[WebAssembly](http://webassembly.org/) 是为了解决性能问题而全新设计的。它可以克服传统 JavaScript 无法解决的瓶颈问题。在 WebAssembly 中，不需要解析和解释代码。WebAssembly 充分利用了其字节码格式，为您提供了与本机程序相匹配的运行速度。

从另一个角度来想:把传统的 JavaScript 想象成一个好的、万能的工具，可以带你去任何地方。相比之下，WebAssembly 是能够实现接近本机速度的高性能解决方案。现在有两个独立的编程工具供您使用。

我的问题是:WebAssembly 会取代传统的 JavaScript 吗？如果不是，那么学习 WebAssembly 值得投资吗？

## 什么是 WebAssembly？

WebAssembly 是可以发送到浏览器的不同类型的代码。它是在 **[字节码](https://en.wikipedia.org/wiki/Bytecode)** 中，这种格式意味着它在到达浏览器时是用低级汇编语言发布的。字节码不是手工编写的，而是可以用任何编程语言编译，比如 C++或 Rust。然后，浏览器可以获取任何 WebAssembly 代码，将其作为本机代码加载，并获得高性能。

您可以将这个 WebAssembly 字节码看作一个模块:浏览器可以获取模块、加载它并执行它。每个 WebAssembly 模块都有导入和导出功能，其行为很像 JavaScript 对象。WebAssembly 模块的行为很像任何其他 JavaScript 代码，只是它以接近本机的速度运行。从程序员的角度来看，您可以像处理当前 JavaScript 对象一样处理 WebAssembly 模块。这意味着你已经知道的关于 JavaScript 和 web 的知识也可以转移到 WebAssembly 编程中。

WebAssembly 工具通常由 C++编译器组成。当前开发中有许多工具，但其中一个已经成熟的是 [Emscripten](http://kripken.github.io/emscripten-site/) 。该工具将 C++代码编译成 WebAssembly 模块，并构建可在任何地方运行的符合标准的模块。编译后的输出将有一个 WASM 文件扩展名，表明它是一个 WebAssembly 模块。

WebAssembly 的一个优点是，当您获取模块时，您拥有所有相同的 HTTP 缓存头。另外，您可以使用 IndexedDB 缓存 WASM 模块，或者使用[会话存储](https://developer.mozilla.org/en-US/docs/Web/API/Window/sessionStorage)缓存模块。缓存策略围绕缓存 fetch API 请求，并通过保留本地副本来避免另一个请求。由于 WebAssembly 模块是字节码格式的，您可以将该模块视为字节数组并将其存储在本地。

现在我们知道了什么是 WebAssembly，那么它的局限性是什么？

## 已知限制

JavaScript 运行在不同于任何典型 C++程序的环境中。因此，限制包括本机 API 在浏览器环境中可以做什么。

网络功能必须是异步和非阻塞的操作。所有底层的 JavaScript 网络功能在浏览器的 Web API 中都是异步的。然而，WebAssembly 并没有从异步 I/O 绑定操作中受益。I/O 操作必须等待网络响应，这使得所有接近本机的性能提升都可以忽略不计。

在浏览器中运行的代码，在沙盒环境中运行，不能访问文件系统。您可以创建一个内存中的虚拟文件系统，而不是预加载数据。

应用程序的主循环使用协作式多任务处理，每个事件都有执行的机会。网络上的事件通常来自鼠标点击、手指点击或拖放操作。事件必须将控制权返回给浏览器，以便可以处理其他事件。明智的做法是避免劫持主事件循环，因为这会变成调试的噩梦。DOM 事件通常与 UI 更新绑定在一起，这是很昂贵的。这给我们带来了另一个限制。

WebAssembly 无法访问 DOM 它依靠 JavaScript 函数来进行任何更改。目前，有人提议允许[与 web 上的 DOM 对象](https://github.com/WebAssembly/gc/blob/master/proposals/gc/Overview.md)互操作。仔细想想，DOM 重画既慢又贵。从接近原生的性能中获得的所有收益都会被 DOM 所阻碍。一种解决方案是将 DOM 抽象成一个内存中的本地副本，稍后 JavaScript 可以对其进行协调。

在 WebAssembly 中，一些好的建议是坚持执行速度非常快的东西。在避免陷阱的同时，使用能够产生最大性能增益的工具。可以把 WebAssembly 想象成这个超高速的系统，在隔离状态下运行良好，没有任何阻塞。

WebAssembly 中的浏览器兼容性令人沮丧，除了在现代浏览器中。IE 里没有支持。不过 Edge 16+确实支持 WebAssembly。像 Firefox 52+、Safari 11+和 Chrome 57+这样的现代大玩家都支持 WebAssembly。一个想法是在 WebAssembly 模块之间进行特性检测和特性对等，并回退到 JavaScript。这样你就不会破坏网页，现代浏览器可以从网页组装中获得所有的性能提升。

我可以使用 wasm 吗？caniuse.com 主要浏览器对 wasm 功能的支持数据。