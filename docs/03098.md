# CSS 的@supports 规则介绍(特性查询)

> 原文：<https://www.sitepoint.com/an-introduction-to-css-supports-rule-feature-queries/>

解决浏览器对最新技术不均衡支持的两种通用方法是**优雅降级**和**渐进增强**。

适度降级利用先进的技术来设计复杂的用户体验和功能。功能较弱的浏览器用户仍可访问网站，但功能和浏览体验会有所下降。

使用渐进式增强，开发人员通过为大多数浏览器能够支持的用户体验水平进行设计来建立基线。他们的应用程序提供了对浏览器功能的内置检测，从而相应地提供更高级的功能和更丰富的浏览体验。

渐进式增强方法中最广泛采用的工具是 [Modernizr](https://modernizr.com/) JavaScript 库。

Modernizr 以编程方式检查浏览器是否支持下一代 web 技术，并相应地返回`true`或`false`。有了这些知识，您就可以利用支持浏览器的新特性，并且仍然有一种可靠的方法来迎合旧的或不兼容的浏览器。

尽管这听起来不错，但更好的东西已经酝酿了一段时间。您可以使用带有@supports 规则的原生 [CSS 特性查询来执行特性检测。](https://drafts.csswg.org/css-conditional-3/#at-supports)

在这篇文章中，我将深入研究`@supports`及其相关的 JavaScript API。

## 使用`@supports`规则检测浏览器功能

`@supports`规则是 [CSS3 条件规则规范](https://drafts.csswg.org/css-conditional-3/)的一部分，它还包括我们在响应式设计工作中使用的更广泛的`@media`规则。

通过媒体查询，您可以检测显示特性，如视窗宽度和高度，`@supports` **允许您检查浏览器对 CSS 属性/值对的支持**。

考虑一个基本的例子，假设你的网页显示了一幅你想用 [CSS 混合](https://drafts.fxtf.org/compositing-1/#propdef-mix-blend-mode)增强的艺术作品。没错，CSS 混合模式在不支持的浏览器中会优雅地降级。然而，在这种情况下，您可能想通过显示一些同样特别的东西来取悦不支持浏览器的用户，而不是浏览器默认显示的内容。这就是使用`@supports`在样式表中执行 CSS 混合检查的方式:

```
@supports (mix-blend-mode: overlay) {

  .example {
    mix-blend-mode: overlay;
  }

}
```

要对不支持`mix-blend-mode`的浏览器应用不同的样式，可以使用以下语法:

```
@supports not(mix-blend-mode: overlay) {

  .example {
    /* alternative styles here */
  }

}
```

需要注意一些事情:

*   您测试的条件必须在括号内。换句话说，`@supports mix-blend-mode: overlay { ... }`是无效的。但是，如果添加的括号比需要的多，代码就不会有问题。例如，`@supports ((mix-blend-mode: overlay))`是有效的。
*   条件必须包括属性和值。在上面的例子中，您正在检查属性`mix-blend-mode`和该属性的值`overlay`。
*   在你测试的声明中添加一个结尾`!important`不会影响你代码的有效性。

让我们用一个小演示来充实上面的例子。支持`mix-blend-mode`的浏览器将应用`@supports() { ... }`块中的样式；其他浏览器将应用`@supports not() { ... }`块中的样式。

HTML:

```
<article class="artwork">
  <img src="myimg.jpg" alt="cityscape">
</article>
```

CSS:

```
@supports (mix-blend-mode: overlay) {

  .artwork img {
    mix-blend-mode: overlay;
  }

}

@supports not(mix-blend-mode: overlay) {

  .artwork img {
    opacity: 0.5;
  }

}
```

查看 CodePen 上的演示:

请看 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )的 Pen[@支持规则演示](http://codepen.io/SitePoint/pen/VepbLw/)。