# 使用 Flickr API 创建图片库——需求和标记

> 原文：<https://www.sitepoint.com/creating-image-gallery-using-flickr-api-requirements-markup/>

毫无疑问，API 在今天扮演着非常重要的角色。从小到大的 Web 服务都需要通过一个 API 来公开它们的数据，开发者可以使用这个 API 来创建一个新的服务。想想像谷歌、雅虎这样的大公司。、Twitter 和微软:它们都为自己的服务提供 API。这些 API 提供的数据的使用示例有 [SnapBird](http://snapbird.org/) ，一个在 Twitter 上提供扩展搜索选项的工具，以及 [JoindIn.js](https://github.com/AurelioDeRosa/JoindIn.js) ，一个我开发的基于 [JoindIn](http://joind.in/) 提供的数据创建小部件的库。

在这篇由两部分组成的文章中，我将向您展示如何使用 Flickr API 开发一个简单的服务，该服务允许用户在搜索字段中输入文本，然后显示 Flickr 中标题或描述与该文本匹配的图片集。这将是一个很好的机会，既可以使用第三方 API，也可以学习一些好的技术。

该项目的源代码可以在名为 [Flickr gallery demo](https://github.com/AurelioDeRosa/flickr-gallery-demo) 的存储库中的[我的 GitHub 账户](https://github.com/AurelioDeRosa)上获得。

## 要求

在我们编写任何代码之前，理解如何开发服务是很重要的。首先，我们的项目将由一个包含三个主要部分的 HTML 页面组成:标题、图库和缩略图列表。

页眉将包含页面的标题和一个搜索表单，用户可以使用它来查找与给定文本匹配的照片。

图库将位于页眉之下，并以其自然大小显示所选图像，最大宽度和高度固定(我们不希望图像溢出我们的布局，不是吗？).该图库还具有左右按钮，分别显示上一张和下一张图片。

最后，我们将有一个画廊下面的照片缩略图列表。我们不希望列表变得太长，所以我们将匹配的照片分页，每页最多显示 15 张照片，分三行显示。(在这个项目中，点击右箭头不会导致下一页缩略图的加载。但是，在阅读完本系列之后，如果您愿意，可以更新代码来触发此操作。)

整个服务的开发将关注可访问性，因此在开发过程中，我们将在有意义的地方使用相关的`role`属性，并确保可以通过使用`TAB`和`ENTER`键来导航网站和触发各种动作。

下图显示了我们努力的结果:

![The final result of this article](img/8527f35a2cac99d9baa2312c30789012.png)

### 类名约定

虽然这个项目本身并不复杂，但是我将使用 [BEM 方法](https://www.sitepoint.com/bem-smacss-advice-from-developers/)来命名除了一些助手之外的类。有些人可能会认为这是多余的，但我认为这是一个学习您可能不熟悉的新方法的好机会。用于样式目的的类名不会被用作 JavaScript 挂钩。因此，如果一段 JavaScript 需要检索一个或多个元素，而不是使用现有的类名，我将为元素添加一个特定的类名，前缀为`js-`。这是许多开发人员用来分离类名和 JavaScript 挂钩的常用技术。为了强化这个概念，让我们简单讨论一个例子。

假设我们已经这样定义了 gallery 元素:

```
<div class="gallery"><!-- other code goes here --></div>
```

`gallery`类名用于样式化元素，但是后者需要由 JavaScript 函数检索以执行一些动作。不是使用如下所示的`gallery`类名来检索元素:

```
var gallery = document.getElementsByClassName('gallery')[0];
```

我将添加另一个名为`js-gallery`的类:

```
<div class="js-gallery gallery"><!-- other code goes here --></div>
```

然后我会像这样检索元素:

```
var gallery = document.getElementsByClassName('js-gallery')[0];
```

这样做可以确保，即使在项目的后期,`gallery`类名被重命名或删除，JavaScript 代码仍然可以工作。如果你不喜欢前缀`js-`，我听说一些开发人员使用前缀`do-`来突出元素用于执行动作的事实。两个约定是等效的，你可以选择你最喜欢的一个。

已经建立了我们项目的目标、组成部分和约定，让我们开始乐趣吧！

## 加价

创建服务的第一步是编写页面的标记，我们将从头部开始。

### 朗读者

标题所需的 HTML 非常简单。我们将用一个`header`元素包装上一节描述的两个元素——标题和表单。页面的标题由一个`h1`元素定义，表单由一个`form`元素定义。该表单由一个`label`、一个`input`和一个`button`组成，用户可以在其中输入要搜索的文本，然后提交请求。通过使用`for`属性将`label`绑定到`input`。

`form`也将有一个`role="search"`属性，因为我们想强调它是一个搜索表单，输入的`type`将是`search`。最后，我们将添加一个名为`form-search`的类用于样式化，以及一个`js-form-search`类，JavaScript 将使用它来检索用户输入的文本。

标题的完整代码如下所示:

```
<header class="island">
   <h1>Flickr gallery</h1>
   <form class="js-form-search form-search" action="/search" method="get" role="search">
      <label for="query">Search:</label>
      <input type="search" name="query" id="query" placeholder="Search" required />
      <input type="submit" value="Search" />
   </form>
</header>
```

现在让我们讨论标记中最重要的元素——图库。

### 画廊

图库由三个不同的小元素组成:`img`元素用于以自然大小显示所选图像，左右箭头用于改变显示的图像。两个箭头都将使用一个`button`元素创建。这是正确的选择，因为我们希望在它们被点击时执行一个动作。从视觉上看，这些按钮看起来像箭头，但这只对视力正常的人有意义。从一个视障用户的角度来看，箭头是完全无用的。因此，我们必须通过提供按钮角色的指示来增强这些按钮。我们将通过添加文本作为按钮的内容来做到这一点——这些文本在视觉上是隐藏的(T4 ),但是可以被屏幕阅读器访问。下面采用的技术来自于 [HTML5 样板项目](https://html5boilerplate.com/)，并且使用了一个名为`visually-hidden`的类。

图库的完整 HTML 代码如下:

```
<div class="island">
   <div class="js-gallery gallery">
      <img src="" class="js-gallery__image" />
      <button class="js-gallery__arrow--left gallery__arrow gallery__arrow--left">
         <span class="visually-hidden">Previous photo</span>
      </button>
      <button class="js-gallery__arrow--right gallery__arrow gallery__arrow--right">
         <span class="visually-hidden">Next photo</span>
      </button>
   </div>
</div>
```

在我们完成标记之前，还有最后一个组件需要讨论——缩略图列表。

### 缩略图列表

最后一个组件由两个非常简单的子组件组成:当前页面的缩略图列表和分页器。两者都被定义为一个列表，但是第一个是无序列表，而第二个是有序列表。这两个列表都需要使用 JavaScript 动态生成，所以我们将在它们上面添加一个前缀为`js-`的类名。

最后一个组件的代码如下所示:

```
<div class="island thumbnails">
   <ul class="js-thumbnails__list thumbnails__list"></ul>
   <ol class="js-thumbnails__pager thumbnails__pager"></ol>
</div>
```

有了这最后一个片段，我们已经完成了对页面标记的讨论，所以是时候赋予它一些样式了。

## 款式

该项目的视觉风格将被组织成小模块，每个模块将处理一个特定的方面。作为基础，我们将使用 [normalize.css](http://necolas.github.io/normalize.css/) ，众所周知的样式表，<q>使浏览器更加一致地呈现所有元素，并符合现代标准。它只针对需要规范化的样式。</q>然后我们将有一个助手类模块，一个图库模块，一个缩略图模块，等等。

在这一节中，我们将讨论项目中使用的每个模块，除了 normalize.css。

### 布局模块

这个模块是对元素的默认样式进行分组的模块，这意味着元素在没有定义任何类的情况下应该是什么样子。例如，我们将在这里定义`a`元素和`body`元素的默认颜色。唯一的例外是`island`类，你已经看到它被用在标记中，我用它来创建*隔离块*。这个课程的灵感来自于哈里·罗伯茨的文章“岛屿”,所以如果你想了解更多，我建议你阅读引用的文章。

本模块中唯一值得一提的声明块如下:

```
*,
*:before,
*:after {
   -moz-box-sizing: border-box;
   box-sizing: border-box;
}
```

它定义了页面的所有元素必须使用 CSS `border-box` box 模型值。如果你想进一步了解这个话题，我建议你阅读保罗·爱尔兰的文章[* { Box-Sizing:Border-Box } FTW](http://www.paulirish.com/2012/box-sizing-border-box-ftw/)和克里斯·科伊尔的文章 [Box Sizing](http://css-tricks.com/box-sizing/) 。

layout.css 文件的完整代码如下所示:

```
*,
*:before,
*:after {
   -moz-box-sizing: border-box;
   box-sizing: border-box;
}

.island {
   display: block;
   max-width: 1024px;
   margin: 0 auto;
}

body {
   background-color: #060C10;
   color: #FFFFFF;
   font-family: Arial, Calibri, Helvetica, sans-serif;
}

h1 {
   display: block;
   color: #FCB720;
}

a {
   color: #FFFFFF;
}
```

### 助手类

这可能是该服务中最简单的模块，它定义了三个众所周知的助手类，即使不是全部，也是大多数人都很熟悉的。等级有:`visually-hidden`、`hidden`和`clearfix`。

第一个是`visually-hidden`，用于在视觉上隐藏一个元素。这意味着该元素不会显示在页面上，但仍然可以被屏幕阅读器访问，这对辅助功能非常重要。`hidden`是一个单声明类，用于在视觉上和屏幕阅读器上隐藏一个元素。最后一个类，`clearfix`，用于包含浮动，而不需要使用表示标记。如果你想了解更多关于这个话题的内容，你可以看看尼古拉斯·加拉格尔的文章[一个新的微 clearfix hack](http://nicolasgallagher.com/micro-clearfix-hack/) 。

该模块的完整代码如下所示:

```
.visually-hidden {
   border: 0;
   clip: rect(0 0 0 0);
   height: 1px;
   margin: -1px;
   overflow: hidden;
   padding: 0;
   position: absolute;
   width: 1px;
}

.hidden {
   display: none !important;
}

.clearfix:before,
.clearfix:after {
   content: " ";
   display: table;
}

.clearfix:after {
   clear: both;
}
```

## 结论

在本文的第一部分中，我们讨论了我们正在构建的服务的细节，以及您将从这个小系列中学到什么。然后我们讨论了我在项目创建过程中使用的约定，这样你就知道为什么类名是以某种方式定义的。

指定了目标和服务组件后，我们接着讨论了组成我们正在开发的服务的所有标记，强调了一些选择，比如为什么我选择了`button`元素作为图库的箭头。最后，在本文的最后一部分，我们开始检查项目的一些 CSS 模块。

在本系列的第二部分也是最后一部分中，您将看到剩余的 CSS 模块，并了解支持该服务的 JavaScript 代码。

## 分享这篇文章