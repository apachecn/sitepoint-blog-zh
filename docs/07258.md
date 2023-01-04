# 火狐 18 的新功能

> 原文：<https://www.sitepoint.com/firefox-18-whats-new/>

新年，新浏览器。Mozilla 于 2013 年 1 月 8 日发布了 Firefox 18。你可能已经收到了更新，如果没有，点击菜单**帮助** > **关于火狐** > **查看更新**。或者，去 getfirefox.com[下载一份新版本。](http://getfirefox.com/)

火狐一直在快速发展，和 Chrome 一样，不同版本之间的重大变化很少。然而，在 18 版本中有许多令人讨厌的新玩具…

## 新的 IonMonkey JIT 编译器

根据 Mozilla 的基准测试，IonMonkey 提供了 26%的 JavaScript 性能提升。我不太相信它会导致现实世界中明显的速度提升，但这是一个令人印象深刻的说法。

## @支持支持

[@supports](https://www.sitepoint.com/supports-native-css-feature-detection/) 是 CSS 的原生特征检测工具，例如

```
@supports (text-shadow: 0 0 5px #000)
{
	.blur-text
	{
		text-shadow: 0 0 5px #000;
		color: transparent;
	}
} 
```

本质上，不再需要类似 Modernizr 的 JavaScript 风格检测。这是一个实验性的特性，必须通过在 about:config 中将`layout.css.supports-rule.enabled`设置为 **true** 来启用。

(注意@supports 在 Firefox 17 中是可用的，但是我没有发表一篇关于那个版本的文章——几乎没有什么可报道的！)

## CSS Flexbox 布局

不需要太多的细节，新的 CSS3 [灵活的框布局模块](http://dev.w3.org/csswg/css3-flexbox/)允许你在不同的屏幕尺寸下安排可预测行为的元素。不需要使用媒体查询或复杂的浮动块。

现在使用该功能还为时过早；只有 Opera 12.1+实现了不带前缀的良好支持。然而，如果你在 about:config 中将`layout.css.flexbox.enabled`设置为 **true** ，你可以在 Firefox 中尝试一下。有关更多信息，请参阅 MDN 上的[使用 CSS 柔性盒](https://developer.mozilla.org/en-US/docs/CSS/Using_CSS_flexible_boxes)。

## 无前缀页面可见性 API

[页面可见性 API](https://developer.mozilla.org/en-US/docs/DOM/Using_the_Page_Visibility_API) 确定网页是否可见。因此，您可以检测浏览器何时最小化或用户何时切换选项卡，并做出相应的反应，例如停止视频播放、取消自动幻灯片动画、减慢 Ajax 轮询请求等。

这个 API 在 Firefox 版本 10 中就有了，前缀是 moz。Firefox 18 以标准的无前缀实现加入了 Opera 12.10。

## WebRTC 支持

[WebRTC](http://www.webrtc.org/) —网络实时通信—提供本地浏览器功能，无需插件即可进行 VOIP、聊天、视频聊天和文件共享。可以通过在 about:config 中将`media.peerconnection.enabled`设置为 **true** 来启用初步的实现，但是你需要等待一段更长的时间来获得一个可靠的 API。

## 杂项更新

至今印象不深？关于:

*   更快的标签切换
*   改进的图像缩放
*   [window . devicepixelrratio](http://www.quirksmode.org/blog/archives/2012/06/devicepixelrati.html)实现
*   W3C 触摸事件取代了 MozTouch 事件
*   Mac OS 上的 Retina 显示屏支持
*   已经在 Mac OS 上实现了`window.navigator.battery` API
*   Android 版火狐的搜索建议
*   改进 Android 上的网络钓鱼和恶意软件检测
*   数百只虫子被压扁

最后，动画题材粉丝*(有吗？)*听到功能被移除会很失望。Mozilla 发现，随着图像尺寸增加以适应各种屏幕分辨率和 Retina 显示屏，会出现性能问题。如果你不能在没有动画主题的情况下使用 Firefox，安装[人物角色洗牌插件](https://addons.mozilla.org/en-US/firefox/addon/personas-shuffler/)。

在过去的一两年里，火狐可能失去了一些 Chrome 用户，但是 Mozilla 并没有失去他们的动力。该浏览器速度快、稳定、外观好、提供优秀的开发工具和标准支持。再试一次——火狐 18 可能会让你大吃一惊。

## 分享这篇文章