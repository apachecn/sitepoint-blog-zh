# 用 Awesomplete 构建 JavaScript 自动完成小部件

> 原文：<https://www.sitepoint.com/javascript-autocomplete-widget-awesomplete/>

自动完成是 web 应用程序中的一项功能，它可以在用户仍在键入时预测单词或句子的剩余部分。用户通常按 tab 键接受建议，或者按向下箭头键接受几个建议中的一个。

在本教程中，我们将看看如何使用 [Awesomplete](https://github.com/LeaVerou/awesomplete) JavaScript 库在我们的网站中创建一个自动完成的小部件。Awesomplete 是由著名的演讲者、作家和 W3C CSS 工作组的特邀专家 [Lea Verou](http://lea.verou.me/) 创建的。

## 为什么不用 HTML5 `datalist`元素？

HTML5 数据列表元素可能是在网站中实现自动完成功能的最简单的方式。不幸的是，[浏览器对这个元素](http://caniuse.com/#feat=datalist)的支持是有限的，它的实现也是不一致的(比如 Chrome 只从一开始匹配，Firefox 随处可见)。也不可能根据你的网站设计来设计风格，尽管很有希望，但这可能还不是正确的选择。

另一方面，Awesomplete 是一个超轻量级、可定制的 autocomplete 小部件，您可以将它放入您的页面。它没有依赖性(没有 jQuery)，可以在所有现代浏览器上工作，并且可以根据你网站的主题来设计风格。

那么我们还在等什么呢？让我们开始吧！

## 在网页中包含完整的内容

要使用完整的库，我们需要两个文件:`awesomplete.css`和`awesomplete.js`。

您可以使用 bower 获得这些信息:

```
bower install https://github.com/LeaVerou/awesomplete.git#gh-pages
```

直接从完整的网站下载。

或者，通过 [RawGit CDN](https://rawgit.com/) 包含它们(它直接从 GitHub 提供原始文件，带有适当的内容类型头)。这将在下面演示。

要实例化基本的小部件，您需要一个 input 元素和一个类`awesomplete`，后面是 datalist 元素中的关联选项。input 元素的`list`属性必须与 datalist 元素的`id`匹配。这是一个合理的默认配置，因为它为禁用 JavaScript 的用户提供了一个后备。

```
<!doctype html>
<html>
  <head>
    <link rel="stylesheet" href="https://cdn.rawgit.com/LeaVerou/awesomplete/gh-pages/awesomplete.css">
  </head>
  <body>
    <input class="awesomplete" list="mylist" />
    <datalist id="mylist">
      <option>One</option>
      <option>Two</option>
      <option>Three</option>
    </datalist>

    <script src="https://cdn.rawgit.com/LeaVerou/awesomplete/gh-pages/awesomplete.min.js"></script>
  </body>
</html>
```

## 基本功能

有许多方法可以使用这个多用途的库。让我们从一个基本的用例开始。

### 使用`data-list`属性

可以将选项从前面提到的 datalist 元素移动到 input 元素本身的`data-list`属性中。

```
<input class="awesomplete"
       data-minchars="1"
       data-maxitems="5"
       data-list="China, India, Japan, Russia, UK, USA" />
```

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看到笔[awesomblete(1)](http://codepen.io/SitePoint/pen/xGgNyr/)。