# JavaScript 和屏幕阅读器

> 原文：<https://www.sitepoint.com/javascript-and-screen-readers/>

以下内容转载自《科技时报》第 163 期。

试着用一个屏幕阅读器来使用一个典型的 Ajax 网络应用程序，比如 GMail，你就再也不想碰屏幕阅读器了。为了支持那些没有那种奢侈的用户，那么，你必须不使用 JavaScript 吗？

这个问题的答案最终取决于您需要构建的 web 应用程序的类型。当然，如果你正在开发像 Photoshop 的网络版[一样愚蠢的东西，你可以在一开始就排除屏幕阅读器支持。](http://news.com.com/2100-7345_3-6163015.html)

然而，大多数网络应用程序将会陷入一个灰色地带，在那里*可能*支持屏幕阅读器——即使不牺牲任何你想提供给你的其他用户群的 JavaScripty 优点。那么问题就变成了，*实用吗？*

## 实际例子

暂且把 GMail 之类的东西放在一边，让我们再次看看手风琴控件，它是我即将出版的书中的一个例子。

几个问题之前，我们修改了这个例子，使键盘用户可以访问它。我们所要做的就是在手风琴的每个部分的标题中包含一个超链接，这样它就可以通过键盘聚焦。正如我们将看到的，让屏幕阅读器用户可以访问这个构造同样简单——同时做这两件事有点棘手！

为了提醒您，此页面的 HTML 代码如下所示:

```
<ul class="accordion">
  <li id="archer">
    <h2><a href="#archer">Jonathan Archer</a></h2>
    <p>Vessel registry: NX-01</p>
    <p>Assumed command: 2151</p>
    ...
  </li>
  ...
</ul>
```

如果这就是这个页面的全部内容，那么它对于屏幕阅读器来说是完全可以访问的。只有当我们的 JavaScript 代码通过在组成手风琴的列表项上动态设置一个类`"collapsed"`来隐藏部分页面内容时，我们才会遇到麻烦。该类隐藏列表项中除标题以外的所有内容:

```
li.collapsed * {
  display: none;
}
li.collapsed h2,
li.collapsed h2 a:link, li.collapsed h2 a:visited {
  display: block;
}
```

这里的问题是，使用`display: none`隐藏一个元素也会对屏幕阅读器隐藏该元素，并且屏幕阅读器用户无法阅读页面的这一部分。

当然，如果通过关闭`"collapsed"`属性来显示元素也向屏幕阅读器用户显示了这些元素，这可能不是问题。在这种情况下，屏幕阅读器用户可以像其他用户一样使用 accordion:通过“单击”accordion 中的标题来显示其内容。

然而，事实证明，情况并非总是如此。大多数屏幕阅读器使用页面可见内容的静态副本——一个不经常且不可预测地更新的副本(尽管一些供应商的早期迹象表明这可能会改变)。甚至在屏幕阅读器中，*会在用户点击链接时*刷新页面内容的副本，让用户*意识到*内容已经泄露也是有问题的。

不，在这种情况下，您的最佳选择是隐藏折叠的折叠内容，使屏幕阅读器用户仍然能够阅读它们。最有用的技术叫做 **offleft 定位**，CSS 代码如下所示:

```
li.collapsed * {
  position: absolute;
  left: -9999px;
}

li.collapsed h2,
li.collapsed h2 a:link, li.collapsed h2 a:visited {
  position: static;
}
```

本质上，这种技术通过将页面元素放置在页面左侧(准确地说，是页面左侧的 9999 个像素)来隐藏页面元素，屏幕阅读器仍然可以看到这些元素。

因此，现在屏幕阅读器用户可以阅读页面的全部内容，而不会受到 JavaScript 驱动的折叠效果的任何干扰！问题解决了，对吧？

不完全是。你看，左置不仅向屏幕阅读器展示了隐藏的元素，也让键盘用户可以使用它们！在这种情况下，当键盘用户点击 Tab 键遍历页面中可聚焦的元素时，焦点也会遍历隐藏内容中的所有超链接。由于这些元素是不可见的，键盘焦点似乎会消失，导致愤怒的电话。

因为没有可靠的方法既能对键盘用户隐藏元素，又能对屏幕阅读器用户隐藏元素，所以我们需要采取不同的方法。当键盘焦点被给予一个隐藏的元素时，我们做一些有用的事情怎么样…比如展开手风琴的相关部分！

我们可以向 accordion 中的每个可聚焦元素(当然，除了始终可见的标题中的链接)添加一个`focus`事件监听器:

```
var folds = accordion.getElementsByTagName("li");
for (var i = 0; i < folds.length; i++)
{
  var foldLinks = folds[i].getElementsByTagName("a");
  var foldTitleLink = foldLinks[0];
  addEvent(foldTitleLink, "click", Accordion.clickListener);

  for (var j = 1; j < foldLinks.length; j++)
  {
    addEvent(foldLinks[j], "focus", Accordion.focusListener);
  }

}
```

当被触发时，`focusListener`功能将展开包含它的手风琴部分:

```
focusListener: function(event)
{
  var element = this;
  while (element.parentNode)
  {
    if (element.parentNode.className == "accordion")
    {
      Accordion.expand(element);
      return;
    }
    element = element.parentNode;
  }
}
```

这就是它——键盘和屏幕阅读器用户都可以访问的手风琴控件！

## 更复杂的情况

正如您所料，并不是所有的 JavaScript 增强都可以避免干扰屏幕阅读器和键盘导航。有时候，你能做的最好的事情就是让这些用户更容易地禁用那些对他们没有用的东西。

例如，在[最近的一篇博客文章](http://adactio.com/journal/1277/)中，[杰里米·基思](http://adactio.com/)称赞了[丹·钱皮恩](http://blether.com/)的新社交书评网站 [Revish](http://www.revish.com/) ，该网站为用户提供了禁用网站 Ajax 功能的选项，这些功能在用户注册账户时无法与屏幕阅读器一起使用。这个解决方案很漂亮，让用户感觉在控制中，并且非常容易实现！

## 分享这篇文章