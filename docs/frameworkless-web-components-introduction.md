# 无框架 Web 组件简介

> 原文：<https://www.sitepoint.com/frameworkless-web-components-introduction/>

本教程介绍了不使用 JavaScript 框架编写的标准 web 组件。您将了解它们是什么，以及如何在您自己的 web 项目中采用它们。HTML5、CSS 和 JavaScript 的合理知识是必要的。

## 什么是 Web 组件？

理想情况下，您的开发项目应该使用简单、独立的模块。每个人都应该有明确的责任。代码是封装的:只需要知道给定一组输入参数将输出什么。其他开发人员不应该需要检查实现*(当然，除非有 bug)。*

大多数语言鼓励使用模块和可重用代码，但是浏览器开发需要 HTML、CSS 和 JavaScript 的混合来呈现内容、样式和功能。相关代码可以跨多个文件拆分，并可能以意想不到的方式发生冲突。

React、Vue、Svelte 和 Angular 等 JavaScript 框架和库通过引入自己的组件化方法缓解了一些令人头疼的问题。相关的 HTML、CSS 和 JavaScript 可以合并到一个文件中。不幸的是:

*   这是另一件需要学习的事情
*   框架的发展和更新经常导致代码重构甚至重写
*   在一个框架中编写的组件通常不能在另一个框架中工作
*   框架可能会很重，并且受到 JavaScript 可实现内容的限制

十年前，jQuery 引入的许多概念被添加到浏览器中(比如 [querySelector](https://developer.mozilla.org/docs/Web/API/Document/querySelector) 、 [closest](https://developer.mozilla.org/docs/Web/API/Element/closest) 、 [classList](https://developer.mozilla.org/docs/Web/API/Element/classList) 等等)。今天，供应商正在实现 web 组件，这些组件在没有框架的情况下在浏览器中本地工作。

这需要一些时间。亚历克斯·罗素在 2011 年提出了最初的提议。谷歌(polyfill) [的聚合物框架](https://github.com/Polymer/polymer)于 2013 年问世，但三年后 Chrome 和 Safari 才出现原生实现。谈判有些令人担忧，但 Firefox 在 2018 年增加了支持，随后在 2020 年增加了 Edge (Chromium 版本)。

## Web 组件是如何工作的？

考虑 HTML5 `<video>`和`<audio>`元素，它们允许用户使用一系列内部按钮和控件来播放、暂停、倒带和快进媒体。默认情况下，浏览器处理布局、样式和功能。

Web 组件允许你添加自己的 HTML **定制元素**——比如一个`<word-count>`标签来计算页面中的字数。元素名必须包含一个连字符(`-`)，以保证它永远不会与正式的 HTML 元素冲突。

然后为您的自定义元素注册一个 ES2015 JavaScript 类。它可以添加按钮、标题、段落等 DOM 元素。为了确保这些不会与页面的其他部分冲突，你可以将它们附加到一个内部的**影子 DOM** ，它有自己的作用域样式。你可以把它想象成一个迷你的`<iframe>`，尽管 CSS 属性比如字体和颜色是通过层叠继承的。

最后，您可以使用可重用的 **HTML 模板**向影子 DOM 添加内容，这些模板通过`<slot>`标签提供一些配置。

与框架相比，标准的 web 组件是初级的。它们不包括数据绑定和状态管理等功能，但 web 组件有一些引人注目的优势:

*   它们重量轻，速度快
*   他们可以实现仅靠 JavaScript 无法实现的功能(比如影子 DOM)
*   他们在任何 JavaScript 框架中工作
*   他们将获得数年的支持——如果不是数十年的话

## 您的第一个 Web 组件

首先，向任何网页添加一个`<hello-world></hello-world>`元素。(结束标签是必不可少的:您不能定义自结束`<hello-world />`标签。)

创建一个名为`hello-world.js`的脚本文件，并从同一个 HTML 页面加载它(ES 模块被自动延迟，所以它可以放在任何地方——但是在页面中越早越好):

```
<script type="module" src="./hello-world.js"></script>

<hello-world></hello-world> 
```

在脚本文件中创建一个`HelloWorld`类:

```
// <hello-world> Web Component
class HelloWorld extends HTMLElement {

  connectedCallback() {
    this.textContent = 'Hello, World!';
  }

} 
```

Web 组件必须扩展 [HTMLElement 接口](https://developer.mozilla.org/docs/Web/API/HTMLElement)，它实现每个 HTML 元素的默认属性和方法。

*注意:Firefox 可以扩展特定的元素，比如`HTMLImageElement`和`HTMLButtonElement`。然而，它们不支持影子 DOM，这种做法在其他浏览器中也不受支持。*

每当自定义元素被添加到文档中时，浏览器都会运行`connectedCallback()`方法。在这种情况下，它会更改内部文本。*(不使用阴影 DOM。)*

该类必须注册到 [CustomElementRegistry](https://developer.mozilla.org/docs/Web/API/CustomElementRegistry) 中的自定义元素:

```
// register <hello-world> with the HelloWorld class
customElements.define( 'hello-world', HelloWorld ); 
```

加载页面，出现“Hello World”。可以使用`hello-world { ... }`选择器在 CSS 中设计新元素的样式:

看笔 [< hello-world >组件](https://codepen.io/SitePoint/pen/ZEKyeoY)by SitePoint([@ SitePoint](https://codepen.io/SitePoint))
on[code Pen](https://codepen.io)。