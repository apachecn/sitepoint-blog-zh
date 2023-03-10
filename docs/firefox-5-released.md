# Mozilla 发布 Firefox 5

> 原文：<https://www.sitepoint.com/firefox-5-released/>

如果你一直在等着升级到火狐 4，那就太晚了！正如承诺的那样，Mozilla 在 2011 年 6 月 21 日发布了 Firefox 5——就在第 4 版发布后三个月。该组织已经开始了一个类似 Chrome 的发布-少，发布-通常快速更新的时间表。

如果你太兴奋而不想继续阅读，从[getfirefox.com](http://getfirefox.com/)下载安装程序或者通过选择帮助>关于火狐>检查更新来更新。你可能会很幸运地收到一个快速增量更新——它对我不起作用，完整的安装程序已被下载。

火狐 4 是一次重大更新。您不太可能在版本 5 中发现任何直接的差异，因为大多数变化都是隐藏的:

*   支持带有-moz 前缀的 [CSS3 动画](https://developer.mozilla.org/en/CSS/CSS_animations)
*   改进的 JavaScript 和画布性能
*   附加的 HTML5、SVG 和 MathML 特性
*   更快的浏览

开发人员还应该注意，setTimeout 和 setInterval 事件在非活动选项卡上每秒只执行一次，或者执行频率更低。它复制了 [requestAnimationFrame](https://developer.mozilla.org/en/DOM/window.mozRequestAnimationFrame) 的行为，以节省 CPU 和功耗。

很好——但是也有不好的一面。你可能会发现你的几个插件被 Firefox 5 禁用了。它们应该可以工作，但是许多作者还没有更新他们插件的版本号。Firebug 和 Web Developer 工具栏没问题，但是 Console <sup>2</sup> 和 HttpFox 被屏蔽了。

不是每个插件作者都有时间或资源来配合新的时间表。这很不幸，我希望 Mozilla 能解决这个问题。也许可以采用一种不太正式的方法，允许社区测试和批准插件，而不需要依赖作者硬编码支持的版本。或者，Mozilla 可以简单地发布 Firefox 4.1——编号变得越来越无关紧要。

尽管附加的麻烦，很高兴看到更新更有规律地出现。让我们知道你对火狐 5 的看法。

## 分享这篇文章