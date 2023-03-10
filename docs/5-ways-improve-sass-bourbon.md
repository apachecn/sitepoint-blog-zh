# 用波旁威士忌改善口齿伶俐的 5 种方法

> 原文：<https://www.sitepoint.com/5-ways-improve-sass-bourbon/>

如果你写了很多 CSS，很有可能你已经熟悉了 [Sass](http://sass-lang.com) 。Sass 是预处理 CSS 的最好方法之一，这样你就可以获得更干净、[、更干燥、更易维护的代码的优势。光是顶嘴就能让你走得很远。](http://en.wikipedia.org/wiki/Don%27t_repeat_yourself)

但是 Sass 最好的一点是它为第三方开发人员提供了创建扩展的空间，可以进一步加快您的 Sass 体验。我见过的增强 Sass 的最轻也是最强大的工具之一是波旁酒。

Bourbon 是一个 Sass 混合库、函数库和附加组件库，它继承了 Sass 预处理器的不足，为您提供了大量方便的功能，您一定会在 Sass 工作流中受益匪浅。如果你不熟悉波旁酒，我写了[一步一步地介绍如何安装 Sass 和波旁酒](https://www.sitepoint.com/getting-started-sass-bourbon/)，你可以看一下就开始了。

一旦您安装了 Bourbon，这里有几个我最喜欢的方法来在您的日常 Sass 编码中利用 Bourbon 库。

## 1.字体格式

对于所有不同的浏览器和不同的字体格式，[需要一大块代码来在 CSS 中声明一个新的字体。但是字体在开发网站时提供了如此多的功能，无论你只是添加一个引人注目的标题字体，还是加入一个多功能的](http://www.fontspring.com/blog/the-new-bulletproof-font-face-syntax)[图标字体](http://css-tricks.com/examples/IconFont/)，它可以为你节省单个位图的巨大带宽，同时以不同的尺寸呈现矢量清晰的图像。

为了支持所有正在使用的浏览器，您需要创建各种不同格式的字体文件，然后在开始使用字体之前，在 CSS 中引用所有这些文件。浏览器会足够智能，知道在特定的会话中可以使用哪一个，但是你需要引用所有的浏览器来使你的站点健壮。调用字体外观的 CSS 看起来像这样:

```
@font-face {
    font-family: My-Font;
    font-weight: normal;
    font-style: italic;
    src: url('/styles/fonts/myfont.eot');
    src: url('/styles/fonts/myfont.eot?#iefix') format('embedded-opentype'),
       url(/styles/fonts/myfont.woff') format('woff'),
       url('/styles/fonts/myfont.ttf') format('truetype'),
       url('/styles/fonts/myfont.svg##My-Font) format('svg');
}
```

所有这些只是为了让你可以使用你想要的标题字体:

```
h1, h2, h3 {
    font-family: 'My-Font', sans-serif;
}
```

Bourbon 为 Sass 提供了一个非常方便的 mixin，可以让您快速轻松地定义一个新的字体系列。只要你对你的字体文件使用一致的命名，并把它们上传到你的服务器的同一个目录中，Bourbon 将会处理所有重复的 CSS，并给你一个和你在上面看到的一样健壮的 CSS，代码如下:

```
@include font-face(My-Font, '/fonts/my_font/MyFont-Italic', normal, italic);
```

现在你可以在 CSS 中的任何地方使用“我的字体”,所有的格式都会被所有合适的浏览器自动识别。

Bourbon 还为预定义的字体堆栈提供了默认变量，这些字体堆栈适用于使用用户已安装字体的标准 web 浏览器。这些将帮助您创建快速和强大的字体堆栈，紧密模仿首选字体的风格，并在不同平台之间进行逻辑降级。

以下萨斯/波旁威士忌:

```
font-family: $helvetica;
font-family: $georgia;
font-family: $lucida-grande;
font-family: $monospace;
font-family: $verdana;
```

自动生成以下 CSS 字体堆栈:

```
font-family: "Helvetica Neue", Helvetica, Roboto, Arial, sans-serif;
font-family: Georgia, Cambria, "Times New Roman", Times, serif;
font-family: "Lucida Grande", Tahoma, Verdana, Arial, sans-serif;
font-family: "Bitstream Vera Sans Mono", Consolas, Courier, monospace;
font-family: Verdana, Geneva, sans-serif;
```

## 2.表单输入

web 开发的核心是对表单表示的控制。但是添加跨多个表单元素一致工作的样式通常意味着必须写出复杂的选择器，似乎永远都不会结束。

例如:

```
input[type="email"], input[type="number"],
input[type="password"], input[type="search"],
input[type="tel"], input[type="text"],
input[type="url"], input[type="color"],
input[type="date"], input[type="datetime"],
input[type="datetime-local"], input[type="month"],
input[type="time"], input[type="week"] {
  color: #999;
}
```

Bourbon 定义了一组非常有用的附加组件，使统一的表单元素选择器样式易于创建和管理。上面的 CSS 可以用下面一点波旁风味的 Sass 生成:

```
#{$all-text-inputs} {
 color: #999;
}
```

存在一个类似的附加组件来支持按钮输入。此代码:

```
#{$all-button-inputs} {
 background: #f3f;
}
```

将产生这组 CSS 选择器:

```
input[type="button"],
input[type="reset"],
input[type="submit"] {
  background: #f3f;
}
```

如果你想控制这些形状元素的`:active`和`:hover`状态，波本威士忌也能满足你。将`-active`或`-focus`添加到上述波旁威士忌中，将会产生你想要的东西。例如，这段代码:

```
#{$all-text-inputs-hover} {
 color: #000;
}
```

将产生这个 CSS:

```
input[type="email"]:hover, input[type="number"]:hover,
input[type="password"]:hover, input[type="search"]:hover,
input[type="tel"]:hover, input[type="text"]:hover,
input[type="url"]:hover, input[type="color"]:hover,
input[type="date"]:hover, input[type="datetime"]:hover,
input[type="datetime-local"]:hover, input[type="month"]:hover,
input[type="time"]:hover, input[type="week"]:hover {
  color: black;
}
```

## 3.单位转换

有一件事总是需要时间和脑力，那就是弄清楚如何从图像和字体中转换像素值，以匹配相对缩放值，如 em 或 rem。虽然您每次都可以计算，并随着您的值的变化而更新，但 Bourbon 使用一些方便的转换函数来完成这项工作。

首先，如果您有一个以 px 为单位的维度，Bourbon 可以使用默认基数 16px = 1em 将其直接转换为灵活的 em 值。以下代码:

```
p {
 font-size: em(10);
}
```

将输出以下 CSS:

```
p {
  font-size: 0.625em;
}
```

如果您想基于不同的标度而不是 16 来计算 em 值，您可以将替代值作为第二个可选参数传递给`em()`函数:

```
p {
 font-size: em(10, 32);
}
```

将输出以下 CSS:

```
p {
  font-size: 0.3125em;
}
```

如果您更喜欢 rem 而不是 em，不管父元素的规模如何都从基值继承，那么还有一个对应的`rem()`函数。此代码:

```
p
 font-size: rem(10)
```

将输出以下 CSS:

```
p {
  font-size: 0.625rem;
}
```

Bourbon 还支持其他几个转换，包括舒缓的`golden-ratio()`功能，当你想让你的布局或字体节奏与时代的审美智慧产生共鸣时，这个功能可以派上用场。

## 4.柔性盒支架

CSS 布局中的新热点是 [flexbox](https://www.sitepoint.com/are-we-ready-to-use-flexbox/) ，它允许你定义你的布局元素，以及它们如何以过去需要额外 HTML 和复杂 CSS 的繁琐组合的方式表现。但是 flexbox 实际上从 2009 年就以各种风格出现了。

当然，为了支持所有不同的浏览器以及它们解释 flexbox CSS 的方式，您需要编写许多特定于浏览器的前缀，并涵盖回退情况。根据您希望提供多远的支持，使用 flexbox 编写 CSS 可能会迫使您创建如下所示的复杂 CSS:

```
.container {
  display: -webkit-box;
  display: -moz-box;
  display: box;
  display: -webkit-flex;
  display: -moz-flex;
  display: -ms-flexbox;
  display: flex;
  -webkit-box-orient: vertical;
  -moz-box-orient: vertical;
  box-orient: vertical;
  -webkit-flex-direction: column;
  -moz-flex-direction: column;
  flex-direction: column;
  -ms-flex-direction: column;
  -webkit-box-align: center;
  -moz-box-align: center;
  box-align: center;
  -webkit-align-items: center;
  -moz-align-items: center;
  -ms-align-items: center;
  -o-align-items: center;
  align-items: center;
  -ms-flex-align: center;
  -webkit-box-pack: justify;
  -moz-box-pack: justify;
  box-pack: justify;
  -webkit-justify-content: space-between;
  -moz-justify-content: space-between;
  -ms-justify-content: space-between;
  -o-justify-content: space-between;
  justify-content: space-between;
  -ms-flex-pack: justify; 
}

.container > .box {
  -webkit-box-flex: 1;
  -moz-box-flex: 1;
  box-flex: 1;
  -webkit-flex: 1;
  -moz-flex: 1;
  -ms-flex: 1;
  flex: 1;
}
```

这看起来不像是随时会发生的手抽筋吗？因此，如果您需要这些旧浏览器的支持，Bourbon 可以为您处理 flexbox 语法，只需几行简单的代码就可以生成所有的 CSS:

```
.container {
 @include display(flex);
 @include flex-direction(column);
 @include align-items(center);
 @include justify-content(space-between);
}

.container > .box {
 @include flex(1);
}
```

Bourbon 包括对一套相当完整的 flexbox 选项的支持。只要您保持 Bourbon 安装的更新，您就可以期待它随着浏览器改进对 flexbox 布局的支持而适应。

## 5.视网膜图像

现在，我们的访问者希望我们支持标准和高 dpi 显示器，我们必须了解我们在不同设备上显示的图像，以节省带宽，但仍然为我们的所有用户提供最佳的视觉体验。

一种方法涉及使用 CSS 媒体查询并定义最小设备像素比率，以根据特定设备上的显示器是否支持更高的分辨率来加载不同的背景图像。实现这一点的 CSS 如下所示:

```
.hero {
  background-image: url(hero-background.png);
}

@media only screen and (-webkit-min-device-pixel-ratio: 1.3),
       only screen and (min--moz-device-pixel-ratio: 1.3),
       only screen and (-o-min-device-pixel-ratio: 1.3 / 1),
       only screen and (min-resolution: 125dpi),
       only screen and (min-resolution: 1.3dppx) {

    .hero {
      background-image: url(hero-background_2x.png);
      background-size: 400px 300px; 
    }

}
```

你可以看到这里有很多冗余，特别是如果你对如何命名和存储你的背景图像文件保持一致的话。利用这种一致性，Bourbon 允许您使用这个简单的一行程序添加所有 CSS:

```
.hero {
 @include retina-image(hero-background, 400px 300px, png);
}
```

如果您使用的是 PNG 图像，extension 参数是可选的，因为 Bourbon 默认为 PNG。您也可以使用其他可选参数(如特定的 retina 图像和后缀)来自定义此命令。

## 今天就开始

内置了所有这些有用的功能，并且在此基础上还有许多其他特性，很难想象如果没有波本威士忌，还会编写普通的 Sass，更不用说普通的 CSS 了。我希望你会受到鼓励[尝试一下](http://bourbon.io/)，并让我知道你最喜欢的波旁威士忌特色是什么！

## 分享这篇文章