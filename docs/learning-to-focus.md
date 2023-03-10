# 学习专注()

> 原文：<https://www.sitepoint.com/learning-to-focus/>

感谢好运，我有一双功能正常的眼睛。当谈到浏览网页时，这是一个好处，因为将我的注意力从页面的一部分转移到另一部分只需要半意识地收缩我的眼外肌并转动我的眼球一小部分。

虽然我的视觉焦点——因此也是认知焦点——现在可以说已经改变了，但是用户界面本身并不知道我的注意力已经转移。至少，直到我开始与新仔细检查的区域进行物理交互。换句话说，转动我的眼球是一个没有记录的事件。

这里存在一个非常普遍但经常未被解决的可访问性问题:如果我不用眼睛(和鼠标手来指)，我如何“聚焦”在某个东西上以便与之交互？我相信你知道，答案是通过[项目焦点](http://en.wikipedia.org/wiki/Focus_%28computing%29)。然而，这并不像在我们的设计中加入“可聚焦”的元素那么简单。有时候，不是用户，而是代表用户的界面必须在不同的区域之间移动焦点。这需要开发人员方面深思熟虑的[焦点管理](http://www.yuiblog.com/blog/2009/02/23/managing-focus/)。

对于可访问性来说，管理焦点在许多甚至是最简单的 JavaScript 驱动的交互式小部件中是必不可少的，但是用自动化的 <abbr title="Quality Assurance">QA</abbr> 工具来测试正确的用法几乎是不可能的。这就是为什么我今天想带你们看几个简单的例子。

## 滚动条动画

在这个例子中，我邀请你想象你已经“增强”了一个同页导航链接，这样，你就不会突然跳到链接的目标片段(`#section1`)，而是通过一个 JavaScript [scrollTop](https://developer.mozilla.org/en-US/docs/Web/API/Element.scrollTop) 动画被引导到这个目的地。使用这种技术时，关于可访问性需要注意的重要一点是必须覆盖`<a>`元素的默认函数。

```
event.preventDefault();
```

通过这样做，你是在告诉浏览器要不惜一切代价避免做任何标准的、预期的或可互操作的事情。你告诉链接*不要链接*。事实上，确保链接将用户正确带到页面片段的唯一方法是关闭 JavaScript。那好吧。

通过简单地链接到一个页面片段——就像关闭 JavaScript 一样——浏览器基本上*关注*这个片段。这是屏幕阅读器输出和键盘交互的基础。通过动画显示`scrollTop`，没有这样的聚焦动作发生，这意味着屏幕阅读器输出和键盘交互从不再可见的页面区域继续。这不好。

## 补救措施

我们需要通过 JavaScript 聚焦目标片段。首先，我们需要以编程方式使片段可聚焦，这需要使用`tabindex`属性。`-1`的`tabindex`值是一个特殊的值，这意味着脚本可以聚焦元素，但不能聚焦用户。在这种情况下，这比`tabindex: 0`更好，因为没有理由使用 **TAB** 键来聚焦这个非交互元素。

*注意:感谢 [Patrick 和评论](https://www.sitepoint.com/learning-to-focus/#comment-1344063113)中的其他人指出，tabindex 值应该使用“-1”而不是“0”，我们现在已经纠正了这一点。*

```
<section id="section1" tabindex="-1">
    ...
</section>
```

我们的第二个任务是在动画的回调中包含 JavaScript `focus()`方法，确保片段在动画结束后被聚焦*。*

```
document.getElementById('section1').focus();
```

最后，在 URL 中记录我们的子页面位置是一个好主意，就像简单地链接到片段一样(例如`http://my-site/#section1`)。通过这种方式，我们可以将地址复制为指向特定部分的链接(即我们可以“深度链接”)。在`focus()`发生后包含以下行:

```
window.location.hash = 'section1';
```

当然，您可以用一个变量替换“section1 ”,这个变量基于相应链接的`href`减去`#`。

```
<a href="#whatever">scroll to section 'whatever'</a>
```

## 结果呢

下面嵌入了一个小小的 CodePen 演示。试着像习惯使用键盘的用户一样，只使用**键**和**键** + **键**。

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看笔 [idxjL](http://codepen.io/SitePoint/pen/idxjL/) 。