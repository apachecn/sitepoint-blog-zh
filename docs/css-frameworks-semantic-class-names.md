# CSS 框架和语义类名

> 原文：<https://www.sitepoint.com/css-frameworks-semantic-class-names/>

![A bulldozer pushing the letters C S S](img/b7faa8078f1541441655f9eef78e2717.png "CSS Dozer")

关于 CSS 框架，比如[蓝图](http://blueprintcss.org/)、 [YUI 网格](http://developer.yahoo.com/yui/grids/)和 [960.gs](http://960.gs/) 的一个最常见的抱怨是，它们要求设计者通过在 HTML 文档中添加表示性的类名来弄脏手指，就像这样:

```
<div class="span-9 last">
<div class="grid_6 alpha">
```

像`"span-9"`这样的类名确实让那些关心 HTML 代码质量的设计者感到困扰，因为它们描述了一个元素的**外观**；这实际上应该留给在站点样式表中定义的 CSS 属性。W3C 的 QA 技巧 [<cite>在考虑语义的情况下使用`class`</cite>](https://www.w3.org/QA/Tips/goodclassnames)很好地解释了表示类名的问题:

> **好名字不改。**想想*为什么*你想让某样东西看起来像某个样子，而不是真的去想*它应该是什么样子*。外观总是可以改变的，但是给某物一个外观的原因是不变的。

也许你是一个务实的人，对这类事情没有异议，或者考虑到 CSS 语言的局限性，认为这是必要的。然而，有很多前端忍者因为这个原因拒绝使用 CSS 框架。

然而，事实证明，最新的 CSS 框架为表示类名的问题提供了巧妙的解决方案。

CSS 布局框架的鼻祖 Blueprint CSS ，现在在其下载包中包含了一个聪明的 Ruby 脚本 compress.rb。在 [<cite>Blueprint 的 compress . Rb:A walk through</cite>](http://www.jdclayton.com/blueprints_compress_a_walkthrough.html)中，Blueprint 作者 Joshua Clayton 解释了如何使用脚本，使用自己的语义类名生成 Blueprint 样式表的自定义版本。

这个过程可以归结为编写一个简单的配置文件，告诉脚本如何将 Blueprint 的表示类名映射到您自己的语义上有意义的类名。该文件将如下所示:

```
demo:
  path: /Users/kyank/Documents/myproject
  semantic_classes:
    ".header, .footer": ".span-24, div.span-24"
    ".content": ".span-18, div.span-18"
    ".sidebar": ".span-6, div.span-6,
                 .last, div.last"
```

`semantic_classes`部分提供了映射。在这个例子中，`header`和`footer`类将是 24 个网格单位宽，而`content`类将是 18 个网格单位宽。`sidebar`等级将是 6 个网格单位宽，并且是其行中的最后一个块。

写好这个配置文件后，只需运行 compress.rb 脚本，蓝图样式表文件的定制版本(screen.css、print.css 和 ie.css)就会在指定的路径中生成。样式表将包含像这样的规则，这些规则将网格维度应用于您的自定义类名:

```
/* semantic class names */
.content {float:left;margin-right:10px;
  width:710px;}
```

…就这样，你获得了 Blueprint CSS 的所有布局工具，而没有 HTML cruft！

如果用 Ruby 脚本手动编译样式表听起来有点痛苦(这是可能的——特别是如果您在没有安装 Ruby 的 Windows 计算机上开发),那么服务器端 CSS 框架可能是您更好的选择。

所有主要的服务器端编程语言都出现了 CSS 框架。两个突出的例子包括 [Compass](http://compass-style.org/) (对于 Ruby)和 [Scaffold](http://wiki.github.com/anthonyshort/csscaffold) (对于 PHP)。这些框架允许您编写带有额外语言特性的样式表，并在将它们发送到浏览器时自动编译成标准 CSS 代码。

例如，使用 Scaffold，您可以像这样编写样式表:

```
@grid {
  column-width:30;
  column-count:24;
  right-gutter-width:20;
  baseline:20;
  unit:px;
}
.header, .footer {
  +columns(24);
}
.content {
  +columns(18);
}
.sidebar {
  +columns(6);
  +last;
}
```

`@grid` at-rule 为 Scaffold 的[布局插件](http://wiki.github.com/anthonyshort/csscaffold/layout-plugin)设置选项，然后当浏览器请求样式表时，类似`+columns(24);`(称为 **mixins** )的行被编译成标准 CSS 属性。

这些只是最新的 CSS 框架回应那些抱怨不得不牺牲 HTML 代码质量的批评家的几种方式。现在，您可以拥有一个经过良好测试的布局框架的所有好处，并按照您的方式将它们应用到您的 HTML 代码中。

<cite>(图片:[恩鲍尔](http://www.sxc.hu/photo/858987) )</cite>

## 分享这篇文章