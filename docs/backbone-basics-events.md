# Backbone.js 基础知识:用事件赋予应用生命

> 原文：<https://www.sitepoint.com/backbone-basics-events/>

在之前的教程中，我们深入研究了 Backbone.js 的工作原理，这是一个用于构建应用程序的 MV* JavaScript 框架。构建一个小型冲浪商店应用程序(以帮助我们跟踪库存的冲浪板)，我们研究了如何创建模型、将模型实例分组到集合中，以及迭代集合以创建视图。我们还更进一步，将我们的每个模型实例呈现到它自己的视图中，并在一个父视图中将它们组合在一起。这是[我们离开](http://codepen.io/SitePoint/pen/qdPzjO)的地方。

在本教程中，我们将看到最后一步的重要性，并引入一些控制器类型的逻辑。我在上一篇文章中提到了控制器类型的逻辑以及它的归属，但是在我们继续之前，让我们把这一点讲清楚。

## 关注点分离

在 MV*框架中，视图通常负责以下内容:

1.  呈现 UI，并向最终用户显示从数据库和/或集合中检索的数据。
2.  通过事件处理用户输入，并以某种方式将这些事件传递给模型。

“不知何故”部分是控制器逻辑发挥作用的地方。在 Backbone.js 中，我们能够在实例化期间将事件绑定到视图。如果我们想添加或删除股票，或者完全删除一个模型，我们可以这样做。我们将一步一步地介绍如何做到这一点，但是现在，假设我们的视图中有一个按钮，允许我们从库存中删除一个商品。我们可以在视图中注册该事件，视图本质上是控制器逻辑。当点击这个按钮时，我们与相应的模型进行通信，并对其进行必要的更新。模型中的变化也可以触发视图可以处理的事件，最终重新呈现视图并显示正确的数据。

简而言之:

1.  视图呈现模型数据，并注册事件。
2.  当一个事件通过用户输入触发时，我们可以运行某种回调或函数来与模型通信。
3.  在模型内部，逻辑得到执行。在现实世界中，您可能还会在这里更新数据库。
4.  模型更改触发了一个事件。
5.  视图选择该事件，并相应地采取行动，可能会重新呈现自己。

记住所有这些，我们现在需要首先考虑如何注册这些事件。让我们继续。

## 使用主干事件

根据[主干事件文档](http://backbonejs.org/#Events):

> Events 是一个可以混合到任何对象中的模块，赋予对象绑定和触发自定义命名事件的能力。

在主干应用程序中有许多处理事件的方法，但是我们将利用两种方法:

1.  我们将使用[内置事件目录](http://backbonejs.org/#Events-catalog)和`listenTo`方法[，后者告诉一个对象监听另一个对象上的特定事件。](http://backbonejs.org/#Events-listenTo)
2.  我们还将直接在视图中使用事件散列来委托事件。

让我们首先看看如何利用视图实例中的事件散列。

## 利用事件散列

以下是上一次`SurfboardView`的当前代码:

```
var SurfboardView = Backbone.View.extend({

  tagName: 'tr',

  template: _.template($('#surfboard-template').html()),

  render: function() {
    this.$el.html(this.template(this.model.attributes));
    return this;
  }

});
```

您会注意到，每个视图实例都被包装在自己的`tr`元素中，该元素是从我们之前制作的模板中填充的。让我们稍微编辑一下这个模板，添加几个按钮，允许我们添加和删除股票。我们还需要更新表格的标记:

```
<table class="table">
  <thead>
    <tr>
      <th>Manufacturer</th>
      <th>Model</th>
      <th>Stock</th>
      <th>Add/Remove</th>
    </tr>
  </thead>
  <tbody id="table-body"></tbody>
</table>

<script type="text/template" id="surfboard-template"> <td><%= manufacturer %></td>
  <td><%= model %></td>
  <td><%= stock %></td>
  <td>
    <button class="add-one">+1</button>
    <button class="minus-one">-1</button>
  </td> </script>
```

我们的每个视图实例现在都应该有两个按钮，但是它们现在什么都不做。现在让我们看一下事件散列，看看我们如何在这两个按钮上注册点击。主干中的事件散列通常如下所示:

```
events: {
  'event target': 'callback'
}
```

`event`是实际发生的事件，如`click`、`dblclick`、`mouseover`等。`target`是目标元素，它是基于视图的元素通过 DOM 遍历找到的。换句话说，如果我们指定了一个类名、一个 ID 或一个标记，它将查找第一个匹配的类型。这就是我在模板中的按钮上添加类名的原因。最后，`callback`是事件触发时调用的函数。

因此，在我们的例子中，我们可以将以下内容添加到我们的`SurfboardView`视图中:

```
events: {
  'click .add-one': 'addOne',
  'click .minus-one': 'minusOne'
},

addOne: function(e) {
  e.preventDefault();
  // code to add one to stock here...
},

minusOne: function(e) {
  e.preventDefault();
  // code to minus one from stock here...
}
```

现在唯一缺少的是实际更新模型的脚本。

## 更新模型

如果您在视图中的`addOne`或`minusOne`函数内放置一个警告或控制台语句，并通过单击按钮触发事件，您将很快看到事件的运行。然而，这并不能真正帮助我们，因为我们必须将这个用户动作反馈给模型。到目前为止，我只是阻止了默认的浏览器按钮行为。接下来，我们需要调用一个模型函数(我们仍然需要编写它)。这两个模型功能将驻留在模型本身中，忠实于我们的“关注点分离”思想。下面是更新后的`SurfboardView`:

```
var SurfboardView = Backbone.View.extend({

  tagName: 'tr',

  events: {
    'click .add-one': 'addOne',
    'click .minus-one': 'minusOne'
  },

  template: _.template($('#surfboard-template').html()),

  render: function() {
    this.$el.html(this.template(this.model.attributes));
    return this;
  },

  addOne: function(e) {
    e.preventDefault();
    this.model.addOne();
  },

  minusOne: function(e) {
    e.preventDefault();
    this.model.minusOne();
  }

});
```

请记住，每个视图实例都包含对其相应模型实例的引用。这就是为什么我们能够使用`this.model.FUNCTION`来访问模型函数。回到我们的`Surfboard`模型，我们需要考虑这些函数。

这里的逻辑很简单，因为我们只需要获取现有的库存编号，或者加一或者减一，然后更新该型号的库存编号。我们已经看了如何从模型中`get`一个属性值。同样，我们可以`set`它。以下是更新后的型号代码:

```
var Surfboard = Backbone.Model.extend({

  defaults: {
    manufacturer: '',
    model: '',
    stock: 0
  },

  addOne: function() {
    this.set({
      stock: this.get('stock') + 1
    });
    // probably update a database
  },

  minusOne: function() {
    this.set({
      stock: this.get('stock') - 1
    });
    // probably update a database
  }

});
```

在真实的应用程序中，您可能也想在这里更新数据库，但这超出了本教程的范围。

现在，如果您运行它，您会注意到当我们单击按钮时，事件被触发，并且模型实例中相应的属性值被更新。然而屏幕上什么也没有发生。这是为什么呢？我们还没有重新渲染相关的视图，所以让我们来看看。

## 在视图实例化时监听事件

这里有一个简单的场景。假设我们有一个已经呈现在屏幕上的视图。然后，用户与该视图进行交互，这触发了模型的变化。然后，该模型触发一个`change`事件(我们知道可以从事件目录中触发该事件)。当模型改变时，我们希望重新渲染视图。我们如何做到这一点？

首先，让我们记住在我们的视图实例中有一个初始化函数。我们可以用它来添加监听器，就像这样:

```
initialize: function() {
  this.listenTo(this.model, "change", this.render);
}
```

我们的视图对象现在正在监听模型对象的变化，当它确实发生变化时，我们再次调用 render 函数，更新视图。

你猜怎么着？我们差不多已经完成了，因为到目前为止我们已经编写了代码。通过单击每个视图实例中的按钮，我们不仅运行了关联回调函数，还触发了一个变更事件。这意味着通过在我们的视图中插入这一小段代码，当模型的 change 事件触发时，我们可以做任何我们想做的事情。下面是我们更新后的`SurfboardView`代码:

```
var SurfboardView = Backbone.View.extend({

  tagName: 'tr',

  events: {
    'click .add-one': 'addOne',
    'click .minus-one': 'minusOne'
  },

  template: _.template($('#surfboard-template').html()),

  initialize: function() {
    this.listenTo(this.model, "change", this.render);
  },

  render: function() {
    this.$el.html(this.template(this.model.attributes));
    return this;
  },

  addOne: function(e) {
    e.preventDefault();
    this.model.addOne();
  },

  minusOne: function(e) {
    e.preventDefault();
    this.model.minusOne();
  }

});
```

现在，一切都应该工作，你应该能够增加和减少股票，因为你！下面是它的演示和代码库:

参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )的 Pen[backbone . js Basics-Part 4](http://codepen.io/SitePoint/pen/Ejbozv/)。