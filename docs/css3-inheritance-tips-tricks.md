# CSS3 继承技巧和诀窍

> 原文：<https://www.sitepoint.com/css3-inheritance-tips-tricks/>

很容易忽略样式表的*层叠*特性。大多数开发者都知道`inherit`关键字，但是 CSS3 中有一些新的继承特性你可能不知道…

## 财产:继承；

`inherit`关键字表示*“使用分配给我父母的任何值”*。如果没有在父元素上显式定义值，浏览器将沿着 DOM 树向上查找，直到找到该属性。最终，它终止于浏览器默认设置，例如

```
#myparent
{
	margin: 10px;
	border: 1px solid #000;
}

/* use the same border as the parent */
#myparent p
{
	border: inherit;
}
```

在实践中，很少需要使用`inherit`。许多更有用的属性会自动向下级联，例如字体、字体大小、颜色等。

`inherit`使用安全。IE6 和 IE7 不支持它，但是没有它你的设计不太可能崩溃。

## 属性:初始；

Ooo，一个闪亮的新 CSS3 关键字！`initial`将属性设置回其初始值——由浏览器定义的默认值，例如

```
body
{
	font-size: 0.5em;
}

/* reset paragraphs to 1em */
p
{
	font-size: initial;
}
```

有用吗？潜在的，尽管你不能依赖所有的浏览器都有相同的默认值。

支持合理——Chrome、Firefox、Safari 和 Opera 15+。这在 IE 中是行不通的，但我正在努力想出一种情况，那会是一个灾难性的问题。

## 属性:未设置；

这是一个有点不寻常的例子。当使用`unset`时，当继承值可用时，它的行为就像是`inherit`。如果它找不到一个——例如，它是一个非继承的属性，比如`box-shadow`——它就像`initial`一样工作，并应用默认的浏览器值。

不可否认，我想不出`unset`的许多用途，而且目前对它的支持也很少。

## all:[inherit | initial | unset]；

最后，`all`是属性而不是值。您可以指定`inherit`、`initial`或`unset`来影响所有属性，例如将每个 CSS 属性重置回浏览器默认值:

```
#mywidget
{
	all: initial;
}
```

如果您正在向页面添加第三方小部件，并且希望避免样式表冲突，这可能是作用域 CSS 的替代方法。

不幸的是，在一段时间内你还不能依赖于一致的跨浏览器支持，但它可能是一个值得关注的有用特性。

## 分享这篇文章