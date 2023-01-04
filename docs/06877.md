# jQuery 2.0 的新特性

> 原文：<https://www.sitepoint.com/whats-new-in-jquery-2-0/>

网络上最受欢迎的 JavaScript 库的第二个分支于 2013 年 4 月 18 日发布。 [jQuery 2.0 现在可以下载了](http://jquery.com/download/)但是不要急着安装它——有一个重大的变化…

## 不再支持 IE6/7/8

*(或 IE9 和 IE10，如果它们用于“兼容性视图”模式)。*

我仍然认为放弃 IE8 还为时过早，但是团队不能再等了。jQuery 2.0 删除了所有用于节点选择、DOM 操作、事件处理和 Ajax 的遗留 IE 代码。

这导致文件大小减少了 11%，从 32，819 字节减少到 29，123 字节。这是 3.6Kb 的 gzipped minified 版本——即使在拨号连接上也不太可能被注意到。诚然，该团队希望节省更多，但发现 Android/Webkit 2.x 浏览器仍然需要许多变通办法。

如果您需要支持 IE8 及更低版本，请暂时使用 jQuery 1.9.x。您可以有条件地在所有其他浏览器中加载 2.0 版，但是:

1.  条件加载将抵消文件大小减小和处理方面的任何收益，并且
2.  您可能会遇到两个 jQuery 分支之间的差异。该团队承诺将 API 分歧最小化，但几乎肯定会有问题。jQuery 1.10 将解决已知的兼容性问题。

## 自定义版本

在 2.0 版中，定制构建特性得到了改进，因此您可以排除 12 个未使用的模块中的任何一个，并将 jQuery 压缩到 10Kb 以下。可以省略的模块有:

*   ajax :所有的 ajax 功能、传输和事件简写。
*   **ajax/xhr** :仅 XMLHTTPRequest Ajax 传输。
*   **ajax/script** : `<script>`仅 ajax 传输。
*   **ajax/jsonp** : JSONP 仅 ajax 传输(依赖于 ajax/script)。
*   **css** :方法`.css()`加上非动画`.show()`、`.hide()`和`.toggle()`。
*   **已弃用**:已弃用的方法(目前仅`.andSelf()`)。
*   **尺寸** : `.width()`和`.height()`方法，包括`inner-`和`outer-`变化。
*   **效果**:方法`.animate()`及其简称如`.slideUp()`。
*   **事件别名**:事件附加/触发简称，如`.click()`。
*   **偏移**:方法`.offset()`、`.position()`、`.offsetParent()`、`.scrollLeft()`、`.scrollTop()`。
*   **换行**:方法`.wrap()`、`.wrapAll()`、`.wrapInner()`、`.unwrap()`。
*   咝咝声:咝咝声选择器引擎。当这个模块被排除时，它被一个基于本机`querySelectorAll`方法的初级选择器引擎所取代，该引擎不支持一些高级的 jQuery 选择器。

例如，如果你使用 CSS3 动画而不是 jQuery 方法，你可以省略**效果**模块，也可能省略**尺寸**、**偏移**、**环绕**和**嘶嘶**。

创建自己的定制版本不适合胆小的人，你需要一些 Git、Node.js 和 Grunt 的知识。完整的说明是可用的，但是，从长远来看，我希望团队能够实现一个类似于 [Modernizr](http://modernizr.com/download/) 的在线构建过程。

## 我应该升级吗？

理解 jQuery 2.0 具有与 jQuery 1.9 同等的 API 是很重要的。有少量的错误修复，但没有新功能。

然而，如果你是那些放弃对 IE6/7/8 支持的幸运开发者之一，[抓住 jQuery 2.0](http://jquery.com/download/) 不要回头。

## 分享这篇文章