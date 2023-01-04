# JavaScript 键盘辅助功能

> 原文：<https://www.sitepoint.com/javascript-keyboard-accessibility/>

以下内容转载自《科技时报》第 161 期。

JavaScript 可访问性是一个充满争议和不完美解决方案的问题，特别是在支持许多视力受损用户所依赖的屏幕阅读器软件时。这些困难已经导致许多开发人员完全放弃可访问性，当使您的 JavaScript 可被某些用户访问时会令人耳目一新！

在基于 JavaScript 的 web 应用程序中，最实际的用户群是键盘用户。通常由于缺乏精细的运动控制，这些用户不用鼠标就能通过，而是使用键盘在网络上导航。

让我们再次看看我在 Tech Times #159 中介绍的手风琴控件，看看我们如何让它为键盘用户工作。

下面是手风琴的 HTML 的结构:

```
<ul class="accordion">
  <li>
    <h2>Jonathan Archer</h2>
    <p>Vessel registry: NX-01</p>
    <p>Assumed command: 2151</p>
    ...
  </li>
  ...
</ul>
```

鼠标用户使用该控件的方式是，手风琴每个折叠的标题都是可点击的:

```
var folds = accordion.getElementsByTagName("li");
for (var i = 0; i < folds.length; i++)
{
  var foldTitle = folds[i].getElementsByTagName("h2");
  addEvent(foldTitle, "click", Accordion.clickListener);
}
```

当用户单击其中一个标题时，会调用一个函数来展开相应的文件夹，并折叠所有其他的文件夹:

```
clickListener: function(event)
{
  var fold = this.parentNode;
  Accordion.expand(fold);
  preventDefault(event);
},
```

现在，让我们考虑一下这个脚本将如何影响键盘用户。该脚本在页面第一次加载时折叠折叠面板，隐藏其内容。目前，键盘用户无法访问折叠的内容。

鼠标用户可以点击文档中的任何元素，但是键盘用户只能“点击”键盘可聚焦的元素。典型地，键盘用户将重复地敲击 Tab 键(或者 Opera 中的 A 键)来将键盘焦点移动到期望的元素，然后敲击 Enter 来发起“点击”。然而，默认情况下，像我们的 accordion 中的`<h2>`标签这样的标题是键盘不可聚焦的。

要解决这个问题，您需要做两件事之一:

*   使非键盘焦点元素成为键盘焦点元素。
*   向文档中添加一个键盘可聚焦的元素，如超链接。

第一种选择是理想的——在一个完美的世界里。在 Firefox 1.5 或更高版本以及 Internet Explorer 5 或更高版本中，您可以将非键盘焦点 HTML 元素的`tabIndex`属性设置为零，它将神奇地变为键盘焦点。不幸的是，这个技巧没有在任何标准中规定，也不被其他浏览器如 Safari 和 Opera 支持。

幸运的是，另一种方法在大多数情况下都足够好:只需在文档中添加一个超链接，让键盘用户能够关注和点击。

尽管这个解决方案很优雅，但有一个问题需要考虑:链接将链接到哪个 URL？如果将链接直接插入到 HTML 代码中，它需要链接到在 JavaScript 被禁用时有意义的地方:

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

但是，如果您不能为链接找到合适的目标，您可以使用 JavaScript 动态地将链接插入到页面中，这样当 JavaScript 被禁用时，它就不会出现。如果这样做，您就可以安全地将链接指向任何地方(`"#"`和`"javascript:;"`是常见的选择)。

现在，您可以调整 JavaScript 代码来监听链接而不是标题上的“点击”(鼠标和键盘变化):

```
var folds = accordion.getElementsByTagName("li");
for (var i = 0; i < folds.length; i++)
{
  var foldLinks = folds[i].getElementsByTagName("a");
  var foldTitleLink = foldLinks[0];
  addEvent(foldTitleLink, "click", Accordion.clickListener);
}
```

考虑到被点击元素的深度增加，对`clickListener`函数的微小改变也是必要的:

```
clickListener: function(event)
{
  var fold = this.parentNode.parentNode;
  Accordion.expand(fold);
  preventDefault(event);
},
```

在许多现实世界的脚本中，为键盘用户提供可访问性真的很容易。

## 分享这篇文章