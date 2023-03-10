# 使用 Angular UI 路由器在 AngularJS 中创建有状态模式

> 原文：<https://www.sitepoint.com/creating-stateful-modals-angularjs-angular-ui-router/>

在 AngularJS 应用中实现模态有很多方法，包括流行的 [angular-dialog-service](https://github.com/m-e-conroy/angular-dialog-service) 和官方的 [Angular-UI 引导模态](http://angular-ui.github.io/bootstrap/#/modal)。在这篇文章中，我将分享我如何在 Angular 中处理模态，使用另一个 Angular-UI 服务， [ui-router](https://github.com/angular-ui/ui-router) 。

## 在状态中思考

这种方法背后的核心思想是，模态实际上是应用程序的唯一状态。考虑一个电子商务网站。当你点击“添加到购物车”按钮时，会弹出一个对话框要求你登录。您输入您的详细信息，然后单击“继续”，这将向您显示另一个包含您的购物车详细信息的模式。

你刚刚经历了许多状态，只是碰巧都是情态动词。如果我们认为模态是状态，那么使用状态管理工具在不同的模态状态之间来回切换是有意义的。

## UI 路由器入门

让我们从简单开始。首先，我们将安装 ui 路由器，并将其添加到我们的 Angular 应用程序中。

### 1.创建一个简单的 HTML 页面

我们将从创建一个包含以下内容的`index.html`文件开始:

```
<!doctype html>
<html ng-app="modalApp">
  <head>
    <title>Modals with Angular and Ui-Router</title>
    <link rel="stylesheet" href="css/app.css" />
    <script type="text/javascript" src="js/jquery.min.js"></script>
    <script type="text/javascript" src="js/angular.min.js"></script>
    <script type="text/javascript" src="js/angular-ui-router.min.js"></script>
    <script type="text/javascript" src="js/app.js"></script>
  </head>
  <body>
  </body>
</html>
```

jQuery 已经包含在以后的一些 DOM 工作中。

除了包括 Angular 本身的最新版本，我还包括了 Angular UI Router，一个 CSS 文件(目前为空)，当然还有我们应用程序的主 JavaScript 文件。接下来让我们来看看。

### 2.创建您的角度应用程序

在这一点上,`app.js`文件非常简单。我们只是为我们的`modalApp`创建一个模块，然后添加`ui.router`作为依赖项:

```
angular.module("modalApp", ["ui.router"])
```

### 3.充实界面

在我们打开一个模态之前，我们需要一个用户界面来与用户交互。在这个例子中，我用`index.html`中的“添加到购物车按钮”来简化事情:

```
<!doctype html>
<html ng-app="modalApp">
  <head>
    <title>Modals with Angular and Ui-Router</title>
    <link rel="stylesheet" href="css/app.css" />
    <script type="text/javascript" src="js/jquery.min.js"></script>
    <script type="text/javascript" src="js/angular.min.js"></script>
    <script type="text/javascript" src="js/angular-ui-router.min.js"></script>
    <script type="text/javascript" src="js/app.js"></script>
  </head>
  <body>
    <h3>Pusheen Hoodie</h3>
    <p>Now you too can look like Pusheen!</p>
    <button>Add to cart</button>
  </body>
</html>
```

### 4.配置初始状态

我们将为每个模态定义一些状态，但是我们首先需要做一些设置。因为我们很可能想要在不同的模态之间共享行为，所以让我们创建一个父`Modal`状态，然后我们的单个模态可以继承它。以下代码属于`js/app.js`:

```
angular.module("modalApp", ["ui.router"]).config(function($stateProvider) {
  $stateProvider.state("Modal", {
    views:{
      "modal": {
        templateUrl: "modal.html"
      }
    },
    abstract: true
  });
});
```

我们已经定义了一种叫做“模态”的状态。这是一个[抽象](https://github.com/angular-ui/ui-router/wiki/Nested-States-%26-Nested-Views#abstract-states)状态，意味着它不能被直接转换。它只作为其子状态的父状态来提供共享功能。我们还为名为视图(也称为模态视图)的[定义了一个模板。命名视图是一个好主意，可以避免错误的状态加载到应用程序中的错误位置，特别是如果您在应用程序中使用 ui-router 来处理其他状态。](https://github.com/angular-ui/ui-router/wiki/Multiple-Named-Views)

### 5.定义 ui-view 指令，以便我们的模型加载到

ui 路由器使用一个名为`ui-view`的指令来决定一个状态的模板应该被加载到哪里。我们将在我们的`index.html`页面上包含一个`ui-view`指令:

```
<!doctype html>
<html ng-app="modalApp">
  <head>
    <title>Modals with Angular and Ui-Router</title>
    <link rel="stylesheet" href="css/app.css" />
    <script type="text/javascript" src="js/jquery.min.js"></script>
    <script type="text/javascript" src="js/angular.min.js"></script>
    <script type="text/javascript" src="js/angular-ui-router.min.js"></script>
    <script type="text/javascript" src="js/app.js"></script>
  </head>
  <body>
    <h3>Pusheen Hoodie</h3>
    <p>Now you too can look like Pusheen!</p>
    <button>Add to cart</button>

    <div ui-view="modal" autoscroll="false"></div>
  </body>
</html>
```

因为我们将视图命名为“modal ”,所以我们也需要将它传递给指令。`autoscroll="false"`设置防止 ui 路由器试图将模式滚动到视图中。

### 6.创建我们的第一个实际模型

让我们开始定义我们的第一个实际模型。我们将创建一个弹出窗口，询问用户是否确定要将产品添加到购物车中。

```
angular.module("modalApp", ["ui.router"]).config(function($stateProvider) {
  $stateProvider.state("Modal", {
    views:{
      "modal": {
        templateUrl: "modal.html"
      }
    },
    abstract: true
  });

  $stateProvider.state("Modal.confirmAddToCart", {
    views:{
      "modal": {
        templateUrl: "modals/confirm.html"
      }
    }
  });
});
```

就这么简单。我们使用路由器的[点符号](https://github.com/angular-ui/ui-router/wiki/Nested-States-%26-Nested-Views#methods-for-nesting-states)将新的`confirmAddToCart`状态定义为`Modal`的子状态。我们还为新模型指定了一个模板。

因为`confirmAddToCart`模态是`Modal`状态的孩子，ui 路由器将在`Modal`状态的模板(`modal.html`)中寻找`ui-view`指令，以便呈现确认模板。接下来让我们创建这两个模板。

### 7.创建基本模式状态的模板

状态的模板负责创建一个透明的背景来覆盖应用程序，以及一个各种各样的容器，在那里我们将加载我们不同的子状态的模板。

```
<div class="Modal-backdrop"></div>
<div class="Modal-holder" ui-view="modal" autoscroll="false"></div>
```

这里有一些属于`css/app.css`的基本风格:

```
*
{
  box-sizing: border-box;
}

.Modal-backdrop
{
 position: fixed;
 top: 0px;
 left: 0px;
 height:100%;
 width:100%;
 background: #000;
 z-index: 1;
 opacity: 0.5;
}

.Modal-holder
{
  position: fixed;
  top: 0px;
  left: 0px;
  height:100%;
  width:100%;
  background: transparent;
  z-index: 2;
  padding: 30px 15px;
}
```

### 8.创建确认模式的模板

这个模板是为包含确认消息的实际模式框准备的。下面的代码放在`modals/confirm.html`中:

```
<div class="Modal-box">
  Are you sure you want to do that?
  <button>Yes</button>
</div>
```

`Modal-box`的造型:

```
.Modal-box
{
  margin: 0 auto;
  width: 100%;
  background: #fff;
  padding: 15px;
  border-radius: 4px;
  box-shadow: 1px 2px 5px rgba(0,0,0,0.3);
  position: relative;
}

@media screen and (min-width: 992px)
{
  .Modal-box
  {
    width: 50%;
    padding: 30px;
  }
}
```

### 9.全部装上电线

现在我们有了一个带有子状态的父模态状态，以及它们的两个模板，剩下要做的就是实际触发模态。ui-router 为我们提供了一个名为`ui-sref`的指令，它的行为类似于锚标签的`href`属性。它将把我们与我们提供的州名联系起来。

```
<!doctype html>
<html ng-app="modalApp">
  <head>
    <title>Modals with Angular and Ui-Router</title>
    <link rel="stylesheet" href="css/app.css" />
    <script type="text/javascript" src="js/jquery.min.js"></script>
    <script type="text/javascript" src="js/angular.min.js"></script>
    <script type="text/javascript" src="js/angular-ui-router.min.js"></script>
    <script type="text/javascript" src="js/app.js"></script>
  </head>
  <body>
    <h3>Pusheen Hoodie</h3>
    <p>Now you too can look like Pusheen!</p>
    <button ui-sref="Modal.confirmAddToCart">Add to cart</button>

    <div ui-view="modal" autoscroll="false"></div>
  </body>
</html>
```

现在，当我们单击该按钮时，路由器会将我们导航到`Modal.confirmAddToCart`状态。`Modal`状态的模板将首先加载到`index.html`中的`ui-view`指令中。这将显示我们的背景，并准备持有人。然后，白色的确认对话框将加载到父模态模板中的`ui-view`指令中，嘿，很快！我们有一个模型！

### 10.关闭模式

你可能注意到的第一件事是，你不能关闭模态。让我们解决这个问题。

AngularUI 路由器为我们提供了进入和离开状态时触发的`onEnter`和`onExit`回调。我们将使用`onEnter`回调来设置一些关闭模态的事件监听器。但这回避了一个问题:我们实际上如何关闭它？关闭基于状态的模式只是转换到不同的状态。无论这是模式起源的状态还是一些默认的无操作状态，都取决于您。现在，让我们创建一个空的默认状态，并使用它来导航离开我们的模态状态。

```
angular.module("modalApp", ["ui.router"]).config(function($stateProvider) {
  $stateProvider.state("Default", {});

  $stateProvider.state("Modal", {
    views:{
      "modal": {
        templateUrl: "modal.html"
      }
    },
    onEnter: ["$state", function($state) {
      $(document).on("keyup", function(e) {
        if(e.keyCode == 27) {
          $(document).off("keyup");
          $state.go("Default");
        }
      });

      $(document).on("click", ".Modal-backdrop, .Modal-holder", function() {
        $state.go("Default");
      });

      $(document).on("click", ".Modal-box, .Modal-box *", function(e) {
        e.stopPropagation();
      });
    }],
    abstract: true
  });

  $stateProvider.state("Modal.confirmAddToCart", {
    views: {
      "modal": {
        templateUrl: "modals/confirm.html"
      }
    }
  });
});
```

我们设置的事件相当琐碎。我们只需要倾听`esc`键，以及背景上的任何点击声。此外，还添加了一个`e.stopPropagation()`调用，以防止模式内的点击冒泡到背景并无意中关闭模式。

去测试一下吧。

### 11.过渡到另一种模式

现在我们可以打开和关闭模态，我们可以开始看到这种方法的真正优势。用一个状态来表示每一个模态不仅是清晰而有意义的，而且它允许我们在这些模态中遍历状态。

我们开始要求用户确认他们的购买。现在，让我们用另一种模式向他们展示成功消息。同样，我们将只定义一个新的状态及其关联的模板:

```
angular.module("modalApp", ["ui.router"]).config(function($stateProvider) {
  $stateProvider.state("Default", {});

  $stateProvider.state("Modal", {
    ...
  }

  $stateProvider.state("Modal.confirmAddToCart", {
    views:{
      "modal": {
        templateUrl: "modals/confirm.html"
      }
    }
  });

  $stateProvider.state("Modal.successfullyAdded", {
    views:{
      "modal": {
        templateUrl: "modals/success.html"
      }
    }
  });
});
```

```
<div class="Modal-box">
  Added! Yay, now you too can look like Pusheen :)
  <button ui-sref="Default">Awesome!</button>
</div>
```

现在，剩下要做的就是将确认模式中的“Yes”按钮链接到新的成功状态。在那里，我们可以添加一个“No”按钮，链接到我们的默认状态来关闭模态。

```
<div class="Modal-box">
  Are you sure you want to do that?
  <button ui-sref="Modal.successfullyAdded">Yes</button>
  <button ui-sref="Default">No</button>
</div>
```

现在你知道了！基于状态的可导航模式。我发现，将模态视为状态是确保用户在应用程序中使用模态时拥有流畅体验的可靠方法。

因为模态只是状态，所以您可以获得 Angular UI 路由器的所有其他附加好处，包括:

*   嵌套状态——您可以在模态中呈现其他状态
*   回调——当你的模式打开或关闭时触发事件，通知你的应用程序的其他部分
*   状态参数和依赖注入——通过状态参数将数据传递给模型，或者将数据服务依赖注入到状态控制器中
*   URL——州是 URI 可路由的，这意味着你可以有一个链接到模态的 URL，非常漂亮

本文中的所有代码都可以在 GitHub 上找到。我很想听到你对这种方法的反馈，所以请留下你的评论:)

## 分享这篇文章