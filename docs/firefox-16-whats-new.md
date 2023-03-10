# 火狐 16:有什么新内容，为什么取消

> 原文：<https://www.sitepoint.com/firefox-16-whats-new/>

火狐 16 于 2012 年 10 月 9 日发布。我评测火狐 15 真的已经六周了吗？如果你设法升级，你是少数；一天后，Mozilla 取消了下载…

## 安全漏洞警告

Firefox 16 引入了一个安全漏洞，允许恶意网站检查您的历史记录并提取 URL 和参数。恶心。没有人利用了这个漏洞，但是 Mozilla 采取了前所未有的措施，从他们的服务器上移除了 16.0 版本，并建议用户降级到 15.0.1。

好在 10 月 11 日发布了 Firefox 16.0.1。如果你还没有升级，停止阅读这篇文章，现在就升级！

*已经回来了。让我们继续……*

## Firefox 16.0.1 的新特性

火狐一直在快速发展。虽然 AverageWebUser 夫妇没有什么明显的变化，但 Mozilla 为开发人员增加了几个很棒的工具和更新。

## 无前缀 CSS

厌倦了输入前缀？如果你没有使用 [LESS](http://lesscss.org/) 、 [Sass](http://sass-lang.com/) 、 [Stylus](http://learnboost.github.com/stylus/) 或其他 CSS 预处理器，你会很高兴听到 Mozilla 是第一个认为 CSS3 动画、过渡、变换和渐变足够稳定以至于可以不加前缀的供应商:

*   **动画属性**:动画、动画名称、动画持续时间、动画延迟、动画计时功能、动画迭代计数、动画方向、动画播放状态、动画填充模式、@关键帧
*   **过渡属性**:过渡、过渡属性、过渡延迟、过渡持续时间、过渡定时功能
*   **变换属性**:变换、变换原点、变换样式、背面可见性、透视、透视原点
*   **图像属性**:线性渐变()、径向渐变()、重复线性渐变()、重复线性渐变()
*   **单位属性** : calc() ( [*我个人最喜欢的*](https://www.sitepoint.com/css3-calc-function/) 之一)

在所有人都升级到 16+版本之前，你不能放弃`-moz`属性，但是我希望其他厂商会很快跟进。

## 反向动画

在 Firefox 16 之前，反转动画的唯一方法是定义一组新的关键帧，以相反的方式运行。幸运的是，`animation-direction`属性现在接受以下值:

*   `normal` —动画应在每个循环中向前播放
*   `alternate`-动画在每个循环中反转方向，并向后执行步骤。定时功能也是相反的，即`ease-in`被`ease-out`取代
*   `reverse` —动画在每个循环中向后播放
*   `alternate-reverse` —动画在第一次播放时向后播放，然后在下一次播放时向前播放

你也可以混合方向，例如

```
 animation-direction: normal, reverse
animation-direction: alternate, reverse, normal 
```

此更新可能很快会出现在 Webkit 中。

## 开发者工具栏

Firefox 16 提供了新的开发者工具栏(菜单> Web Developer >开发者工具栏):

![Firefox Developer Toolbar](img/139d5d70cb719d560be31e99783a355a.png)

它具有到检查器、调试器(见下文)、控制台(显示错误数量)以及命令行的链接。键入`help`查看完整的命令列表，例如`edit styles.css 123`、`console clear`、`restart`等。对于我们这些懒得伸手去拿鼠标的人来说很方便。

## 一个有效的 JavaScript 调试器

我提到了 Firefox 15 中的 [JavaScript 调试器(菜单> Web Developer >调试器):](https://www.sitepoint.com/firefox-15-whats-new/)

![JavaScript debugger](img/c0537439af778cad8ce299c33c5877ca.png)

你会很高兴听到 Mozilla 已经修复了页面刷新时断点被移除的问题，这意味着你不能分析启动代码。它很好用，看起来也很棒。我还不确信它比 Firebug 好，但我会在未来几周内对它进行评估。

## HTML5 APIs

Firefox 16 中实现了几个新的 W3C APIs，尽管它们主要用于移动开发人员:

*   [电池状态 API](https://www.w3.org/TR/battery-status/) :例如，当电池耗尽时，你可以优化你的 web 应用程序来做更少的工作或发出更少的 Ajax 请求。
*   [震动 API](https://www.w3.org/TR/vibration/) :可能用于游戏和提醒。

两个 API 都不需要前缀`-moz`。

## Web 应用程序支持

Firefox 已经为 Mozilla Marketplace 准备好了更新(如果你喜欢，也可以称之为 *AppStore* ，但这将导致苹果和亚马逊无休止的法律纠纷)。只需一点点努力，你就可以把你的网站变成一个商业应用，可以在市场上推广，可以通过 Firefox 安装，也可以离线运行。

现在开始规划你的商业战略还为时过早——市场还不可用——但是 Mozilla 已经在 MDN 上提供了[初步的应用文档。](https://developer.mozilla.org/en-US/docs/Apps)

## 杂项更新

以下是其他亮点，排名不分先后…

*   JavaScript 中的增量垃圾收集。垃圾收集被分成更小的工作块来提高响应能力，而不是停止浏览器。
*   [IndexedDB](https://developer.mozilla.org/en-US/docs/IndexedDB) 已达到候选人推荐状态，已取消前缀。
*   最近打开的文件可以在便签簿中查看(菜单> Web Developer >便签簿)
*   about:memory 页面显示每个选项卡的内存使用情况 *(wohh，CNet —您真的需要 24MB 的宝贵资源吗！)*
*   默认情况下支持 [Opus 音频编解码器](http://www.opus-codec.org/)。
*   默认情况下，在 Mac OSX 中，VoiceOver 支持是打开的。
*   阿乔利语和哈萨克语本地化可用。
*   使用地址栏时，URL 突出显示更加清晰。
*   Firefox mobile 接收了一个新的阅读器模式选项来优化文章的外观。
*   几十个错误和安全补丁。

Mozilla 的创新步伐令人印象深刻。如果你已经迁移到另一个浏览器，我建议你重新评估火狐 16。开发工具本身就值得下载。

## 分享这篇文章