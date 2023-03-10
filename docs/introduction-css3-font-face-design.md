# CSS3 字体设计介绍

> 原文：<https://www.sitepoint.com/introduction-css3-font-face-design/>

## 带有@font-face 的 Web 字体

自网络早期以来，设计师们就一直梦想着用漂亮的排版来创建网站。但是，众所周知，浏览器只能以用户安装在系统上的字体显示文本。实际上，这使得大多数网站只能使用几种字体:Arial、Verdana、Times、Georgia 和其他一些字体。

多年来，我们已经为这个问题提出了许多巧妙的解决方法。我们为网站标题、徽标、按钮和导航元素创建了 JPEGs 和 png。当这些元素需要额外的状态或变体时，我们会创建更多的图像或图像精灵来确保页面保持快速响应。每当设计或文字发生变化，所有这些图像都必须重新创建。

对于页面上的某些元素来说，这可能是一个可以接受的解决方案，但期望设计师在 Photoshop 中手工制作每篇新文章的标题，然后上传到网站上，这是不现实的。因此，对于需要经常改变的关键页面元素，我们只能使用同样的几种字体。

为了填补这一印刷空白，一些非常酷的字体嵌入脚本被创建出来，如基于 Flash 和 JavaScript 的 sIFR，以及基于 canvas 的 Cufón。虽然这些方法是一个很好的权宜之计，允许我们包含自己的字体，但它们有严重的缺点。有时它们很难实现，需要启用 JavaScript，在 sIFR 的情况下，还需要安装 Flash 插件。此外，它们大大降低了页面的下载和呈现速度。

幸运的是，现在有一个更好的方法:`@font-face`是一个嵌入字体的纯 CSS 解决方案——它在任何市场份额的浏览器上都受支持，从 IE6 开始。

## 实现@font-face

`@font-face`是几个 CSS *at-rules* 中的一个，像`@media`、`@import`、`@page`，还有我们刚刚看到的那个`@keyframes`。At-rules 是将几个规则封装在一个声明中，作为浏览器 CSS 处理器的指令。`@font-face` at-rule 允许我们指定自定义字体，然后我们可以将这些字体包含在其他声明块中。

要使用`@font-face`包含字体，您必须:

1.  将字体文件以多种格式加载到服务器上，以支持所有不同的浏览器
2.  在`@font-face`规则中命名、描述并链接到该字体
3.  将字体名称包含在`font-family`属性值中，就像对系统字体一样

您已经知道如何将文件上传到服务器，所以我们将在下一节讨论各种文件类型的细节。现在，我们将关注第二步和第三步，这样您就可以熟悉`@font-face`的语法。

这里有一个`@font-face`块的例子:

```
@font-face {
  font-family: 'fontName';
  src: source;
  font-weight: weight;
  font-style: style;
}
```

字体系列和源是必需的，但是粗细和样式是可选的。

你需要为你的站点中包含的每种字体包含一个单独的 at-rule。您还必须为字体的每种变化包括单独的 at-rule——常规、细、粗、斜体、黑色等等。

`@font-face` at-rule 声明的`font-family`部分与您已经熟悉的`font-family`属性略有不同。在这种情况下，我们将*声明*为我们的字体命名，而不是将一个给定名称的字体分配给一个元素。字体名称可以是您喜欢的任何名称—它只是对字体文件的引用，因此它甚至不需要与字体名称相对应。当然，使用字体名称来保持 CSS 的可读性和可维护性是有意义的。确定一个约定并坚持使用你的所有字体是很好的。

```
@font-face {
  font-family: 'LeagueGothic';
}

@font-face {
  font-family: 'AcknowledgementMedium';
}
```

## 声明字体源

现在我们已经为我们的`@font-face`规则设计了一个框架，并且我们已经给了它们一个名字，是时候将它们链接到实际的字体文件了。`src`属性可以采用几种格式。此外，您可以声明多个源。如果浏览器找不到第一个源，它将尝试下一个源，以此类推，直到它找到一个源，或者它用完了选项。

让我们为我们的联盟哥特式宣言添加更多格式:

```
@font-face {
  font-family: 'LeagueGothicRegular';
  src: url('../fonts/League_Gothic-webfont.eot') format('eot'),
       url('../fonts/League_Gothic-webfont.woff') format('woff'),
       url('../fonts/League_Gothic-webfont.ttf') format('truetype'),
       url('../fonts/League_Gothic-webfont.svg#webfontFHzvtkso') format('svg');
}
```

上面的代码块中列出了四种字体源。第一个声明是 EOT 字体声明，这是 Internet Explorer 的专有格式，也是 IE4–8 唯一理解的文件类型。

然后，我们定义了 WOFF (Web 开放字体格式，一种新兴标准)、OTF (OpenType)、TTF (TrueType)和 SVG(可缩放矢量图形)字体文件。虽然大多数桌面浏览器将使用前三种声明中的一种，但一定要包括 SVG 格式，这是 iPhone 最初支持的唯一格式。

添加这些额外的字体格式确保了对每个浏览器的支持，但不幸的是，这将在 IE9 之前的 IE 版本中引起问题。这些浏览器会将第一个`url('`和最后一个`')`之间的所有内容视为一个 URL，因此无法加载字体。起初，我们似乎可以在支持 IE 和支持其他浏览器之间做出选择，但幸运的是有一个解决方案。在 [FontSpring 博客文章](http://www.fontspring.com/blog/the-new-bulletproof-font-face-syntax)中有详细描述，它包括在 EOT URL 的末尾添加一个查询字符串。这会欺骗浏览器，让它认为`src`属性的其余部分是查询字符串的延续，所以它会寻找正确的 URL 并加载字体:

```
@font-face {
  font-family: 'LeagueGothicRegular';
  src: url('../fonts/League_Gothic-webfont.eot?#iefix') format('eot'),
       url('../fonts/League_Gothic-webfont.woff') format('woff'),
       url('../fonts/League_Gothic-webfont.ttf') format('truetype'),
       url('../fonts/League_Gothic-webfont.svg#webfontFHzvtkso') format('svg');
}
```

这种语法有一个潜在的失败点:IE9 有一个被称为*兼容模式*的特性，在这个特性中，它将尝试以 IE7 或 ie8 的方式呈现页面。这是为了防止旧网站在 IE9 更符合标准的渲染中出现问题。但是，兼容模式下的 IE9 并没有重现加载 EOT 字体时的 bug，所以上面的声明会失败。为了弥补这一点，您可以在单独的`src`属性中添加一个额外的 EOT URL:

```
@font-face {
  font-family: 'LeagueGothicRegular';
  src: url('../fonts/League_Gothic-webfont.eot');
  src: url('../fonts/League_Gothic-webfont.eot?#iefix') format('eot'),
       url('../fonts/League_Gothic-webfont.woff') format('woff'),
       url('../fonts/League_Gothic-webfont.ttf') format('truetype'),
       url('../fonts/League_Gothic-webfont.svg#webfontFHzvtkso') format('svg');
}
```

这可能是一个不必要的预防措施，因为一般来说，用户在查看您的站点时需要故意将 IE 切换到兼容模式，以避免出现此问题。或者，您也可以通过将这个`meta`元素添加到文档的`head`中来强制 IE 退出兼容模式:

```
<meta http-equiv="X-UA-Compatible" content="IE=Edge">
```

通过添加一个额外的 HTTP 头也可以达到同样的效果；这可以通过您的。htaccess 文件(或等效文件):

```
<IfModule mod_setenvif.c>
  <IfModule mod_headers.c>
    BrowserMatch MSIE ie
    Header set X-UA-Compatible "IE=Edge"
  </IfModule>
</IfModule>
```

## 字体属性描述符

字体属性描述符(包括`font-style`、`font-variant`、`font-weight`等)可以随意添加，以定义字体的特征，并用于将样式与特定字体相匹配。这些值与等效的 CSS 属性相同:

```
@font-face {
  font-family: 'LeagueGothicRegular';
  src: url('../fonts/League_Gothic-webfont.eot');
  src: url('../fonts/League_Gothic-webfont.eot?#iefix')format('eot'),
       url('../fonts/League_Gothic-webfont.woff') format('woff'),
       url('../fonts/League_Gothic-webfont.ttf') format('truetype'),
       url('../fonts/League_Gothic-webfont.svg#webfontFHzvtkso') format('svg');
  font-weight: bold;
  font-style: normal;
}
```

同样，该行为与您预期的不同。你不是在告诉浏览器把字体加粗；相反，你告诉它这个*是字体的粗体变体*。这可能会令人困惑，并且在某些浏览器中行为可能会很古怪。

然而，有理由在`@font-face`规则声明中使用`font-weight`或`font-style`描述符。您可以为同一个`font-family`名称声明多个字体源:

```
@font-face {
  font-family: 'CoolFont';
  font-style: normal;
  src: url(fonts/CoolFontStd.ttf);
}

@font-face {
  font-family: 'CoolFont';
  font-style: italic;
  src: url(fonts/CoolFontItalic.ttf);
}

.whichFont {
  font-family: 'CoolFont';
}
```

注意，两个 at-rules 使用相同的字体系列名称，但是字体样式不同。在这个例子中，`.whichFont`元素将使用 CoolFontStd.ttf 字体，因为它匹配 at-rule 中给出的样式。但是，如果元素继承了斜体字体样式，它将切换到使用 CoolFontItalic.ttf 字体。

## Unicode 范围

另外还有`unicode-range`描述符，用来定义字体支持的 Unicode 字符的范围。如果省略该属性，字体文件中包含的所有字符都将可用。
我们不会在我们的网站上使用它，但这里有一个它看起来像什么的例子:

```
unicode-range: U+000-49F, U+2000-27FF, U+2900-2BFF, U+1D400-1D7FF;
```

## 应用字体

一旦使用`@font-face`语法声明了字体，就可以像在 CSS 中引用任何普通系统字体一样引用它:将它作为`font-family`属性的值包含在“堆栈”中。最好声明一两种备用字体，以防嵌入的字体加载失败。

```
h1 {
  text-shadow: #fff 1px 1px;
  font-family: LeagueGothic, Tahoma, Geneva, sans-serif;
  text-transform: uppercase;
  line-height: 1;
}
```

我们的两种嵌入字体用在样式表的许多不同地方，但是您已经明白了。

## @font-face 故障排除

如果你的字体不能在任何浏览器中显示，问题很可能出在你的 CSS 中的路径上。检查以确保字体文件确实在您期望的位置。基于浏览器的调试工具——如 WebKit 中的 Web Inspector、Opera 中的蜻蜓或 Firebug Firefox 扩展——会指示文件是否丢失。

如果您确定路径是正确的，并且文件在它应该在的地方，请确保您的服务器被正确配置以提供字体。如果 Windows IIS 服务器无法识别文件的 MIME 类型，它们将不会提供这些文件，因此请尝试将 WOFF 和 SVG 添加到您的 MIME 类型列表中(EOT 和 TTF 应支持开箱即用):

```
.woff  application/x-font-woff
.svg   image/svg+xml
```

最后，一些浏览器要求字体文件和它们嵌入的页面来自同一个域。

这是来自真实世界的 [HTML5 & CSS3 的摘录，作者是 Alexis Goldstein，Louis Lazaris & Estelle Weyl。](https://www.sitepoint.com/premium/books/html5-css3-for-the-real-world-2nd-edition/)

## 分享这篇文章