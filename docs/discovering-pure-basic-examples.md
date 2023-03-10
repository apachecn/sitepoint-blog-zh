# 发现纯粹:基本范例

> 原文：<https://www.sitepoint.com/discovering-pure-basic-examples/>

## 介绍

该网络的一些最经常的读者可能还记得我以前的一个系列，名为[从零开始建立一个响应迅速、移动友好的网站](https://www.sitepoint.com/series/build-a-responsive-mobile-friendly-website-from-scratch/)。它从实体模型和线框的创建开始，一直到根据所用设备调整布局。

我决定接手这个小项目，向您展示如何使用一个简单的 CSS 框架“ [Pure](http://purecss.io/) ”在更短的时间内重新创建相同的网页、样式和布局。

在这个简短的教程中，我将只关注一些你可以在网站上找到的元素，特别展示如何重新创建经典的水平菜单，一个最小的两列网格结构和一个简单的联系人表单。

在继续之前，让我简单介绍一下这个最新的现代框架，挑出它的主要模块并解释它的一些主要特性。

## 纯:什么事？

Pure 是一个框架，可以帮助你管理网站的所有元素，使它们在所有屏幕尺寸上都看起来很棒，不管你选择使用什么设备。它可以在 IE 7+，Firefox，Chrome，Safari，iOS 6.x 和 Android 4.x 中工作。

它的一个主要特点是它的重量。事实上，它是一个轻量级的完整库，只有 4.4KB，由六个基本模块组成，它们是:

*   基础
*   网格
*   形式
*   小跟班
*   桌子
*   菜单

在其最重要的特性中，应该提到的是，该库基于著名的 [Normalize.css](http://necolas.github.io/normalize.css/) ，这是一个可定制的 css 文件，它使浏览器更加一致地呈现所有元素，符合现代标准，并有助于修复跨浏览器兼容性问题(这总是很多！).Pure 提供了一个极其简约的外观，在几个方面都非常容易定制。

例如，您可以使用 Skin Builder 创建自己的配色方案并修改其他功能，如边框半径和垂直填充，使用 Grid Builder 创建自己的网格系统，其中包含自定义的媒体查询断点和列大小。Pure 的其他令人惊叹的特性包括垂直和水平菜单、表单、按钮、各种常见的表格样式，并且它在默认情况下也是有响应的(带有一个无响应选项)。

在这一点上，开始发现 Pure 的有趣世界所需要的第一件事，是通过从 Yahoo 的 CDN 获取 Pure CSS 文件，将它包含在我们的项目中:

```
<link rel="stylesheet" href="http://yui.yahooapis.com/pure/0.3.0/pure-min.css"/>
```

一旦完成，我们就可以继续我们在本文开头安排的三个不同的任务。

请注意，为了避免混淆，我将只报告 HTML 代码，您将在一个独特的可下载文件中找到关于该样式的所有规范。

请不要关注风格:我在本教程中的唯一目标是向您介绍一种新的方法，一种编写代码的替代方法，允许没有这方面知识的开发人员从头开始，从基础开始学习。那么请原谅，我希望很快有机会用一个漂亮的图形教程给你带来惊喜。

### 任务 1:创建水平菜单

在你可以在网上的许多网站中找到的预置的公共元素中，有一个非常著名和常用的水平菜单。您可能还记得，在专门编写语义 HTML5 代码的文章的第三部分中，我使用了`<nav>`元素，这是一个语义元素，用于声明网站的导航部分(即链接到其他页面或页面内部分的页面部分),并包装了一个包含我们网站主要链接的无序列表。完成后，我在 CSS 样式表中应用了必要的规则，以便水平显示。

现在，我们要怎么做才能用纯库得到同样的结果呢？为了实现这个目标，第一步是对列表包装器(`<nav>`)应用三个类，即:`.pure-menu`、`.pure-menu-open`和`.pure-menu-horizontal`。第一个应用了 Pure 中所有菜单通用的规则；第二个(`.pure-menu-open`)用于显示列表项，而第三个也是最后一个负责在同一行显示列表项。

注意，默认情况下，菜单不是居中的。此外，为了改善用户界面，我们应该突出显示链接到当前页面的列表项。这是通过将`.pure-menu-selected`类应用到您想要突出显示的`<li>`元素来实现的。通过这样做，文本的颜色将从灰色变成黑色。

完成后，这应该是您的最终代码:

```
<nav id="main-menu" class="pure-menu pure-menu-open pure-menu-horizontal">
   <ul class="menu-list">
      <li class="pure-menu-selected"><a class="menu-item-link" href="#">Homepage</a></li>
      <li class="pure-menu-selected"><a class="menu-item-link" href="#">News</a></li>
      <li class="pure-menu-selected"><a class="menu-item-link" href="#">Graphics</a></li>
      <li class="pure-menu-selected"><a class="menu-item-link" href="#">Mobile</a></li>
      <li class="pure-menu-selected"><a class="menu-item-link" href="#">Web Design</a></li>
   </ul>
</nav>
```

下面是这一小部分代码的截图:
![horizontal menu](img/d68f3f9eaf177043819db0ecb22d67bb.png)

### 任务 2:内容网格

菜单之后，让我们看看如何管理网页的内容部分。在为之前的 RWD 系列创建的简单主页中，我们发现一个由两个单元组成的网格:左边有一个专门用于截屏部分的方框，而右边有一个空间用于包含作者的最后一条推文。

只要屏幕的宽度大于 767px，左单元和右单元都会占据其 50%的空间。相反，如果屏幕的宽度等于或小于该宽度，网格单元将堆叠起来，并占据 100%的可用宽度。

当我列出 Pure 中可用的模块时，还有一个专门用于管理网格的模块。要声明一个响应式网格，您必须向容器应用一个名为`.pure-g-r`的类。网格单元共享一个相似的名字`.pure-u-*-*`，其中类的最后一部分指定了给定单元将占用多少空间。

Pure 构想的网格系统非常有用，因为它会让你节省很多时间，但最重要的是，避免管理“讨厌的”元素，如 float、clear 和其他用于创建布局的元素。此外，他们将把你从所有的问题和尴尬的局面中解救出来，这些问题和尴尬的局面都与管理布局的响应面有关。

也就是说，让我们看一个纯网格系统如何工作的例子，实现上面描述的情况:

```
<div class="pure-g-r">
      <aside id="video-box" class="pure-u-1-2 small-box shadow">
         <p>
            Screencast box
         </p>
      </aside>
      <aside id="twitter-box" class="pure-u-1-2 small-box shadow">
         <p>
            Twitter box
         </p>
      </aside>
   </div>
```

![grid](img/aa2cf890253ed73ccaf431694ef96033.png)

### 任务 3:回复联系表格

虽然在我创建的这个示例页面中没有表单，但我决定详细研究一下，并提出 Pure library 的另一个(肯定有用的)模块，由于它，您可以以几种不同类型的样式显示表单。对于本例，我们将创建一个堆叠表单，其中输入元素位于标签下方。为了更进一步，我们必须向`<form>`元素添加两个类:`.pure-form`和`.pure-form-stacked`。

然后，我们必须将标签和输入标记放在一个包装器中，我们将对其应用`.pure-control-group`类，然后将所有这些包装器放入一个`<fieldset>`元素中。**提交**和**重置**按钮也将有一个包装器，但是它们的包装器将有类`.pure-controls`。对于**提交**按钮，我们使用了`.pure-button-primary`类来突出显示它，它属于 Buttons 模块，用于更改按钮的样式(您可以在相应的部分找到更多信息和示例)。

这是你的最终结果:

```
<div class="pure-u-1-2">
   <form id="contact" name="contact" action="#" method="post" class="pure-form pure-form-stacked">
      <fieldset>
      <legend>A Stacked Form</legend>
            <label>Name:</label>
            <input type="text" name="name" placeholder="Name" />
            <label>Email</label>
            <input type="email" name="email" placeholder="Email" />
            <label>Subject</label>
            <input type="text" name="subject" placeholder="Subject" />
            <label>Message:</label>
            <textarea name="message" placeholder="Your message here..."></textarea>
         <div class="pure-controls">
            <input type="submit" class="pure-button pure-button-primary" value="Submit" />
            <input type="reset" class="pure-button" value="Reset" /> 
         </div>
      </fieldset>
   </form>
</div>
```

下面是您的简单表单将如何出现:
![stacked form](img/c89f49a14e8f9822ee6ee9a578383d4e.png)

## 结论

通过这个简单而简短的教程，你应该已经理解了这个新的通用框架的巨大潜力。考虑在你未来的项目中使用它的一个或多个模块的可能性，这不仅是为了节省时间，也是为了用不同的方法练习编码。

如果你想了解更多关于 Pure 的信息，你可以在网站上找到更多的信息，或者在 GitHub 上访问[它的知识库。](http://github.com/yui/pure/)

你可以在这里找到[的全部代码](https://uploads.sitepoint.com/wp-content/uploads/2013/10/code.zip)。

## 分享这篇文章