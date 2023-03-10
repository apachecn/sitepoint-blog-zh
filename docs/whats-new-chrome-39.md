# Chrome 39 有什么新功能

> 原文：<https://www.sitepoint.com/whats-new-chrome-39/>

尽管已经有六年的历史，但 Chrome 在版本数量上正迅速接近中年。Chrome 39 已经发布，你可能已经安装了。有几个很好的新功能…加上一两个稍微可疑的补充。

## ECMAScript 6 生成器

生成器是用`function*`声明的特殊函数，它创建迭代器。迭代器是一个带有`next()`方法的对象，调用该方法可以返回值。生成器函数使用一个`yield`语句来提供序列中的下一个值。

Arunoda Susiripala 在 [JavaScript 生成器和防止回调地狱](/javascript-generators-preventing-callback-hell/)中提供了一个基本示例:

```
function* HelloGen() {
    yield 100;
    yield 400;
}

var gen = HelloGen();

console.log(gen.next()); // {value: 100, done: false}
console.log(gen.next()); // {value: 400, done: false}
console.log(gen.next()); // {value: undefined, done: true}
```

Chrome、Opera 和 Firefox 31+都支持 ECMAScript 6 生成器。

## Beacon API

新的 [Beacon API](https://www.w3.org/TR/beacon/) 让你可以向服务器发送数据，而不必等待响应。浏览器会对请求进行排队并尽快发送，但重要的是，它不会延迟当前页面的卸载或下一个页面的加载。

向`navigator.sendBeacon()`传递一个 URL 和数据——可能是一个字符串或 FormData 值。通常，它可以在传输统计信息时使用

```
navigator.sendBeacon('/log', 'page-unloaded');
```

如果浏览器成功地将信标请求排队，则该方法返回`true`。我不确定如果`false`被返回，你能做什么，但是信标不应该被用于基本功能或消息传递。

Chrome、Opera 和 Firefox 31+都支持 Beacon API。

## Web 动画控件

Chrome 36 中引入了网页动画。想法是允许简单的类似 CSS3 的关键帧和动画声明来自 JavaScript，例如

```
var myanimation = myelement.animate([
    { color: "#f00" },
    { left: "20em" },
    { transform: "rotate(180deg)" }
], {
    duration: 1000,
    iterations: 1,
    delay: 0
});
```

这样做的好处是，您可以根据用户输入或其他条件来控制和同步播放。Chrome 39 增加了`play()`、`pause()`、`reverse()`、`finish()`(将动画置于最终状态)和`cancel()`(清除所有效果)等播放方式。

可以将 Web 动画看作是简单的 CSS3 动画和复杂的 JavaScript 全帧控制之间的折衷，使用 requestAnimationFrame 和您自己的计时函数。对于一般的网页效果来说，这可能有点过了，但是对于游戏来说还不够强大。应用和演示可能是很好的用例？

Chrome 和 Opera 是目前唯一支持网页动画的浏览器。Firefox nightlies 提供了支持，polyfill 也可用，但这项技术暂时还不稳定。

## Web 应用程序清单

不要与[应用程序缓存清单](/offline-browsing-in-html5-with-applicationcache/)混淆， [Web 应用程序清单](http://w3c.github.io/manifest/)是一个 JSON 文件，您可以在其中放置元数据，如名称、起始地址、图标、显示模式、方向等。

你需要在你的 HTML `head`中有一个`link`:

```
<link rel="manifest" href="manifest.json">
```

和清单文件本身，例如

```
{
    "name": "My Application",
    "icons": [{
        "src": "icon/lowres",
        "sizes": "64x64"
      }, {
        "src": "icon/small",
        "sizes": "64x64"
      }, {
        "src": "icon/hd_hi",
        "sizes": "128x128",
        "density": 2
      }],
    "start_url": "/index.html",
    "display": "fullscreen",
    "orientation": "landscape"
}
```

这最终意味着我们可以从每一页的顶部删除 57 个 iOS 图标大小选项… *假设苹果决定在 Safari 中实现该技术！*

跨浏览器支持是不完整的。Firefox 为其 Marketplace 使用清单，但是在属性名称上似乎有一些不同。

## Windows 7 沉浸式模式

在 Windows 7 上切换到沉浸式模式使 Chrome 的行为有点像 Windows 8 全屏 Metro 应用程序。奇怪的是，它随后在 Windows 任务栏上方放置了一个类似 Chrome OS 的半透明任务栏——带有自己的开始按钮和时钟。

*为什么？*如果 Windows 7 用户想要 Windows 8 体验，他们可以升级。然而，许多 Windows 7 选择坚持使用该操作系统是有原因的:他们不想要 Windows 8！

我也对谷歌坚持将 Chrome OS 小工具偷偷植入其他操作系统感到困惑。随处可见的 Chrome 应用图标很好，但复制原生功能毫无意义。或许他们希望吸引人们使用 Chrome 操作系统，但惹恼他们不会有任何帮助。

## 杂项更新

还有一些小功能…

*   你现在无需登录就可以从 Play Store 安装免费的 Chrome 应用。
*   `scrollTop`和`scrollLeft`现在返回高 DPI/Retina 屏幕上像素的分数。如果你认为像素完美是不好的，等到客户开始要求亚像素完美！
*   现在可以编辑保存的密码。
*   64 位版本的 Chrome 现在是 Mac OS 上唯一可用的版本。
*   SHA-1 加密技术正被淘汰，取而代之的是 SHA-2。
*   添加了对菜单内扩展按钮图标的实验支持(在**关于:标志**中设置*启用扩展工具栏重新设计*)。

尽管有一些不可靠的添加和一些 Firefox 的追赶，版本 39 是另一个伟大的更新。Chrome 依然快速、稳定，是几乎一半网络用户的首选浏览器。推荐。

## 分享这篇文章