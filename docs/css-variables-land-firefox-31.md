# CSS 变量登陆火狐 31

> 原文：<https://www.sitepoint.com/css-variables-land-firefox-31/>

火狐 31 于 2014 年 7 月 22 日发布。你可能已经有了，但是如果没有，从菜单中打开关于火狐的对话框或者去 Firefox.com 的 T2 下载一个新的版本。

不要期待太多惊喜。有一些新的语言环境和开发工具的调整，但只有一个主要的增加:CSS 变量。Firefox 31 是第一个支持原生 CSS 变量而无需启用实验性功能的主流浏览器。

## CSS 变量语法

我第一次[写 CSS 变量](/native-css-variables/)是在 2012 年 9 月。我们已经等了很长时间，你应该注意到语法已经改变了。

要声明 CSS 变量:

```
element {
  --my-variable: #c00;
}
```

其中:

*   **元素**是一个选择器。变量值可用于从该选择器继承的所有样式。如果你想让变量在页面的样式表中全局可用，使用`:root`。
*   **‐我的变量**是变量名。它必须以两个连字符开头，后跟您选择的名称。命名约定似乎相当宽松，除了 CSS 语法要求的字符之外，您可以使用大多数字符，例如:{ }.也就是说，我建议保持简单，坚持使用字母数字字符。
*   最后，在冒号后面设置值。任何类型都可以在其他地方定义和使用，例如颜色、字体、尺寸、过渡设置等。

示例:

```
:root {
  --my-font: arial, helvetica, sans-serif;
  --my-color: #333;
  --my-background: #fff;
  --page-width: 80%;
  --max-page-width: 50em;
}
```

现在，必要时可以使用`var`功能符号引用变量，例如

```
body {
  font-family: var(--my-font);
  color: var(--my-color);
  background-color: var(--my-background);
}

main {
  width: var(--page-width);
  max-width: var(--max-page-width);
}
```

## 浏览器支持

CSS 变量有一个 W3C 工作草案规范,但是支持仅限于 Firefox 31 和更高版本。其他厂商[没有承诺任何一种方式](http://caniuse.com/#feat=css-variables)，所以，目前，你可能应该避免使用原生 CSS 变量，除非你是为了迎合只有 Firefox 的观众。

## 太少太晚了？

如果在十年前实现，原生 CSS 变量将会彻底改变我们的工作生活。今天，如果您想在样式表中使用变量，您可能已经这样做了。诸如 [Sass](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#variables_) 、 [Less](http://lesscss.org/features/#variables-feature) 和 [Stylus](http://learnboost.github.io/stylus/docs/variables.html) 等预处理器提供了具有许多其他好处的可变功能:

1.  不存在浏览器支持问题。
2.  变量语法更简单，例如在萨斯/SCSS 中的`$my-variable`。
3.  变量只需预处理一次，而不是每次呈现页面。
4.  预处理程序报告了不正确的语法。
5.  你可以利用类似编程语言的特性，比如包含、嵌套、混合、函数、条件、循环等等。

不可否认，您需要花一点时间安装一个预处理器并学习一些语法选项，但是您将在几个小时内变得富有成效。

一旦我们有了更好的浏览器支持，也许原生 CSS 变量对于更小的项目、模型、教程和演示将是实用的。在那一天到来之前，我怀疑它们对我们大多数人都没什么用处。

## 分享这篇文章