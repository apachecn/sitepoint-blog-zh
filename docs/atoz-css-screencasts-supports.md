# AtoZ CSS 截屏:CSS @supports 规则

> 原文：<https://www.sitepoint.com/atoz-css-screencasts-supports/>

这个截屏是我们 AtoZ CSS 系列的一部分。你可以在这里找到系列[的其他条目。](https://www.sitepoint.com/blog/)

## 副本

当使用新的或实验性的 CSS 时，知道浏览器是否支持我们正在编写代码的特性是很有用的。

特性检测通常在 JavaScript 中用 Modernizr 这样的工具来完成，但是在一些浏览器中，我们现在可以从 CSS 中检测功能。

`@supports`规则允许我们有条件地为支持或不支持的特性应用样式。

在这一集里，我们将学习所有关于

*   对各种 CSS 特性的浏览器支持
*   at 规则是如何工作的
*   以及如何为不受支持的功能提供替代样式

### 浏览器支持

如果你写 CSS 已经有一段时间了，你可能会发现不是所有的浏览器都以相同的方式呈现事物，也不是所有的浏览器都支持相同的特性。

我几乎每天都使用的检查浏览器支持的一个很棒的资源是 caniuse.com 网站。

该站点允许您搜索支持表的数据库，以找出哪些版本的浏览器支持哪些功能。

在上一集中，我们了解了`:required`伪类，它在 IE10、Firefox、Chrome、Safari、Opera 和 Blackberry 中受支持，但在 Opera Mini 或 iOS Safari 或 Android 浏览器中不受支持。

我们还在*[第 11 集:`@keyframes`](https://www.sitepoint.com/atoz-css-screencast-keyframe)*中了解到了`@keyframes`。除了 Opera Mini 之外，目前所有浏览器都支持它，但从版本 10 开始，它只在 IE 中可用，并在 Chrome、Safari、Opera、Android 和 Blackberry 中作为前缀。

正如您所看到的，特性支持前景是广阔而复杂的。这个资源是惊人的，也是了解在什么样的浏览条件下什么会起作用的不可或缺的一部分。它允许我们决定我们可能需要提供哪些功能，以备所需的功能是网站设计或功能的关键。

### `@supports`规则

`@supports`是一个 at 规则，有点像我们在*[第 13 集:媒体查询](https://www.sitepoint.com/atoz-css-screencast-media-queries)*中看到的`@media`查询。

只有当条件为真时，这些 at-rules 才应用其中包含的样式。

该条件包含在括号内，包含一个 CSS 属性，后跟一个值，用冒号分隔。

```
@supports (property: value) {
  /* styles applied if the condition is true */
}
```

如果括号内的 CSS 属性接受具有指定语法的值，则条件为真。

如果我们想检测对无前缀 CSS 动画的支持，我们可以使用下面的代码片段:

```
@supports (animation-name: test) {
  body {background: #cc3f85;}
}
```

这里我使用了一个视觉提示，让页面的`background`变成粉红色，以显示该特性是否受支持。我们可以看到`background`仍然是黑色的，所以 Chrome 不支持无前缀的动画，这就是我目前在这里使用的。

```
@supports (animation-name: test) or (-webkit-animation-name: test) {
  body {background: #cc3f85;}
}
```

我们可以使用关键字`or`来检查多个属性或多个供应商前缀。在这种情况下，`background`确实改变了颜色，这告诉我 Chrome 支持`animation`属性的 `webkit`前缀版本。

也可以用`and`指定多个条件，或者用`not`关键字否定一个条件。

### 提供后备样式

我们可以使用这些支持条件来检查功能，但是我们也可以在某个功能不受支持的情况下使用它们来提供后备样式。

我这里有一张图片，上面有一个标题。标题有一个半透明的背景，使用了我们在《T2》[第三集:CSS `color`语法](https://www.sitepoint.com/atoz-css-screencast-color)中提到过的`hsla`。

旧版本的 IE 浏览器不支持`hsla`，因此用户根本看不到`background`。由于图片的性质和文字的颜色，标题很难辨认，这是一个真正的可用性问题。

我们可以用符合设计的纯色`background`创建默认样式，然后使用`@supports`添加半透明的`background`，如果该功能可用的话。

```
.caption {
  background: #000;
}

@supports (background: hsla(0, 0%, 0%, 0.5) {
  .caption {
    background: hsla(0,0%,0%,0.5);
  }
}
```

您可能会认为这有点罗嗦，对于这个特殊的问题，更常见的方法是允许`hsla`声明覆盖坚实的背景，如下所示:

```
.caption {
  background: #000;
  background: hsla(0,0%,0%,0.5);
}
```

这种简单的方法在这种情况下很好，但是如果我们想要添加大量的 CSS，如果某个特性被支持或不被支持，我们将需要一些更全面的东西。

一个很好的例子是 flexbox，它是一个完全不同的布局模型，用于创建复杂的用户界面。

我们可以从基于浮动或位置的布局开始，作为一个良好的坚实基础，然后用`@supports`检测 flexbox，并利用新布局引擎提供的所有功能。

在这里，我制作了一个类似应用程序的标题栏，它显示了视图的名称，左右两边有后退和前进按钮。为了获得这种效果，我们可以结合使用绝对定位和文本居中对齐。

然而，有了 flexbox 的支持，我们可以通过`space-between`将导航容器设置为`display:flex`和`justify-content`来平均分配这三个元素。

我们可以用一个`@supports`规则包装 flexbox 样式，并用一个`not (display:flex)`规则包装回退样式。

```
nav {
  background: #eee;
  padding: 10px;
}
@supports not (display:flex) {
  nav {
    text-align: center;
  }
  .nav-back {
    position: absolute;
    left: 1em;
  }
  .nav-forward {
    position: absolute;
    right: 1em;
  }
}
@supports (display:flex) {
  nav {
    display: flex;
    justify-content: space-between;
  }
  .nav-back,
    .nav-forward {
      position: static;
  }
}
```

值得注意的是，不支持 flexbox 的浏览器几乎肯定不支持`@supports`，所以为了向后兼容，必须移除`@supports not (display:flex)`条件。

虽然我真的很期待这个功能获得更多的关注，但它仍然有很长的路要走，因为浏览器对`@supports`本身的支持非常有限。

同时，我倾向于使用 Modernizr JavaScript 库进行特性检测。

这将把类添加到`html`元素中，如`flexbox`(如果该特性受支持)或`no-flexbox`(如果不受支持)。这些可以用来代替`@supports`规则:

```
nav {
  background: #eee;
  padding: 10px;
}
.no-flexbox {
  nav {
    text-align: center;
	}
  .nav-back {
    position: absolute;
    left: 1em;
  	}
  .nav-forward {
    position: absolute;
    right: 1em;
  }
}
.flexbox {
  nav {
    display: flex;
    justify-content: space-between;
	}
  .nav-back,
  .nav-forward {
    position: static;
	}
}
```

功能检测和跨浏览器支持是前端设计和开发的一个重要但经常令人沮丧的部分。但是，有了我们在这里看到的工具，希望可以减轻一些痛苦，我们可以继续专注于制作出色的网站。

## 分享这篇文章