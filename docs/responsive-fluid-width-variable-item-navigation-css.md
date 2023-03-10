# 具有 CSS 的响应性、可变宽度、可变项目导航

> 原文：<https://www.sitepoint.com/responsive-fluid-width-variable-item-navigation-css/>

大约六个月前，我被要求开发一个单行导航栏，它可以包含数量可变的菜单项，同时填满整个容器的宽度。导航项目的数量由其他地方决定，将来可能会改变。

预先考虑一下，每次添加或删除菜单项时都改变 CSS 是不切实际的。必须找到一个解决方案，最好不要使用 JavaScript。

## 使用百分比宽度

固定宽度无法始终如一地填满响应容器，如下图所示。

![Fixed-width nav example](img/856498bc27cbf64928c81b7dd1618c4b.png)

所以我们需要用百分比来完成这个任务。百分比是在响应式设计中实现流体网格布局的常用方法，在这里会很有用。

例如，考虑下面熟悉的 HTML:

```
<nav>
  <ul>
    <li><a href="home.html">Home</a></li>
    <li><a href="about.html">About</a></li>
    <li><a href="services.html">Services</a></li>
    <li><a href="products.html">Products</a></li>
    <li><a href="jobs.html">Jobs</a></li>
    <li><a href="contact.html">Contact</a></li>
  </ul>
</nav>
```

我们将添加以下 CSS:

```
nav {
    width: 100%;
    background: #f0f0f0;
    border: 1px solid #ccc;
    border-right: none;
}

nav ul {
    overflow: hidden;
    margin: 0;
    padding: 0;
}

nav ul li {
    list-style: none;
    float: left;
    text-align: center;
    border-left: 1px solid #fff;
    border-right: 1px solid #ccc;
    width: 16.6667%; /* fallback for non-calc() browsers */
    width: calc(100% / 6);
    box-sizing: border-box;
}

nav ul li:first-child {
    border-left: none;
}

nav ul li a {
    display: block;
    text-decoration: none;
    color: #616161;
    padding: 10px 0;
}
```

值得注意的领域有:

*   给予`<nav>`元素`width: 100%`来填充整个可用空间。
*   `<li>`元素提供了两个宽度属性——`width: calc(100% / 6);`和`width: 16.6667%;`。这是菜单项在`<nav>`元素上被分成 6 个相等宽度的地方。这些属性被列出的顺序对于允许支持`calc()`的浏览器在标准百分比宽度上使用它是很重要的。不支持`calc()`的浏览器会简单地忽略它并使用回退。我倾向于使用带百分比的`calc()`来帮助提高可读性，并允许浏览器使用优化后的结果。这样我就不用担心是否包含重复出现的小数位。
*   `box-sizing: border-box;`在`<li>`元素上声明。这将强制`width`属性在总宽度中包含`border-left`和`border-right`值。如果不使用该属性，`<li>`元素将无法放入`<nav>`区域，最后一个`<li>`元素将换行。许多人喜欢将`box-sizing: border-box;`设置为所有元素，以便于管理宽度，正如 [Paul Irish 建议的](http://www.paulirish.com/2012/box-sizing-border-box-ftw/)。

添加这些样式的结果显示在下面的 CodePen 演示中:

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看到笔[定宽流体导航](http://codepen.io/SitePoint/pen/FkihJ/)。