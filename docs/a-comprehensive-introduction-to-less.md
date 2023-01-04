# LESS 的全面介绍

> 原文：<https://www.sitepoint.com/a-comprehensive-introduction-to-less/>

如果你正在读这篇文章，你已经知道 CSS 是一种简单易学、易于使用的样式表语言，它让我们的网站大放异彩。随着新的 CSS3 特性的引入，可能性更大了。在这篇文章中，我将介绍 LESS 的基础知识。下周二，我们将深入探讨它，包括混合、函数甚至更多！

所有这些听起来都很棒，但这个故事有一个弱点。CSS 是静态的。这意味着我们在样式表中一遍又一遍地重复代码，从而使 DRY(不要重复你自己)原则只是一个梦想。幸运的是，更少可以解决这个问题。根据 lesscss.org 的说法，LESS 是一种动态样式表语言。使不那么动态的是，它用变量、混合、操作和函数来扩展 CSS，迫使 CSS 的行为更像一种编程语言。但是一个关键的特性是，它还保留了 CSS 中使用的原始语法——除了一些例外。

从技术上讲，LESS 是一个 CSS 预处理程序。简单地说，CSS 预处理是一种扩展 CSS 功能集的方法，首先用一种新的扩展语言编写样式表，然后将代码编译成纯 CSS，以便浏览器可以阅读。LESS 是三个流行的 CSS 预处理器之一——另外两个是 Sass 和 Stylus。

我选择了 LESS，因为它被设计成尽可能接近 CSS，所以语法几乎与您当前的 CSS 相同，这意味着您可以在现有代码中立即使用它。你所需要学习的是使用同一种语言的新方法。

更少可以在客户端或者服务器端运行(用 [Node.js](https://www.sitepoint.com/serving-static-files-with-node-js/) )。对于本教程，我们将在客户端使用较少，因为它更容易开始。那么，我们还在等什么？我们开始吧！

## 3，2，1…开始

使用 JavaScript 编译器非常容易。只需将脚本包含在您的 HTML 代码中——它将在页面加载时处理较少的内容。然后我们可以像对待标准样式表一样包含 LESS 文件。下面是放在标记的`<head>`标签之间的代码:

```
<link rel="stylesheet/less" type="text/css" href="styles.less">
<script src="less.js" type="text/javascript"></script>
<script type="text/javascript">
  less.watch();
</script>

```

样式表链接位于脚本之上，以确保预处理程序可以使用它。注意典型的`rel="stylesheet"`已经变成了`rel="stylesheet/less".`。同样，确保`href`值指向你的`.less`文件的位置。

如果您愿意，可以直接从 Google 代码服务器引用`less.js`脚本。使用这种方法，您甚至不必下载脚本就可以使用它。

```
<script src="http://lesscss.googlecode.com/files/less-1.2.2.min.js"></script>

```

正如您所看到的，我们声明了一个样式表，将`rel`属性设置为`stylesheet/less`，并创建了一个名为`styles.less`的文件。现在，我们所有的 LESS 和 CSS 代码都将放在这个文件中。我们还声明了 LESS JavaScript 文件，它将把我们的 LESS 代码编译成 CSS。这个 JavaScript 文件将执行一些 AJAX 调用来读取你的 LESS 文件，所以你需要使用一个 web 服务器。如果你没有，只要根据你的操作系统下载 WAMP (Windows)、MAMP (Mac)或 LAMP (Linux)就可以了。

第二个脚本在 LESS 中激活“观察”模式，这是开发模式中的一个客户端特性。每当你的`.less`文件被保存并有新的改变时，这将刷新你页面上的 CSS。最好的事情是它不会刷新整个页面——只刷新 CSS 的正确部分。可有可无，但我觉得不使用太好了。

如果在您最喜欢的编辑器中打开`style.less`文件，您会注意到，由于您正在编辑一个`.less`文件，默认情况下您不会看到任何语法高亮显示。要解决这个问题，只需找到您的编辑器的首选项，在那里您可以选择哪些文件应该与 CSS 高亮显示相关联。LESS 和 CSS 语法非常接近。即使有一点不同，它也应该工作正常。

## 进口

您可以导入`.less`文件，其中的所有变量和 mixins 将对主文件可用。`.less`扩展名是可选的:

```
@import "typography.less";
@import "typography";

```

如果您想要导入一个 CSS 文件，并且不希望 LESS 处理它，只需使用。css 扩展:

```
@import "typography.css";

```

在你跳过这一步之前，我想告诉你导入是非常强大的。在开发阶段的最后，所有的`.less`文件都将被编译和压缩成一个纯的`.css`文件，为生产做好准备。因此，您不需要担心您的页面会发出多少服务器请求，以及这是否会降低页面加载速度。我想你会同意维护一个长的`.css`文件比几个分开的短文件要困难得多。所以，把你的主文件分成几个特定的文件是个好主意，比如颜色，排版，网格，重置等等。这将帮助你以一种更加模块化的方式构建你的代码。

## 评论

注释使你的代码更具可读性。你可以像在普通 CSS 中一样在`.less`文件中使用注释，但是你必须记住以下几点:

多行注释以较少的形式保留:

```
/* Hello, sir! This will be shown in your compiled file */
.class {color: white}
```

单行注释是“无声的”，它们不会显示在编译后的 CSS 输出中:

```
// What's up,  dude? Don't worry, this won't show up in your compiled file
.class {color: white}
```

## 变量还是常量？

LESS 使您能够像在其他编程语言中一样使用变量。这使得设置可以在单个位置更改的全局样式变得非常容易，而不是通过对代码进行排序来查找该样式的每个实例。

变量是通过@符号声明和使用的。您给它们一个名称和值，然后您可以在您的`.less`文件中的任何地方引用该变量的名称:

```
@name: value;              // syntax
@color: red;               // example
background-color: @color;  // usage
background-color: red;     // output
```

LESS 还允许您使用如下变量名定义变量:

```
@mainColor: red;
@bgColor: 'mainColor';
background-color: @@bgColor; // background-color: red;
```

在转到更复杂的例子之前，你需要明白 LESS 中的“变量”实际上是常量，这意味着它们只能定义一次，一旦设置，它们的值就不能更改。

难道你不认为作为人类，我们使用带有真实姓名的颜色比十六进制或 RGB 值要自然得多吗？是啊，伙计！我也是，伙计，我也是。幸运的是，这可以用更少的资源轻松完成。让我们看看我的意思:

```
//Define Color Palette
@dimGray: #696969;
@gold: #FFD700;
@greenYellow: #ADFF2F;
@deepSkyBlue: #00BFFF;

//Define Color Scheme
@backgroundColor: @dimGray;
@primaryColor: @gold;
@secondaryColor: @deepSkyBlue;

//Now you can use it like this:
section {
   background-color: @backgroundColor;
   color: @primaryColor;
}

section ul li {
   color: @secondaryColor;
}
```

任何时候我们想在设计中使用这些颜色，我们都使用我们一直使用的 CSS，但是插入变量名而不是颜色代码。如果你想更深入，你可以用颜色变量(`colors.less`)创建一个单独的文件，然后把它导入你的主`.less`文件。这样，您不需要为每个新项目定义颜色。导入使用即可。

变量声明不会出现在输出中。它们可以保存任何 CSS 属性值，并且可以在文件的最外层范围或 CSS 属性可能出现的任何地方声明。LESS 中的作用域与大多数编程语言非常相似。你可以在不同的地方使用相同名称的变量。首先在本地搜索变量和混合，如果没有找到，编译器将在层次结构中向上移动，直到找到它们:

```
@color: red;
header {
  @color: white;
  color: @color; // white
}
footer  {
  color: @color; // red
}
```

你可能已经注意到，在上面的所有例子中，我使用匈牙利符号——第一个单词小写，然后后面单词的第一个字母大写——表示我的变量。这只是我个人的喜好，它帮助我与纯粹的 CSS 区别更少。如果您愿意，也可以使用 CSS 样式的命名约定，在小写单词之间使用破折号。这完全取决于你。

我们知道如何设置变量，所以是时候做一些更高级的决定了。

## 下一次…

请继续关注本系列的第二部分，在这里我将向您介绍 LESS-Mixins 的更高级的部分。

## 分享这篇文章