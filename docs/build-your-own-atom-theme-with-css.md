# 用 CSS 构建自己的 Atom 主题

> 原文：<https://www.sitepoint.com/build-your-own-atom-theme-with-css/>

![Two scientists assessing a giant Atom logo in the lab](img/b5bc80cc672a08c63acfd587e66662c9.png)

Atom 将自己描述为“21 世纪的可破解文本编辑器”。它已经成为全世界成千上万开发人员的首选文本编辑器，并且非常容易扩展和定制。开发人员通过发布包和主题与 Atom 社区共享新特性。阅读完本文后，您可以发布自己的 Atom 语法主题——这是破解 Atom 的第一步！

### 什么是语法主题？

一个**语法主题**允许您在编辑器中设置文本/代码区域的样式。另一方面，**界面主题**允许您对 Atom 文本编辑器的所有其他方面进行样式化(比如侧栏、状态栏、标签等等)。我们将仅限于编写一个语法主题。本文只假设了 CSS 的工作知识。

## 入门指南

那么入门需要什么呢？您只需要下载 [Atom 文本编辑器](https://atom.io/)，就可以开始了！Atom 使用 Less，它是 CSS 的一个超集，有一些很棒的特性，比如变量，我们会在后面学习。

### 生成一个 Atom 语法主题包

曾经有一段时间，编写一个语法主题意味着大量的工作。如今，Atom 内置了一个出色的自动生成功能。

*   打开 Atom 并点击`Cmd + Shift + P`(对于 Windows 使用`Ctrl + Shift + P`)
*   类型`generate`
*   选择**包生成器:生成语法主题**选项

Atom 将提示您保存包的位置。你可以把它保存在任何你喜欢的地方。

### 命名您的包

Atom 会将生成的包作为项目打开，您可以开始编辑它。Atom 建议包名以`-syntax`结尾，最好使用小写连字符分隔的名称。我将把我的包命名为`blue-everywhere-syntax`,好吧，把蓝色翻出来。

### 程序包结构

这个自动生成的包已经被精心布局，所以非常容易编辑。

*   主样式表驻留在`index.less`中。
*   基本样式位于`styles/base.less`中，颜色位于`styles/colors.less`中。我们很快会深入挖掘这些文件。
*   如果你用过 Node.js 和 NPM，那么`package.json`会很熟悉。它给包一个名称和描述，并让您提供一些有用的元数据。
*   文件允许你使用 markdown 来描述你的主题。如果您发布您的主题，此自述文件将对访问您的下载页面的用户可见。

## 给我看看代码

所以让我们开始吧。不必深入细节，知道 Atom 的渲染引擎基本上是引擎盖下的 Chrome 是有用的(如果你想了解它是如何工作的，请查看 [Electron](http://electron.atom.io/) )。这就是为什么我们可以使用传统的 CSS 样式。但是由于构建 Atom 的人想利用一些方便的特性，比如变量和嵌套导入，他们选择少用。不要担心，因为我们只需要几个较少的特性，我已经在文章中为您展示了它们！

因为 Atom 使用了 Chromium，所以您所需要做的就是，您猜对了，重新加载！您可以使用`Cmd + Alt + Ctrl + L`或`View > Developer > Reload`来重新加载 Atom。让我们进入 Atom Settings ( `Cmd + ,` ) > Themes，将编辑器的语法主题设置为我们刚刚创建的新主题。

### 让我们把东西变成蓝色

打开 colors.less 文件(`style > colors.less`)。你可以看到一个名为`@very-dark-gray`的变量，它的值被设置为`#1d1f21`。

![The initial theme colors.less file](img/fa55e5642557d21798a77e775bde26be.png)

我们的初始主题 colors.less 文件

让我们把它改成深蓝色。现在重新加载 atom ( `Cmd + Alt + Ctrl + L`或`View > Developer > Reload`)。嘣！文本区域的背景应该已经改变。

![The theme colors.less file after the change](img/c274c9839527a882d8bd0f86eb0a8868.png)

更改后的主题 colors.less 文件

### 刚刚发生了什么？

让我们更深入地看看这些较少的文件。`index.less`进口`base.less`。如果我们打开`base.less`，我们会发现它与 CSS 非常相似。我们可以看到主题使用了一些较少的功能，如变量。变量很容易识别，因为它们以一个`@`符号开始。

现在，让我们把重点放在编辑器的背景色上。颜色在前几行中定义。

```
@import "syntax-variables";

atom-text-editor, :host {
  background-color: @syntax-background-color;
```

我们可以看到编辑器的`background-color`被设置为变量`@syntax-background-color`。但是`@syntax-background-color`是在哪里定义的呢？

我们可以看到在第一行中已经导入了一个名为`syntax-variables`的文件。让我们在`syntax-variables.less`中寻找`@syntax-background-color`的定义。

```
@import "colors";

// This defines all syntax variables that syntax themes must implement when they
// include a syntax-variables.less file.

// General colors
@syntax-text-color: @very-light-gray;
@syntax-cursor-color: white;
@syntax-selection-color: lighten(@dark-gray, 10%);
@syntax-background-color: @very-dark-gray;
```

我们发现`@syntax-background-color`被设置为`@very-dark-gray`。您可能已经猜到，`@very-dark-gray`在`colors.less`中有定义。这就是为什么我们打开`colors.less`并改变`@very-dark-gray`来改变编辑器的背景颜色。

### 样式表的组织

如何在样式表之间组织变量是个人喜好的问题。Atom 通过其自动生成的模板建议您使用`base.less`中的语法变量将应该具有相同颜色的项目组合在一起，然后在`syntax-variables.less`中为每个变量分配一种颜色。但是你可以看到这更多的是为了我们的方便，很多时候颜色也是直接在`base.less`中定义的。有了这些信息，在你的新主题中改变颜色应该是相当容易的！尝试全新的调色板，并通过重新加载 Atom 窗口来查看结果。

## 高级造型

但是如果我们想在非常特殊的情况下改变样式呢？除了变量和导入之外，还有一些我们在阅读和修改样式表时会发现有用的特性。如果你已经习惯于少用，你可以跳过下面两小节。

1.  嵌套样式
2.  `&`操作员

### 嵌套样式

Less 支持嵌套样式。它们是如何工作的？

```
<div class="container">
  <a href="http://some-url.com">Click here!</a>
  <div class="red-block">
    <a href="http://some-url.com">Click here!</a>
    <a href="http://some-url.com">Click here!</a>
  </div>
  <a href="http://some-url.com">Click here!</a>
  <a href="http://some-url.com">Click here!</a>
</div>
```

假设我们希望`.red-block`内的所有`<a>`标签都是红色的，而不是外面的标签。我们将这样编写 Less 样式表:

```
.container {
  .red-block {
    a {
      color: red;
    }
  }
}
```

这与以下内容相同:

```
a.container.red-block {
  color: red;
}
```

自动生成的样式表广泛使用嵌套样式，因为这样可以确保相关的样式一起出现。

### `&`操作员

最好用一个例子来解释一下`&`操作符。

```
.box {
  color: black;
  &-red {
    color: red;
  }
  &-blue {
    color: blue;
  }
  &:hover {
    color: gray;
  }
}
```

这与以下内容相同:

```
.box {
  color: black;
}
.box-red {
  color: red;
}
.box-blue {
  color: blue;
}
.box:hover {
  color: gray;
}
```

如您所见，`&`是父选择器的简写。它在`base.less`中被频繁使用，现在我们知道它是如何工作的了。

### 蓝色变量名

现在让我们尝试一些高级的颜色变化。让我们把所有的变量名都变成深蓝色。当鼠标悬停在变量名上时，我们也给它加下划线。

Atom 自动将`.variable`类添加到代码编辑器的所有变量中。因此，我们需要修改`.variable`类的样式。

```
.variable {
  color: #336699;
  &:hover {
    text-decoration: underline;
  }
  ...
```

重新加载页面，所有变量名(以`@`开头)现在应该是蓝色的。悬停在一个图标上会产生一条下划线！

### 当前行号

现在让我们把当前的行号也变成蓝色，这样它就能引起我们的注意。如果你通读`base.less`，你会发现一个叫做`.gutter`的部分，在里面我们可以设计`.line-number`。

在`colors.less`中我们加上`@deep-sky-blue: #009ACD;`。

然后我们在`base.less`中使用这种颜色。您可能已经注意到样式表中有两个`cursor-line`类——`.cursor-line-no-selection`和`.cursor-line`。现在，如果在一行中选择了一些文本，我们可以通过使行号变亮来使其更加突出。Less 有一个漂亮的`lighten`函数，让我们可以很容易地做到这一点。

```
.gutter {
  background-color: @syntax-gutter-background-color;
  color: @syntax-gutter-text-color;

  .line-number {
    &.cursor-line {
      background-color: @syntax-gutter-background-color-selected;
      color: lighten(@deep-sky-blue, 10%);
    }

    &.cursor-line-no-selection {
      color: @deep-sky-blue;
    }
```

如果做得正确，这种变化看起来是这样的:

![The current line number with a different theme](img/156d45348596ab1cfffb4ba124fd0e7b.png)

我们当前行号的新主题！

## 结论

通过一系列简单的例子和 CSS 的工作知识，我们为 Atom 创建了一个全新的语法主题。您可以不断改进您的 Atom 主题，如果您喜欢您所构建的内容，您可以使用 [Atom Package Manager (APM)](https://github.com/atom/apm) 与世界分享。黑客快乐！请在评论中分享你创造的主题。

## 资源

这里有一个资源列表，你可以用它来更深入地研究本教程中介绍的一些观点:

1.  [Less 中的色彩操作功能](http://lesscss.org/functions/#color-operations) : Less 有“变亮”、“变暗”等多项冷色功能。
2.  发布 Atom 主题:关于如何发布 Atom 包的说明。
3.  [Atom 飞行手册](http://flight-manual.atom.io/):Atom 所有东西的“入门”手册。

## 分享这篇文章