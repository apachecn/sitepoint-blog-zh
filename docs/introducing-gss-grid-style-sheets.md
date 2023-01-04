# GSS 简介:网格样式表

> 原文：<https://www.sitepoint.com/introducing-gss-grid-style-sheets/>

你可能最近听说过 HTML 和 CSS 领域的[网格样式表](http://gridstylesheets.org/) (GSS)。GSS 重新想象 CSS 布局，用一个利用*食火鸡约束求解器*的引擎替换浏览器的布局引擎。那些一个字也不懂的人…你好，欢迎！

GSS 承诺更美好的未来。GSS 承诺相对定位和规模。GSS 承诺用一行代码将任何元素放在另一个元素的中心。GSS 带来了。问题是:怎么做？

在本文中，我将介绍 GSS 的简史，并深入概述它所提供的特性。我还将研究 GSS 中基于约束的布局、食火鸡约束算法，并引导您完成安装和使用 GSS 的过程。

那些不愿意等待 W3C 或浏览器赶上来的人，我敦促你们坚持下去，在我解释 GSS 的神秘之处时，请密切注意。关于这一点，让我们从一点历史开始。

## 简史

GSS 是网格的产物，丹·托奇尼是其创始人兼首席执行官。这就解释了为什么不太基于网格的样式表被称为网格样式表。

web 开发人员和前端技术之间将想法呈现到 web 上的战争已经持续了多年。在过去的十年里，CSS 已经被证明是成功的。然而，用不随时间发展的工具构建越来越复杂的用户界面是 web 开发人员经常要做的事情。例如，用 CSS 将元素垂直居中并不是最简单的任务，尤其是高度可变的元素。

Flexbox 是最新的解决方案之一，但即使是很小的变化也需要你深入 HTML 内容和 CSS 表示并做出改变。

> 是 GSS 上场的时候了。GSS 解决了这些问题以及更多的问题——这些问题是开发人员十多年来一直面临的问题。

本质上，GSS 是一个 CSS 预处理器和 JavaScript 运行时，利用了 Cassowary.js。你们中有些人不知道，Cassowary.js 是苹果在 [Cocoa Autolayout](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/AutolayoutPG/VisualFormatLanguage.html) 中使用的 JavaScript 端口。

GSS 和食火鸡都是建立在约束编程的基础上的，这使得它非常适合增强 CSS 这样的声明性语言。约束编程是一种范式，web 开发人员通过这种范式来声明“什么”并将“如何”留给数学求解器。

> 约束编程关注的是意图，而不是实现。

现在我们已经建立了一些背景信息，让我们继续 GSS 提供的功能。

## GSS 概况

CSS 最大的问题之一就是相对性。您可以预期任何 CSS 元素都有一个无止境的属性列表——填充、高度、宽度、浮动、边距、边框、轮廓——但是这些信息都没有告诉我们该元素相对于页面上其他元素(甚至整个页面)的位置。无尽的列表也没有回答元素在不同屏幕尺寸下的显示位置。

这给我们带来了 GSS 的第一个特点:**你定义你想要的布局是什么**。花无数时间反复试验，策划如何构建布局的日子已经一去不复返了。

因为我们已经讨论过 GSS 使用 Cassowary.js，这是 GSS 的另一个伟大特性:**一行代码**就可以将一个元素放在另一个元素的中心。这使得许多变通办法变得不必要，成为过去。

例如，如果您想在站点页面的右侧垂直添加一个订阅按钮，比如一个标题，您可以使用下面的代码:

```
.subscribe-button[right] == ::window[width];
.subscribe-button[center-y] == .header[center-y];
```

另一个特性: **GSS 让浮动、表格单元格、clearfix 和水平/垂直居中变得过时。**告别浮动的危险陷阱，因为 W3C 自己说浮动对于应用程序布局来说并不理想。

> 随着网站从简单的文档发展到复杂的交互式应用，文档布局工具，例如浮动，不一定很适合应用布局
> –[W3C 网格布局模块(工作草案)](https://www.w3.org/TR/css-grid-1/#background)

像`!important`这样的 CSS 特性呢？GSS 的第四个特性做了一些类似的事情: **GSS 使用约束层次来优先考虑具有优势的约束**。我们在这里谈论的是四种内在的力量水平:

*   `!weak`
*   `!medium`
*   `!strong`
*   `!require`

请注意，`!require`是一个特殊的*T2 强度，它确保约束成立，如果约束不成立，一切都会崩溃。建议谨慎使用，不要频繁使用。*

强度级别在列表中向下增加，并且在执行期间更强的约束被给予更高的优先级。让我们看一个例子:

```
#light[years] == 50 !weak;
#light[years] == 20 !medium;
#light[years] == 35 !strong;

/* #light[years] will hold the value 35 */
```

你已经做到了这一步，现在让我们看看一些基于约束的布局。

## 基于约束的布局的 GSS

约束基本上是两个或多个变量之间的关系，它们可能成立，也可能不成立。元素的所有数字属性都有资格被约束。这里有一个例子:

```
p[line-height] >= 10;
p[line-height] <= ::window[height] / 20;
```

*   `p`叫做`selector`
*   `line-height`是 GSS 将计算其价值的财产
*   `[]`用于访问属性
*   `<=`和`>=`定义不等式约束
*   `10`和`20`是以像素为单位的数值

在上面给出的例子中，两个约束都有效。这里有一个约束不成立的例子。

```
#elementa[height] == 150;
#elementb[height] == 150;
#elementa[height] + #elementb[height] == 225;
```

最初，元素`elementa`和`elementb`的高度都被限制为 150 像素。在第三行，两个元素的和是 225px。因此，两个元素的约束中的一个将不成立。

## GSS 的选拔者

在 GSS 中，是对一组 HTML 元素的查询，它们用于确定最终将受约束影响的元素。`Selectors`很重要，因为在对它们应用约束之前，您必须从 DOM 中选择并观察元素。

GSS 支持以下基本原则。

```
#elementID[height] == 150;   /* id      */
div[height] == 150;         /* element */
.className[height] == 150; /* class   */
```

## GSS 的规则集

规则集将允许您在单个选择器上定义多个约束。您也可以嵌套它们并在其中使用 CSS 属性。

此嵌套规则集:

```
section < article {
  .aclass {
    height: == 150;
  }
}
```

与以下内容相同:

```
(section < article .aclass)[height] == 150;
```

## GSS 的房产

我已经在上面的例子中介绍了属性，但是让我们更仔细地看看它们。在 GSS，属性是属于一个元素的变量。当我们使用 CSS 已知的属性时，它们相应的 GSS 计算值被指定为元素的内联样式。

大概是这样的:

```
.container {
  height: == #elm[height];
}
```

将等于:

```
.container {
  &[height] == #elm[height];
}
```

## 食火鸡约束算法简介

GSS 采用了 Badros、Borning 和 Stuckey 于 1999 年提出的[cassoware 线性算术约束求解算法](https://constraints.cs.washington.edu/solvers/cassowary-tochi.pdf)的 JavaScript 端口(Cassowary.js)。该算法基于用户以自然语言给出的输入约束找到布局的最佳解决方案。

用户不需要确保输入约束不相互矛盾。事实上，这是食火鸡算法的精髓；它递增地评估约束并自动发现最佳解决方案。

### 食火鸡算法的计算限制

GSS 背后的约束求解器被称为食火鸡算法。该算法只能计算线性约束(即形式为`y = mx + c`)。该算法支持基本运算符(+、-、*、/)。两个(或更多)约束变量的乘法和除法不是线性的，因此会产生错误。

```
/* this expression is not linear */
#elementa[height] * #elementb[width] == newElement;
```

## 安装 GSS

对于客户端安装，通过 Bower 安装:

```
$ bower install gss
```

然后将这段代码添加到您的标记的`<head>`部分:

```
<script src="/bower_components/gss/dist/gss.js"></script>
<script type="text/javascript"> window.engine = new GSS(document); </script>
```

也可以通过 GitHub 下载 2.0.0 版[作为 zip 文件](https://github.com/gss/engine/archive/v2.0.0.zip)。

一旦你安装了 GSS，加载你的。通过在`<link>`标签上添加`type=text/gss`的 gss 样式表:

```
<link rel="stylesheet/gss" type="text/gss" href="my-first-gss-styles.gss"></link>
```

或者使用一个`<style>`元素:

```
<style type="text/gss"> /* GSS code ... */ </style>
```

一旦一切就绪并开始运行，就可以开始使用一些代码示例了。下面我将复习初学者教程。

## GSS 初学者教程

我将创建的例子将通过 CodePen 显示，但我会像一个标准的 HTML 文档一样浏览教程。首先，我将在我的 HTML 中添加以下代码行，以添加 GSS 引擎脚本:

```
<!-- GSS engine script -->
<script src="gss.js"></script>
```

我将使用 CodePen 托管版本的文件，但你可以在这里找到[一个 CDN 托管版本。接下来，我将在 GSS 引用脚本下添加以下代码(我刚刚在上面添加的那一行),以将`document`对象传递给 GSS。](https://s3-us-west-2.amazonaws.com/cdn.thegrid.io/gss/v2.0.0/v2.0.0/gss.js)

```
<!-- Giving GSS the document object -->
<script type="text/javascript"> window.engine = new GSS(document); </script>
```

如果您愿意，这可以放在一个单独的 JavaScript 文件中，包含在引擎脚本之后。

## 示例 1:垂直居中元素

我将创建一个`div`，在 GSS 布局的`h2`标签中包含一些文本，并将其添加到 HTML:

```
<div class="foo">
  <h2>When in doubt, use GSS.</h2>
</div>
```

添加一些基本的样式后，我可以开始添加一些 GSS 来创建布局。这就是乐趣的开始。

我的目标是垂直居中。foo 元素，并且即使元素的大小发生变化，也能够保持相同的对齐方式。

以下是我为实现这一目标而应用的约束条件:

*   使用`::window`选择器将元素与浏览器中页面的可见部分居中。
*   使用`::[intrinsic-height]`属性获取元素`height`的相对值，该值将用于确定相对`width`。

首先，我将在 HTML 中添加一个`<style>`块，并将`type`属性设置为`text/gss`:

```
<style type="text/gss">  </style>
```

一个`<style>`块对于定义我将要添加的 GSS 是必要的。我将通过在`<style>`标记中添加以下代码来将元素定位在屏幕的中央:

```
<style type="text/gss"> .foo {
    center: == ::window[center];
    height: == ::[intrinsic-height];
    width: == height / 2;
  } </style>
```

这就是我们所需要的。现在，元素使用 GSS 垂直居中(具有动态高度)。下面是演示:

通过[码笔](http://codepen.io)上的 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看笔[与 GSS](http://codepen.io/SitePoint/pen/xwLPdW/) 垂直对中。