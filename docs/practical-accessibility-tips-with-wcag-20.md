# WCAG 2.0 实用辅助功能提示

> 原文：<https://www.sitepoint.com/practical-accessibility-tips-with-wcag-20/>

德里克·费瑟斯通最近写了一篇名为“[什么时候是可访问性的合适时机”的博客。](http://boxofchocolates.ca/archives/2009/03/22/the-right-time)“他在其中研究了什么时候投资无障碍环境是合适的。是稍后发布产品并添加可访问性更好，还是一开始就内置可访问性更好？

就网站建设而言，我同意詹姆斯·爱德华兹的观点，他认为确保内容的可访问性是网站开发者和设计者的职责。

> 我们应该努力创造无障碍的内容，因为这是我们工作的一部分。而且坦白说，这并不需要太多的努力；不难。

我必须承认，对我来说，可访问性似乎经常是一个雷区，每个看似微小的决定都有可能造成一场可访问性灾难。我已经寻找了一个中心资源，它将向我展示正确标记的实际例子，这样我就可以避免犯将来很难修复的可访问性错误。我认为“[WCAG 2.0](https://www.w3.org/TR/WCAG20-TECHS/)的技术”文档是一个很好的起点。

通常 W3C 文档是乏味的，技术性的，并且很难涉水而过——相信我，我在编辑 [SitePoint CSS 参考文献](https://reference.sitepoint.com/)时做了很多涉水工作。但是“WCAG 2.0 技术”与此完全不同；它是为 web 开发人员和设计人员编写的——而不是浏览器开发者——这使得它非常容易使用(哈哈)。

作为所介绍技术的快速示例，这里有一些构造表单的技巧。您会马上注意到，这些技术经常看起来像是基本常识，但可访问性通常就是这样。

## 使用标准 HTML 表单元素

如 [H91:使用 HTML 表单控件和链接](https://www.w3.org/TR/2008/NOTE-WCAG20-TECHS-20081211/H91.html)中所述，建议使用标准的 HTML 表单控件和链接。标准表单元素已经建立了由用户代理提供的键盘快捷键，辅助技术软件利用了每个操作系统中内置的相关辅助功能 API。是的，可以这么简单。

## 始终使用明确的标签

[H44:使用标签元素将文本标签与表单控件相关联](https://www.w3.org/TR/2008/NOTE-WCAG20-TECHS-20081211/H44.html)建议您应该始终为所有表单字段准备一个带有适当`[for](https://reference.sitepoint.com/html/label/for)` [属性](https://reference.sitepoint.com/html/label/for)值的[标签元素](https://reference.sitepoint.com/html/label):

```
<label for="fname">First Name:</label> 
<input type="text" name="fname" id="fname" value=""/>
```

它还建议您避免像下面这样的隐式标签，因为有些屏幕阅读器存在兼容性问题:

```
<label>First Name: <input type="text" name="fname" id="fname" value=""/></label>
```

## 使用`tabindex` 属性

[H4:通过链接、表单控件和对象创建逻辑标签顺序](https://www.w3.org/TR/2008/NOTE-WCAG20-TECHS-20081211/H4.html)突出了 [`tabindex`属性](https://reference.sitepoint.com/html/select/tabindex)的重要性。将属性添加到所有表单元素中以创建逻辑 tab 键顺序，这样可以高效地进行表单的键盘导航:

```
<label for="fname">First Name:</label> 
<input type="text" name="fname" id="fname" value="" tabindex="1"/>
```

## 指示必填字段

指示所需的表单控件是我在这里提到的几个控件中最喜欢的一个，也是指南中实用帮助的一个例子。它概述了三种简单、可访问的方式来指示一个字段是必需的。

在字段标签中使用单词*必需的*:

```
<label for="fname">First Name (required):</label> 
<input type="text" name="fname" id="fname" value="" tabindex="1"/>
```

如果你使用星号来表示必填字段(就像很多网站一样)，那么尝试使用 [`abbr`元素](https://reference.sitepoint.com/html/abbr)来添加非常需要的信息:

```
<label for="fname">First Name <abbr title="required">*</abbr>:</label> 
<input type="text" name="fname" id="fname" value="" tabindex="1"/>
```

如果使用图标，确保使用 [`alt`属性](https://reference.sitepoint.com/html/img/alt):

```
<label for="fname">First Name <img src="required.png" alt="Required field"/>:</label>
<input type="text" name="fname" id="fname" value="" tabindex="1"/>
```

在 WCAG 2.0 技术页面上有更多的提示，足以为可访问性艺术打下坚实的基础。当你通读它们的时候，你会意识到可访问性更多的是关于如何让每个人都能访问你的内容，而不仅仅是各种屏幕阅读器。这就是网络的意义所在。

## 分享这篇文章