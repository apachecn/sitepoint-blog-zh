# 关于 jQuery 方法的 5 个鲜为人知的细节

> 原文：<https://www.sitepoint.com/5-little-known-details-jquery-methods/>

jQuery 是世界上使用最多的 JavaScript 库，但我们都知道这一点。尽管最近几个月有很多批评，但它仍然吸引了开发者的很多关注。无论您是 jQuery 初学者还是 JavaScript 专家(Dave Methvin 和其他团队成员除外)，您可能都不知道 jQuery 的一些特性。在本文中，我们将讨论其中的五种。

## 在事件活页夹中返回`false`

众所周知，jQuery 的首要目标是使用一组统一的方法来标准化不同浏览器的行为方式。在可能的情况下，jQuery 增强了浏览器的特性，集成了那些本地不支持的特性。想一想，由于 jQuery，您可以使用 Internet Explorer 6 和 7 本身不支持的选择器(属性选择器、`:not`、`:last-child`等等)。

有时，尽管这些是罕见的例外，jQuery 还是会稍微偏离标准行为。例如，在 jQuery 事件处理程序中返回`false`(例如一个附加到`on()`的事件处理程序)与调用:

```
event.stopPropagation();
event.preventDefault();
```

相反，在像`addEventListener()`这样的本机事件处理程序中返回`false`相当于编写:

```
event.preventDefault();
```

这种行为可以通过查看 jQuery 源代码的相关部分来证明:

```
if ( ret !== undefined ) {
   if ( (event.result = ret) === false ) {
      event.preventDefault();
      event.stopPropagation();
   }
}
```

## 伪选择器做的比你想象的要多

在许多伪选择器的 jQuery 文档中，您可以找到以下注释(在本例中与`:checkbox`伪选择器相关):

> $(“:checkbox”)相当于$(“[type = checkbox]”)。与其他伪类选择器(以“:”开头的选择器)一样，建议在它前面加上一个标记名或其他一些选择器；否则，将隐含通用选择器(" * ")。换句话说，裸露的$(':checkbox ')等价于$( "*:checkbox ")，所以应该用$( "input:checkbox ")来代替。

现在，让我们看看 jQuery 中的实际代码:

```
function createInputPseudo( type ) {
	return function( elem ) {
		var name = elem.nodeName.toLowerCase();
		return name === "input" && elem.type === type;
	};
}
```

从代码中可以看出，文档略有错误。就搜索内容而言，`$(':checkbox')`实际上相当于`$('input[type="checkbox"]')`(注意`name === "input"`)，但是测试页面的所有元素，就像你调用它时指定了通用选择器或者什么都没有一样。

考虑到这一点，当使用这样的过滤器时，您可能不想再预先考虑元素选择器，并编写:

```
var $checkboxes = $(':checkbox');
```

然而，出于性能原因，使用它仍然是值得的，这样 jQuery 就不会扫描页面的每个元素。因此，您仍然应该写一个这样的语句:

```
var $checkboxes = $('input:checkbox');
```

## `jQuery.type()`

这一部分的标题对你来说可能已经是新的了。您知道 jQuery 有一个方法来<q cite="http://api.jquery.com/jquery.type/">确定对象</q>的内部 JavaScript [[Class]]吗？

即使您已经知道了这个方法，您可能会忽略的是，它与原生 JavaScript `typeof`操作符确实不同。事实上，`jQuery.type()`返回了传递的参数的更精确的指示。让我们看一些例子:

```
// prints object
console.log(typeof null);

// prints object
console.log(typeof [1, 2, 3]);

// prints object
console.log(typeof new Number(3));
```

使用`jQuery.type()`来测试相同的参数，我们有:

```
// prints null
console.log($.type(null));

// prints array
console.log($.type([1, 2, 3]));

// prints number
console.log($.type(new Number(3)));
```

因此，如果你正在开发一个 jQuery 插件，你可能想使用`jQuery.type()`来更精确地了解你正在处理的参数类型。

## `attr()`能`removeAttr()`

我知道这一部分的标题至少听起来很奇怪，但它只说了事实。对于那些不知道 jQuery 的`attr()`方法的人来说，它检索匹配元素集中第一个元素的属性值，或者为每个匹配元素设置一个或多个属性。

虽然您可能会忽略这种行为，但是除了数字或字符串之外，`attr()`方法还可以接受`null`作为它的第二个参数。当这种情况发生时，它充当其对应的方法:`removeAttr()`。顾名思义，后者从匹配元素集中的每个元素中删除一个属性。

不相信我？让我们来看看源代码的相关部分:

```
attr: function( elem, name, value ) {
    ...
    if ( value !== undefined ) {

        if ( value === null ) {
		    jQuery.removeAttr( elem, name );
    ...
}
```

如您所见，该方法测试给定值是否已定义(`if ( value !== undefined )`)，然后显式检查它是否是`null`，在这种情况下调用`removeAttr()`方法。

当您必须根据条件设置或移除属性，并且不希望对代码进行分支时，这非常方便。例如，你可以这样写:

```
$(selector).attr(anAttr, condition ? value : null);
```

代替

```
condition ? $(selector).attr(anAttr, value) : $(selector).removeAttr(anAttr);
```

您真的应该在代码中使用这个技巧吗？决定权在你，但如果我是你，我不会为了代码清晰而使用它。如果你感兴趣的话，目前这种行为没有被记录下来，并且有一个关于它的讨论。

## 将类似数组的对象转换为数组

如你所知，JavaScript 有类型，比如函数中的`nodeList`或`arguments`变量，它们类似于数组，但不是数组。这意味着我们可以使用类似数组的符号(例如`arguments[0]`)来访问它们的元素，但是我们不能使用数组方法，例如`forEach()`和`join()`。

假设我们有一个 DOM 元素的`nodeList`,检索如下:

```
var list = document.getElementsByClassName('book');
```

我们想使用`forEach()`方法迭代这个类似数组的对象。如果我们直接在变量(`list.forEach(...)`)上调用`forEach()`，我们会得到错误:“未捕获类型错误:未定义不是函数”。为了避免这个问题，最常用的技术之一是使用如下所示的`prototype`属性和`call()`方法:

```
Array.prototype.forEach.call(list, function() {...});
```

或者，你可以写:

```
[].forEach.call(list, function() {...});
```

无论您选择哪种解决方案，读或写起来都不是很优雅。幸运的是，jQuery 拯救了我们。多亏了`jQuery.makeArray()`方法，我们可以简单地写:

```
$.makeArray(list).forEach(function() {...});
```

好多了，不是吗？

## 结论

除了这五个主题之外，您可以从本文中学到的是，即使像 jQuery 这样令人惊叹的可靠项目也不是完美的。它有缺陷和文档问题，并且知道一个方法做什么的唯一信任来源是它的源代码。实际上，甚至代码也可能会因为与开发人员的意图不同而撒谎，但这是另一个故事了。

另一个教训是，你应该对你采用的框架和库感到好奇，不时地阅读源代码，并尽可能多地尝试学习新的有用的技巧和技术。

最后一个建议是，如果你像我一样热爱 jQuery，请为这个项目做点贡献。即使是报告一个 bug 或修复一个小的文档问题，也可以对数百万开发人员产生巨大的影响。

哦，如果你想知道我是如何知道这些微妙的细节的，原因是因为[我写了几本关于 jQuery](http://aurelio.audero.it/books) 的书，并且因为我跟踪 jQuery 问题跟踪器。:)

## 分享这篇文章