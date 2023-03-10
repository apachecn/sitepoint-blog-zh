# 用降价给你的电子邮件增添趣味

> 原文：<https://www.sitepoint.com/spicing-up-your-emails-with-markdown/>

Markdown 提供了一种简单的方法来标记纯文本，以便将其转换为 HTML。

我每天用 Markdown 写文档，网站内容等等。我也写了很多电子邮件，所以我很高兴发现了一个简单的方法来创建漂亮的 HTML 电子邮件。

## 这里降价

![the Markdown Here logo](img/f9ed1ad39c3f0b69b3c81b68d3b47685.png) [ Markdown Here ](http://markdown-here.com/) (MDH)是一个简单的浏览器扩展，可以安装在 Chrome、Firefox、Safari、Opera 等浏览器中。它会在浏览器的地址栏中添加一个图标:

![MDH address bar icon](img/836dfb70d4c367a419ebf732103e372f.png)

只需在你的电子邮件中添加 Markdown 语法，当你准备发送时，点击 MDH 图标。(你也可以通过你选择的键盘快捷键激活 MDH——默认为**CTRL+ALT+M**——或者通过下拉上下文菜单。)点击这个图标意味着你所有的降价信息都被转换成了 HTML 格式——所以你最终会发送一封 HTML 格式的电子邮件。(实际上，降价信息不会随邮件一起发送，所以你不必担心邮件客户看不懂。)

例如，您可以发送如下所示的电子邮件:

![email styled by MDH](img/5b817d242f5591cd22bc76bfd3fdb734.png)

### 其他用途

如果你不喜欢在浏览器中发送电子邮件，你也可以在电子邮件客户端如雷鸟、邮箱和冰鸽中使用 MDH。

这里的减价不仅限于电子邮件。可以在 Google Groups、Evernote、WordPress 等其他网页编辑界面使用。

## 创建 HTML 电子邮件

我倾向于在浏览器中直接通过 Gmail 发送电子邮件。能够用一些 HTML 格式增强电子邮件真的很好，这很容易通过 Markdown 实现。

我经常需要发送较长的电子邮件，能够添加标题、引号、内联代码、代码块和列表以使电子邮件可读并分解内容是非常棒的。

这里有一些你能做什么的例子。

### 密码

由于参与了网页设计和开发，我经常通过电子邮件发送代码样本。编写内联代码很简单:

```
You could try `.element {display: block;}` instead.
```

MDH 转换后，上面的文字看起来是这样的:

![inline code](img/1edf67462ab70ba6b523826b0e3d3200.png)

代码块也很容易。请注意，您可以指定语言来获得漂亮的语法高亮显示:

```
```javascript
(function() {
    var method;
    var noop = function () {};
    var methods = [
        'assert', 'clear', 'count', 'debug', 'dir', 'dirxml', 'error',
        'exception', 'group', 'groupCollapsed', 'groupEnd', 'info', 'log'
    ];
    var length = methods.length;
    var console = (window.console = window.console || {});
}());

// From the HTML5 Boilerplate
``` 
```

这就是结果:

![a block of code with syntax highlighting](img/230697ed00cef9967a67f8e261781835.png)

### 引用

在电子邮件中引用文字很方便。简单地复制文本并在它前面放置一个`>`非常简单:

```
> Should I click this email link saying I'll win $1MM?

Nah, maybe not.
```

这导致了:

![quoted text](img/f970193345d3784d732f3d83bb79d116.png)

### 链接

您可以轻松地将它们转换为美观的基于文本的链接，而不是发送长而难看的 URL:

```
There are some great articles on [SitePoint](https://www.sitepoint.com/).
```

它是这样呈现的:

![linked text](img/1fb2ca18d20ae3f7186d0c10053ca87b.png)

### 列表

列表中的文本通常更容易阅读。创建有序和无序列表就像这样简单:

```
- first item in an unordered list
- second item in an unordered list

1\. first item in an ordered list
1\. second item in an ordered list 
```

这是结果:

![unordered and ordered lists](img/f5e9809e71a3c14ad6f6bc2532ad1dc4.png)

### 标题

您可以像这样标记标题:

```
# Heading One

Some text

## Heading Two
```

如此渲染:

![heading examples](img/90d1c7bf1b64ae88ed76bd7e77012ab5.png)

### 强调

您可以轻松地添加斜体和粗体，如下所示:

```
*These* are both _italic_, and **these** are both __bold__. 
And this is *__italic and bold__*.
```

看起来是这样的:

![italics and bold](img/ebdb03209805f56af8a7c204b68f8260.png)

### 水平标尺

如果我要改变话题，加入一条横向规则会很好:

```
End of one topic.

---

Beginning of the next …
```

像这样的输出:

![horizontal rule](img/a2718992f85615fc5ebda7d5cdeb0967.png)

### 桌子

诚然，我很少需要在电子邮件中放置表格，但你可以这样做也很好:

```
Selector    |   Property        |   Value
---         |   ---             |   ---
`body`      |   **color**       |   *#30353b*
`.aside`    |   **float**       |   *right*
`img`       |   **display**     |   *inline-block*
```

上面的文本呈现如下:

![a table](img/b2dff01e86c19aa84aabcc8771502ef4.png)

### 形象

您可以将图像添加到您的电子邮件中，只要您可以在线链接到它们:

```
Here's a cool picture of grass:

![grass](https://unsplash.it/600/200?image=487)
```

![an image of grass](img/3e49029b6e3b748edf92cd53cc5215dd.png)

### 其他 HTML 元素

MDH 还允许你添加 Markdown 没有覆盖的 HTML 元素。例如，您可以添加上标:

```
This is the 1<sup>st</sup> presentation …
```

![superscript example](img/ea2945d7ced40eebbc4155c61360a1fd.png)

```
This text should be <s>crossed out</s>.
```

![strikethrough demo](img/ad795239c4f9ff33c17cf2c859d0ab81.png)

## 式样

MDH 默认使用类似 GitHub 的风格。但是如果您愿意，您可以轻松地定制样式。例如，在 Mac 版 Chrome 中，进入**窗口** > **扩展**，你会看到 MDH 扩展:

![Markdown Here extension in Chrome](img/0ac95fd99fc5c5ab5550a0d5031f5d26.png)

点击**选项**链接。这将打开一个非常有用的选项页面，您可以在其中更改各种 MDH 设置，包括样式。你可以从主题列表中选择(包括一些在代码编辑器中流行的漂亮的主题，比如 Sublime Text)，或者完全编写你自己的 CSS:

![MDH styling options](img/9a6cbe48c5f8f8d424af6b8ca344d0ec.png)

当你在这个页面上的时候，一定要检查其他可用的选项，比如设置一个激活 MDH 的首选键盘组合。

## 装置

MDH 很容易安装。官方网站提供了许多链接，通过这些链接你可以将它添加到你的浏览器或电子邮件客户端。您也可以从该页面获得 GitHub 托管的项目源代码。

## 额外资源

最后，这里有一些使用和了解 MDH 的资源链接:

*   MDH [首页](http://markdown-here.com/)
*   MDH [功能一览](http://markdown-here.com/features.html)
*   MDH [安装页面](http://markdown-here.com/get.html)
*   一个方便的 MDH [小抄](https://github.com/adam-p/markdown-here/wiki/Markdown-Here-Cheatsheet)
*   MDH [技巧和诀窍列表](https://github.com/adam-p/markdown-here/wiki/Tips-and-Tricks)
*   一个 MDH 的维基页面
*   关于[与其他应用程序兼容性的指南](https://github.com/adam-p/markdown-here/wiki/Compatibility)
*   GitHub 上的 MDH [库。](https://github.com/adam-p/markdown-here/)

## 包裹

我希望你喜欢这篇关于 MDH 的简介。这是一个非常简单的扩展，但是我每天都在使用它，老实说，我讨厌没有它。

* * *

```
So Clickable

document.getElementById("clicker").addEventListener("click", function() {
alert("you triggered " + this.id);
}); 
```

```
<button id="clicker">So Clickable</button>

document.getElementById("clicker").addEventListener("click", function() {
alert("you triggered " + this.id);
}); 
```

## 分享这篇文章