# Backbone.js 基础知识:模型、视图、集合和模板

> 原文：<https://www.sitepoint.com/backbone-basics-models-views-collections-templates/>

在本教程中，我们将探索流行的 MV*框架的底层基础， [Backbone.js](http://backbonejs.org/) 。我们将看看模型、视图、集合和模板，并了解在构建应用程序时，它们是如何相互依赖的。我们还将谈到责任和关注点的分离，最终为我们用一个健全的代码库构建一个可伸缩的应用程序铺平道路。

主干只有一个硬依赖，那就是[下划线. js.](http://underscorejs.org/) 我们还将利用 [jQuery](http://jquery.com/) 来实现简单的 DOM 操作。典型的文档结构应该如下所示:

```
<html>
<body>

app content here

<script src="path/to/jquery.js">
<script src="path/to/underscore.js">
<script src="path/to/backbone.js">
<script src="path/to/app.js">

</body>
</html>
```

所有应用程序脚本必须包含在各种库之后。除此之外，我们已经准备好投入进去了！

## backbone . js & MV *模式

主干属于 MV*框架的范畴，这意味着它主要由 **M** 模型和 **V** 视图组成。传统的 MVC 框架包含额外的 **C** ，它代表控制器。从历史上看，控制器会响应某种用户输入，并将该输入传递给模型，或者返回给视图。然而，在像 Backbone 这样的 MV*框架中，控制器逻辑是在视图本身内部处理的。为了更好地理解 JavaScript MV*框架的结构，请看这篇文章。

我们在这里有点操之过急了，因为我们还没有看到什么是模型和视图。嗯，没那么复杂！我试图将模型视为“数据模型”，就像建筑师可能有一个房子模型，或者工程师可能有一个机翼模型一样。记住这一点，就更容易理解一个特定的模型是如何表示一组特定的数据的。在生产级应用程序中，数据可能存储在某个数据库中。因此，模型可以与数据库通信，并执行某些操作，比如 CRUD 操作。

观点呢？鉴于“视图”这个名称，很容易对他们的责任做出假设。如果您认为它是为最终用户呈现数据，那么您基本上是正确的。观点确实对此负有责任。然而，在主干中，它们还有一个我之前提到过的主要功能。它们处理控制器逻辑。在本系列的第二部分中，我将讨论视图内部的事件处理，与模型通信，然后将更新发送回视图。但是现在，重要的是理解这个逻辑确实存在，但是存在于视图中。让我们进入模型，更好地理解它们。

## 挖掘模型

这里有一小段摘录自[关于模型](http://backbonejs.org/#Model)的主干文档:

> 模型是任何 JavaScript 应用程序的核心，包含交互数据以及围绕它的大部分逻辑:转换、验证、计算属性和访问控制。

记住这一点，让我们起草一个小例子，我们将使用前进。假设我们在经营一家冲浪用品店，我们想建立一个我们携带的冲浪板的数据库。这样，如果客户询问我们是否有来自特定制造商的主板，或者主板的确切型号，或者两者都有，我们可以快速查找。我们还假设我们想要跟踪股票。我们的模型层次结构如下所示:

```
Surfboard
  |__manufacturer
  |__model
  |__stock
```

在主干中，我们通过如下扩展`Backbone.Model`创建了一个新模型:

```
var Surfboard = Backbone.Model.extend({

});
```

现在，我们可以使用构造函数创建这个模型的一个新实例，如下所示:

```
var board1 = new Surfboard({
  manufacturer: 'Channel Islands',
  model: 'Whip',
  stock: 12
});
```

变量`board1`现在引用了我们的`Surfboard`模型的一个新实例，并包含它自己的一组值。尽管如此，我们可以传入任何值。让我们利用`defaults`函数向我们的模型添加一些[默认属性。](http://backbonejs.org/#Model-defaults)现在，它应该是这样的:

```
var Surfboard = Backbone.Model.extend({
  defaults: {
    manufacturer: '',
    model: '',
    stock: 0
  }
});
```

如果我们想从那个实例中获取一些数据，我们将使用`get`，它从模型中获取属性的当前值。假设我们的文档中有这样的标记:

```
<table class="table">
  <tr>
    <th>Manufacturer</th>
    <th>Model</th>
    <th>Stock</th>
  </tr>
  <tr>
    <td id="board1-manufacturer"></td>
    <td id="board1-model"></td>
    <td id="board1-stock"></td>
  </tr>
</table>
```

我们可以像这样填充这些字段:

```
$('#board1-manufacturer').html(board1.get('manufacturer'));
$('#board1-model').html(board1.get('model'));
$('#board1-stock').html(board1.get('stock'));
```

以下是包含第二个模型实例的结果:

参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )的 Pen[backbone . js Basics-Part 1](http://codepen.io/SitePoint/pen/qdPGGe/)。