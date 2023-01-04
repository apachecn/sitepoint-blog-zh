# 使用 Angular NgModules 实现可重用代码等等

> 原文：<https://www.sitepoint.com/angular-ngmodules/>

NgModules 是 Angular 的核心概念，是每个应用程序的一部分，有助于为编译器和应用程序运行时连接一些重要的细节。它们对于将代码组织成特性、延迟加载路径和创建可重用的库特别有用。

在本指南中，我们将介绍 NgModules 的主要用途，并通过一些例子向您展示如何在 Angular 项目中使用它们！本指南假设您具备 Angular 的工作知识。

## JavaScript 模块不是 ngm 模块

让我们先澄清一下什么是 JavaScript 模块(有时称为 ES6 模块)。它们是一种语言结构，使组织代码变得更加容易。

最基本的， [Javascript 模块](https://www.sitepoint.com/understanding-es6-modules/)是包含`import`或`export`关键字的 Javascript 文件，这使得在该文件中定义的对象是私有的，除非您将其导出。我鼓励您查看上面的链接以获得更深入的理解，但本质上这是一种组织您的代码并轻松共享它的方法，而不依赖于可怕的全局范围。

当您使用 TypeScript 创建 Angular 应用程序时，只要您在源代码中使用`import`或`export`，它就会被视为一个 JavaScript 模块。TypeScript 能够为您处理模块加载。

*注意:为了让本文更清晰，我总是用全称来指代 JavaScript 模块和 NgModules。*

## 基本的 NgModule，AppModule

让我们从每个角度应用程序中都存在的一个基本 NgModule 开始，即`AppModule`(在任何新的角度应用程序中默认生成)。它看起来就像你在这里看到的:

```
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';

import { AppComponent } from './app.component';

@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { } 
```

Angular 使用[decorator](https://angular.io/guide/glossary#decorator--decoration)来定义它在编译时需要知道的元数据。要定义 NgModue，只需在类上添加`@NgModule()`装饰器。这个类可能不总是空的，但经常是空的。然而，您需要为 NgModule 定义一个具有一些属性的对象来做任何事情。

当应用程序启动时，需要给它一个 NgModule 来实例化。如果您查看应用程序的主文件(通常也称为`main.ts`)，您会看到`platformBrowserDynamic().bootstrapModule(AppModule)`，这是应用程序如何注册和启动`AppModule`(它可以被命名为任何名称，但几乎总是被命名为 this)。

## NgModule 的性质

[NgModule API](https://angular.io/guide/ngmodule-api) 文档页面概述了在定义 NgModule 时可以传递的属性，但是我们也将在这里讨论它们。它们都是可选的，但是您需要至少为其中一个定义值，以便 NgModule 做任何事情。

### `providers`

`providers`是一个数组，包含可用于这个 NgModule 的任何[提供者](https://www.sitepoint.com/angular-2-components-providers-classes-factories-values/#whatareproviders)(可注入服务)的列表。提供者有一个作用域，如果它们被列在一个延迟加载的 NgModule 中，它们在那个 NgModule 之外是不可用的。

### `declarations`

数组应该包含这个 NgModule 定义的任何指令、组件或管道的列表。这使得编译器可以找到这些项目，并确保它们被正确地捆绑在一起。如果这是根 NgModule，那么声明可用于所有 ng module。否则，它们只对同一个 NgModule 可见。

### `imports`

如果你的 NgModule 依赖于另一个 NgModule 的任何其他对象，你必须把它添加到`imports`数组中。这确保了编译器和依赖注入系统知道导入的项。

### `exports`

使用`exports`数组，您可以定义哪些指令、组件和管道可用于导入这个 NgModule 的任何 NgModule。例如，在 UI 库中，您可以导出组成库的所有组件。

#### `entryComponents`

任何需要在运行时加载的组件都必须添加到列表`entryComponents`中。本质上，这将创建组件工厂，并在需要动态加载时存储它。你可以从文档中了解更多关于如何动态加载组件的信息。

### `bootstrap`

首次加载应用程序时，您可以定义任意数量的组件来引导。通常你只需要引导主根组件(通常称为`AppComponent`)，但是如果你有不止一个根组件，那么每个都要在这里声明。通过将一个组件添加到`bootstrap`数组中，它也被添加到`entryComponents`列表中并被预编译。

### `schemas`

模式是一种定义 Angular 如何编译模板的方式，以及当它发现不是标准 HTML 或已知组件的元素时是否会抛出错误。默认情况下，Angular 在模板中发现它不知道的元素时会抛出一个错误，但是您可以通过将模式设置为 [NO_ERRORS_SCHEMA](https://angular.io/api/core/NO_ERRORS_SCHEMA) (允许所有元素和属性)或 [CUSTOM_ELEMENTS_SCHEMA](https://angular.io/api/core/CUSTOM_ELEMENTS_SCHEMA) (允许名称中带有`-`的任何元素或属性)来改变这种行为。

### `id`

此属性允许您为 NgModule 提供一个唯一的 ID，您可以用它来检索模块工厂引用。目前这是一个罕见的用例。

## ng 模块示例

为了说明 NgModule 与 Angular 一起使用的方式，让我们看一组例子，展示如何轻松处理各种用例。

### 特征模块

除了`AppModule`之外，NgModules 最基本的用例是用于[特性 NgModules](https://angular.io/guide/feature-modules) (通常称为特性模块，但试图保持术语一致)。它们有助于分离应用程序的各个部分，强烈推荐使用。在大多数方面，它们与主应用 NgModule 相同。让我们来看看 NgModule 的一个基本特性:

```
@NgModule({
  declarations: [
    ForumComponent,
    ForumsComponent,
    ThreadComponent,
    ThreadsComponent
  ],
  imports: [
    CommonModule,
    FormsModule,
  ],
  exports: [
    ForumsComponent
  ]
  providers: [
    ForumsService
  ]
})
export class ForumsModule { } 
```

这个简单的特性 NgModule 定义了四个组件、一个提供者，并导入了组件和服务所需的两个模块。总之，这些组成了应用程序的论坛部分的必要部分。

`providers`中的项目对于任何导入要注入的`ForumsModule`的 NgModule 都是可用的，但是理解每个 NgModule 都将获得它自己的服务实例是很重要的。这与根 NgModule 中列出的提供者不同，从根 ng module 中您将总是获得相同的实例(除非它被重新提供)。这就是理解依赖注入很重要的地方，特别是[层次依赖注入](https://angular.io/guide/hierarchical-dependency-injection)。很容易认为您将获得服务的同一个实例并在其上更改属性，但在应用程序的其他地方永远看不到这些更改。

正如我们之前了解到的，`declarations`中的项目实际上不能在其他 NgModule 中使用，因为它们是这个 ng module 的私有项。为了解决这个问题，您可以选择导出那些您希望在其他 NgModules 中使用的声明，就像在这个代码片段中，它只导出了`ForumsComponent`。现在，在任何其他特性 NgModules 中，您可以放置`<app-forums></app-forums>`(或者组件的选择器)来显示模板中的`ForumsComponent`。

另一个关键区别是`ForumsModule`导入了 [CommonModule](https://angular.io/api/common/CommonModule) 而不是 [BrowserModule](https://angular.io/api/platform-browser/BrowserModule) 。`BrowserModule`应该只在根 NgModule 导入，但是`CommonModule`包含核心角度指令和管道(如`NgFor`和`Date`管道)。如果你的特性 NgModule 不使用这些特性中的任何一个，它实际上就不需要`CommonModule`。

现在，当您想要在项目中使用`ForumsModule`时，您需要将它导入到您的`AppModule`中，如下所示:

```
@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule,
    ForumsModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { } 
```

然后，这个 NgModule 被导入到 main `AppModule`中以正确地加载它，它包括`ForumsModule` providers 数组中的项目和任何导出的项目，以便在您的应用程序中使用。

使用 Angular CLI 时，您可以通过运行新 NgModule 的生成器来轻松生成特征 ng module:

```
ng generate module path/to/module/feature 
```

你可以用任何你认为合适的方式来组织你的特征模块，但是一般的建议是把在同一个视图中使用的相似的东西组合在一起。我尝试制作少量的功能 NgModules 来保存通常共享的东西，然后更多地关注应用程序的每个主要功能的 NgModules。

### 延迟加载带路由的 NgModules

有时候你想只在用户需要的时候加载代码，而 Angular 目前可以通过同时使用路由器和特性 NgModules 来实现。当用户请求特定路由时，路由器能够延迟加载 NgModules。如果您是布线新手，请参阅这篇关于带角度布线的[初级读本。](https://www.sitepoint.com/component-routing-angular-router/)

最好的开始方式是为路线的独特部分创建一个功能模块。如果它们几乎总是一起使用，您甚至可能想要对多个路线进行分组。例如，如果您有一个客户帐户页面，其中有几个用于管理帐户详细信息的子页，那么您很可能会将它们声明为同一个 NgModule 的一部分。

您定义 NgModule 本身的方式没有什么不同，除了您需要用`RouterModule.forChild()`定义一些路线。您应该有一个具有空路径的路由，它将充当此功能 NgModule 的根路由，并且所有其他路由都悬挂于此:

```
@NgModule({
  declarations: [
    ForumComponent,
    ForumsComponent,
  ],
  imports: [
    CommonModule,
    FormsModule,
    RouterModule.forChild([
      {path: '', component: ForumsComponent},
      {path: ':forum_id', component: ForumComponent}
    ])
  ],
  providers: [
    ForumsService
  ]
})
export class ForumsModule { } 
```

行为上有一个重要的变化，它与提供者向应用程序注册的方式没有明显的关系。因为这是一个惰性加载的 NgModule，所以[提供者对于应用程序的其余部分来说*不可用*](https://angular.io/guide/providers#limiting-provider-scope-by-lazy-loading-modules) 。这是一个重要的区别，在规划应用程序架构时应该考虑到这一点。理解有角度的[依赖注入](https://angular.io/guide/dependency-injection)是如何工作的在这里非常重要。

为了加载惰性路径，main `AppModule`定义了到这个特性 NgModule 的路径。为此，您必须为新路由更新根路由器配置。这个例子展示了如何定义一个惰性加载的路由，给它一个`path`和`loadChildren`属性:

```
const routes: Routes = [
  {
    path: 'forums',
    loadChildren: 'app/forums/forums.module#ForumsModule'
  },
  {
    path: '',
    component: HomeComponent
  }
]; 
```

`loadChildren`属性的语法是一个字符串，它包含 NgModule 文件的路径(没有文件扩展名)，一个`#`符号，然后是 NgModule 类的名称:`loadChildren: 'path/to/module#ModuleName`。Angular 用这个来知道运行时在哪里加载文件，并知道 NgModule 的名字。

惰性加载路由的路径是在路由的根级别定义的，所以惰性加载 NgModule 甚至不知道它的路由的具体路径是什么。这使得它们更具可重用性，并使应用程序知道何时延迟加载该 NgModule 成为可能。考虑将所有路由定义为相对路径的惰性加载 NgModule，通过组合根路由和惰性加载路由来提供完整路径。

例如，如果您在这个应用程序中访问`/`路线，它将加载`HomeComponent`而`ForumsModule`不会被加载。然而，一旦用户点击一个链接来查看论坛，它会注意到`/forums`路径需要加载`ForumsModule`，下载它，并从它注册定义的路线。

#### 路由 ng 模块

Angular 的一个常见模式是使用一个单独的 NgModule 来托管所有的路由。这样做是为了分离关注点，并且是完全可选的。Angular CLI 支持在通过传递`--routing`标志创建新模块时自动生成路由 NgModule:

```
ng generate module path/to/module/feature --routing 
```

实际情况是，您创建了一个定义路线的独立 NgModule，然后您的功能 NgModule 导入它。路由 NgModule 可能是这样的:

```
const routes: Routes = [
  { path: '', component: ForumsComponent }
];

@NgModule({
  imports: [RouterModule.forChild(routes)],
  exports: [RouterModule]
})
export class ForumsRoutingModule { } 
```

然后，您只需将它导入到您的`ForumsModule`中，如下所示:

```
@NgModule({
  declarations: [
    ForumComponent,
    ForumsComponent,
  ],
  imports: [
    CommonModule,
    FormsModule,
    ForumsRoutingModule,
  ],
  providers: [
    ForumsService
  ]
})
export class ForumsModule { } 
```

这在很大程度上是一种偏好，但也是您应该考虑的一种常见模式。本质上，这是 NgModules 用于代码分离的另一种方式。

### 单身服务

我们已经看到了一些关于提供者的问题，除非你只在根 NgModule 中提供服务，否则你不能保证在 ng module 中得到相同的服务实例。有一种方法可以定义您的 NgModule，这样它可以只为根 NgModule 声明提供者，而不为所有其他 ng module 重新声明它们。

事实上，角路由器就是一个很好的例子。当你在根 NgModule 中定义一个路由时，你使用`RouterModule.forRoot(routes)`，但是在特征 ng module 中你使用`RouterModule.forChild(routes)`。这种模式对于任何需要服务的单个实例(singleton)的可重用库都是常见的。我们可以通过向 NgModule 添加两个静态方法来对任何 NgModule 执行相同的操作，如下所示:

```
@NgModule({
  declarations: [
    ForumComponent,
    ForumsComponent,
    ThreadComponent,
    ThreadsComponent
  ],
  imports: [
    CommonModule,
    FormsModule,
  ],
  exports: [
    ForumsComponent
  ]
})
export class ForumsModule {
  static forRoot(): ModuleWithProviders {
    return {
      ngModule: ForumsModule,
      providers: [ForumsService]
    };
  }

  static forChild(): ModuleWithProviders {
    return {
      ngModule: ForumsModule,
      providers: []
    };
  }
} 
```

然后在我们的`AppModule`中，您可以用`forRoot()`方法定义导入，这将返回带有提供者的 NgModule。在任何其他导入`ForumsModule`的 NgModule 中，您可以使用`forChild()`方法，这样就不用再次声明提供者(从而创建一个新实例):

```
@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule,
    ForumsModule.forRoot()
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { } 
```

### NgModules 用于对 NgModules 进行分组

您可以将许多其他 NgModules 组合成一个单独的模块，以便于导入和重用。例如，在我工作的 [Clarity](https://vmware.github.io/clarity) 项目中，我们有许多 ng module，它们只导出其他 ng module。例如，这是主`ClarityModule`，它实际上重新导出了包含每个组件的其他单个 NgModules:

```
@NgModule({
  exports: [
    ClrEmphasisModule, ClrDataModule, ClrIconModule, ClrModalModule, ClrLoadingModule, ClrIfExpandModule, ClrConditionalModule, ClrFocusTrapModule, ClrButtonModule, ClrCodeModule, ClrFormsModule, ClrLayoutModule, ClrPopoverModule, ClrWizardModule
  ]
})
export class ClarityModule { } 
```

这使得一次导入许多 ng module 变得很容易，但它确实使编译器更难知道哪些 ng module 用于或不用于树抖动优化。

## 摘要

我们对 Angular 中的 NgModules 进行了一次旋风式的考察，并涵盖了关键的用例。关于 NgModules 的[角度文档](https://angular.io/guide/ngmodules)也相当深入，如果你卡住了，我建议你查看一下 [FAQ](https://angular.io/guide/ngmodule-faq) 。

## 分享这篇文章