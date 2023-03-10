# 正则表达式匹配属性选择器

> 原文：<https://www.sitepoint.com/regex-matching-attribute-selectors/>

他们不存在，但那不是很酷吗？我不知道实现[正则表达式](https://www.sitepoint.com/learn-regex/)有多难，或者解析起来有多昂贵，但这难道不是一个炸弹吗？

假设我有一堆元素，都有相似的类名，它们有一些共享的样式，但也需要单独的规则，例如:

```
<ul id="menu">
	<li id="menu-home"><a href="/">Home</a></li>
	<li id="menu-products"><a href="/products/">Products</a></li>
	<li id="menu-about"><a href="/about/">About</a></li>
</ul>
```

我可以对这些列表项进行图像替换，以创建一个图形导航栏，规则如下:

```
#menu li
{
	background:none #fff no-repeat;
}

#menu li#menu-home
{
	background-image:url("home.png");
}

#menu li#menu-products
{
	background-image:url("products.png");
}

#menu li#menu-about
{
	background-image:url("about.png");
}
```

不算太坏，但是随着结构尺寸的增长， <abbr title="Cascading Style Sheets">CSS</abbr> 也会增长。如果结构变得非常大，那么 <abbr title="Cascading Style Sheets">CSS</abbr> 也会变得同样冗长；更不用说每次添加新项目时我都必须手动编辑它。

但是如果我可以这样做呢:

```
#menu li[id%="/^menu-([a-z]+)$/"]
{
	background-image:url("$1.png");
}
```

现在，我的菜单样式可以无限扩展——我可以添加任意数量的新项目，而无需接触 <abbr title="Cascading Style Sheets">CSS</abbr> ！

只是一个想法…

## 分享这篇文章