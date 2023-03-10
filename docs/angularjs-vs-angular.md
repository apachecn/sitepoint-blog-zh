# AngularJS 和 Angular 2+的详细比较

> 原文：<https://www.sitepoint.com/angularjs-vs-angular/>

**本文比较了原始 AngularJS 和 Angular 2+之间的主要差异。如果你目前陷入了 AngularJS 项目，并且不确定是否应该跳跃，这篇文章应该可以帮助你开始。**

近年来，我们已经看到 Angular 作为一个框架和平台在开发单页面应用程序(spa)和渐进式 web 应用程序(pwa)方面取得了巨大的发展。AngularJS 建立在声明式编程应该用于构建视图的思想之上。这需要将 DOM 操作从应用程序的业务逻辑中分离出来，这种方法本身有很多好处。

然而，AngularJS 在性能和如何工作方面有许多缺点。因此，开发团队花了一年时间从头重写代码，最终在 2016 年末发布了 Angular 2。大多数开发者认为 Angular 2 是一个不同的平台，与最初的 AngularJS 几乎没有相似之处。

所以我们来对比对比 AngularJS 和 Angular 2+。

## AngularJS 和 Angular 2 中的框架

AngularJS 遵循传统的 MVC 架构，包括一个模型、一个视图和一个控制器。

*   控制器:控制器表示如何处理用户交互，并绑定模型和视图。
*   视图:视图代表表示层和实际的 UI。
*   模型:模型是数据的抽象表示。

一些开发者认为 AngularJS 遵循 MVVM 模式，用视图模型代替控制器。视图模型是一个 JavaScript 函数，类似于控制器的函数。它的特别之处在于它同步了视图和模型之间的数据。对 UI 元素所做的更改会自动传播到模型中，反之亦然。

下图显示了各种 AngularJS 零件如何连接在一起。

![AngularJS and Angular 2: AngularJS architecture](img/b5749265301f5d4d27e62ef8bcbc45f7.png)

你可以在[官方文档页面](https://docs.angularjs.org/guide/concepts)上阅读更多关于 AngularJS 的架构。

Angular，另一方面，hand 有一个基于组件的架构。每个角度应用至少有一个称为根分量的分量。每个组件都有一个负责处理业务逻辑的相关类和一个表示视图层的模板。多个紧密相关的组件可以堆叠在一起形成一个模块，每个模块独立形成一个功能单元。

![AngularJS and Angular 2: High-level overview of the Angular architecture](img/a39da117a28557e73ffd7bc934eb60f1.png)

如图所示，组件被绑定到模板。组件是使用 TypeScript 类组成的，模板是使用`@Component`注释附加到组件上的。可以使用 Angular 的依赖注入子系统将服务注入到组件中。Angular 中的模的概念与 AngularJS 中的模的概念完全不同。NgModule 是用于定义功能单元的容器。NgModule 可以包含组件、服务和其他功能。然后，模块化单元可以被导入并与其他模块一起使用。

所有的角度概念在 [Angular.io](https://angular.io/guide/architecture) 中有更好的解释。

## AngularJS 和 Angular 2 中的模板

在 AngularJS 中，模板是用 HTML 编写的。要使其动态，您可以添加特定于 AngularJS 的代码，如属性、标记、过滤器和表单控件。此外，它支持前面提到的双向数据绑定技术。以下代码片段演示了模板中指令和双花括号的用法:

```
<html ng-app>
 <!-- Body tag augmented with ngController directive  -->
 <body ng-controller="MyController">
   <inpu#t ng-model="foo" value="bar">
   <!-- Button tag with ngClick directive -->
   <!-- Curly bracket is a template binding syntax -->
   button ng-click="changeFoo()">{{buttonText}}</button>
   <script src="angular.js"></script>
 </body>
</html> 
```

在 Angular 中，AngularJS 的模板结构被重新设计，许多新功能被添加到模板中。主要的区别是每个组件都有一个附加的模板。除了`<html>`、`<body>`、`<base>`和`<script>`之外的所有 HTML 元素都在模板中工作。除此之外，还有模板绑定、模板插值、模板语句、属性绑定、事件绑定和双向绑定等特性。像 NgClass、NgStyle 和 NgModel 这样的内置属性指令和 NgIf、NgForOf、NgSwitch 这样的内置结构指令也是模板的一部分。

## AngularJS 和 Angular 2 中的依赖注入

依赖注入是一种设计模式，它负责满足依赖关系，并在需要时将它们注入到组件中。这避免了将依赖关系硬编码到组件中的需要。AngularJS 有一个注入器子系统，负责创建组件、注入依赖项和解析所有依赖项列表。可以根据需要注入以下成分:

*   价值
*   工厂
*   服务
*   供应者
*   常数

可以通过使用工厂方法来注入服务、指令和过滤器。这里有一个工厂方法的例子。工厂方法是用名为`myModule`的模块注册的:

```
angular.module('myModule', [])
.factory('serviceId', ['depService', function(depService) {
  // ...
}])
.directive('directiveName', ['depService', function(depService) {
  // ...
}])
.filter('filterName', ['depService', function(depService) {
  // ...
}]); 
```

尽管方法保持不变，Angular 有一个新的依赖注入系统，它不同于旧的 DI 模式。Angular 的依赖注入通过由`providers`和`declarations`组成的`@NgModule`数组来管理。`declarations`数组是声明组件和指令的空间。依赖项和服务通过`providers`数组注册。

假设您有一个检索名为`ContactlistService`的联系人列表并将其提供给`ContactList`组件的服务。你首先需要将`ContactlistService`注册到`providers`数组中的`app.module.ts`中。接下来，您需要将服务注入到组件中，如下所示:

```
import { Component }   from '@angular/core';
import { Contact }        from './contact';
import { ContactListService } from './contactlist.service';

@Component({
  selector: 'app-contacts-list',
  template: ` <div *ngFor="let contact of contacts">
      {{contact.id}} - {{contact.name}} - {{contact.number}}
    </div> `
})
export class ContactListComponent {
  contacts: Contact[];

  constructor(contactlistService: ContactlistService) {
    this.contacts = contactlistService.getcontacts();
  }
} 
```

这里，我们告诉 Angular 将服务注入到组件的构造函数中。

## JavaScript 与类型脚本

AngularJS 是一个纯 JavaScript 框架，AngularJS 中的模型是普通的旧 JavaScript 对象。这使得建立项目的整个过程变得容易得多。任何有一些基本 JavaScript 经验的开发人员都可以开始使用这个框架。正因为如此，Angular 1.0 与其他前端框架相比，学习曲线非常平缓。

Angular 2+引入了 TypeScript 作为构建应用程序的默认语言。TypeScript 是 JavaScript 的语法超集，可以编译成普通的 JavaScript。Angular 团队选择了 TypeScript 而不是 JavaScript，因为类型注释特性允许您进行可选的静态类型检查。类型检查可以防止编译时错误爬进您的代码，否则这些错误会被忽略。这使得您的 JavaScript 代码更容易预测。

除此之外，TypeScript 还因其类、接口和装饰器(类装饰器、属性装饰器和参数装饰器)而广受欢迎。Angular 使用 TypeScript 类来定义组件。是一个流行的例子，展示了如何使用类装饰器将元数据附加到组件上。通常，这包括组件配置细节，如模板选择器标签、`templateUrl`和`providers`数组，这样您就可以将任何相关的依赖注入到该组件中:

```
@Component({
  selector:    'app-hero-list',
  templateUrl: './hero-list.component.html',
  providers:  [ HeroService ]
})
export class HeroListComponent implements OnInit {
/* . . . */
} 
```

## AngularJS 和 Angular 2 的工具支架

更好的工具支持帮助开发人员更快地构建东西，并增加了整体开发工作流程。例如，命令行界面(CLI)可以大大减少从头创建应用程序所花费的时间。类似地，还有其他工具，如 ide、文本编辑器、测试工具包等。这有助于使开发变得更加容易。

AngularJS 没有官方的 CLI，但有许多第三方生成器和工具可用。对于 IDE 来说，WebStorm 和 Aptana 是开发人员的热门选择。如果你像我一样，你可以定制一个普通的文本编辑器，比如 Submlime 文本编辑器，并在其中添加合适的插件。AngularJS 有一个用于调试和测试的浏览器扩展，叫做 ng-inspector。AngularJS 的结构允许第三方模块毫无争议地导入。你可以在 ngmodules.org 找到所有流行的 ng 模块，这是一个托管 AngularJS 模块的开源项目。

与 AngularJS 相比，Angular 拥有更多的工具支持。有一个官方的 CLI，可以让你初始化新项目，为它们服务，也可以为生产构建优化的包。你可以在 GitHub 阅读更多关于 Angular CLI 的内容。因为 Angular 使用 TypeScript 而不是 JavaScript，所以 Visual Studio 作为 IDE 受到支持。这还不是全部。有许多 IDE 插件和独立工具可以帮助您自动化和加速开发周期的某些方面。用于调试的占卜，用于代码分析的 NgRev，用于代码验证的 Codelyzer 等。是非常有用的工具。

## 摘要

AngularJS 有许多缺陷——大多数都与性能有关——但它曾经是快速原型制作的首选。然而，回到 AngularJS 或维护 AngularJS 项目已经没有任何意义了。如果你还没有转变，你应该考虑这样做。

在本文中，我们讨论了 AngularJS 和 Angular 2 之间的五大区别。除了模板结构和依赖注入方法之外，几乎所有其他特性都进行了改进。许多流行的 Angular 1.0 特性，如控制器、范围、指令、模块定义等。已经被其他替代品取代。此外，基础语言已经改变，结构也已修改。

## 分享这篇文章