# 哪些 CSS 变量可以做预处理器不能做的事情

> 原文：<https://www.sitepoint.com/css-variables-can-preprocessors-cant/>

Mozilla Firefox 最近实现了备受期待的 CSS 变量规范。从那以后，[一些文章开始出现](https://www.sitepoint.com/css-variables-land-firefox-31/)，暗示它们现在已经随着像 Sass、Less 和 Stylus 这样的预处理器的出现和广泛使用而基本过时了。然而，我认为它们提供了一些预处理程序不能提供的东西，至少不能有效地*。*

 *预处理器变量是*静态*；用变量编译一个 Sass 文件总是会在输出文件中产生相同的、固定的 CSS 值。然而，原生 CSS 变量可以在运行时改变*，允许更优雅地实现主题化之类的事情。除此之外，它们还被限定了作用域，使得一个变量只能在 DOM 树的一个子集上被改变。*

 *让我们更详细地看看这个。

## 没有 CSS 变量的站点主题

让我们试着为一个站点实现颜色主题来说明 CSS 变量的用处。你的任务是根据`<body>`元素上的一个类改变各种颜色。在没有 CSS 变量的 CSS 中，你可以这样做:

```
.red-theme .primary-color {
  color: #801515;
}

.red-theme .primary-color-bg {
  background-color: #801515;
}

.red-theme .secondary-color {
  color: #D46A6A;
}

.red-theme .secondary-color-bg {
  background-color: #D46A6A;
}

.red-theme .tertiary-color {
  color: #AA3939;
}

.red-theme .tertiary-color-bg {
  background-color: #AA3939;
}
```

这导致 CSS 中有 6 个单独的选择器——这只是针对一个主题。除此之外，您可能需要将上面的类散布在整个标记中，或者将显式的选择器添加到主题样式 CSS 中，这两种方法都不是可维护的或者节省空间的解决方案。

所以这意味着，要么:

```
<h1 class="tertiary-color">For Example</h1>
<button class="primary-color-bg">A button</button>
<a class="secondary-color">A link</a>
```

或者:

```
.red-theme .primary-color-bg,
.red-theme button {
  background-color: #801515;
}

.red-theme .secondary-color,
.red-theme a {
  color: #D46A6A;
}

.red-theme .tertiary-color,
.red-theme h1 {
  color: #AA3939;
}
```

## 带有 CSS 变量的站点主题

使用变量实现这一点要高效得多。在这种情况下，你可以在`.red-theme`上定义颜色变量，然后直接在其他样式上使用它们。这将把样式信息排除在标记之外，它不属于标记，而 CSS 选择器则属于标记。

```
.red-theme {
  --theme-primary: #801515;
  --theme-secondary: #D46A6A;
  --theme-tertiary: #AA3939;
}

button, a {
  background-color: var(--theme-primary);
  color: var(--theme-secondary);
}

h1 {
  color: var(--theme-tertiary);
}
```

这是一个更好的解决问题的方法。除此之外，如前所述，它们是有作用域的，所以我们可以在导航栏上使用不同的主题。

```
<body class="red-theme">
  <nav class="blue-theme">
    <button>Blue button</button>
  </nav>
  <main>
    <button>Red button</button>
  </main>
</body>
```

## 超越网站主题

除了主题之外，CSS 变量还可以扩展预处理程序的功能。另一个例子是实现一个类似 Gmail 的间距系统——允许用户选择他们想要的 UI 是紧凑、舒适还是舒适。

```
.compact {
  --option-padding: 0.1em 0.2em;
}

.cosy {
  --option-padding: 0.3em 0.6em;
}

.comfortable {
  --option-padding: 0.5em 1em;
}

.list-item {
  padding: var(--option-padding);
}
```

## 最后的想法

我在这里展示了如何以一种不同于预处理程序变量的方式应用即将到来的 CSS 变量特性。请记住，CSS 变量目前只在最新的 Firefox 和 Firefox for Android 中启用，所以这只是未来的尝试。

## 分享这篇文章**