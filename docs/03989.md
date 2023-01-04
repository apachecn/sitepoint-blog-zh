# WordPress 使用的 JavaScript 和 PHP 库

> 原文：<https://www.sitepoint.com/javascript-and-php-libraries-used-by-wordpress/>

几乎所有现代软件(包括 WordPress 等平台)都依赖于第三方库。这是因为，作为开发人员，我们不需要重新发明轮子。

如果我必须开发使用 HTTP 抽象的软件，我可能会使用类似于 Symfony HttpKernel 的包。如果我的软件需要更多(顺便说一下，这不太可能，但是让我们想象一下我们的例子)，我可以扩展这个包，或者在最坏的情况下，从头创建一个包。从头开始创造东西并不是一件坏事。这实际上是一件好事，因为它让你成为一名更好的开发人员。但是通常，在大多数现代项目中，您必须依赖第三方库(或包)。在本文中，我们将看看 WordPress 使用的各种第三方 JavaScript 和 PHP 库。

## WordPress 中使用的库列表

在 WordPress 中，第三方库与 WordPress 文件共存。为了更好地理解 WordPress 中的文件结构，WordPress 代码库页面会给你一些答案。`wp-includes`文件夹保存了应用程序执行时包含的一些库。此外，在`wp-admin`中，你会找到第二个`include`文件夹，其功能与第一个相同。在这两个文件夹中，你都可以找到 PHP 和 JavaScript 文件。

第三方 PHP 库以`class-*.php`(单个文件)命名，或者位于它们自己的文件夹中。其他名为`class-*.php`的 PHP 文件可以是 WordPress 文件(非第三方)。PHP 文件位于`wp-include`或`wp-admin/include`文件夹下。

JavaScript 文件也位于这些文件夹下，但是它们被分组在`js`文件夹中。

CSS 文件主要是 jQuery 或 TinyMCE CSS 文件，位于 include 文件夹中的`CSS`文件夹下。

## JavaScript 库

让我们从 JavaScript 库开始。WordPress 大量使用 [jQuery](https://jquery.com) 。它使用:

*   jQuery
*   [jQuery 砌体](http://masonry.desandro.com)
*   jQuery 热键
*   jQuery 建议
*   [jQuery 表单](https://github.com/malsup/form)
*   [jQuery 颜色](http://github.com/jquery/jquery-color)
*   [jQuery 迁移](https://github.com/jquery/jquery-migrate)
*   jQuery 计划
*   jquery ui

你可能会注意到，其中一些库没有链接。这是因为有些已经很旧了，回购已经不存在了。让我们一个一个地检查一下。

jQuery 是一个著名的库，一个不需要介绍的库。

jQuery Masonry 是一个插件，它使得创建像这样的砖石布局更加容易:

![jQuery Masonry plugin](img/fbf51c60aa4c1241a1d66c0d414295cc.png)

像这样的布局从头开始有点难，jQuery Masonry 使这变得容易。

jQuery 热键处理浏览器上的快捷方式。如果你习惯使用 StackEdit.io 这样的工具，你可以使用“ctrl+s”(command+s)在 gDrive 或其他云提供商上保存文件。浏览器中默认的快捷键(ctrl+s)可以用来保存网页，使用这个插件你可以覆盖这些默认的快捷键。

jQuery Form 处理表单和 AJAX 调用。如果你很好奇，想了解更多关于这个插件的信息，你可以查看他们的文档。

jQuery Color 被 WordPress 用于颜色操作和动画。

```
jQuery("#go").click(function(){
    jQuery("#block").animate({
            backgroundColor: "#abcdef"
    }, 1500 );
});
```

第一行用`id="go"`选择元素，并告诉浏览器应该在 1.5 秒内用`id="block"`动画显示元素的背景色。

jQuery Migrate 有助于您处理早期的 jQuery 代码库。这对 WordPress 来说非常重要，因为它是一个**应该向后兼容**的平台。

事实上，向后兼容是 WordPress 最著名的特性之一。另一方面，这也是 WordPress 的问题之一。WordPress 发布于 2003 年，那个版本使用的 PHP 版本很古老。今天，PHP 是一门现代语言，并借鉴了其他语言的许多有用的特性。但这是另一个主题，将在以后的文章中讨论。

WordPress 也使用其他 JavaScript 库，不仅仅是 jQuery。以下是它使用的其他一些流行的库:

*   [骨干](http://backbonejs.org)
*   颜色选择器
*   [hoverIntent](http://cherne.net/brian/resources/jquery.hoverIntent.html) (位于 jQuery 文件夹之外的 jQuery 插件)
*   [社福对象](http://code.google.com/p/swfobject/)
*   tinymce

这样的例子不胜枚举。

这个列表中最著名的是 TinyMCE 和 Backbone。TineMCE 用于在 WordPress 仪表盘上撰写新的文章和页面。

Backbone 是一个前端框架，事实上它是第一个。它今天仍在使用，但失去了 Angular 和其他前端框架的大量市场份额。是的，我在和你说话[流星](https://www.meteor.com)。但是不要担心，在 JavaScript 世界中，框架(库)被遗忘的速度和它们流行的速度一样快。

## PHP 库

PHP 库的列表有点不同。我们看到 JavaScript 主要受 jQuery 和其他一些流行库的影响。对于 PHP，情况就不同了，因为大多数第三方库只是一个类(文件)。以下是当前第三方 PHP 库的简短列表:

*   [原子库](https://code.google.com/p/phpatomlib/)
*   文本差异
*   单纯形
*   坡莫
*   [ID3](https://github.com/JamesHeinrich/getID3)
*   史努比
*   [PHPMailer](https://github.com/PHPMailer/PHPMailer/)
*   POP3 类
*   [PHPass](http://www.openwall.com/phpass/)
*   [PemFTP](http://www.phpclasses.org/browse/package/1743.html)

## 公开讨论

许多图书馆都有点过时了。这是讨论建筑等话题的时候。我个人希望看到 Bower 和 Composer 与 WordPress 的整合。我想问问 SitePoint 社区对这些想法的看法，并就如何用 WordPress 改进进行公开讨论。请在下面留下你的评论和想法。

## 结论

在这篇文章中，我们看到了 WordPress 使用的各种第三方库。了解了 WordPress 在幕后使用了什么，你就会对它的工作原理有更好的理解。你理解得越好，你就能越快、越聪明地使用 WordPress。也许你甚至可以为 WordPress core 做贡献。毕竟这是开源的世界，我们互相学习，分享思想(代码和披萨都有！).

## 分享这篇文章