# 使用 Backbone 和 EaselJS 实现拖放

> 原文：<https://www.sitepoint.com/implementing-drag-drop-using-backbone-easeljs/>

在本文中，我们将使用 [EaselJS](http://easeljs.com) 和 [Backbone.js](http://backbonejs.org) 构建一个简单的拖放应用程序。Backbone 将通过提供*模型*、*集合*和*视图*为我们的应用程序提供结构。Easel 将使使用 HTML5 `canvas`元素变得容易。虽然对于这样一个简单的应用程序，我们不一定需要 Backbone，但是以这种方式开始使用 Backbone 是很有趣的。

## 开始

首先，我们创建目录结构，如下所示:

```
.
|-- index.html
+-- js
    |-- main.js
    |-- models
    |   +-- stone.js
    +-- views
        +-- view.js 
```

接下来，在`index.html`中包含 JavaScript 文件和一个`canvas`元素，如下面的代码示例所示。一旦完成，我们就可以操纵`canvas`了。

```
<body>
  <!-- Canvas Element -->
  <canvas id="testcanvas" height="640" width="480"/>

  <script src="/bower_components/jquery/jquery.min.js"></script>
  <!-- underscore is needed by backbone.js -->
  <script src="/bower_components/underscore/underscore-min.js"></script>
  <script src="/bower_components/backbone/backbone.js"></script>
  <script src="/bower_components/easeljs/lib/easeljs-0.7.1.min.js"></script>
  <!-- tweenjs is for some animations -->
  <script src="/bower_components/createjs-tweenjs/lib/tweenjs-0.5.1.min.js"></script>
  <script src="/js/models/stone.js"></script>
  <script src="/js/views/view.js"></script>
  <script src="/js/main.js"></script>
</body>
```

## 主干模型

通过创建主干模型，我们将在该模型上拥有键值绑定和自定义事件。这意味着我们可以监听模型属性的变化，并相应地呈现我们的视图。主干集合是模型的有序集合。您可以绑定`change`事件，以便在集合中的任何模型发生变化时得到通知。接下来，让我们创建一个石头模型，和一个石头收藏。以下代码属于`js/models/stone.js`。

```
var Stone = Backbone.Model.extend({

});

var StoneCollection = Backbone.Collection.extend({
  model: Stone
});
```

## 使用 EaselJS 初始化主干视图

主干视图并不决定 HTML 的任何东西，它可以用于任何 JavaScript 模板库。在我们的例子中，我们没有使用模板库。相反，我们操纵`canvas`。您可以将视图的`render()`函数绑定到模型的`change`事件，这样当模型数据改变时，视图会自动更新。

为了开始使用 Easel，我们创建了一个包装了`canvas`元素的 stage，并添加了作为子元素的对象。后来，我们通过这个阶段，我们的骨干观点。下面显示了`js/main.js`中实现这一点的代码。

```
$(document).ready(function() {
  var stage = new createjs.Stage("testcanvas");
  var view = new CanvasView({stage: stage}).render();
});
```

我们已经创建了我们的`CanvasView`并调用它的`render()`函数来呈现它。我们将很快重新讨论`render()`的实现。首先，让我们看看我们的`initialize()`函数，它是在`js/views/view.js`中定义的。

```
var CanvasView = Backbone.View.extend({
  initialize: function(args) {
    // easeljs stage passed as argument.
    this.stage = args.stage;
    // enableMouseOver is necessary to enable mouseover event http://www.createjs.com/Docs/EaselJS/classes/DisplayObject.html#event_mouseover
    this.stage.enableMouseOver(20);

    // stone collection
    this.collection = new StoneCollection();

    // bounds of pink area and our stones. the pink area is called "rake".
    this.rakeOffsets = {
      x: 10,
      y: 400,
      height: 150,
      width: 300,
      stoneWidth: 50,
      stoneHeight: 50
    };

    // listen to collection's add remove and reset events and call the according function to reflect changes.
    this.listenTo(this.collection, "add", this.renderStone, this);
    this.listenTo(this.collection, "remove", this.renderRake, this);
    this.listenTo(this.collection, "reset", this.renderRake, this);
  },
  //...
});
```

`listenTo()`监听模型/集合的变化，并调用作为第二个参数传递的函数。我们传递函数被调用的上下文作为第三个参数。当我们向集合中添加一块石头时，一个`add`事件将分派`this.renderStone()`并将新石头传递给函数。类似地，当集合被重置时，一个`reset`事件将分派`this.renderRake()`。通过实现这些呈现函数，视图将始终与集合保持同步。

## 渲染视图

下面显示的`render()`函数只调用`this.renderRake()`并更新 stage。

```
render: function() {
  this.renderRake();

  // stage.update is needed to render the display to the canvas.
  // if we don't call this nothing will be seen.
  this.stage.update();

  // The Ticker provides a centralized tick at a set interval.
  // we set the fps for a smoother animation.
  createjs.Ticker.addEventListener("tick", this.stage);
  createjs.Ticker.setInterval(25);
  createjs.Ticker.setFPS(60);
},
```

也存储在`js/views/view.js`中的`renderRake()`方法如下所示。

```
renderRake: function() {
  // http://stackoverflow.com/questions/4886632/what-does-var-that-this-mean-in-javascript
  var that = this;

  // create the rake shape
  var rakeShape = new createjs.Shape();

  rakeShape.graphics.beginStroke("#000").beginFill("#daa").drawRect(this.rakeOffsets.x, this.rakeOffsets.y, this.rakeOffsets.width, this.rakeOffsets.height);

  // assign a click handler
  rakeShape.on("click", function(evt) {
    // When rake is clicked a new stone is added to the collection.
    // Note that we add a stone to our collection, and expect view to reflect that.
    that.collection.add(new Stone());
  });

  // add the shape to the stage
  this.stage.addChild(rakeShape);

  // a createjs container to hold all the stones.
  // we hold all the stones in a compound display so we can
  // easily change their z-index inside the container,
  // without messing with other display objects.
  this.stoneContainer = new createjs.Container();
  this.stage.addChild(this.stoneContainer);

  // for each stone in our collection, render it.
  this.collection.each(function(item) {
    this.renderStone(item);
  }, this);
},
```

做两件事。首先，它在画布上呈现耙子形状(粉色矩形)，并在其上创建一个`click`
处理程序。其次，它遍历石头集合，并在每个项目上调用`renderStone()`。`click`处理者向收藏中添加一颗新宝石。

接下来，我们来看看`renderStone()`函数。

```
renderStone: function(model) {
  // var that = this;
  var baseView = this;

  // build the stone shape
  var stoneShape = buildStoneShape();

  // make it draggable
  // the second argument is a callback called on drop
  // we snap the target stone to the rake.
  buildDraggable(stoneShape, function(target, x, y) {
    rakeSnap(target, false);
  });

  // add the stone to the stage and update
  this.stoneContainer.addChild(stoneShape);
  this.stage.update();

  function buildStoneShape() {
    var shape = new createjs.Shape();

    shape.graphics.beginStroke("#000").beginFill("#ddd").drawRect(0, 0, baseView.rakeOffsets.stoneWidth, baseView.rakeOffsets.stoneHeight);
    return shape;
  };
},
```

我们调用了`buildDraggable()`函数来使石头可拖动。接下来我们将看到如何实现它。但是首先，让我们回顾一下我们的主干视图是如何工作的。`CanvasView`监听集合的`add`事件，当添加新石头时，它调用`renderStone()`。`render()`方法呈现耙子，并对集合中的每颗石头调用`renderStone()`。当耙子被点击时，一个新的石头模型被添加到石头集合中，然后在新的石头上调用`renderStone()`。

现在，让我们看看实现拖放功能的`buildDraggable()`函数:

```
renderStone: function(model) {
  // ...

  function buildDraggable(s, end) {
    // on mouse over, change the cursor to pointer
    s.on("mouseover", function(evt) {
      evt.target.cursor = "pointer";
    });

    // on mouse down
    s.on("mousedown", function(evt) {
      // move the stone to the top
      baseView.stoneContainer.setChildIndex(evt.target, baseView.stoneContainer.getNumChildren() - 1);

      // save the clicked position
      evt.target.ox = evt.target.x - evt.stageX;
      evt.target.oy = evt.target.y - evt.stageY;

      // update the stage
      baseView.stage.update();
    });

    // on mouse pressed moving (drag)
    s.on("pressmove", function(evt) {
      // set the x and y properties of the stone and update
      evt.target.x = evt.target.ox + evt.stageX;
      evt.target.y = evt.target.oy + evt.stageY;
      baseView.stage.update();
    });

    // on mouse released call the end callback if there is one.
    s.on("pressup", function(evt) {
      if (end) {
        end(evt.target, evt.stageX + evt.target.ox, evt.stageY + evt.target.oy);
      }
    });
  };
  // ...
},
```

对于将石头弹到耙子上的约束，这是我们需要的最终效用函数。

```
// drag the stone, either by animating or not
function dragStone(s, x, y, animate) {
  if (animate) {
    // Use tween js for animation.
    createjs.Tween.get(s).to({x: x, y: y}, 100, createjs.Ease.linear);
  } else {
    // set x and y attributes without animation
    s.x = x;
    s.y = y;
  }

  // update
  baseView.stage.update();
};

// calculate x position to snap the rake
function snapX(x) {
  if (x &lt; baseView.rakeOffsets.x) {
    x = baseView.rakeOffsets.x;
  } else if (x > baseView.rakeOffsets.x + baseView.rakeOffsets.width - baseView.rakeOffsets.stoneWidth) {
    x = baseView.rakeOffsets.x + baseView.rakeOffsets.width - baseView.rakeOffsets.stoneWidth;
  }

  return x;
};

// calculate y position to snap the rake
function snapY(y) {
  if (y &lt; baseView.rakeOffsets.y) {
    y = baseView.rakeOffsets.y;
  } else if (y > baseView.rakeOffsets.y + baseView.rakeOffsets.height - baseView.rakeOffsets.stoneHeight) {
    y = baseView.rakeOffsets.y + baseView.rakeOffsets.height - baseView.rakeOffsets.stoneHeight;
  }

  return y;
};

// drag stone within the rake bounds. animation is disabled if second argument is given. animation is enabled by default
function rakeSnap(s, animateDisabled) {
  dragStone(s, snapX(s.x), snapY(s.y), !animateDisabled);
};
```

## 结论

总之，Backbone 不局限于 DOM 操作，可以在任何需要模型-视图结构的地方使用。尽管它可以用来构建单页面应用程序，但它不是一个完整的框架，在本文中我们只看到了主干的一面。如果您喜欢将 Backbone 用于大规模应用程序，我建议使用 [Marionette.js](http://marionettejs.com) ，它可以处理一些与 Backbone 相关的原始问题。

本文的完整代码可以在 [GitHub](https://github.com/eguneys/drag-drop-sample) 上找到。在 [Heroku](http://drag-drop.herokuapp.com/) 上也有现场演示。要开始，只需点击粉红色区域创建一个可拖动的石头。石头将是可拖动的，它将被限制在粉红色区域内。

## 分享这篇文章