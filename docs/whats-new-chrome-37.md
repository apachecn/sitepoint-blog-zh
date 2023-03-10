# Chrome 37 有什么新功能

> 原文：<https://www.sitepoint.com/whats-new-chrome-37/>

距离我写关于 Chrome 的文章已经有一段时间了，但是版本 37 是在 2014 年 8 月的最后一周在所有平台上发布的。该浏览器在 9 月 2 日庆祝了它的第六个生日，所以也许这是一个深入研究新功能的好时机。与苹果、Opera、微软和 Mozilla 不同，谷歌淡化了最新的增强功能。或者，他们懒得创建一个更好的[“有什么新消息”页面](https://chromium.googlesource.com/chromium/src/+log/36.0.1985.0..37.0.2062.0?pretty=full)！

让我们看看隐藏在 Blink 表面下的宝石。

## Windows DirectWrite 支持

Windows 用户已经忍受了几年糟糕的字体渲染。出于某种原因，Chrome 36 及以下版本使用古老的图形设备界面来显示文本，它从未与其他浏览器相比。令人欣慰的是，经过广泛的重新设计，Chrome 现在使用 DirectWrite 来获得更好看和更快执行的字体。

![Chrome DirectWrite rendering](img/f62d7cf8b3fa02d28d90afe59afc7eb5.png)

## CSS 形状

[CSS Shapes](https://dev.w3.org/csswg/css-shapes-1/) 模块允许文本环绕圆形或多边形等不规则区域的内部或外部。矩形块的时代已经结束，我们将在任何地方制作杂志式的布局！

这个模块是由 Adobe 提出的，该公司已经制作了几个有趣的演示。我们[以前也在 SitePoint](https://www.sitepoint.com/css-shapes-breaking-rectangular-design/) 上报道过这个规范。支持目前仅限于 Chrome，但我希望 CSS 形状成为其他浏览器供应商的优先选择。

![CSS shapes](img/084573010f8ff1c7366fa06dd831b667.png)

## HTML5 <dialog>元素</dialog>

有着曲折的历史。它最初是用来表示对话的，但是五年前从规范中删除了。它的新功能更加有用；您可以在页面中创建弹出对话框，而无需借助复杂的 CSS 或脚本。

坏消息:在撰写本文时，只有 Chrome 37+支持`dialog`。Chrome 团队已经为其他浏览器发布了一个[聚合填充](https://github.com/GoogleChrome/dialog-polyfill),但是规范还在不断变化，元素暂时不太可能可靠。

但是，以下演示应该可以在 Chrome 37+中运行:

请看 [CodePen](https://codepen.io) 上 Craig Buckler([@ Craig Buckler](https://codepen.io/craigbuckler))的笔 [mauhq](https://codepen.io/craigbuckler/pen/mauhq/) 。