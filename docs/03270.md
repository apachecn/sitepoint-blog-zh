# 用 Typescript 和 ES5 在 Angular 2 中创建组件

> 原文：<https://www.sitepoint.com/creating-components-angular-2-typescript-es5/>

*这篇文章由[斯蒂芬·马克斯](https://www.sitepoint.com/author/smax/)、[杰夫·史密斯](https://www.sitepoint.com/author/jeffsmith/)和[拉维·基兰](https://www.sitepoint.com/author/rkiran/)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

随着今年接近尾声，Angular 团队比以往任何时候都更接近于发布 Angular 2.0 的稳定版本。这将重塑 Angular 应用程序的开发方式，但会变得更好。在整篇文章中，我将向您展示 Angular 2.0 中的一些核心概念，以及如何使用它们。具体来说，我将带您从头到尾了解在 Angular 2 中构建组件的过程。首先，我们将详细了解如何使用 [TypeScript](http://www.typescriptlang.org) 完成这一工作，然后我们将迁移我们的角度组件，以便它可以使用普通 ES5 工作。

本教程的代码可以在我们的 [GitHub repo](https://github.com/sitepoint-editors/SP-Angular2) 上找到。repo 有两个分支，一个用于 TypeScript 版本，一个用于 ES5 版本。如果你想克隆一个特定的分支，使用`git clone <url> --branch <branch>`。

## 什么是组件？

在过去的几个月里，JavaScript 中组件的使用有了巨大的增长。它们被用于 [React](https://facebook.github.io/react/docs/component-api.html) 、 [Knockout](http://knockoutjs.com/documentation/component-overview.html) 、 [Ember](https://www.sitepoint.com/understanding-components-in-ember-2/) 等项目中，所以 Angular 将它们纳入 2.0 版本也就不足为奇了。代码模块化一直是 Angular 团队关注的焦点，组件的使用强调了这一点，因为它们允许我们将代码分解成封装的片段。

但是什么是组件呢？嗯，它本质上是一些可以在整个应用程序中重用的代码。它由两部分组成:一个视图和一些逻辑。通过利用 Angular 开发团队对组件的关注，我们可以利用一些非常强大的功能。Angular 2 使得创建由不同组件组成的动态应用程序变得非常容易，这些组件已经取代了指令成为框架的头儿。在 Angular 2 中，指令是轻量级组件，它们只是用来向 DOM 添加一些功能。现在，Angular 开发者不必担心由于关于`isolate $scope`的冲突问题而搞乱应用程序。相反，组件的使用是一种确保应用程序某一部分的代码不会干扰另一部分的方法。

## 以打字打的文件

Angular 2.0 已经被创建来使用 [TypeScript](http://www.typescriptlang.org) ，它是 JavaScript 的超集。Angular 的开发者花了大量的时间来开发这个版本。他们致力于优化页面速度和工作流程自动化方面的性能。TypeScript 类似于其他代码转换程序，允许开发人员编写可以轻松转换为有效 JavaScript 的代码。也就是说，它变得越来越受欢迎，尤其是在过去的一年里，所以[Angular 团队决定用它来创建框架](http://blogs.msdn.com/b/typescript/archive/2015/03/05/angular-2-0-built-on-typescript.aspx)。

使用 TypeScript 的好处之一是它的[类型系统](https://basarat.gitbooks.io/typescript/content/docs/types/type-system.html)，它允许开发人员用类型信息来注释他们的 JavaScript。这个带注释的代码通过一个编译器运行，该编译器帮助捕捉错误，否则这些错误会潜伏在代码库中，等待用户发现它们。现在让我们来看看 TypeScript 的运行情况。

下面，我们看到一个我从 TJ Van Toll 的文章[打字稿的兴起](http://developer.telerik.com/featured/the-rise-of-typescript/)中摘录的例子。在这个函数中，我们看到`height`和`width`参数都应该是`number`类型。函数体之前的`: number`指定了返回类型，也是类型`number`。因此，传递给这个函数的任何不是数字的东西都会导致编译器在编译时抛出一个错误。

```
function calculateArea( height: number, width: number ) : number {
  return height * width;
}
console.log(calculateArea(2,3));
//Will work just fine

console.log(calculateArea("Ten", "Eleven"));
//Argument of type 'string' is not assignable to parameter of type 'number'. 
```

类型声明帮助我们记录我们的 API，并使我们的代码随着时间的推移更易于维护。

### 装置

将 TypeScript 编译成 JavaScript 的过程极其简单。首先从 npm 获取[类型脚本包](https://www.npmjs.com/package/typescript):

```
npm install -g typescript 
```

安装后，将 TypeScript 编译成 JavaScript 就像从命令行运行以下命令一样简单(TypeScript 文件使用`.ts`扩展名保存):

```
tsc <filename.ts> 
```

现在，让我们看看 Angular 2 如何利用 TypeScript 的强大功能来轻松创建定制组件。我们第一个例子的代码可以在我们的 [GitHub repo](https://github.com/sitepoint-editors/SP-Angular2/tree/typescript) 的 TypeScript 分支中找到

## 在 TypeScript 中创建组件

因为 TypeScript 是 JavaScript 的超集，任何有效的 JavaScript 都可以在`.ts`文件中正常工作。通过使用 TypeScript，开发人员能够扩展他们的 JavaScript 代码，以利用最新的 ES6 功能。在这个例子中，我们将利用类。

下面，我使用 TypeScript 代码创建了一个组件。我做的第一件事是使用 [ES6 导入语法](http://es6-features.org/#ValueExportImport)导入 Angular。在这个例子中，我们将定义一个组件，以及该组件的一个视图。一旦完成，我们将需要 Angular 的`bootstrap`函数，以便 Angular 运行代码。在这段代码中，我们会看到`@`符号，它用来告诉 Angular 我们正在尝试构建什么。

```
import {Component, View, bootstrap} from 'angular2/angular2';
@Component({
  selector: 'user-name'
}) 
```

因为 Angular 2 构建在 TypeScript 之上，所以框架识别出我们的`@Component`注释，并知道我们正在尝试创建一个新组件。此外，它告诉 Angular，每当它在我们的 HTML 中看到`<user-name></user-name>`时，我们都希望实例化一个组件。

如上所述，组件由两部分组成:

*   一种观点
*   一些逻辑

由于组件已经被定义，我们现在需要创建视图和逻辑。

紧接着我们的组件，我们可以添加一些类型脚本代码来定义我们的视图。让我们来看一下上面代码的延续，看看 Angular 让向定制组件添加视图的过程变得多么简单:

```
@View({
  template: '<h1>My name is Tom Greco</h1>'
})
class UserComponent { } 
```

现在，当我将`<user-name></user-name>`添加到我的 index.html 文件中时，这个模板将被注入到 DOM 中。也就是说，我们组件的逻辑部分是空的，因为我们的`UserComponent`类不包含任何代码。

在上面的例子中，我只有一个空类。然而，现在我将创建一个`name`变量，然后使用表达式在我们的视图中呈现这个`name`变量:

```
@View({
  template: '<h1>My name is {{ name }}</h1>'
})
class UserComponent {
  name: 'Tom Greco'
}
bootstrap(UserComponent); 
```

你还会看到我之前提到的[引导函数](https://docs.angularjs.org/api/ng/function/angular.bootstrap)。虽然他们共享一个名字，但是这个功能是用来启动，或者说 *bootstrap* 我们的 Angular 应用，和 [Twitter BootStrap 框架](http://getbootstrap.com/)没有任何关系。如果我们忘记将我们的组件传递给这个函数，Angular 将不知道加载我们的组件。

我想快速说明的另一件事是，我们的应用程序必须使用某种服务器才能正确显示。如果直接访问它，那么 System.js 将无法加载我们的`main`模块，该模块保存我们的代码。

对于这个例子，使用存储库的人现在可以在根目录中运行`node app.js`。一旦运行了这个命令，我们就可以通过访问 [http://localhost:8000](http://localhost:8000) 来查看组件的运行情况。希望这演示了 Angular 使向组件添加逻辑变得多么容易！

## 将组件迁移到 ES5

对于那些想利用 2.0 的 ES5 的人来说，Angular 2 团队已经创建了一个可以直接放入网站的框架版本。这是必要的，因为 ES5 没有模块系统，所以我们需要某种自执行捆绑包。如果你检查了第一个例子的代码[，你会发现我需要在应用程序中添加三个不同的脚本标签。在这个例子中，我们需要做的就是添加下面的脚本。](https://github.com/sitepoint-editors/SP-Angular2/blob/typescript/tsc-component/index.html)

```
<script src="http://code.angularjs.org/2.0.0-alpha.30/angular2.sfx.dev.js"></script> 
```

在这个脚本的帮助下，开发人员可以使用他们的 ES5 知识，而不必担心牺牲框架的任何功能。让我们看看如何在 ES5 中构建一个角度组件。这个例子的代码可以在 GitHub repo 的 ES5 分支[上找到。也就是说，让我们开始吧！](https://github.com/sitepoint-editors/SP-Angular2/tree/es5)

为了使用 ES5 而不是 TypeScript 重新创建组件，我将使用一些不同的方法。这本质上和我在上面的例子中做的是一样的，但是我们将在`ng`对象上链接方法，而不是使用`@`符号。这显示在下面的代码中:

```
var UserComponent = ng.Component({
  selector: 'user-name'
}) 
```

现在，我们可以继续向组件添加功能，每次应用程序读取`<user-name>`选择器时都会显示这些功能。

让我们利用`View`和`Class`方法。在我们的`View`方法中，我们只需要传入我们之前使用的模板字符串。因为在 ES5 中不支持类，我们将在我们的`Class`方法中模仿它们的使用，通过创建一个简单的构造函数，它将保存我们的`name`属性。

```
.View({
  template: '<h1>My name is {{ name }}</h1>'
})
.Class({
  constructor: function(){
    this.name="Tom Greco";
  }
}); 
```

但是我们忽略了一件事。在我们的 TypeScript 示例中，我们使用了`bootstrap`函数来启动我们的角度代码。下面是我们如何在 ES5 中做同样的事情:

```
<script>
  document.addEventListener('DOMContentLoaded', function () {
    angular.bootstrap(UserComponent);
  });
</script> 
```

这应该放在我们的自定义应用程序代码下面。这将导致 Angular 引导我们的应用程序，并在页面加载后加载组件。与我们之前的例子(需要一个服务器)不同，这个页面可以直接在浏览器中查看。

正如你所看到的，Angular 团队为那些希望使用 ES5 在 2.0 中构建应用程序的人提供了一个清晰的解决方案。如果这是你感兴趣的东西，我强烈推荐你查看一下 [a.js 库](https://github.com/angular/a)，它允许开发者使用受 TypeScript 启发的语法在 ES5 中构建 Angular 应用。

## 结论

希望这已经让你对 Angular 的各个方面有了深入的了解，它将出现在框架的下一个版本中。如果你想更进一步，使用 Angular 2 和 TypeScript 构建一个完整的应用程序(在这种情况下是一个插接板)，那么我建议查看这篇文章:[使用 TypeScript 开始使用 Angular 2](https://www.sitepoint.com/getting-started-with-angular-2-using-typescript/)。

我也很想听听你对 Angular 2 的体验。你已经试过了吗？你有什么想分享的东西吗？请在评论中告诉我。

## 分享这篇文章