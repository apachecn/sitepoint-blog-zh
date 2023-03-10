# 从约曼，咕噜和鲍尔开始你的 AngularJS 发展

> 原文：<https://www.sitepoint.com/kickstart-your-angularjs-development-with-yeoman-grunt-and-bower/>

不管你喜欢它还是讨厌它，不可否认 AngularJS 是每个开发人员口中的框架。它可能不适合所有人，但 AngularJS 有一个古怪，高效和强大的功能集。再加上一些有用的开发工具，比如 Yeoman、Grunt 和 Bower，你就有了一个令人难以置信的快速原型制作过程。

## 我们将涵盖的内容

本 AngularJS 教程将涵盖:

*   用 Yeoman 生成一个简单的 AngularJS 应用程序
*   使用 Grunt 加速开发并帮助执行重复性任务
*   使用 Bower 添加第三方插件/框架
*   对 AngularJS 应用程序进行小改动

## 先决条件

为了充分利用本教程，我们建议您具备以下技能和资源:

*   一个终端和命令行的基本知识
*   [NodeJS](http://nodejs.org/) 和 NPM 安装完毕
*   基本的 JS，CSS 和 HTML 知识

## 文件

你可以在这里找到这个教程项目[的回购。](https://github.com/Haizyfox/sp-yeoman)

## 让我们开始吧！

好吧，我们开始吧。你需要做的第一件事是安装 Yeoman，Grunt 和 Bower。我们将使用节点包管理器一次完成这一切。在终端中，运行以下命令:

```
npm install -g yo grunt-cli bower
```

就这么简单，我们现在有了一套强大的工具。当我们使用它的时候，我会解释每一个。

### 约曼

Yeoman 是用来为你生成你的 app 的脚手架的。它将创建基本的文件夹、文件和配置，让您快速启动并运行。不仅如此，还有一些很棒的定制生成器可以用来创建特定类型的应用程序——我们将使用漂亮的 AngularJS 生成器。

Yeoman 最好的特性之一是能够使用定制的生成器。我们将安装 AngularJS 发生器来帮助我们尽快启动并运行 Angular。

运行以下程序安装 AngularJS 生成器:

```
npm install -g generator-angular
```

现在是时候生成一个闪亮的新 AngularJS 应用程序了。在新的项目目录中，运行:

```
yo angular <your_app_name></your_app_name>
```

生成器会问你几个问题。你可以回答是来包含 Twitter 的 bootstrap。也请回答“是”以包含 ngResource。其余的我们暂时不需要，所以回答“不”。

坐下来(几秒钟),看着发电机变魔术。Yeoman 将创建您的文件和文件夹，然后它将运行 bower install(稍后将详细介绍)和 npm install 来获取任何依赖项，最后它将执行任何强制配置。

## 盒子里有什么？

让我们看看约曼给了我们什么:

*   。鲍尔维奇
*   .editorconfig
*   。gitattributes
*   。吉蒂尔
*   。jshintrc
*   Gruntfile.js
*   **app/**
*   组件. json
*   karma-e2e.conf.js
*   karma.conf.js
*   **节点 _ 模块/**
*   package.json
*   **测试/**

让我们回顾一下这里需要注意的一些更重要的事情:

**app/目录**
app 目录包含你的静态 app。它里面有你的 html，css 和 javascript，这是你花大部分时间开发的地方。
**package . JSON**
package . JSON 文件帮助 npm 识别我们的项目以及管理它的所有依赖项。它还可以包含与您的项目相关的各种其他元数据。
**node_modules**
这个是不言自明的。这是您的项目所依赖的所有节点模块的存储位置。
**grunt file . js**
grunt file 是一个 javascript 文件，负责配置您的项目以及您的项目所需的任何任务或插件。例如，您的 gruntfile 可能指定您的项目使用 uglify，并且您希望它在构建时在特定目录上运行 Uglify。一会儿再谈咕噜声。
**component . JSON**
component . JSON 文件用于通知 Bower 包管理器您的项目依赖关系以及其他元数据。在 Bower 的最新版本中，这个文件被称为 Bower . JSON——稍后会详细介绍。
**。鲍尔维奇**T22。bowerrc 文件用于将常规配置选项传递给 bower。
**Karma 文件**
Karma 是一个测试框架。我们将使用它来运行我们的 Angular 应用程序的一些测试。

唷！这是一个很大的进步——一旦你对约曼发电机的输出更加熟悉，你将会爱上它为你处理这一切的事实！

在开始实际开发之前，让我们为我们的项目添加一些东西。

## bower——网络包管理器

在使用 Bower 之前，我们需要自己做一点配置。Bower 最近将他们的命名约定从`component.json`文件更改为`bower.json`文件，所以我们需要使我们的代码库与此一致。

我们需要做的第一件事是对我们在`.bowerrc`中的 Bower 配置做一个小小的修改，打开它并添加下面一行:

```
 {
      "directory": "app/components",
      "json": "bower.json" // Add this line
  }
```

它告诉 Bower 使用一个包的`bower.json`文件来获得如何安装这个包的指令。

由于我们使用 bower 作为我们自己项目的依赖项，我们还需要将项目根目录中的`component.json`文件重命名为`bower.json`。使用这种尖端技术时的一个小问题:)

鲍尔是一名包装经理。它将帮助我们快速找到并安装我们喜爱的 CSS 框架、javascript 库和插件，只需几个简单的命令。

好吧，让我们给鲍尔一个旋转。Yeoman 之前好心用 bower 给我们安装了 Bootstrap，但那只是 Bootstrap CSS。我们还想要所有漂亮的 Javascript 小部件。

由于我们正在构建 AngularJS 应用程序，我们将需要与 Angular 一起工作的引导 javascript。

幸运的是， [Angular UI](http://angular-ui.github.io/bootstrap/) 的团队已经将所有的引导 Javascript 移植到 Angular 中！。让我们使用 Bower 来安装他们的库。

```
bower install angular-bootstrap --save
```

–save 标志告诉 bower 将它作为一个依赖项添加到我们的 bower.json 文件中

太棒了。那很容易，不是吗？现在，导航到您的应用程序/目录，让我们看看我们必须处理什么。

## 我们的静态应用

看一下应用程序/目录的内容。

*   favicon.ico
*   index.html
*   robots.txt
*   **组件/**
*   **脚本/**
*   **样式/**
*   **浏览量/**

**index.html**
这应该是你们大多数人都熟悉的，这是你的 app 的核心 html 页面。
**组件/目录**
组件目录类似于 node_modules 目录，除了 Bower。你用 Bower 安装的所有软件包都将保存在这里。例如，安古拉瑞自举会在那里。
**脚本/目录**
再来，熟悉的领域最多。这是您的应用程序 javascript 存储的地方。注意，像 AngularJS 这样的库将存在于 components 目录中，所以 scripts/ is 是为您编写的文件准备的！
**样式/目录**
你所有的 css/sass 让你的应用看起来更漂亮。
**视图**
这个漂亮的文件夹是你的角度模板将要存放的地方。

接下来，我们将仔细查看 AngularJS 文件

## 安古斯

约曼角度发生器给了我们最基本的东西:一个模块，一个控制器和一个视图。让我们来看看其中的每一项:

**模块:/app/scripts/app.js**

```
'use strict';
  // Here we set up an angular module. We'll attach controllers and 
  // other components to this module.
  angular.module('testApp', [])
    // Angular supports chaining, so here we chain the config function onto
    // the module we're configuring.
    .config(function ($routeProvider) {

      // We use AngularJS dependency injection to fetch the route provider.
      // The route provider is used to setup our app's routes. 

      // The config below simply says when you visit '/' it'll render
      // the views/main.html template controlled by the MainCtrl controller.

      // The otherwise method specifies what the app should do if it doesn't recognise 
      // the route entered by a user. In this case, redirect to home.
      $routeProvider
        .when('/', {
          templateUrl: 'views/main.html',
          controller: 'MainCtrl'
        })
        .otherwise({
          redirectTo: '/'
        });
    });
```

**控制器:/app/scripts/controllers/main . js**

```
'use strict';

  // Here we attach this controller to our testApp module
  angular.module('testApp')

    // The controller function let's us give our controller a name: MainCtrl
    // We'll then pass an anonymous function to serve as the controller itself.
    .controller('MainCtrl', function ($scope) {

      // Using AngularJS dependency injection, we've injected the $scope variable
      // Anything we attach to scope will be available to us in the view.

      // In this case, we're attaching a collection of Awesome Things to display
      // in app/views/main.html
      $scope.awesomeThings = [
        'HTML5 Boilerplate',
        'AngularJS',
        'Karma'
      ];
    });
```

**视图:app/views/main.html**

```
<div class="hero-unit">
    <h1>'Allo, 'Allo!</h1>
    <p>You now have</p>
    <ul>

        <!-- Here we use the AngularJS directive: ng-repeat to loop through our awesomeThings 
        and print them out as list items using the {{}} bindings -->
        <li ng-repeat="thing in awesomeThings">{{thing}}</li>
    </ul>
    <p>installed.</p>
    <h3>Enjoy coding! - Yeoman</h3>
  </div>
[/js]

<strong>The Index File: app/index.html</strong>
[html]  <!doctype html>
  <html>
    <head>...</head>
    <!-- The ng-app directive tells angular which module we'll use
    for our app. In this case the one defined in scripts/app.js -->
    <body ng-app="testApp">
      ...
      <!-- The ng-view directive specifies that our templates
      (such as views/main.html) will be loaded into this div. -->
      <div class="container" ng-view></div>

      <!-- Here we load AngularJS and the AngularJS resource component -->
      <script src="components/angular/angular.js"></script>
      <script src="components/angular-resource/angular-resource.js"></script>

      <!-- Here we include our own angular scripts -->
      <!-- build:js scripts/scripts.js -->
      <script src="scripts/app.js"></script>
      <script src="scripts/controllers/main.js"></script>
      <!-- endbuild -->

      ...
    </body>
  </html>
```

## 让我们看看它的实际效果吧！

我们已经准备好第一次查看我们的应用程序。导航回应用程序的根目录，然后运行:

```
grunt server
```

Grunt 是一个强大的、功能丰富的 Javascript 任务运行程序。简而言之，它可以让你自动化重复的任务，比如编译 coffeescript，缩小 css，代码验证等等。我们将使用它来完成所有这些工作，并为开发和部署准备我们的代码。

Grunt 将快速浏览我们的项目文件夹，并为我们准备一切，例如将我们包含的引导程序 SASS 编译成 css。

几秒钟后，会弹出一个浏览器窗口，显示你的应用程序正在运行，看起来很棒。

为了确保万无一失，请查看页面的源代码，并查看其中包含的`main.css`文件。它应该充满了引导代码——多亏了 Bower 和 Grunt 的魔力。

## 让我们改变它

是我们尝试做出一些改变的时候了。由于这是有角度的，我们将从一些角度测试开始。

Yeoman 为我们的控制器生成了一个示例测试，所以让我们从这里开始。

我们将向我们的精彩事物列表中添加另一个事物，让我们改变我们的测试，期待 4 个精彩事物，而不是 3 个:

**test/spec/controllers/main . js**

```
'use strict';

    describe('Controller: MainCtrl', function () {

      // load the controller's module
      beforeEach(module('testApp'));

      var MainCtrl,
        scope;

      // Initialize the controller and a mock scope
      beforeEach(inject(function ($controller, $rootScope) {
        scope = $rootScope.$new();
        MainCtrl = $controller('MainCtrl', {
          $scope: scope

        });
      }));

      it('should attach a list of awesomeThings to the scope', function () {
        // Change this line
        expect(scope.awesomeThings.length).toBe(3);

        // To this
        expect(scope.awesomeThings.length).toBe(4);
      });
    });
```

现在我们可以使用 Grunt 的另一个伟大特性:

```
grunt test
```

这将运行我们的因果报应测试。他们应该会失败，因为测试需要 4 个人，而我们只有 3 个人。让我们去解决这个问题，让我们的测试通过。

打开`app/scripts/controllers/main.js`并在列表中添加另一个很棒的东西:

**/app/脚本/控制器/main.js**

```
.controller('MainCtrl', function ($scope) {
      $scope.awesomeThings = [
        'HTML5 Boilerplate',
        'AngularJS',
        'Karma',
        'SitePoint'
      ];
    });
```

保存文件并再次运行测试:

```
grunt test
```

这一次他们应该会通过。现在你可以在你的浏览器(grunt 服务器)中启动这个应用程序，你会注意到有一个额外的项目符号。整洁吧？

## 使用我们的凉亭套餐

让我们使用之前包含的 AngularUI 引导库，将我们的令人惊叹的列表变成令人惊叹的下拉列表。

*重要信息*由于 Bower 只是一个包管理器，它不负责将我们的文件添加到我们的`index.html`文件中。我们需要自己去做。

所以打开`app/index.html`并添加下面一行:

```
<script src="components/angular-bootstrap/ui-bootstrap.js"></script>
```

然后，根据 AngularUI Bootstrap 网站上的入门文档，我们需要将他们的模块作为依赖项添加到我们自己的 Angular 模块中

打开`app/scripts/app.js`并添加`ui.bootstrap`模块作为依赖:

**/app/scripts/app.js**

```
'use strict';

  angular.module('testApp', ['ui.bootstrap'])
  ...
```

好了，可以用了。现在，我们需要对我们的视图进行一些更改:

**视图:app/views/main.html**

```
<ul>
    <li class="dropdown">
      <a class="dropdown-toggle">
        Click me to see some awesome things!
      </a>
      <ul class="dropdown-menu">
        <li ng-repeat="thing in awesomeThings">
          <a>{{thing}}</a>
        </li>
      </ul>
    </li>
  </ul>
```

我们使用了一些引导 css 类，并移动了 ng-repeat 来创建菜单项，而不仅仅是一个普通的旧列表。

AngularUI Bootstrap 指令作用于类，所以只需简单地将 dropdown-toggle 类添加到我们的标签中，我们就有了一个全功能的 dropdown！

我们需要将引导 UI 模块添加到我们的测试中，否则测试将会失败，因此需要进行以下更改:

**test/spec/controllers/main . js**

```
'use strict';

    describe('Controller: MainCtrl', function () {

      // load the controller's module
      beforeEach(module('testApp'));
      // load the BootstrapUI module
      beforeEach(module('ui.bootstrap')); // Add this line
     ...
    });
```

**/karma.conf.js**

```
// Karma configuration

  // base path, that will be used to resolve files and exclude
  basePath = '';

  // list of files / patterns to load in the browser
  files = [
    JASMINE,
    JASMINE_ADAPTER,
    'app/components/angular/angular.js',
    'app/components/angular-mocks/angular-mocks.js',
    'app/components/angular-bootstrap/ui-bootstrap.js', // Add this line
    'app/scripts/*.js',
    ...
```

运行 **grunt 测试**以确保一切通过

现在，你可以在浏览器(`grunt server`)中再次打开你的应用程序，看看你方便的工作。

## 结论

好了，你知道了！一个简单的 Angular 应用程序，一个第三方 JS 库，一些测试，缩小和一大堆其他的好东西。

我们只是触及了 Yeoman 和它的伙伴们可能做到的事情的表面——但我希望这能激励你下次有好主意时快速制作一个 AngularJS 应用程序！

敬请期待更多关于 AngularJS 最佳实践的 AnuglarJS 教程和文章即将发布！

对本文的评论已经关闭。对 AngularJS 有疑问？为什么不在我们的[论坛](https://www.sitepoint.com/forums/forumdisplay.php?15-JavaScript-amp-jQuery?utm_source=sitepoint&utm_medium=link&utm_campaign=forumlink)上问呢？

## 分享这篇文章