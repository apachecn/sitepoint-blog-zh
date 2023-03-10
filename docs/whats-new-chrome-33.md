# Chrome 33 有什么新功能

> 原文：<https://www.sitepoint.com/whats-new-chrome-33/>

过去的几个 Chrome 版本几乎没有引起人们的注意。我们收到了一些好的开发者工具和小的界面调整，但是核心的 Blink 渲染引擎基本上没有改变(以一种明显的方式)。幸运的是，Chrome 33 提供了新的 HTML5 玩具，让开发者垂涎三尺…

## 自定义元素

自定义元素将开发人员多年来一直在做的事情形式化:创建他们自己的 HTML 标签。诚然，我们不应该这样做，但我们都尝试过在某个时候添加`<sarcasm>`和`<kitten>`标签。Chrome 现在支持自定义元素，作为新的 [Web 组件](http://w3c.github.io/webcomponents/explainer/) API 的一部分。

这里需要解释的东西太多了，但本质上，你可以:

*   使用您喜欢的任何名称创建新的 HTML 和 DOM 元素
*   从其他元素扩展新元素(包括现有的 DOM 元素)
*   将自定义功能捆绑到单个标签中

我仍在研究这些可能性，所以请关注 SitePoint 上的一篇深入文章。

## 网络语音 API

你还在使用鼠标、键盘或触摸屏来浏览你的网络应用和网页吗？多么古雅！Chrome 现在支持大部分的网络语音 API，并具有语音识别和合成功能。理论上，现在有可能实现类似[的巴别鱼](http://www.babelfish.com/)的设施，这样你可以用一种语言交谈，但却能被另一种语言听到。

基础知识:

```
var hello = new SpeechSynthesisUtterance("Hello world!");
window.speechSynthesis.speak(hello);
```

还有更多的选择，所以，请关注 SitePoint 上的更多文章。你也应该参考 Aurelio 最近的帖子[介绍网络语音 API](/introducing-web-speech-api/) 。

## 插件和扩展稳定性

已经实现了几个更有争议的更新:

*   默认情况下，PDF 文档显示在内部查看器中。
*   网景插件 API 将于 2014 年底被取消，Chrome 现在正在积极阻止这些插件。一些，比如 Silverlight 和 Java，已经被列入白名单，Flash 使用更新的 [PPAPI](https://en.wikipedia.org/wiki/Google_Native_Client#PPAPI) 。
*   未托管在 Web 商店中的扩展将被自动禁用。

虽然这无疑会使浏览器更加强大，但谷歌似乎有意建造自己的围墙花园，并正在种植类似苹果的树。插件开发可能注定要失败，但我们是在为了稳定而牺牲自由吗？

## 杂项更新

除了常见的错误和安全修复，Chrome 33 还提供了:

*   无前缀的[页面可见性 API](/introduction-to-page-visibility-api/)
*   更好的网页字体下载*(虽然我还是觉得渲染不如 IE 或者火狐)*
*   CSS 动画和过渡现在由新的 [Web Animations 1.0](http://dev.w3.org/fxtf/web-animations/) 模型支持，该模型将允许开发人员通过 JavaScript 创建、修改和控制动画
*   最新的 [Web 通知 API](https://www.w3.org/TR/notifications/) 已经实现
*   Windows 密码保护已得到改进；你需要输入你的 Windows 密码[来显示那些储存在 Chrome 中的密码](http://blog.elliottkember.com/chromes-insane-password-security-strategy)
*   突出显示开发模式下加载的扩展(目前仅适用于 Windows 版本)
*   在书签中搜索还可以找到文件夹名称
*   隐姓埋名的警告信息被简化了。

最后，[Chrome Frame](https://www.google.com/chromeframe)——ie 浏览器的 Chrome 窗口插件——已经正式退役。它仍然可用，但开发停止了一段时间。

相比最近略显停滞的版本，Chrome 33 感觉更加充实。它仍然是近 44%的网络用户的首选浏览器，而且这个数字看起来还会进一步上升。

## 分享这篇文章