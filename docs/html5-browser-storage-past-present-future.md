# HTML5 浏览器存储:过去、现在和未来

> 原文：<https://www.sitepoint.com/html5-browser-storage-past-present-future/>

我写这篇文章有一点自私的原因；我永远记不住 HTML5 开发者可用的所有客户端存储机制！*我现在可能忘记了一些……*

## 为什么要在客户端存储数据？

主要原因是实用性。运行在浏览器上的 JavaScript 代码不一定需要将所有信息发送到服务器。有几个使用案例:

1.  你想提高性能。您可以在客户端缓存数据，这样就可以在没有额外的服务器请求的情况下检索数据。
2.  您有大量的客户端专用数据，例如 HTML 字符串或小部件配置设置。
3.  你想让你的应用程序离线工作。

让我们浏览选项。

## JavaScript 变量(过去、现在和未来)

最简单的选择是 JavaScript 变量。创建单个全局变量来存储应用程序数据可能比较实用，例如

```
// global application storage
var appDataStore = {};

// set values
appDataStore.hello = "Hello World!";
appDataStore.info = { a:1, b:2, c:3 };

// retrieve values
console.log(appDataStore.hello);
console.log(appDataStore.info.b);
```

也可以将值作为节点属性存储在页面 DOM 中。这对于特定于小部件的值很有用，但是比 JavaScript 变量更慢，风险更大；未来的浏览器或其他库可能会以意想不到的方式解释您的数据。

```
// store data in node
var mywidget = document.getElementById("mywidget");
mywidget.setAttribute("data-myvalue", "Hello World!");

// retrieve values
mywidget.textContent = mywidget.getAttribute("data-myvalue");
```

JavaScript 变量的优势:

*   最快最简单的解决方案
*   不需要序列化或反序列化数据
*   单页应用的理想选择

缺点是:

*   非常脆弱——链接到其他地方、刷新或关闭标签将会擦除所有数据
*   全局变量可以被第三方脚本覆盖和分析。

## Cookies(过去、现在和未来)

Cookies 是特定领域的文本数据块。它们听起来很美味，但是在 JavaScript 中 cookie 的处理很笨拙，因为基本的`document.cookie`字符串必须被解析，例如

```
// store cookie data
document.cookie = "hello=" + escape("Hello World") + "; path=/";

// retrieve value
cookies =
	document.cookie.split(";"),
	value = null,
	regexp = new RegExp("^\s*hello=(.*)$");
for (var c = 0; c < cookies.length && !value; c++) {
	var match = cookies[c].match(regexp);
	if (match.length == 2) value = unescape(match[1]);
}

console.log(value);
```

饼干的优点:

*   一种在客户端和服务器之间保持状态的可靠方法
*   通过设置到期日期，cookie 数据将在页面刷新和标签关闭后继续存在
*   所有现代浏览器都支持 cookies

缺点是:

*   笨重的 JavaScript 实现——您将需要一个 cookie 处理库
*   值只是字符串——其他数据必须使用`JSON.stringify`和`JSON.parse`等方法进行序列化
*   cookie 存储空间有限—不要依赖于拥有超过 20 个每个 4KB 的 cookie
*   cookies 可以被删除或阻止
*   cookies 被不公平地贴上了威胁互联网隐私的标签；你可能需要遵守[奇怪的地方法规](https://www.sitepoint.com/europe-website-cookie-privacy-law/)。

cookie 客户机/服务器共享的另一面导致了最大的技术问题。Cookie 数据在每个请求和响应的 HTTP 头中发送。因此，它被附加到每个 HTML 页面、图像、CSS 文件、JavaScript 文件、Ajax 调用等等。如果您有 50Kb 的 cookie 数据，并下载了 10 个 1KB 的图像，这将导致 1 兆字节的额外网络流量。

## window.name(过去和现在)

`window.name`属性有点奇怪。您可以设置一个字符串值，该值在浏览器刷新或链接到其他地方并单击后退之间保持不变，例如

```
window.name = "Hello World!";
console.log(window.name);
```

`window.name`的优点:

*   简单易用
*   数据仅保留在客户端，不会发送到服务器
*   该属性允许几兆字节的信息
*   广泛的浏览器支持

缺点是:

*   当选项卡或浏览器关闭时，数据会丢失
*   只能存储单个字符串值—序列化是必要的
*   其他域中的页面可以读取或更改`window.name`数据——不要将它用于敏感信息

`window.name`不是为数据存储而设计的。这是一次黑客攻击，供应商随时可能停止支持。因此，最好使用替代存储选项，尽管该技术已在传统浏览器填充和聚合填充中采用。

参见:[如何为 JavaScript 编写一个无 Cookie 的会话库](https://www.sitepoint.com/javascript-session-variable-library/)

## HTML5 Web SQL 数据库(过去)

Web SQL 数据库是供应商将基于 SQL 的关系数据库引入浏览器的最初尝试。它已经在 Chrome、Safari 和 Opera 15+中实现，但遭到 Mozilla 和微软的反对，转而支持 [IndexedDB](#indexeddb) 。

Web SQL 数据库的优势:

*   专为强大的客户端数据存储和访问而设计
*   它像许多服务器端应用程序一样使用 SQL
*   对 Webkit/Blink 桌面和移动浏览器的一些支持

缺点是:

*   SQL 似乎从来都不适合客户端开发
*   数据库模式必须预先定义
*   边缘浏览器支持和 Webkit/Blink 团队最终可能会放弃它
*   W3C 规范在 2010 年被放弃

综上:**不要用 Web SQL 数据库**！

参见: [W3C Web SQL 数据库规范](https://www.w3.org/TR/webdatabase/)

## HTML5 网络存储(现在和未来)

Web 存储提供了两个具有相同 API 的对象:`window.localStorage`用于保留持久数据，而`code.sessionStorage`用于保留仅会话数据，这些数据在标签关闭时会丢失。特定于域的字符串使用名称/值对存储。与 cookies 不同，存储限制要大得多(至少 5MB ),并且信息永远不会传输到服务器。

```
// is localStorage available?
if (typeof window.localStorage != "undefined") {

	// store
	localStorage.setItem("hello", "Hello World!");

	// retrieve
	console.log(localStorage.getItem("hello"));

	// delete
	localStorage.removeItem("hello");
}
```

网络存储的优势:

*   易于使用简单的名称/值对
*   会话和持久存储选项可用
*   事件模型可用于保持其他选项卡和窗口同步
*   广泛支持桌面和移动浏览器，包括 IE8+
*   [网络存储聚合填充](https://github.com/Modernizr/Modernizr/wiki/HTML5-Cross-Browser-Polyfills#web-storage-localstorage-and-sessionstorage)可用于较旧的浏览器，这些浏览器回退到 cookie 和`windows.name`存储方法

缺点是:

*   仅字符串值—可能需要序列化
*   没有事务、索引或搜索功能的非结构化数据
*   在大型数据集上可能表现不佳

另请参见:

*   [W3C 网络存储规范](http://dev.w3.org/html5/webstorage/)
*   [网络存储 API 概述](https://www.sitepoint.com/an-overview-of-the-web-storage-api/)
*   [网络存储聚合填充物](https://github.com/Modernizr/Modernizr/wiki/HTML5-Cross-Browser-Polyfills#web-storage-localstorage-and-sessionstorage)

## HTML5 索引数据库(未来)

IndexedDB 提供了一个结构化的、事务性的、高性能的、类似 NoSQL 的数据存储，带有同步和异步 API。这里需要记录的内容太多了，但是 API 允许您创建数据库、数据存储和索引、处理修订、使用事务填充数据、运行非阻塞查询以及使用游标遍历数据集。

指数化的优势 b:

*   专为强大的客户端数据存储和访问而设计
*   在现代桌面浏览器中的良好支持:IE10+，Firefox 23+，Chrome 28+和 Opera 16+

缺点是:

*   该 API 非常新，可能会修订
*   在老版本和移动浏览器中支持很少
*   一个庞大而复杂的 API——创建一个索引数据库聚合填充是困难的，也是不切实际的
*   像任何 NoSQL 商店一样，数据是非结构化的，这可能会导致完整性问题

另请参见:

*   [W3C IndexedDB 规范](https://www.w3.org/TR/IndexedDB/)
*   [索引数据库参考](http://docs.webplatform.org/wiki/apis/indexedDB)

## HTML5 文件 API(未来)

HTML5 文件 API 正在被扩展，以支持在本地文件系统上写入和读取顺序数据。您的域提供了一个完整的沙盒层次文件系统供其选择使用。

HTML5 文件 API 的优点:

*   可以创建和存储大型文本和二进制文件
*   性能*应该*好

缺点是:

*   可能会修订的早期规范
*   除非限制文件写入，否则这是一个明显的安全风险
*   在当前的浏览器中支持很少，聚合填充可能不切实际
*   没有事务、索引或搜索功能的非结构化数据

另请参见:

*   [W3C 文件 API 规范](https://www.w3.org/TR/FileAPI/)

## 摘要

这就是我们的目标——至少有七种存储替代方案。此时此刻，我的务实建议是:

1.  JavaScript 变量总是必不可少的，但只用于易变的内存数据。尽可能避免 DOM 属性/特性。
2.  假设有必要，使用 cookies 来保留客户端和服务器之间的状态。但保持饼干小；包含几个字符的单个令牌应该足够了。
3.  使用 [HTML5 Web 存储](#webstorage)离线存储数据，或者在需要大量客户端专用信息时使用。
4.  如果你必须支持 IE6 和 IE7 这样的老浏览器，使用一个[网络存储聚合填充](https://github.com/Modernizr/Modernizr/wiki/HTML5-Cross-Browser-Polyfills#web-storage-localstorage-and-sessionstorage)，它使用 cookies 和`window.name`实现 API。
5.  关注[indexed db](#indexeddb)；当传统浏览器消亡时，它最终将成为一种可行的存储机制。
6.  类似地，[文件 API](#fileapi) 将越来越适用于存储非结构化数据。这可能是生成图像、音频、视频和 pdf 等二进制数据的最佳解决方案。

或者你有更进一步的想法？

对本文的评论已经关闭。有关于 HTML5 的疑问？为什么不在我们的[论坛](https://www.sitepoint.com/forums/forumdisplay.php?52-(X)HTML?utm_source=sitepoint&utm_medium=link&utm_campaign=forumlink)上问呢？

## 分享这篇文章