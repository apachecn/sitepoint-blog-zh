# 用 Yeti 同时进行多浏览器 JavaScript 测试

> 原文：<https://www.sitepoint.com/simultaneous-multiple-browser-javascript-testing-with-yeti/>

开发人员讨厌测试。验证*显然*完美的代码是很乏味的。更糟糕的是，卑微的 web 开发人员很少知道用户将选择什么操作系统或浏览器，并且必须测试各种平台。排名前五的浏览器可能相对容易，但它们已经被越来越多种类和功能强大的移动设备所加入。

JavaScript 尤其成问题。开发人员正在创建复杂的客户端应用程序，单元测试变得至关重要。有很多库可以帮忙，包括 [YUI 测试](http://developer.yahoo.com/yui/3/test/)、 [FireUnit](http://fireunit.org/) 和 [JsUnit](http://www.jsunit.net/) 。不幸的是，这仍然是一个很大程度上的手动过程，因为一次只能测试一个浏览器。

雅虎的 YUI 开发者有一些最严格的流程:他们目前支持 11 种浏览器环境，并测试几个新兴的移动平台。一次在一个浏览器上测试 JavaScript 功能是很费力的，而且自动化测试工具的配置和维护也很复杂。

为了帮助减轻负担，雅虎发布了[Yeti——YUI 简易测试界面](http://yuilibrary.com/projects/yeti/)。这是一个基于 [node.js](http://nodejs.org/) 的开源项目，允许从命令行进行单元测试:

```
 $ yeti mytestpage.html
✔ DOM Tests from Safari (Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10_6_4; en-us) 
AppleWebKit/533.16 (KHTML, like Gecko) Version/5.0 Safari/533.16)
  20 passed
  0 failed 
```

然而，Yeti 的多浏览器功能会让大多数开发者兴奋不已。不带参数运行 Yeti 会在 http://localhost:8000/启动一个 web 服务器。任何浏览器都可以指向这个 URL——包括移动设备，如果你使用像 [localtunnel](http://localtunnel.com/) 这样的系统为本地服务器创建一个公共地址。然后，您运行的每个测试都会在访问 Yeti URL 的所有浏览器上自动执行，结果会返回到控制台。太神奇了。

雅虎承认 Yeti 并不完整，但你可以今天下载 0.1 版代码。它只在 Mac OS X 上进行了测试，尽管它可能在装有 node.js 的 Linux PC 上运行。一旦它运行，任何操作系统上运行的任何浏览器都可以指向服务器。

Yeti 有可能使 JavaScript 单元测试比现在容易得多。这当然是一个我们会感兴趣地关注的项目。

相关链接:

*   [Yeti 项目页面](http://yuilibrary.com/projects/yeti/)
*   [介绍雪人](http://www.yuiblog.com/blog/2010/08/25/introducing-yeti-the-yui-easy-testing-interface/)
*   [性能测试卷土重来](https://www.sitepoint.com/boomerang-user-web-performance-testing/)

## 分享这篇文章