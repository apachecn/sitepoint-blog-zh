# 火狐 35 的新功能

> 原文：<https://www.sitepoint.com/whats-new-firefox-35/>

我们又来了。火狐 35 于 2015 年 1 月 13 日发布。很少有用户会注意到任何区别，但是对于 web 开发人员来说，在异常大的 46MB 更新(在 Windows 上)下潜伏着一些令人兴奋的新功能。如果你还没有升级，从菜单中选择**帮助** > **关于火狐**或者前往 firefox.com 的[。](http://firefox.com/)

## CSS3 筛选器属性

属性允许你在图像或 DOM 中的任何元素上应用类似 Photoshop 的滤镜。这些影响包括:

*   `blur(radius)` —应用高斯模糊
*   `brightness(percent)` —改变亮度。100%不变，超过 100%使元素变亮，低于 100%使元素变暗(0%是全黑)。
*   `contrast(percent)` —改变对比度。100%不变，超过 100%对比度较低，低于 100%对比度较高(0%为全黑)。
*   `grayscale(percent)` —将彩色图像转换为灰度图像。0%不变，100%为全灰度。
*   `sepia(percent)` —将彩色图像转换为棕褐色。0%不变，100%全棕褐色。
*   `drop-shadow(offsetx, offsety, blur, spread, color)` —类似于标准的`box-shadow`效果，除了阴影将应用于应用了 alpha 蒙版的非矩形图像。
*   `hue-rotate(angle)` —根据色环周围的度数改变颜色。
*   `saturate(percent)` —使颜色饱和。100%不变。
*   `invert(percent)` —反转颜色。0%不变，100%创建完整的负像。
*   `opacity(percent)` —与 CSS `opacity`属性相同，尽管有些浏览器会提供硬件加速以提高性能。
*   `url(file.svg#target)` —指定一个 SVG 过滤器

在同一个声明中可以应用任意数量的效果，例如

```
.element {
	filter: blur(2%) grayscale(50%) brightness(10%);
}
```

除了 Internet Explorer 之外的所有浏览器现在都支持过滤器，所以我希望看到过滤器的使用呈指数级增长——尤其是在与动画结合使用时。更多信息和示例，请参考 [MDN 过滤器参考](https://developer.mozilla.org/en-US/docs/Web/CSS/filter)。

## CSS 列表符号属性

没有必要再使用无聊的数字或字母列表了！可以向`list-style`属性传递一个`symbols`值，该值定义了您想要的任何字符或图标序列。例如:

```
ul {
	list-style: symbols(cyclic 'x', 'y', 'z');
}
```

将显示无序列表，第一项为“x”，第二项为“y”，第三项为“z”。`cyclic`参数指定符号重复，因此第四项再次为“x”。

更多信息和示例，请参考 [MDN 符号参考](https://developer.mozilla.org/en-US/docs/Web/CSS/symbols)。

## JavaScript 更新

有许多 JavaScript 增强和补充，包括:

*   `element.closest(selector)`返回最近的祖先(父母、祖父母等。)添加到与选择器匹配的元素中(与 [jQuery 的 API](http://api.jquery.com/closest/) 相同)
*   [资源计时 API](https://www.w3.org/TR/resource-timing/) 允许您分析文档中所有资源的下载统计数据。更多信息，请参考奥雷利奥·德·罗萨的[对资源计时 API](/introduction-resource-timing-api/) 的介绍。
*   您现在可以使用类似数组的符号来引用 CSS 样式，比如`element.style['background-image']`以及常见的`element.style.backgroundImage`属性。这使得 Firefox 与 Chrome 的实现保持一致。
*   使用`let`重新定义变量或者在`let`语句之前引用它们现在会抛出一个错误。

## 开发人员工具更新

![Firefox 35 inspect pseudo element](img/0c9f4c9040033ac7aed1d89fef081382.png)您可以使用`::before`和`::after`来检查 CSS 中添加的伪元素。该实现与其他工具略有不同，因为元素显示在 DOM 树中，而不是 CSS 属性中——这实际上更有意义。单击元素以查看其样式。

CSS 源代码映射现在默认为预处理器启用，比如 Sass 和 LESS(假设您的编译器提供了该功能)。确保在检查器或样式编辑器配置中设置了“显示原始源代码”,这样您就可以链接到预编译的代码，而不是生成的 CSS。

最后，您可以查看节点 DOM 属性列表，方法是选择它，打开 split 控制台，然后输入`inspect($0)`。这有点麻烦，所以我希望该功能将成为像 Firebug 提供的面板。

## 杂项更新

其他随机更新包括:

*   CSS [`mask-type`](https://developer.mozilla.org/en-US/docs/Web/CSS/mask-type) 属性默认启用。它定义了 SVG `<mask>`元素是亮度还是 alpha 蒙版。
*   支持 WOFF2 字体。
*   图像大小调整性能得到了改善。
*   你好——火狐的原生 Skype 视频聊天系统——现在有了聊天室。
*   搜索栏界面得到了改进。我看不出有什么不同，但它可能只适用于某些地区，如美国。
*   一个新的**应用**工具菜单和图标允许快速进入[火狐市场](https://marketplace.firefox.com/)。许多应用程序只能在 Firefox 操作系统上运行，但你会发现许多应用程序适用于移动和桌面浏览器。
*   PDF 渲染系统已经升级。
*   九个安全漏洞已被修复。

版本 35 是另一个很好的改进，尽管我很惊讶升级的规模如此之大？

Firefox 拥有良好的市场份额，对于需要高度可定制应用的网络开发者和高级用户来说，仍然是一个很好的选择。然而，使用率一直在缓慢下降。

这款浏览器没有历史版本的问题和高内存使用率，但它很难赶上 Chrome 和 Opera 的速度和稳定性。我希望 Mozilla 能吸引用户回到 Firefox，但这需要相当大的努力。

如果你已经改用 Chrome 作为你的主要浏览器，什么会让你考虑使用 Firefox？

## 分享这篇文章