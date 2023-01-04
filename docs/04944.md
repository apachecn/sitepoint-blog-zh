# 用聚合物建造推箱子

> 原文：<https://www.sitepoint.com/building-sokoban-polymer/>

当我第一次听说聚合物时，我想起了我过去的银光时代。Silverlight 用 XHTML 做标记，用 C#做代码。Polymer 类似，但是 Polymer 使用 HTML 和 Javascript。关于聚合物的介绍，请看这篇优秀的[文章](https://www.sitepoint.com/introduction-to-web-components-and-polymer-tutorial/)。在本教程中，我们将构建经典的推箱子游戏，利用 Web 组件和一个优秀的约曼生成器，[生成器-聚合物](https://github.com/yeoman/generator-polymer)，并使用 Bower 发布它。

## 设置聚合物

建立一个聚合物项目就像下面两个命令一样简单:

```
$ npm install generator-polymer -g
$ yo polymer
```

它会要求您包含一些标准组件。既然我们不需要任何东西，你可以对所有东西说不。

这是生成的文件夹结构。所有自定义元素都在`app/elements`文件夹中。

```
.
|-- Gruntfile.js
|-- app
|   |-- elements
|   |   |-- elements.html
|   |   |-- soko-ban
|   |   |   |-- soko-ban.html
|   |   |   `-- soko-ban.scss
|   |-- index.html
|   |-- scripts
|   |   |-- app.js
|-- bower.json
`-- package.json
```

开始开发运行`grunt serve`。它将服务于`index.html`,并在文件改变时观察文件的实时重新加载。这是`index.html`，我只包括了使用聚合物的必要部分。

```
<html>
  <head>
    <script src="bower_components/platform/platform.js"></script>
    <!-- build:vulcanized elements/elements.vulcanized.html -->
    <link rel="import" href="elements/elements.html">
    <!-- endbuild -->
  </head>

  <body unresolved>
    <div class="game-container">
      <!-- insert your elements here -->
      <soko-ban></soko-ban>
    </div>

    <script src="scripts/app.js"></script>
  </body>
</html>
```

我们包含`platform.js`来启用聚合物，并导入`elements.html`来进一步导入我们所有的元素。注意，它被包装在一个`build:vulcanized`构建块中，这个构建块将我们所有导入的元素连接到一个文件中。最后，在`body`中，我们添加了自定义的聚合物元素。我已经包含了我们将要构建的最后一个元素，`sokoban-ban`，你可以用其他子元素替换它，在构建的时候测试它们。

## 自定义元素:`sprite-el`

我们将构建的第一个自定义元素是 sprite 元素，它将作为所有 sprite 的基础，比如盒子和我们的播放器。若要添加自定义元素，请运行一个命令。

```
$ yo polymer:el sprite-el
```

这将创建`elements/sprite-el`子文件夹并添加两个文件，`sprite-el.html`和`sprite-el.scss`。它还会在`elements.html`中注入`sprite-el.html`，基本上是为你做样板文件。

见`sprite-el.html`被约曼注入`elements.html`。

`File: elements/elements.html`

```
<link rel="import" href="sprite-el/sprite-el.html">
```

### 元素声明

让我们定义我们的定制元素`sprite-el`。

```
<link rel="import" href="../../bower_components/polymer/polymer.html">
<polymer-element name="sprite-el">
  <template>
    <link rel="stylesheet" href="sprite-el.css">
    <div class="sprite" style="top: {{posY}}px; left: {{posX}}px; height: {{frame.height}}px; width: {{frame.width}}px; background: url({{spriteUrl}}) {{frame.x}}px {{frame.y}}px">
    </div>
  </template>
  <script>
    (function () {
      'use strict';

      Polymer({
       publish: {
         spriteUrl: 'images/sprites.png',
         frame: {
           x: 0,
           y: 0
         },
         position: {
           x: 0,
           y: 0
         },

         computed: {
           posX: 'position.x * 64',
           posY: 'position.y * 64'
         }
       }
     });

    })();
  </script>
</polymer-element>
```

首先我们包含`polymer.html`，并打开一个`polymer-element`标签，带有`sprite-el` name 属性，这是必需的，必须包含一个`-`。接下来，我们有两个子标签，`template`和`script`。包含自定义元素的标记。在`script`中，我们调用`Polymer`函数来启动定制元素。更多信息参见[文档](http://www.polymer-project.org/docs/polymer/polymer.html#element-declaration)。

### 元素模板

在模板中，我们包含了由 Grunt 从`sprite-el.scss`编译而来的样式`sprite-el.css`。

接下来，我们有一个带有`sprite`类和`style`属性的`div`。`style`属性定义了`top`、`left`、`height`、`width`和`background`，样式决定了精灵及其图像的位置和边界。我们内联包含这些样式，因为我们必须对这些样式属性使用数据绑定。

### 数据绑定、发布和计算属性

元素上的属性，可以直接绑定到视图中，用[聚合表达式](http://www.polymer-project.org/docs/polymer/expressions.html)，像`{{posY}}`、`{{frame.height}}`、`{{spriteUrl}}`。

`posX`和`posY`在`computed`属性下定义，表示这些是[计算属性](http://www.polymer-project.org/docs/polymer/polymer.html#computed-properties)。它们是基于其他属性值计算的动态属性。在我们的例子中，它们依赖于`position.x`和`position.y`，所以每当`position`属性改变时，它们也会在视图中被重新计算和更新。

`spriteUrl`和`frame`是[发布的属性](http://www.polymer-project.org/docs/polymer/polymer.html#published-properties)。这意味着您正在使该属性成为元素的“公共 API”的一部分。因此，元素的用户可以更改它们。发布的属性也是数据绑定的，可以通过`{{}}`访问。

## 自定义元素:`box-el`

下一个定制元素是一个盒子元素，这将由我们的`sprite-el`组成，并将代表盒子、墙壁和地面。让我们再次打扰约曼。

```
$ yo polymer:el box-el
```

### 游戏艺术和精灵框架

所有的游戏艺术都来自 1001.com，并获得了 SA 4.0 的许可。你可以在 [GitHub](https://github.com/jsprodotcom/sokoban-polymer) 上找到所有的精灵和完整的源代码。

我们有五个精灵帧——`B`代表盒子，`BD`代表暗盒，`T`代表目标，`W`代表墙壁，`G`代表地面。在单独的层中定义移动的盒子和背景精灵实际上更好，但是为了简单起见，我们把它们都包含在一个元素中。每个帧定义了 sprite-sheet 中的帧位置以及它的高度和宽度。

让我们定义我们的定制元素`box-el`:

```
<polymer-element name="box-el">
  <template>
    <link rel="stylesheet" href="box-el.css">
    <sprite-el frame="{{frame}}" position="{{model.position}}" style="height: {{frame.height}}px; width: {{frame.width}}px;"></sprite-el>
  </template>
  <script>
    (function () {
      'use strict';

      Polymer({
       publish: {
         model: {
           position: {
             x: 0,
             y: 0
           },
           type: 'W'
         }
       },

       computed: {
         frame: 'boxCoords[model.type]'
       },

       ready: function() {
         this.boxCoords = {
           "B": { x:"-192", y:"0", width:"64", height:"64" },
           "BD": { x:"-128", y:"-256", width:"64", height:"64" },
           "T": { x:"-64", y:"-384", width:"32", height:"32" },
           "W": { x:"0", y:"-320", width:"64", height:"64" },
           "G": { x:"-64", y:"-256", width:"64", height:"64" }
         };
       }
      });

    })();
  </script>
</polymer-element>
```

### 继承和构成

盒子和播放器元素将使用基本的 sprite 元素。有两种方法可以做到这一点，使用继承或组合。我们不会扩展`sprite-el`，而是用构图。想了解更多关于继承的信息，请看这篇博文和这篇[参考文献](http://www.polymer-project.org/docs/polymer/polymer.html#extending-other-elements)。

我们在模板中包含了`sprite-el`,并赋予了它的属性。还记得公布的属性`frame`和`position`吗？这里我们通过属性来分配它们。

### 生命周期方法

除了发布和计算的属性之外，`box-el`还有一个额外的属性是`ready` [生命周期方法](http://www.polymer-project.org/docs/polymer/polymer.html#lifecyclemethods)。`ready`当元素完全准备好时，生命周期方法被调用，我们可以在这个回调中分配额外的属性，在我们的例子中是由`frame`计算属性使用的`boxCoords`。

## 自定义元素:`sokoban-el`

我们最后的定制元素是推箱子游戏本身。这将由我们的`player-el`、盒子、墙壁和地面元素组成。

### 游戏模型、游戏控制器和输入管理器

所有的游戏逻辑都在`GameController`类型里面。它生成游戏地图，并直接操纵游戏模型。游戏模型是数据绑定到我们的观点，这是聚合物元素。因此由`GameController`对模型所做的所有更改都会在视图中自动更新。我不会在本文中详细讨论游戏逻辑，您可以查看完整的源代码以了解更多细节。

处理用户输入可以使用[声明性事件映射](http://www.polymer-project.org/docs/polymer/polymer.html#declarative-event-mapping)来完成。但是，仍然有一些警告。看到这个[关于栈溢出](http://stackoverflow.com/questions/25938024/how-to-handle-input-with-polymer-without-blue-border-tab-focus)的问题。所以我使用了一个自定义类型来处理输入，`KeyboardInputManager`。

让我们定义我们的定制元素`soko-ban`:

```
<polymer-element name="soko-ban">
  <template>
    <link rel="stylesheet" href="soko-ban.css">
    <template repeat="{{box in boxes}}">
      <box-el model="{{box}}"></box-el>
    </template>
    <player-el model="{{player}}" id="character"></player-el>
  </template>
  <script>
    (function () {
      'use strict';

      Polymer({
       ready: function() {

         var controller = new GameController();
         var model = controller.getModel();

         /** Sample Model **/
         /**
         this.player = {
           position: {
             x: 0,
             y: 0
           }
         };

         this.boxes = [
           {
             type: 'W',
             position: {
               x: 10,
               y: 10
             }
           },
           {
             type: 'WD',
             position: {
               x: 10,
               y: 100
             }
           }
         ];
         */

         this.player = model.player;
         this.boxes = model.boxes;

         var inputManager = new KeyboardInputManager();
         var char = this.$.character;

         inputManager.on('move', function(val) {
           switch (val) {
             case KeyboardInputManager.Direction.UP:
               controller.move(GameController.Direction.UP);
               break;
             case KeyboardInputManager.Direction.RIGHT:
               controller.move(GameController.Direction.RIGHT);
               break;
             case KeyboardInputManager.Direction.DOWN:
               controller.move(GameController.Direction.DOWN);
               break;
             case KeyboardInputManager.Direction.LEFT:
               controller.move(GameController.Direction.LEFT);
               break;
           }

           if (controller.isGameOver()) {
             this.fire('finished', { target: model.target });
           }
         }.bind(this));
       }
     });

    })();
  </script>
</polymer-element>
```

注意我们的聚合物元素`player`和`boxes`的两个属性，我们将它们设置到我们的模型中。出于测试目的，您可以手动将它们设置为硬编码值，正如您在注释代码中看到的那样。

### 迭代模板

`boxes`属性是一个值数组。我们可以为数组中的每一项生成一个模板实例。注意使用了`template`标签和`repeat`属性来遍历盒子数组。更多信息参见[文档](http://www.polymer-project.org/docs/polymer/binding-types.html#iterative-templates)。

### 激发自定义事件

您还可以使用`fire`方法在您的聚合物元素中触发定制事件。在我们的例子中，当游戏结束时，我们触发一个`finished`事件。您可以监听事件，如下所示。

```
document.querySelector('soko-ban')
        .addEventListener('finished', function(e) {
          alert('Congratz you have pushed all ' +
          e.detail.target + ' boxes!');
});
```

## 发布它

我们使用`generator-polymer`来构建我们的应用程序。还有另一个生成器， [generator-element](https://github.com/webcomponents/generator-element) ，和一个 [Polymer 样板模板](https://github.com/webcomponents/polymer-boilerplate)，用于构建和发布定制元素。一旦使用生成器构建了定制元素，就可以使用 Bower 发布它。更多关于出版的信息，请看这些优秀的文章，[这里](http://www.polymer-project.org/articles/distributing-components-with-bower.html)和[这里](http://thejackalofjavascript.com/building-and-publishing-your-web-component/)。

别忘了给你的`bower.json`加上`web-component`标签。一旦您将它发布到 Bower，您的元素应该可以在 [Bower registry](http://bower.io/search) 上获得。还要确保提交给 [customelements.io](http://customelements.io/) 。

## 了解更多信息并进行现场演示

在本教程中，我们通过建造推箱子看到了聚合物的作用。通常，您不必构建自己的定制元素，您可以使用现有的元素，组合它们来构建更有吸引力的元素。访问位于 [customelements.io](http://customelements.io/) 的 web 组件库。

你可以用聚合物做更多我们没有提到的事情，比如设计元素，观察属性等等。欲了解更多信息，请访问 [API 开发者指南](http://www.polymer-project.org/docs/polymer/polymer.html)。你可以在 [GitHub](https://github.com/jsprodotcom/sokoban-polymer) 上找到该项目的完整源代码，并在[我的网站](http://eguneys.com/games/sokoban/index.html)上看到现场演示。

## 分享这篇文章