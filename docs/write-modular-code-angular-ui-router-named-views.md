# 如何用角度 UI 路由器和命名视图编写模块化代码

> 原文：<https://www.sitepoint.com/write-modular-code-angular-ui-router-named-views/>

web 开发中最重要的概念之一是编写干净的模块化代码。当作为团队的一员处理高度复杂的应用程序时，这一点尤其重要。Angular 框架是为创建高级应用程序而构建的，这些应用程序可以很快变得非常复杂，这反过来使得编写模块化代码变得更加重要。在这个过程中，有一个工具可以给你很大的帮助，那就是 [Angular UI Router](http://angular-ui.github.io/ui-router/site/#/api/ui.router) ，它是用来帮助管理你的 web 应用程序的不同状态的。与本机 AngularJS 路由实现相比，它让您可以完全控制它的每个视图。

如果您以前使用过 ui-router，您可能知道如何使用点符号来定义父状态中的子状态。但是，您可能不知道 ui-router 库如何处理嵌套在父状态中的命名视图。这就是我今天要解释的。

我将向您展示 ui-router 如何使用绝对名称来让您完全控制 web 应用程序特定部分的显示位置。这允许你容易地添加和删除界面的不同部分，以创建一个由不同组件组成的模块化应用程序。具体来说，我将向您展示如何将导航栏、一些正文内容和页脚映射到特定的状态。和以往一样，[这篇教程的代码可以在 GitHub](https://github.com/sitepoint-editors/sp-ui-router) 上找到，你也可以在文章的最后找到一个[演示。](#demo)

## 入门指南

花点时间浏览一下组成这个演示的文件(可以从上面的 GitHub 链接获得)。你可以看到我们有一个包含 AngularJS 的`index.html`文件，以及 ui 路由器的库。在这个文件中，我们还有两个`ui-view`指令，我们将在其中插入我们的内容。接下来我们有一个`app.js`文件，它将包含我们 Angular 应用程序的代码，以及一个`templates`目录。这个目录将用来存放我们所有不同的观点。虽然这个文件夹不是必需的，但在用 Angular 构建应用程序时，使用某种结构肯定对您最有利。如你所见，我在`templates`文件夹中包含了一个`assets`文件夹。这是我喜欢存放可重用组件的地方:页眉、导航栏、页脚等等。我想你可能会发现这个约定很有帮助，因为它让你的代码非常模块化。

## UI 路由器

### 向 ui 路由器添加依赖关系

让我们开始配置我们的应用程序。在我们的`app.js`文件中，我们需要在 ui 路由器上添加一个对主 angular 模块的依赖。

```
angular.module('app', ['ui.router']) 
```

### 配置我们的路线

一旦完成，我们就可以继续应用程序的`config`对象。在这里，我们将处理 [$stateProvider](http://angular-ui.github.io/ui-router/site/#/api/ui.router.state.%24stateProvider) 和 [$urlRouterProvider](http://angular-ui.github.io/ui-router/site/#/api/ui.router.router.%24urlRouterProvider) ，因此我们需要将它们注入到我们的配置对象中，以便使它们可用。

接下来，我们希望将我们家所在州的 URL 传递到`$urlRouterProvider.otherwise()`中，因此它默认将我们的应用程序映射到这个州。然后我们将需要使用`$stateProvider`，这是我们将在本教程的剩余部分处理的。`$stateProvider`是 ui-router 提供给开发者在路由应用程序时使用的工具。就整个 UI 和导航而言，状态对应于应用程序中的一个“位置”。状态描述了用户界面的样子以及它在那个地方做什么。它的工作原理和`ngRoute`使用`routeProvider`一样。

下面是此时`app.js`文件的样子。一旦我们配置了`urlRouterProvider`，我们就利用`$stateProvider`来定义应用程序的不同状态。在本例中，我们定义了一个名为 home 的州，并且只配置了 URL。

```
angular.module('app', ['ui.router'])
  .config(['$stateProvider', '$urlRouterProvider', 
    function($stateProvider, $urlRouterProvider) {
      $urlRouterProvider.otherwise('/');
      $stateProvider
      .state('home', {
        url: '/'
      });
    }
  ]);
```

### 视图对象

既然已经建立了基本框架，就需要在`$stateProvider`中定义一个`views`对象。它应该紧接在国家的 URL 之后。在这个对象内部，我们将定义视图的名称，以及它们的模板路径。在这里你也可以定义控制器之类的东西；然而，为了简洁起见，我在本教程中忽略了这一点。

接下来，我们必须首先创建并定义一个未命名的视图，该视图将指向父状态—在本例中是 home。这个未命名视图的`templateUrl`本质上将两者联系在一起。这就是所谓的相对命名，它告诉 angular 将这个未命名的视图插入到我们的`index.html`文件的`<div ui-view>`中。您的代码现在应该复制下面的`app.js`。

```
angular.module('app', ['ui.router'])
  .config(['$stateProvider', '$urlRouterProvider', 
    function($stateProvider, $urlRouterProvider) {
      $urlRouterProvider.otherwise('/');
      $stateProvider
        .state('home', {
          url: '/',
          views: {
            '': { 
              templateUrl: './templates/main.html' 
            },
          }
        });
     }
   ]);
```

如您所见，未命名的视图解析为`main.html`，应该类似于下面的代码。

```
<div ui-view="nav"></div>
<h1 class="text-center">This content is in main.html</h1>
<div ui-view="body"></div>
<div ui-view="footer"></div>
```

文件`main.html`包括三个命名视图——导航、正文和页脚。为了让这些组件出现在 home 状态下，我们必须使用绝对命名来定义它们。具体来说，我们必须使用`@`语法来告诉 AngularJS 我们的应用程序的这些组件应该被映射到一个特定的状态。这遵循了`viewName@stateName`语法，并告诉我们的应用程序利用来自*绝对*或特定状态的命名视图。你可以[在这里](https://github.com/angular-ui/ui-router/wiki/Multiple-Named-Views#view-names---relative-vs-absolute-names)阅读更多关于相对名和绝对名的信息。

您将看到在我们的配置对象中使用了`@home`,以确保 Angular 知道我们的命名视图以我们的主状态为目标。如果这些绝对名称不存在，应用程序将不知道在哪里可以找到这些命名视图。也就是说，看看下面，看看应用程序应该如何路由。

```
angular.module('app', ['ui.router'])
  .config(['$stateProvider', '$urlRouterProvider', 
    function($stateProvider, $urlRouterProvider) {
      $urlRouterProvider.otherwise('/');
      $stateProvider
        .state('home', {
          url: '/',
          views: {
            '': { templateUrl: './templates/main.html'},
            'nav@home': { templateUrl: './templates/assets/nav.html' },
            'body@home': { templateUrl: './templates/body.html'},
            'footer@home': { templateUrl: './templates/assets/footer.html' }
         }
      });
    }
  ]);
```

这就是我们最终得到的结果:

参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )的 Pen [如何用 Angular UI-Router &命名视图](http://codepen.io/SitePoint/pen/JYdXVa/)编写模块化代码。