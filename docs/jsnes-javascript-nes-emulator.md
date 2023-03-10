# JSNES:用 JavaScript 编写的 NES 模拟器

> 原文：<https://www.sitepoint.com/jsnes-javascript-nes-emulator/>

今天的 JavaScript 最不可思议、可能毫无意义、但却令人惊叹的应用奖颁给了 Ben Firshman 和他的任天堂娱乐系统模拟器 JSNES。

[**访问 JSNES 页面……**](http://benfirshman.com/projects/jsnes/)

仿真器是基于 Java 的 vNES 项目的端口。它使用 HTML `canvas`元素进行屏幕渲染(对不起，Internet Explorer 用户),尽管声音还不被支持。

有 17 个工作游戏可以尝试。大多数将运行在 Firefox 3.5 或 Safari 4 中，但这两种浏览器都不能提供可玩的游戏体验。在最高规格的液氮冷却的 ninja PC 上，你将幸运地达到每秒 10 帧。

然而，真正的启示是谷歌 Chrome——它在一台普通的 PC 上全速运行模拟器(50-60 fps)。谷歌的性能优化似乎远远超过了竞争对手。 [Mozilla](https://bugzilla.mozilla.org/show_bug.cgi?id=509986) 和 [WebKit](https://bugs.webkit.org/show_bug.cgi?id=29534) 都提出了错误来调查为什么会有如此明显的速度差异。

虽然 JSNES 只是一个有趣的实验，但它展示了使用现代 JavaScript 引擎、一些独创性和大量咖啡因可以实现什么。本·弗斯曼——你是个天才。我唯一的问题是…为什么？！！

另见: [SitePoint 对 JSNES 的创始人 Ben Firshman 的独家采访](https://www.sitepoint.com/ben-firshman-jsnes-interview/)

## 分享这篇文章