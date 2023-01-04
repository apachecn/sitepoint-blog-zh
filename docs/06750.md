# 生成的内容其实是内容吗？

> 原文：<https://www.sitepoint.com/is-generated-content-actually-content/>

<abbr title="Cascading Style Sheets Version 2.1">CSS2.1</abbr> 规范将[生成的内容](https://www.w3.org/TR/CSS21/generate.html "Generated content, automatic numbering, and lists (CSS 2.1)")总结为*“[呈现的]不是来自文档树的内容”*——换句话说，就是在 <abbr title="Cascading Style Sheets">CSS</abbr> 中定义的文本和图像，而不是在标记中定义的。

也许最常见的生成内容类型是用 <abbr title="Cascading Style Sheets">CSS</abbr> `content`属性定义的文本或文本符号，并使用`:before`或`:after`伪元素添加到页面中。例如，我经常喜欢在“更多”链接后添加一个向右的箭头，它在生成的内容中实现为 unicode 符号:

```
a.more:after
{
  content:"2192";
  margin-left:0.25em;
}
```

或者对于印刷媒体样式表，最好将链接`hrefs`扩展成完整的<abbr title="Uniform Resource Locators">URL</abbr>:

```
@media print
{
  a[href]:after
  {
    content:" (" attr(href) ")";
  }
}
```

但是尽管我们称之为生成的内容，我认为这是误导——因为 T2 生成的内容根本不是 T4 的内容，而是表现形式。

规范在这一点上不是特别明确。在示例和描述中，它确实将其称为“内容”，但是它给出的许多示例都是边缘情况(我们将在后面看到)。毕竟它是 <abbr title="Cascading Style Sheets">CSS</abbr> 的一部分，这难道不意味着表示吗？

我得出的结论是，它不应该被视为内容，通过查看如何使用生成的内容的不同示例，更重要的是，它如何被浏览器和辅助技术解释。看你是否同意。

## 理想的情况

关于如何使用生成的内容，最近一个理想的例子是 Craig Buckler 的 [CSS3 拨动开关](https://www.sitepoint.com/better-css3-toggle-switches/)。生成的内容用于向开关添加勾号或叉号，这提供了其状态的额外视觉指示:

```
input.switch:empty ~ label:before
{
  content:"2718";
  text-indent:2.4em;
  color:#900;
}

input.switch:checked ~ label:before
{
  content:"2714";
  text-indent:0.5em;
  color:#6f6;
}
```

这传达的信息是*可视化的和补充性的*，因为底层语义是由表单控件的选中状态传达的。所生成的内容仅仅是对红色和绿色的补充，因此患有红绿色觉缺陷的人将能够更容易地区分这些状态。

你不会依靠颜色、字体或边框来传达重要的语义，因为许多用户群体并没有意识到这些信息。对于生成的内容也是如此。

## 一些边缘情况

边缘情况是生成的内容用于补充已经存在但不太清楚的语义信息。[规范](https://www.w3.org/TR/CSS21/generate.html "Generated content, automatic numbering, and lists (CSS 2.1)")在其介绍中描述了两个这样的例子:

> 作者可能希望用户代理在图的标题前插入单词“图”,或者在第七章标题前插入“第七章”

以章节为例——假设所有的章节都在一页上，那么用户可以通过统计标题来知道他们正在阅读的章节号。因此，它所传达的信息已经存在，但在标题前加上“第 7 章”会更明显。

我认为在这种情况下，关键因素是决定额外信息的重要性。对于用户来说，他们正在阅读第七章真的很重要吗？如果没有显示额外的文本，重要的信息会丢失吗？

如果信息*是重要的*，那么它**不应该使用生成的内容来定义**。但是如果文本纯粹是装饰性的或表示性的，那么生成的内容是一个合适的选择。

那么列表的编号呢？在许多情况下，编号是任意的和表象的，即使是有序列表——也就是说，条目的顺序可能很重要，但是它们是否被编号为 **1.2.3。**或 **A.B.C.** 可能根本不重要。在这种情况下，生成内容(或者就此而言，原生列表编号)是一个合适的选择。

但是对于某些类型的文件，特别是像合同这样的法律文件，章和节的编号是其含义不可分割的一部分。一个合同条款可能会引用另一个特定编号的条款，在这种情况下，我会说**编号是内容，而不是表示**，因此生成的内容*不应该被使用*。事实上，我甚至可以说，这样的文档甚至不应该使用本地列表编号——编号应该硬编码到标记中。

## 实际差异

生成的内容与普通文本不同，这可以从几个关键的功能差异中看出。

只有最新的屏幕阅读器会朗读生成的内容，因此旧设备会错过任何以这种方式定义的内容(旧浏览器也会如此，如 <abbr title="Internet Explorer 7">IE7</abbr> )。尽管有些情况下你不希望屏幕阅读器读出文本——例如，我在开始提到的“更多”链接箭头，对读者来说说类似*“更多”这样的话是没有帮助的。*向右箭头。詹姆斯·克莱格提出了额外的 ARIA CSS 属性，这将允许作者控制这样的情况；但我个人认为，如果屏幕阅读器完全不讲生成内容会更好。

对于除 Opera 之外的所有浏览器，生成的内容**不包括在文本选择和剪贴板数据**中。在所有浏览器中，它**不会在文档**中创建文本节点，它**也不会成为事件**的目标。就 <abbr title="Document Object Model">DOM</abbr> 而言，生成的内容是不存在的。

但是它确实存在于表示层中——即在 <abbr title="Cascading Style Sheets Document Object Model">CSS DOM</abbr> 中——并且有一种简单的方法可以从生成的内容中获取文本，方法是引用伪元素的计算样式的`content`属性:

```
var element = document.getElementById('whatever');
var text = window.getComputedStyle(element, ':before').content;
```

## 广泛的结论

对我来说，这些实际差异强调了生成内容和普通文本之间的关键概念差异——生成内容不是内容文本，就像背景图像不是内容图像一样。

文本仍然可以在不传达语义的情况下传达信息——就像颜色和其他设计选择传达信息一样，当信息缺失时，文档仍然有意义。这才是真正的要点——生成的内容可能根本不会被用户看到或察觉，所以如果它很重要，就应该放在标记中。

## 分享这篇文章