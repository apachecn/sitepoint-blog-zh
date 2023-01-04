# 制作跨平台 HTML5 游戏的提示

> 原文：<https://www.sitepoint.com/hints-for-making-a-cross-platform-html5-game/>

你已经知道 HTML5 非常适合游戏开发，对吧？也许最重要的是，它为您提供了一种开发跨平台应用程序的方法，有效地减少了花费在浏览器测试上的时间。即便如此，开发一款 HTML5 游戏并不总是一件容易的事情。在这篇文章中，我将分享一些开发高质量、跨平台 HTML5 游戏的技巧。

## 第一优先:移动

移动优先的政策是我在我的第一个 HTML5 游戏项目中忽略的东西。从现在开始，我再也不会低估移动玩家的数量了。我是一个游戏玩家。我喜欢在桌面上玩《愤怒的小鸟》,我也很高兴不在家的时候可以在我的安卓设备上玩。

如果我早点明白这一点，我会花更多的时间和精力来优化移动游戏体验，而不是专注于我的第一个 HTML5 游戏的桌面浏览器体验。虽然我考虑到了，但移动只是被当作一个次要的游戏环境。把手游体验优化作为重中之重。

## **早想跨平台**

以 Zynga 的 *CityVille* 为例。我以前玩过*城市*，也在我的安卓设备上玩过*城市故乡*(手机版)。坦白地说，游戏体验非常不同。*city ville homeland*围绕着一个完全不同的游戏体验。

从游戏开发者的角度来看， *CityVille* 不适合移动平台——它需要做出重大改变。对于 iPhone 的屏幕来说，它的菜单太大太复杂；这就是为什么 Zynga 在 iOS 和 Android 上发布了《T2 的城市故乡》的个人版本。在开发阶段的早期考虑跨平台的突发事件是有好处的。

## 多平台检测

一旦你开始开发一个 HTML5 游戏，你可能想要为不同平台的玩家展示不同风格和形式的内容(比如不同的用户界面)。这可能会变得非常复杂，因为无论您选择专攻哪种语言，不同的设备对 JavaScript、CSS3 等的支持程度总是不同的。

### JavaScript 的使用

JavaScript 非常受大多数游戏开发者的欢迎。在下面的代码片段中，JavaScript 属性`navigator.userAgent`返回一个 string 对象，使您能够检测任何 web 浏览器的名称和版本。

```
 <script type="text/javascript">// <![CDATA[ var mobile = (/iphone|ipad|ipod|android|blackberry|mini|windowssce|palm/i.test(navigator.userAgent.toLowerCase())); if (mobile) { document.location = "http://www.yoursite.com/mobile.html"; } // ]]></script> 
```

问题是，许多移动设备(目前)不支持 JavaScript，使用一个 JavaScript 脚本来检测所有浏览器类型可能会给用户带来页面性能噩梦。

### CSS @media 查询的使用

媒体查询是为移动设备显示 CSS 样式的好方法。您只需创建一个页面和两个样式表——第一个用于桌面屏幕，第二个用于移动设备。你只需要维护一个游戏页面，样式表定义了它的外观。

我们假设屏幕尺寸是 800px 宽。我们可以使用媒体查询来隐藏或移动对于在移动设备上显示不重要的 UI 元素:

@媒体屏幕和(最大宽度:800 像素){

/*这里有你想要的 CSS */

}

### PHP、JSP、ASP 等的使用

另一种方法是使用服务器端语言(PHP、JSP、ASP 等)来检测浏览器类型。它不依赖于移动设备不使用的脚本语言或 CSS。用于检测的简单 PHP 代码可能如下所示:

```
<? if ( stristr($ua, "Windows CE") or stristr($ua, "AvantGo") or stristr($ua,"Mazingo") or stristr($ua, "Mobile") or stristr($ua, "T68") or stristr($ua,"Syncalot") or stristr($ua, "Blazer") ) { $DEVICE_TYPE="MOBILE"; } if (isset($DEVICE_TYPE) and $DEVICE_TYPE=="MOBILE") { $location='mobile/index.php'; header ('Location: '.$location); exit; } ?> 
```

## 钻研你的 CSS 知识

学习和提高你的 CSS。开发一款运行在桌面、iOS 和 Android 上的游戏，需要大量的 CSS 知识。现在，CSS3 如此强大，你可以完全用它来制作游戏。在我的团队中，有一个人负责所有与 CSS 相关的工作，他根据移动设备创建动态变化的菜单大小。在跨平台 HTML5 游戏开发中，你 50%的工作可能与 CSS 有关。

## 用 DOM 测试

当我开始 HTML5 游戏开发时，我认为用 Canvas 渲染游戏是个好主意，因为它在台式电脑上非常有用。但是在像 iPhone 3GS 这样的平台上，事实证明这有点令人头疼。游戏表现很烂；图像渲染速度低于 5fps。我不得不一个接一个地创建渲染引擎。

目前，我依赖于 DOM(文档对象模型)。它规定了我的应用程序由 HTML 元素和 CSS3 选择器构成的方式，而不是它们如何呈现在画布上。使用 DOM 的好处是，由于硬件加速，游戏可以更快更灵敏。反复测试你的游戏很重要，我个人认为使用 DOM 是比 Canvas 更好的选择。

## 处理音频问题

在游戏中嵌入声音至关重要。不幸的是，开发跨平台 HTML5 游戏的最大问题是声音。在 HTML5 游戏开发中，Canvas 有很好的支持，视频在不断改进，但音频仍然很不完整。游戏不能同时播放声音和音乐。从这方面来看，HTML5 不一定是理想的 Flash 替代品！

其他音频问题包括:

*   不同的浏览器支持不同的声音格式(mp3，wav，ogg 等)
*   iPhones 和 iPads 在任何时候都只允许加载和播放一种 mp3 声音
*   Android 对

    <audio>标签和 JavaScript 音频 API 的支持有限</audio>

有几种方法可以解决这些问题:

*   对于格式问题，我复制了两种格式(OGG 和 MP3)的所有游戏声音，适用于大多数浏览器。
*   对于 iOS 平台，我在桌面版游戏中保留声音和音乐，移动版只保留音乐。
*   至于 Android，因为支持 Flash 音频播放，所以我用 jPlayer 来处理自动 HTML5/Flash 音频过渡。

然而，所有这些都应该是临时措施。围绕 HTML5 音频问题的真正解决方案涉及 W3C 与应用程序和浏览器开发人员合作，以创建一个每个人都可以遵循的良好标准。

## 结论

希望这些提示对指导你开发跨平台的 HTML5 游戏有所帮助。虽然这不是一件容易的事情，而且仍然存在音频集成等问题，但没有理由不开发一款可以跨多个平台运行的游戏，并将您的游戏体验带给各种各样的用户。

## 分享这篇文章