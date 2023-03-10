# 介绍 sGrid:一个基于触笔的 Flexbox 网格系统

> 原文：<https://www.sitepoint.com/introducing-sgrid-a-stylus-based-flexbox-grid-system/>

看完这篇文章的标题，你可能会问:为什么是另一个网格系统？这是一个好问题。

基本上有很多网格，其中许多也是 flexbox 网格。那么，我为什么要开发另一个类似的工具呢？简短而最好的答案是，因为这个工具做一件事，而且只做一件事——它只使用[手写笔预处理器](https://learnboost.github.io/stylus/)负责用语义方法构建 flexbox 网格。

这正是我所需要的。它是一个干净、轻便的工具，没有任何依赖性，因此您可以将它与许多其他 JavaScript 工具和框架一起使用。同样重要的是，它是一个标准的 npm 包，这对我很重要，因为我通常在 JavaScript 栈中工作。我相信您会发现它在您的手写笔工作流程中也很有用。

## 什么是 sGrid？

sGrid 是一个用于手写笔的 flexbox 网格系统。它只基于 3 个`.styl`文件，其中一个是可选的。这一切都包在[一个简单的小 npm 包](https://www.npmjs.com/package/s-grid)里。

这是一个标准的 npm 包，所以你可以将它与你喜欢的任何东西一起使用——与命令行一起使用，与 Grunt 或 Gulp 构建堆栈一起使用，甚至与 [Meteor JavaScript 平台](https://www.meteor.com/)一起使用。这取决于你，这完全取决于你选择的工作流程。

## 用 sGrid 启动一个示例项目

为了介绍 sGrid，我们先从一个简单而基本的例子开始。我们需要`.html`文件和`.styl`文件。我假设你已经安装了手写笔。如果没有，你应该在你的终端里用`npm install -g stylus`全球安装它。

首先，我们创建一个文件夹，并将其命名为`s-grid`。在里面，我们将创建 index.html 文件和 main.styl 文件。我们的 index.html 文件将从一个基本的 HTML 模板开始，该模板链接到我们的 css 文件 main.css。为了编译 main.css 文件，我们需要创建 main.styl 文件。让我们在同一文件夹中创建它。目前它可能是空的。

接下来我们需要安装 sGrid 本身，我们还需要安装 autoprefixer-stylus 插件。这是因为我们需要在 main.css 文件中添加一些基于 flexbox 的厂商前缀。

为此，我们将在终端中输入以下内容:

```
npm install -g s-grid autoprefixer-stylus
```

安装好所有东西后，我们可以将 sGrid 文件导入 main.styl 文件。只需打开 main.styl 并粘贴以下代码:

```
@import 's-grid-settings'
@import 's-grid-functions'
@import 's-grid-classes'
```

我们将保存该文件，并从`s-grid`目录中运行我们的手写笔观察命令。之后，应该会创建一个 main.css 文件。在`s-grid`文件夹中，运行:

```
stylus -u s-grid -u autoprefixer-stylus --with "{ browsers: ['last 2 versions'] }" -w main.styl
```

这样，我们已经告诉 Stylus 它应该监视 main.styl 文件，重新编译 main.css 文件，并且它应该只对最近两个版本的浏览器使用 Autoprefixer。当然它也应该使用 s-grid。

最后一个命令有点长，但这只是为了演示。通常你会使用一些节点应用程序工作流或一些构建系统来完成这类事情。您可以在 sGrid 文档中查看选项[。](http://stylusgrid.com/getting-started.html)

正如您所看到的，main.css 文件已经准备好，并且填充了一些 css。这是因为我们已经导入了 [s-grid-classes](https://github.com/juliancwirko/s-grid/blob/master/s-grid-classes.styl) ，这是一组现成的响应类。该导入是可选的。当您将其注释掉或从 main.styl 文件中删除时，您会看到 main.css 是空的。这很好，因为我们将不能使用助手类，但我们仍然可以使用 sGrid 函数准备我们自己的类。那么我们为什么需要这些课程呢？它们在快速原型开发中很有用，类似于 Bootstrap 和 Foundation 中的库，所以熟悉这些库的开发人员从一开始就会习惯它们。当然你不需要使用它们；事实上，最好不要。

## 使用 sGrid 构建布局

为了创建一些使用 sGrid 的示例，我们将只使用 sGrid 函数(语义方法)并使用 helper 类来编写相同的代码。所以我们不会注释掉或删除 main.styl 文件中的`s-grid-classes`导入。

让我们关注一些基本结构。在这个例子中，我们想要一个简单的居中容器，最大宽度为 500 像素。在内部，我们需要两个相等但响应迅速的列。在 index.html 文件的`body`标签中，粘贴以下代码:

```
<div class="my-grid">
  <div class="my-cell">First cell</div>
  <div class="my-cell">Second cell</div>
</div>
```

让我们也添加一些造型。在`main.style`文件中，在 sGrid 导入后，我们可以添加以下手写笔代码:

```
.my-grid
  background-color #E9E9EA
  margin-top rem-calc(100)
  center(500)
  grid()
  .my-cell
    stack()
    padding rem-calc(10)
    background-color #D5D5D7
    @media screen and (min-width: rem-calc(breakpoints[md]))
      cell(1, 2)
```

如果你还在看 main.styl 文件，你的 main.css 文件应该会更新，你就可以在浏览器中打开 index.html 文件了。您应该会看到类似于下面的 CodePen 演示:

在 [CodePen](http://codepen.io) 上用 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )的 sGrid 看到笔[一个简单的两列网格。](http://codepen.io/SitePoint/pen/qOeaae/)