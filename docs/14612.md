# 结算浮动

> 原文：<https://www.sitepoint.com/clearing-floats/>

CSS 浮动模型的一个常见混淆点是，如果您希望浮动的包含元素完全包含它，就需要在浮动的内容下面有一个“clearing”元素。Eric Meyer 的[包含浮动](http://www.complexspiral.com/publications/containing-floats/)很好地解释了这个问题。

不幸的是，处理这种情况的标准方法是在文档中添加难看的附加标记，以充当“清理器”。通常可以利用文档中的另一个元素，但是经常没有这样的元素可用，必须添加额外的清除元素。

托尼·阿斯莱特已经发表了一篇文章，解释了解决这个问题的方法。首先观察到 CSS :after 伪元素可以在具有良好 CSS 2 支持的浏览器中使用，以使用 CSS 本身添加有效的清除元素:

 `.floatcontainer:after{
content: ".";
display: block;
height: 0;
overflow: hidden;
clear: both;
visibility:hidden;
}`

这足以解决大多数符合现代标准的浏览器中的问题，非常明显(并不是完全出乎意料)的是 Mac 和 Windows 都忽略了 Internet Explorer。Tony 建议对这些浏览器进行修复，这些浏览器利用了大量 CSS 漏洞，包括一个依赖于 IE/Windows 浮动模型中一个已记录的错误的漏洞。

我倾向于对涉及多个浏览器黑客的技术持谨慎态度，因为总是存在以不可预测的方式交互的未记录的 bug 的可能性。这项技术的细节值得一读，因为你肯定会学到一些关于 CSS 浮动模型和/或 IE 对它的不当处理，但我建议在实际实现它时要谨慎。尽管如此，它还是工具箱的另一个工具。

## 分享这篇文章