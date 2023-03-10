# 字幕(HTML 元素)

> 原文：<https://www.sitepoint.com/marquee-html-element/>

## 句法

`<``marquee``align``=``"` { `top` | `middle` | `bottom` } `"``behavior``=``"` { `scroll` | `slide` | `alternate` } `"``direction``=``"` { `left` | `right` } `"``loop``=``"``value (number of loops)``"``bgcolor``=``"``color name or hex value``"``height``=``"``integer (pixels or %)``"``width``=``"``number (pixels or %)``"``hspace``=``"``integer (pixels or %)``"``vspace``=``"``number (pixels or %)``"``scrollamount``=``"``number``"``scrolldelay``=``"``number``"``>``<``/``marquee``>`

## 描述

marquee 元素为浏览器提供了一种无需借助 JavaScript 技术即可呈现在页面中移动的文本的方式。marquee 是非标准的，但是享有(或者可能遭受)良好的浏览器支持。

它通常被认为是一个非常不友好的元素，这给许多用户带来了困难，他们可能很难在文本移动时跟踪文本。我们的建议是不要使用它。JavaScript 是一种更适合在屏幕上移动文本的技术——如果这样做有正当理由的话。

## 例子

没有附加属性的简单选框标记如下:

```
<p>*<marquee>*This text is a moving target. Don't you just love
it?*</marquee>*</p>

```

## 将此用于…

marquee 元素应该包含少量文本。

## 和睦相处

| 微软公司出品的 web 浏览器 | 火狐浏览器 | 旅行队 | 歌剧 | 铬 |
| --- | --- | --- | --- | --- |
| <abbr title="Internet Explorer 5.5">5.5</abbr> | <abbr title="Internet Explorer 6.0">6.0</abbr> | <abbr title="Internet Explorer 7.0">7.0</abbr> | <abbr title="Internet Explorer 8.0">8.0</abbr> | <abbr title="Firefox 1.0">1.0</abbr> | <abbr title="Firefox 1.5">1.5</abbr> | <abbr title="Firefox 2.0">2.0</abbr> | <abbr title="Safari 1.3">1.3</abbr> | <abbr title="Safari 2.0">2.0</abbr> | <abbr title="Safari 3.1">3.1</abbr> | <abbr title="Safari 4.0">4.0</abbr> | <abbr title="Opera 9.2">9.2</abbr> | <abbr title="Opera 9.5">9.5</abbr> | <abbr title="Opera 10.0">10.0</abbr> | <abbr title="Chrome 2.0">2.0</abbr> |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 全部 | 全部 | 全部 | 全部 | 全部 | 全部 | 全部 | 全部 | 全部 | 全部 | 全部 | 全部 | 全部 | 全部 | 全部 |

对字幕的支持是多种多样的。一些浏览器会停止动画而不考虑循环设置，一些会不同地解释方向，一些会尊重高度和宽度属性，大多数根本不注意。然而，鉴于这不是任何 HTML 建议的一部分，这可能是意料之中的。简单的答案是，如果您想让有效的文档能够一致地呈现，并且不会让读者感到厌烦，就不要使用它。

## 分享这篇文章