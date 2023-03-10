# JavaScript 中的无 Cookie 会话变量

> 原文：<https://www.sitepoint.com/cookieless-javascript-session-variables/>

饼干可能是美味佳肴，但如果你烹饪方法不当，它们会留下令人作呕的味道！cookie 可以被用户阻止，存储空间被限制为每个域四个 20Kb 的 cookie，只能使用字符串，路径会造成混乱，数据通常在 HTTP 头中以纯文本形式传递。通常，cookies 对于需要保存临时状态数据的客户端密集型应用程序来说是多余的。

幸运的是，有一个解决方案允许您在浏览器中存储 JavaScript 数据。数据在页面加载之间保留，它将在页面上一页/下一页事件中存活(甚至远离域)，它不需要插件或离线存储设施，它将保存几兆字节的信息，它从不传输到服务器，并在每个浏览器中工作。奇怪的是，它通过利用 window.name 属性(或者 window.top.name，如果您使用多个框架的话)来工作。

开发者很少设置 window.name 属性。一般来说，只有在处理框架或弹出窗口时才需要它。即使我不希望你这样做，你通常不需要为应用程序的启动窗口定义一个名称。虽然 name 属性仍然是一个字符串，但它可以容纳几兆字节的数据。一些版本的 Opera 限制在 2Mb 左右，但大多数浏览器提供 10MB 或更多。

自己尝试一下很容易。将以下 JavaScript 代码插入到您站点的页面中:

```
 window.name = "This message will survive between page loads."; 
```

现在，将以下代码添加到任何其他页面中:

```
 alert(window.name); 
```

从第一页导航到第二页，您会发现消息数据被保留了下来。

通常，有一些警告:

1.  如果您导航到另一个网站上的页面，可以分析和更改 window.name 属性。在应用程序的主窗口中不提供外部链接很容易阻碍这一点。然而，为了安全起见，不要使用 window.name 来存储安全数据(对于仅用于客户端的临时数据存储来说，这不太可能是个大问题)。
2.  window.name 只能存储字符串。如果我们需要保存其他数据类型甚至复杂的对象呢？序列化是答案，幸运的是，我们已经开发了跨浏览器代码来从任何 JavaScript 对象生成 JSON 字符串。

参见:[如何为 JavaScript](https://www.sitepoint.com/javascript-session-variable-library/) 编写一个无 Cookie 的会话库。

## 分享这篇文章