# JavaScript 和 Wii U 浏览器

> 原文：<https://www.sitepoint.com/javascript-and-the-wii-u-browser/>

在所有关于桌面、平板和移动的讨论中，还有另一种类型的浏览器可能会被忽略——控制台浏览器。2012 年末，任天堂发布了 Wii U 游戏机，这是第八代游戏机，它引入了具有第二屏幕体验的游戏手柄。Wii U 还配备了由 WebKit 支持的 NetFront 浏览器，这使得编程模式有点类似于 iOS 版的 Safari。本文探讨了游戏机的浏览器，包括 Wii U 特有的 JavaScript API。


## HTML5 功能

Wii U 可能拥有当今最符合 HTML5 的基于主机的浏览器。当然，这是意料之中的，因为它是最新的。Wii U 在[的 HTML5 测试](http://html5test.com/ "The HTML5 test - How well does your browser support HTML5?")中得分[258/500](http://24ways.org/2012/unwrapping-the-wii-u-browser/ "24 Ways: Unwrapping the Wii U Browser")——在[游戏浏览器](http://html5test.com/results/gaming.html "The HTML5 test - How well does your browser support HTML5?")中最高。虽然这个数字可能看起来并不令人印象深刻(我安装的 Chrome 获得了 448/500 的分数，有 13 个加分)，但它远远高于最初 Wii 主机的 94/500 的分数。

那么 Wii U 支持哪些功能呢？首先，有许多 DOM Level 3 事件用于处理键盘和鼠标风格的输入。该浏览器还支持触摸事件，用于与游戏手柄的触摸屏进行交互。其他一些受支持的特性包括 SVG、画布 2D 上下文、会话历史、 [Web 存储](https://www.sitepoint.com/an-overview-of-the-web-storage-api/ "An Overview of the Web Storage API - SitePoint")和[服务器发送的事件](https://www.sitepoint.com/implementing-push-technology-using-server-sent-events/ "Implementing Push Technology Using Server-Sent Events")。更全面的功能列表可以在[的新评论](http://wiiubrew.org/wiki/Internet_Browser "Internet Browser - WiiUBrew")上看到。

## 访问 Wii U 游戏手柄

Wii U 浏览器暴露了一个特殊的对象`wiiu.gamepad`，它允许 JavaScript 读取游戏手柄的当前状态。任天堂提供了一个漂亮的[示例页面](http://www.nintendo.co.jp/wiiu/hardware/features/internetbrowser/sample.html "Wii U GamePad Demo")，当载入 Wii U 浏览器时，提取状态信息并显示在屏幕上。游戏手柄状态是通过调用`wiiu.gamepad.update()`方法获得的。根据 WiiUBrew 的说法，从定时器中调用`update()`来获得实时更新是安全的。因此，您的初始化代码将类似于下面的示例。在这个例子中，我们每 20 毫秒查询一次游戏手柄的状态。

```
window.addEventListener("load", function(event) {
  if (window.wiiu) {
    setInterval(function() {
      var state = window.wiiu.gamepad.update();

      // process the current state
    }, 20);
  } else {
    // browser is not a Wii U
  }
}, false);
```

查询完游戏手柄的状态后，必须确保数据有效。如果查询成功，一个`isDataValid`标志将被设置为 1，否则它将为零。您还可以使用`isEnabled`标志来确定游戏手柄是否已连接。根据 WiiUBrew，这些标志位于`wiiu.gamepad`对象上(即`wiiu.gamepad.isDataValid`)。然而，任天堂提供的演示页面会检查从`update()`返回的状态对象上的这些标志。根据我自己的测试，这两个位置似乎都是正确的。因此，使用任天堂语法，检查有效数据看起来像下面的代码。

```
if(state.isEnabled && state.isDataValid) {
  // valid GamePad data
} else {
  // invalid GamePad data
}
```

## 结论

本文介绍了新 Wii U 浏览器的一些功能。显然，我们还没有完全涵盖触摸屏、按钮和其他输入设备的阅读。请留意一篇即将发表的文章，这篇文章会继续这篇文章的内容。

## 分享这篇文章