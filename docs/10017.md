# 何时以及如何在视觉上隐藏内容

> 原文：<https://www.sitepoint.com/when-and-how-to-visually-hide-content/>

在 web 页面上可视化地隐藏内容，通常是文本内容，在 web 设计和开发中有时是一种可行的技术。这样做有几个原因，最重要的是为了改善屏幕阅读器用户的体验。其他原因包括改善 CSS 无法呈现时的可读性，以及改善搜索引擎优化(SEO)。对于屏幕阅读器用户，需要的情况包括:

*   标记具有视觉传达意义的表单元素，但不具有其他意义，例如用于搜索和电话号码区号的输入。
*   在相关内容具有视觉传达意义的地方提供标题，但在其他地方没有，如菜单。
*   隐藏“跳转到”链接——跳过页面上的大块内容的锚链接。对于视力正常的键盘用户来说，这些类型的链接应该是可聚焦的和可视的。最典型的是网页开始处的“跳到主要内容”链接，它通常跳过网页桅杆和全局导航。
*   在交互可能会使辅助技术的用户感到困惑的特殊情况下，提供特殊说明。在这一点上要谨慎，而且要小心；重要的是不要像英国人口普查网站那样居高临下。

请注意，*在视觉上隐藏*内容与*故意完全隐藏*内容是一个独立的概念，这是一种可以为所有用户设计的技术。一个例子是一组选项卡和面板，其中一次只显示一个面板的内容。在这种情况下，使用 CSS `display:none`和/或`visibility:hidden`是一种充分且可接受的解决方案。

## 经典方法

多年来，一种被广泛实施和接受的隐藏内容的方法已经被使用。WebAIM 在他们的文章 [CSS in Action:仅为屏幕阅读器用户提供的不可见内容](http://webaim.org/techniques/css/invisiblecontent/)中对此做了很好的解释。下面是对隐藏内容进行编码的经典方法的一个示例，绝对定位包含文本的元素并将其移出屏幕:

```
.hidden {
    position: absolute;
    left: -9999em;
    top: auto;
    width: 1px;
    height: 1px;
    overflow: hidden;
    }

<div class="hidden">Hidden content here.</div>
```

CSS 的变化已经持续了几年，包括使用负的顶部位置(而不是左边)；负文本缩进；高度为 0。

经典方法及其变体存在一些问题，例如:

*   当不可见的元素获得焦点时，可能会导致视口笨拙地跳跃
*   当语言方向是从右向左时，负文本缩进方法将不起作用
*   VoiceOver 屏幕阅读器(Apple)不会阅读高度为 0 的元素中的内容
*   隐藏标题可能会被朗读为“文本标题”，而不是标题文本(在 SnowLeopard 上使用带 VoiceOver 的 Safari 报告)

## 新方法:剪辑

最近出现了一种可视隐藏内容的新方法来解决这些问题，称为 CSS 裁剪。这显然是由 Jeff Burnz 在他的 AdaptiveThemes 文章[中首先提出的，他使用 CSS clip 作为隐藏内容的一种可访问的方法](http://adaptivethemes.com/using-css-clip-as-an-accessible-method-of-hiding-content)。核心概念可以归结为下面这个片段。实际上， [CSS 2.1 clip 属性](https://www.w3.org/TR/CSS21/visufx.html#clipping)允许您使用上、右、下和左值指定绝对定位元素的尺寸，为内容创建一个装箱的容器。通过将所有值设置为 0 像素，内容变得不可见。

```
.hidden {
  position: absolute;
  clip: rect(0px 0px 0px 0px);
}
```

理论上，这应该可以很好地工作，但是我们都知道浏览器的渲染会有很大的不同，而且浏览器很多时候并不完全符合 W3C 标准。屏幕阅读器应用程序的行为也可能不同。其他开发人员改进了 clip 方法，包括 Jonathan Snook 在[中的隐藏内容以提高可访问性](http://snook.ca/archives/html_and_css/hiding-content-for-accessibility)和 Thierry Koblentz 在[中的裁剪隐藏内容以提高可访问性](http://yaccessibilityblog.com/library/css-clip-hidden-content.html)。因此，经过多次迭代和社区中的大量辛勤工作，一些很好的防弹增强功能得以实现，包括(参见下面的最终代码):

*   适应 IE6 和 IE7 中的不同语法；请参见带注释的行。
*   修正了 Webkit 和 Opera 中的一个错误，当指南说不应该的时候，剪辑的内容会导致溢出；用`overflow:hidden`固定。
*   将高度设定为 1 像素，以确保 VoiceOver 读取内容。
*   作为额外的预防措施，填充和边框属性被设置为 0，以防止与剪辑后的框的边缘相关的任何问题。

这是最后的代码片段。请注意，剪辑值从 0 更改为 1，这实现了相同的结果。添加这个类或者用这个类替换你当前的类，你就有了一个更加可靠和易于使用的隐藏内容的技术！

```
.hidden {
  position: absolute !important;
  clip: rect(1px 1px 1px 1px); /* IE6, IE7 */
  clip: rect(1px, 1px, 1px, 1px);
  padding: 0 !important;
  border: 0 !important;
  height: 1px !important; 
  width: 1px !important; 
  overflow: hidden;
}
```

## 摘要

您可能希望隐藏网页上的内容有几个原因，主要是为了屏幕阅读器用户。经典的 CSS 方法是可行的，但是在某些边缘情况下可能会有问题。迄今为止，隐藏内容的剪辑方法已被证明是一种改进的技术。仍然需要一些额外的代码来适应所有的浏览器和屏幕阅读器，但总的来说是一个更可靠的解决方案。

## 延伸阅读:

*   Drupal 7，两个提高可访问性的新系统类
*   [用纯 CSS3](http://dev.opera.com/articles/view/css3-show-and-hide/) (Dev。歌剧)
*   [为屏幕阅读器用户隐藏内容](http://green-beast.com/blog/?p=237) (Mike Cherim，Beast 博客)

## 分享这篇文章