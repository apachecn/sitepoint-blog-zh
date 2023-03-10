# 为每个人提供免费的 JavaScript 速度提升！

> 原文：<https://www.sitepoint.com/a-free-javascript-speed-boost-for-everyone/>

以下内容转载自[《科技时报》第 207 期](https://www.sitepoint.com/the-second-coming-of-the-productivity-tool/)。

![](img/5c4de6f9d914ebc19b2caf5570314cc5.png)

DOM 脚本世界中一个令人兴奋的发展是 [W3C 选择器 API](https://www.w3.org/TR/selectors-api/) 。到目前为止，使用 DOM Level 2 API，获得对 DOM 中 HTML 元素的引用的唯一方法是使用`document.getElementById`或`document.getElementsByTagName`并手动过滤结果。随着 CSS 的兴起，JavaScript 程序员提出了一个显而易见的问题，“如果浏览器有一种非常快速的方法来选择与 CSS 选择器匹配的 HTML 元素，为什么我们不能呢？”。

选择器 API 定义了`querySelector`和`querySelectorAll`方法，它们分别接受 CSS 选择器字符串并返回第一个匹配元素或匹配元素的`StaticNodeList`。可以从`document`对象中调用这些方法，以便从整个文档中选择元素，或者从一个特定的 HTML 元素中选择元素。

为了说明使用选择器 API 会使您的生活变得多么简单，请看下面的 HTML 示例:

```
<ul id="menu">
  <li>
    <input type="checkbox" name="item1_done" id="item1_done"> 
    <label for="item1_done">bread</label>
  </li>
  <li class="important">
    <input type="checkbox" name="item2_done" id="item2_done"> 
    <label for="item2_done">milk</label>
  </li>
  <!-- imagine more items -->
</ul>
```

我们的任务是检查所有带有类别“`important`”的列表项的复选框。仅使用 DOM Level 2 方法，我们可以这样做:

```
var items = document.getElementById('menu').getElementsByTagName('li');
for(var i=0; i < items.length; i++) {
  if(items[i].className.match(/important/)) {
    if(items[i].firstChild.type == "checkbox") {
      items[i].firstChild.checked = true;
    }
  }
}
```

使用新的选择器 API，我们可以将其简化为:

```
var items = document.querySelectorAll('#menu li.important input[type="checkbox"]');
for(var i=0; i < items.length; i++) {
  items[i].checked = true;
}
```

这样好多了！这些方法还支持选择器分组——用逗号分隔多个选择器。选择器 API 现在可以在 Safari 3.1、Internet Explorer 8 beta 和 Firefox 3.1 alpha1 中工作。Opera 也在努力增加对 API 的支持。

如果你是众多可用的 JavaScript 库中的一个的粉丝，你可能会想“但是，我已经可以做到了。”使用 JavaScript 库的好处的一个很好的例子是在几乎所有的 JavaScript 库中都能找到 CSS 选择器的实现。最近，我们看到 CSS 选择器实现的速度有了巨大的提高，因为库作者已经分享了他们的技术。那么使用选择器 API 有什么好处呢？一句话:速度——原生实现快！更好的是，所有的 javascript 库都将从中受益。jQuery 和 [Prototype](http://www.prototypejs.org/) 已经在开发利用选择器 API 的实现，而 Dojo Toolkit 、[domain assistant](http://www.domassistant.com/)和 [base2](http://code.google.com/p/base2/) 已经在利用它了。

这三个图书馆最先受益是有原因的。Kevin 在题为“你的 JavaScript 库符合标准吗？”的文章中谈到了这个潜在的问题选择器 API 使用标准的 CSS 选择器，所以如果浏览器不支持某个选择器，你就不能使用它。已经利用了选择器 API 的库是那些只支持标准 CSS 选择器的库。对于这些库来说，支持 API(几乎)就像这样简单:

```
if(document.querySelector) {
  return document.querySelector(selector);
} else {
  return oldSelectorFunction(selector);
}
```

支持自定义选择器的库将有更多的工作要做。风险在于，如果您在项目中广泛使用了自定义 CSS 选择器，那么您选择的库可能很难将速度优势传递给您，因为该库将不得不使用其默认选择器，而不是选择器 API。如果库以某种方式重新连接它的自定义选择器，以便它们可以利用选择器 API，那么第二个风险就是增加代码膨胀。

希望选择器 API 将鼓励使用标准的 CSS 选择器而不是定制的选择器。事实上，如果对新浏览器版本的吸收良好，并且新选择器 API 的性能优势足够引人注目，我们可以看到自定义选择器功能被转移到补充库，您只需要在遗留兼容性需求的情况下使用。

在我看来，狄恩·爱德华兹的 base2 图书馆拥有最好的实现。Base2 完全实现了 API，这意味着您可以使用标准的 API 方法编写 JavaScript 如果浏览器不支持，Base2 只创建自定义的 querySelector 和 querySelectorAll 方法。没有比这更干净的了。然而，Base2 确实在它的自定义选择器函数中实现了非标准的“`!=`”属性选择器，这显然是因为来自同行的压力，所以它必须为此扣分。

无论您使用的是 JavaScript 库还是自己开发的库，选择器 API 的新浏览器实现都可以让每个人的速度瞬间提升。我们都赢了，万岁！

*图像鸣谢:[瑜伽士](http://www.flickr.com/photos/yogi/320315208/)*

## 分享这篇文章