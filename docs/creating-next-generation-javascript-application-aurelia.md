# 用 Aurelia 创建下一代 JavaScript 应用程序

> 原文：<https://www.sitepoint.com/creating-next-generation-javascript-application-aurelia/>

2015 年带来了 ECMAScript 6 规范的最终确定，并带来了用 JavaScript 构建现代、卓越应用程序的信心。

JavaScript 框架的当前格局由公认的巨头 AngularJS 和 React 主导，这两家公司都以某种方式或形式将 ES6 的新特性纳入其范例。

然而，还有另一个播放器，虽然是新的和相对隐秘的，但在使用现代 JavaScript 特性方面看起来很优雅。我想花一点时间向你们介绍奥雷利亚。

## 奥雷利-谁？

Aurelia 是下一代框架，它利用 ES6、Web 组件和模块化等现代概念来帮助您开发高性能、面向未来的应用程序。

Aurelia 是由[罗布·艾森伯格](https://twitter.com/EisenbergEffect)打造的 AngularJS 竞争对手[杜兰达尔](http://durandaljs.com)的自然发展。奥雷利亚的历史包括多年来与安古拉杰团队的[遭遇](http://angularjs.blogspot.com.au/2014/04/angular-and-durandal-converge.html)中的[号](http://eisenbergeffect.bluespire.com/leaving-angular/)。正是因为这个原因，你们当中的 AngularJS 开发人员可能会对这个框架的许多方面感到熟悉。

## 新技术

正如我所说，Aurelia 是一个“下一代”框架，因此它使用的工具可能对你们中的一些人来说是新的。它运行在 [Node.js](https://nodejs.org/) 上，使用 [npm](https://www.npmjs.com) ，但它依赖于一些很酷的新技术，我们将在下面简要介绍一下:

### [一饮而尽](http://gulpjs.com/)

这一个不是很新，但它是奥雷利亚设置的核心部分。我们将使用 Gulp 通过各种任务来传输所有文件，以确保我们的应用程序已经准备就绪。

### [ES6 模块装载器多孔填料](https://github.com/ModuleLoader/es6-module-loader)

ES6 模块加载器是最初 ES6 规范的一部分的动态模块加载器的 pollyfill。`System`加载器正在被写入浏览器规范的过程中，但与此同时，这个 polyfill 提供了一个我们今天可以使用的面向未来的解决方案。

加载器允许我们使用`System.import`方法动态加载在 ES6 模块语法中定义的模块:

```
System.import('mymodule').then(function(m) { ... });
```

除了加载 ES6 模块，加载器还允许通过使用钩子来加载其他模块语法。

### [系统 JS](https://github.com/systemjs/systemjs)

SystemJS 的名字有点令人困惑，它本质上是 ES6 模块加载器的加载器挂钩的集合，使我们能够从 npm、jspm、ES6 模块和更多的模块加载模块。您可以将其视为功能丰富的模块加载器，构建在面向未来的 ES6 模块加载器 Polyfill 的基础上。

### [jspm](http://jspm.io/)

jspm 是一个包管理器，类似于 npm，设计用于 SystemJS。它允许我们从各种来源安装软件包，并向我们的应用程序公开这些软件包，这样我们就可以轻松地用 SystemJS 导入它们。

## 让我们准备好

我假设您已经安装了 Node.js、npm 和 Git，并且熟悉所有这些工具的用法。

我们将从从 GitHub 克隆[Aurelia 示例应用程序库开始](https://github.com/aurelia/skeleton-navigation)

```
git clone https://github.com/aurelia/skeleton-navigation.git
```

此时你可能会问:“为什么我们要克隆他们的示例应用程序，而不是从头开始开发我们自己的应用程序？”
原因是 Aurelia 仍处于早期阶段，因此还没有简单的`aurelia init`命令，你可以运行它来获得你的`package.json`文件并设置好一切。

我们克隆的存储库充当了我们应用程序的良好基础。它给了我们一个目录结构、一个包清单、一些测试配置等等。希望有一天会有各种各样的安装程序，或者我们会遵从像约曼这样的生成器。因为我们使用存储库进行配置，而不是用于他们的示例应用程序本身，所以您可以继续删除`src/`目录，以及`styles/styles.css`和`index.html`文件。我们很快会创建自己的。

为了安装我们的依赖项并启动我们的应用程序，我们需要安装一些其他东西:

全局安装 gulp，以便我们可以访问 gulp CLI:

```
npm install -g gulp
```

然后，出于同样的原因，全局安装 jspm。

```
npm install -g jspm
```

现在打开 CLI，并移动到您的应用程序的根目录。完成后，运行命令:

```
npm install
```

它将安装我们的依赖项(来自`package.json`文件),其中包括:

*   奥雷利亚工具
*   吞咽插件
*   用于测试的 Karma 包

一旦该过程完成，我们将使用以下命令安装我们的 jspm 包:

```
jspm install -y
```

这是实际安装包含 Aurelia 的模块的地方。

最后，让我们用 jspm 安装 Bootstrap:

```
jspm install bootstrap
```

值得注意的是，Aurelia 库(包含在这些模块中)本身有许多依赖项，包括 SystemJS。作为安装 Aurelia 本身的结果，这些都将通过依赖关系管理来安装。我想强调这一点，以防万一你想知道我们后来如何访问像 SystemJS 这样的东西，尽管没有在我们的依赖项中明确列出它。

## 是时候构建一个应用程序了

我们现在有很多工具来帮助我们构建应用程序。我们接下来需要的是一个`index.html`页面:

```
<!doctype html>
<html>
  <head>
    <link rel="stylesheet" href="jspm_packages/github/twbs/bootstrap@3.3.4/css/bootstrap.min.css">
    <link rel="stylesheet" href="styles/styles.css">
  </head>
  <body aurelia-app>
    <script src="jspm_packages/system.js"></script>
    <script src="config.js"></script>
    <script> System.config({
        "paths": {
          "*": "dist/*.js"
         }
      });
      System.import('aurelia-bootstrapper'); </script>
  </body>
</html>
```

让我们浏览一下`<body>`的内容。

正如我之前提到的，SystemJS 允许我们使用`System.import`方法。在这段代码中，我们用它来导入启动 Aurelia 应用程序的`aurelia-bootsrapper`模块。由于运行`jspm install -y`时 jspm 为我们构建的`config.js`文件，我们可以通过名字引用`aurelia-bootstrapper`。它将模块名映射到它的版本化源代码。相当漂亮的东西。

`System.config`位为我们的模块设置路径，即从哪里开始寻找文件。

现在，创建`styles/style.css`文件，并将以下代码添加到其中:

```
body { padding-top: 74px; }
```

您会注意到，我们包括了之前安装的 Bootstrap。在您阅读本教程时，版本可能已经改变，所以请注意安装的是哪一个 jspm。

## aurelia-bootstrapper 是做什么的？

`aurelia-bootstrapper`模块将扫描`index.html`文件中的`aurelia-app`属性。如果这样的属性指定了一个值，那么引导程序将加载具有该名称的视图/模块；否则，它将加载一个名为`app.html`和`app.js`的视图和模块(这是默认值)。视图将被加载到具有`aurelia-app`属性的元素中(在本例中是`<body>`标签)。它将被连接到`app.js`文件。

让我们在`src`目录下创建一个`app.js`和`app.html`文件来看看这个过程:

```
export class App {
  constructor() {
    this.name = "Brad";
  }
}
```

```
<template>
  Hello, my name is <strong>${name}</strong>
</template>
```

您首先会注意到的是新的 ES6 模块语法和关键字`export`的使用。您还会注意到新的 ES6 类语法和缩写函数签名的使用。多亏了 SystemJS，Aurelia 开箱即可支持许多令人兴奋的 ES6 特性。

这里我们看到`app.js`定义了一个类，它的属性作为变量暴露在`app.html`文件中。这个类被称为视图模型，因为它是支持我们的视图的数据结构。我们使用 ES6 字符串插值语法打印出模板中的变量。

最后一点，我想强调 Aurelia 中的所有模板都包装在一个`<template>`标签中。

## 在浏览器中查看我们的应用

要在浏览器中启动并运行应用程序，我们只需执行以下命令:

```
gulp watch
```

这将完成编译 ES6、实时重新加载等等所有的魔法。您应该可以在`http://localhost:9000/`看到您的应用程序。正如我们所料，我们看到模板的内容呈现在`<bodygt;`标签中，我们看到属性插入到模板中。

我们的`gulpfile`已经为我们设置了 BrowserSync，所以如果你做了任何更改，页面都会重新加载。

## 是时候构建我们的应用程序了

在这一节中，我们将构建一个简单的 Reddit 客户端，它有两个页面:“滑稽”和“gif”。我们将从 Reddit 的 API 中获取每个页面的数据，并在每个页面上显示一个列表。

当构建任何具有多个页面的应用程序时，应用程序的核心是路由器，Aurelia 也不例外。让我们改变一下我们的`app.js`文件，让它成为我们 app 的核心模块。它将负责定义和配置路由。

```
import {Router} from "aurelia-router";

export class App {
  static inject() { return [Router]; }

  constructor(router) {
    this.router = router;
    this.router.configure(config => {
      config.title = "Reddit";

      config.map([
        {route: ["", "funny"], moduleId: "funny", nav: true, title: "Funny Subreddit"},
        {route: "gifs", moduleId: "gifs", nav: true, title: "Gifs Subreddit"}
      ]);
    });
  }
}
```

那么，我们在这里做了什么？

第一行(`import {Router} from "aurelia_router"`)使用 ES6 模块导入语法导入路由器本身。

然后，在`App`类中我们有一个名为`inject`的静态函数。你们中熟悉 AngularJS 的人不仅会知道[依赖注入](http://en.wikipedia.org/wiki/Dependency_injection)。`inject`函数将通过依赖注入来决定哪些参数在我们的构造函数中可用。在这种情况下，将提供一个参数，那就是我们的路由器。你可以看到我们已经修改了构造函数来接受新的参数。

依赖注入是强大的，因为它允许模块的松散耦合，并将控制流提升一个级别，这意味着我们可以在测试期间或稍后更新时交换这些依赖。

现在我们的类的构造函数中有了路由器，我们可以用它来设置路由。

首先，我们用`this.router = router;`将路由器设置为类本身的属性。这是一个奥雷利亚惯例，是路由工作所必需的。请注意，在这种情况下，命名很重要。

其次，我们通过使用在`this.router.configure`的回调中提供给我们的`config`对象来配置我们的路由。我们设置了一个`title`属性，用于设置页面的标题。我们还将路由定义列表传递给`config.map`函数。

每个路由定义具有以下模式:

```
{
  route: ["", "foo"], // Activate this route by default or when on /foo
  moduleId: "foo", // When active, load foo.js and foo.html (module)
  nav: true, // Add this route to the list of navigable routes (used for building UI)
  title: "Foo" // Used in the creation of a pages title
}
```

因此，在我们的实例中，我们有两个可以在`/#/funny`和`/#/gifs`访问的页面，由于两个路由模式的`["", "funny"]`列表，`/#/funny`作为我们的默认页面。

我们还需要更新`app.html`作为我们应用的布局文件。

```
<template>
  <a href="/#/funny">Funny</a>
  <a href="/#/gifs">Gifs</a>
  <router-view>
  </router-view>
</template>
```

你能看到`<router-view></router-view>`自定义元素吗？这是 Aurelia 的另一个内置功能。你可以把它想象成一个 AngularJS 指令或者仅仅是一个 web 组件。与当前路线相关联的视图将自动加载到此元素中。

接下来，我们需要定义两个模块:`funny`和`gifs`。

## 编写我们的页面模块

### “有趣”模块

我们从`funny`开始，然后复制过来作为`gifs`的基础。

用以下内容创建一个`/src/funny.js`文件:

```
import {HttpClient} from 'aurelia-http-client';

export class Funny {
   // Dependency inject the HttpClient
  static inject() { return [HttpClient]; }

  constructor(http) {
    this.http = http; // Assign the http client for use later
    this.posts = [];
    this.subreddit_url = "http://reddit.com/r/funny.json";
  }

  loadPosts() {
    // Aurelia's http client provides us with a jsonp method for
    // getting around CORS issues. The second param is the callback
    // name which reddit requires to be "jsonp"

    return this.http.jsonp(this.subreddit_url, "jsonp").then(r => {
      // Assign the list of posts from the json response from reddit
      this.posts = r.response.data.children;
    });
  }

  // This is called once when the route activates
  activate() {
    return this.loadPosts();
  }
}
```

还创建如下`/src/funny.html`:

```
<template>
  <ul class="list-group">
    <li class="list-group-item" repeat.for="p of posts">
      <img src.bind="p.data.thumbnail" />
      <a href="http://reddit.com${p.data.permalink}">
        ${p.data.title}
      </a>
    </li>
  </ul>
</template>
```

### “Gifs”模块

让我们简单地将我们的`funny.js`和`funny.html`分别复制到`src/gifs.js`和`src/gifs.html`中。我们需要稍微调整一下`gifs.js`的内容。

```
import {HttpClient} from 'aurelia-http-client';

export class Gifs {
  static inject() { return [HttpClient]; }

  constructor(http) {
    this.http = http;
    this.posts = [];
    this.subreddit_url = "http://reddit.com/r/gifs.json";
  }

  loadPosts() {
    return this.http.jsonp(this.subreddit_url, "jsonp").then(r => {
      this.posts = r.response.data.children;
    });
  }

  activate() {
    return this.loadPosts();
  }
}
```

现在你应该可以访问`localhost:9000/#/gifs`来查看 gif 帖子及其链接的列表。

## 我们布局的改进

我们可以使用 Aurelia 的路由器对我们的布局模板进行一些改进。

还记得我们之前在路由配置中设置的`nav:true`属性吗？它的作用是将一条路线添加到一个列表中，我们可以在视图中对其进行迭代，以构建动态导航。让我们现在做那件事。

更新`app.html`的内容如下:

```
<template>
<div class="container">
  <ul class="nav navbar-nav navbar-fixed-top navbar-inverse">
    <li repeat.for="navItem of router.navigation" class="${navItem.isActive ? 'active' : ''}">
      <a href.bind="navItem.href">
        ${navItem.title}
      </a>
    </li>
  </ul>

  <router-view></router-view>
</div>
 </template>
```

## 结论

好了，你知道了！你的第一份 Aurelia 申请。我对奥雷利亚的未来感到非常兴奋，因为我认为它干净而简单。此外，通过使用 ES6，它将一切都保存在可重用、可扩展的模块中。在未来的教程中，我会看看我们如何抽象 gif 和滑稽模块之间的重复，以及对我们的 Reddit 客户端的一些其他改进和添加。我很想知道你第一次尝试用 Aurelia 开发应用程序的情况如何！

我们在本文中构建的完整应用程序可以在[这里](https://github.com/sitepoint-editors/sitepoint-aurelia-app)找到

## 分享这篇文章