# 原型元素方法更方便

> 原文：<https://www.sitepoint.com/prototype-element-methods-more-convenient/>

因为我们最近发表了 Dan Webb 关于 T2 原型库的文章，所以在这个博客中继续追踪这个库的发展是有意义的。

在原型库中，`Element`对象( [documentation](http://www.sergiopereira.com/articles/prototype.js.html#Element) )提供了一组对文档中指定元素进行操作的方法。下面是这篇文章中最著名的方法列表:

```
 var element = $('someId');

// Hides an element
Element.hide(element)
// Shows an element
Element.show(element)
// Adds a CSS class to the element
Element.addClassName(element, "cssClassName")
// Removes a CSS class from the element
Element.removeClassName(element, "cssClassName")
// Returns true if element has the CSS class
Element.hasClassName(element, "cssClassName")
// Returns {width: 123, height: 45}
Element.getDimensions(element)
// replaces the innerHTML of element with newHtml
Element.update(element, newHtml)
```

对于一个旨在减少编写 JavaScript 痛苦的库来说，这些东西实际上相当冗长。但是设计师/开发商~~杰森~~ <ins datetime="2006-03-14T01:06:49+00:00">贾斯汀</ins>帕尔默[抢先报道了一些正在酝酿中的变化](http://encytemedia.com/blog/articles/2006/03/07/prototype-gets-some-serious-syntactic-sugar)。不久，您将能够像这样编写上述方法调用:

```
 var element = $('someId');

element.hide()
element.show()
element.addClassName("cssClassName")
element.removeClassName("cssClassName")
element.hasClassName("cssClassName")
element.getDimensions()
element.update(newHtml)
```

Prototype 方便的`$`函数可以获取任何给定 ID 的元素，现在它会在返回给你之前用`Element`对象的方法增强该元素。Prototype 提供的`$$`和`getElementsByClassName`方法也是如此。

这个阶段的问题是，通过其他方式(即通过标准 DOM 集合/方法)获得的元素引用将*而不是*以这种方式得到增强，所以您必须知道哪些元素引用是由 Prototype 生成的，哪些是标准 DOM 引用——这是对 Prototype 传统的重大突破。事实上，在这个阶段，即使一些由原型方法(例如`Form.getElements`)返回的元素引用仍然会返回“普通的”DOM 元素引用，而没有上面的增强。

对这些不一致不感兴趣的开发人员可能希望坚持使用更冗长的原始语法，但是对于那些大量使用 Prototype 的增强元素获取功能的开发人员来说，输入可以节省大量时间。例如:

```
$('someId').show();
```

这个新功能将在即将发布的 Prototype 1.5 中首次亮相。与此同时，您可以从 Rails 获取最新的进展中版本来试用。

## 分享这篇文章