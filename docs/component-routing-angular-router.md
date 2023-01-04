# 角路由器的元件布线介绍

> 原文：<https://www.sitepoint.com/component-routing-angular-router/>

**这是 SitePoint [Angular 2+教程](https://www.sitepoint.com/angular-2-tutorial/)的第 4 部分，讲述如何用 [Angular CLI](https://github.com/angular/angular-cli) 创建 CRUD 应用。在本文中，我们将介绍 Angular Router，并了解它如何在浏览器 URL 改变时更新我们的应用程序，反之亦然。我们还将了解如何更新我们的应用程序，以使用路由器解析来自后端 API 的数据。**

在第一部分中，我们学习了如何启动并运行我们的 Todo 应用程序，并将其部署到 GitHub 页面。这工作得很好，但不幸的是，整个应用程序被塞进了一个组件。

在[第二部分](https://www.sitepoint.com/understanding-component-architecture-angular/)中，我们研究了一个更加模块化的组件架构，并学习了如何将单个组件分解成更小的组件的结构化树，以便于理解、重用和维护。

在第三部分中，我们更新了我们的应用程序，使用 RxJS 和 Angular 的 HTTP 服务与 REST API 后端进行通信。

1.  [第 0 部分—极限角度 CLI 参考指南](https://www.sitepoint.com/ultimate-angular-cli-reference/)
2.  [第 1 部分—启动并运行我们的第一个 Todo 应用程序版本](https://www.sitepoint.com/angular-2-tutorial/)
3.  [第 2 部分—创建单独的组件来显示待办事项列表和单个待办事项](https://www.sitepoint.com/understanding-component-architecture-angular/)
4.  [第 3 部分—更新 Todo 服务以与 REST API 通信](https://www.sitepoint.com/angular-rxjs-create-api-service-rest-backend/)
5.  **第 4 部分—使用角度路由器解析数据**
6.  [第 5 部分—添加身份验证以保护私人内容](https://www.sitepoint.com/angular-2-authentication-protecting-private-content/)
7.  第 6 部分——如何将 Angular 项目更新到最新版本。

不要担心！你不需要遵循本教程的第一、第二或第三部分，第四部分才有意义。你可以简单地拿一份[我们的回购](https://github.com/sitepoint-editors/angular-todo-app)，检查第三部分的代码，并以此为起点。这将在下面更详细地解释。

## 运行正常的

确保您安装了最新版本的 Angular CLI。如果没有，可以使用以下命令安装它:

```
npm install -g @angular/cli@latest 
```

如果需要删除以前版本的 Angular CLI，可以执行以下操作:

```
npm uninstall -g @angular/cli angular-cli
npm cache clean
npm install -g @angular/cli@latest 
```

之后，您将需要第三部分代码的副本。这个可以在 [GitHub](https://github.com/sitepoint-editors/angular-todo-app) 获得。本系列中的每篇文章在存储库中都有一个相应的标记，因此您可以在应用程序的不同状态之间来回切换。

我们在[第三部分](https://www.sitepoint.com/angular-rxjs-create-api-service-rest-backend/)中结束并在本文中开始的代码被标记为[第三部分](https://github.com/sitepoint-editors/angular-todo-app/tree/part-3)。我们结束这篇文章的代码被标记为[第 4 部分](https://github.com/sitepoint-editors/angular-todo-app/tree/part-4)。

*您可以将标签想象成特定提交 id 的别名。您可以使用`git checkout`在它们之间切换。你可以在这里阅读《T2》。*

因此，要启动并运行(安装了最新版本的 Angular CLI ),我们应该这样做:

```
git clone git@github.com:sitepoint-editors/angular-todo-app.git
cd angular-todo-app
git checkout part-3
npm install
ng serve 
```

然后访问 [http://localhost:4200/](http://localhost:4200/) 。如果一切正常，你应该看到工作的应用程序。

### 快速回顾一下

下面是我们的应用程序架构在第 3 部分结束时的样子:

![Angular Router: Application Architecture](img/4f2a5e5855823cc27ddfc1ddd7a3bfb7.png)

在本文中，我们将:

*   了解为什么应用程序可能需要路由
*   了解什么是 JavaScript 路由器
*   了解什么是 Angular Router，它是如何工作的，以及它能为您做什么
*   设置角度路由器并为我们的应用程序配置路由
*   创建一个解析器来从我们的 REST API 中获取 todos
*   更新我们的应用程序，使用新的解析器获取 todos。

到本文结束时，您会明白:

*   您的应用程序可能需要路由的时间和原因
*   在服务器上路由和在浏览器中路由的区别
*   什么是角路由器，它能为您的应用做些什么
*   如何设置角路由器
*   如何为您的应用程序配置路由
*   如何告诉 Angular Router 在 DOM 中何处放置组件
*   如何优雅地处理未知的网址
*   什么是解析器，它可以用来做什么
*   如何使用角路由器解析数据？

所以，让我们开始吧！

## 为什么路由？

在当前状态下，我们的 web 应用程序没有考虑浏览器 URL。

我们通过一个 URL 如`http://localhost:4200`访问我们的应用程序，而我们的应用程序不知道任何其他 URL 如`http://localhost:4200/todos`。

大多数 web 应用程序需要支持不同的 URL 来将用户导航到应用程序中的不同页面。这就是路由器的用武之地。

在传统网站中，路由由服务器上的路由器处理:

1.  用户单击浏览器中的链接，导致 URL 发生变化
2.  浏览器向服务器发送 HTTP 请求
3.  服务器从 HTTP 请求中读取 URL，并生成适当的 HTTP 响应
4.  服务器向浏览器发送 HTTP 响应。

在现代 JavaScript web 应用程序中，路由通常由浏览器中的 JavaScript 路由器处理。

### 什么是 JavaScript 路由器？

本质上，JavaScript 路由器做两件事:

1.  当浏览器 URL 更改时更新 web 应用程序状态
2.  当 web 应用程序状态更改时，更新浏览器 URL。

JavaScript 路由器使我们开发单页面应用成为可能。

SPA 是一个 web 应用程序，提供类似于桌面应用程序的用户体验。在 SPA 中，与后端的所有通信都在后台进行。

当用户从一个页面导航到另一个页面时，即使 URL 发生变化，页面也会动态更新，而无需重新加载。

有许多不同的 JavaScript 路由器实现可用。

有些是专门为某个 JavaScript 框架编写的比如 [Angular](https://angular.io) 、 [Ember](https://www.emberjs.com) 、 [React](https://facebook.github.io/react/) 、 [Vue.js](https://vuejs.org) 和 [Aurelia](http://aurelia.io) 等。其他实现是为通用目的而构建的，并不依赖于特定的框架。

### 什么是角路由器？

[Angular Router](https://angular.io/guide/router) 是一个官方的 Angular routing 库，由 Angular 核心团队编写和维护。

这是一个 JavaScript 路由器实现，设计用于 Angular，打包为`@angular/router`。

首先，Angular Router 负责 JavaScript 路由器的职责:

*   当用户导航到某个 URL 时，它激活所有必需的角度组件来组成页面
*   它允许用户从一个页面导航到另一个页面，而无需重新加载页面
*   它会更新浏览器的历史记录，这样用户就可以在页面之间来回导航时使用*后退*和*前进*按钮。

此外，角度路由器允许我们:

*   将一个 URL 重定向到另一个 URL
*   在显示页面之前解析数据
*   激活或停用页面时运行脚本
*   惰性加载我们应用程序的一部分。

在本文中，我们将学习如何设置和配置 Angular Router，如何重定向 URL，以及如何使用 Angular Router 从后端 API 解析 todos。

在下一篇文章中，我们将在应用程序中添加身份验证，并使用路由器来确保某些页面只有在用户登录后才能被访问。

## 角度路由器如何工作

在我们深入研究代码之前，理解 Angular Router 的工作原理及其引入的术语非常重要。

当用户导航到一个页面时，Angular Router 按顺序执行以下步骤:

1.  它读取用户想要导航到的浏览器 URL
2.  它应用一个 URL 重定向(如果定义了的话)
3.  它会计算出哪个路由器状态对应于该 URL
4.  它运行路由器状态中定义的防护
5.  它解析路由器状态所需的数据
6.  它激活角度组件来显示页面
7.  它管理导航，并在请求新页面时重复上述步骤。

为了完成其任务，Angular Router 引入了以下术语和概念:

*   **路由器服务**:我们应用程序中的全局角度路由器服务
*   **路由器配置**:定义我们的应用程序可能处于的所有可能的路由器状态
*   **路由器状态**:路由器在某个时间点的状态，以激活的路由快照树表示
*   **激活的路由快照**:提供对路由器状态节点的 URL、参数和数据的访问
*   **guard** :加载、激活或停用路线时运行的脚本
*   **解析器**:在请求的页面被激活之前获取数据的脚本
*   **路由器出口**:角度路由器在 DOM 中放置激活元件的位置。

如果术语听起来让人不知所措，也不要担心。随着我们在本系列中逐步处理这些术语，以及您对 Angular Router 有了更多的经验，您将会习惯这些术语。

使用 Angular Router 的 Angular 应用程序只有一个路由器服务实例:它是一个 singleton。无论何时何地，只要在应用程序中注入`Router`服务，就可以访问同一个 Angular Router 服务实例。

要更深入地了解角度布线过程，请务必查看[角度布线导航的 7 步布线过程](https://www.jvandemo.com/the-7-step-process-of-angular-router-navigation/)。

## 启用路由

要在我们的 Angular 应用中启用路由，我们需要做三件事:

1.  创建一个路由配置，定义应用程序的可能状态
2.  将路由配置导入我们的应用程序
3.  添加一个路由器出口，告诉 Angular Router 将激活的组件放在 DOM 中的什么位置。

让我们从创建路由配置开始。

### 创建路由配置

为了创建我们的路由配置，我们需要一个我们希望应用程序支持的 URL 列表。

目前，我们的应用程序非常简单，只有一个页面显示待办事项列表:

*   `/`:显示待办事项列表

它将 todos 列表显示为我们应用程序的主页。

然而，当用户在浏览器中添加书签`/`来查询他们的待办事项列表，而我们改变了我们主页的内容(这将在本系列的第 5 部分中进行)，他们的书签将不再显示他们的待办事项列表。

因此，让我们给待办事项列表一个自己的 URL，并将我们的主页重定向到它:

*   `/`:重定向至`/todos`
*   `/todos`:显示待办事项列表。

这为我们提供了两个好处:

*   当用户标记 todos 页面时，他们的浏览器将标记`/todos`而不是`/`，即使我们改变了主页内容，它仍将按预期工作
*   我们现在可以通过重定向到我们喜欢的任何 URL 来轻松地更改我们的主页，如果您需要定期更改您的主页内容，这很方便。

官方角度样式指南[建议](https://angular.io/guide/styleguide#angular-ngmodule-names)将角度模块的布线配置存储在文件名以`-routing.module.ts`结尾的文件中，该文件导出一个单独的角度模块，文件名以`RoutingModule`结尾。

我们当前的模块名为`AppModule`，因此我们创建了一个文件`src/app/app-routing.module.ts`，并将我们的路由配置导出为一个名为`AppRoutingModule`的角度模块:

```
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { AppComponent } from './app.component';

const routes: Routes = [
  {
    path: '',
    redirectTo: 'todos',
    pathMatch: 'full'
  },
  {
    path: 'todos',
    component: AppComponent
  }
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule],
  providers: []
})
export class AppRoutingModule {
} 
```

首先我们从`@angular/router`导入`RouterModule`和`Routes`:

```
import { RouterModule, Routes } from '@angular/router'; 
```

接下来，我们定义一个类型为`Routes`的变量`routes`，并将其分配给我们的路由器配置:

```
const routes: Routes = [
  {
    path: '',
    redirectTo: 'todos',
    pathMatch: 'full'
  },
  {
    path: 'todos',
    component: AppComponent
  }
]; 
```

*`Routes`类型是可选的，它让支持 TypeScript 的 IDE 或 TypeScript 编译器在开发过程中方便地验证您的路由配置。*

路由器配置代表了我们的应用程序可能处于的所有可能的路由器状态。

它是一个路由树，定义为一个 JavaScript 数组，其中每条路由可以具有以下属性:

*   **路径**:字符串，匹配 URL 的路径
*   **pathMatch** :字符串，如何匹配 URL
*   **组件**:类引用，激活该路由时要激活的组件
*   **redirectTo** :字符串，激活此路由时重定向到的 URL
*   **数据**:分配给路由的静态数据
*   **解析**:动态数据解析，解析后与**数据**合并
*   **子**:子路由。

我们的应用程序很简单，只包含两个同级路由，但是一个更大的应用程序可能有一个包含子路由的路由器配置，例如:

```
const routes: Routes = [
  {
    path: '',
    redirectTo: 'todos',
    pathMatch: 'full'
  },
  {
    path: 'todos',
    children: [
      {
        path: '',
        component: 'TodosPageComponent'
      },
      {
        path: ':id',
        component: 'TodoPageComponent'
      }
    ]
  }
]; 
```

这里，`todos`有两个子路由，`:id`是路由参数，使路由器能够识别以下 URL:

*   `/`:首页，重定向到`/todos`
*   `/todos`:激活`TodosPageComponent`，显示待办事项列表
*   `/todos/1`:激活`TodoPageComponent`，将`:id`参数值设置为`1`
*   `/todos/2`:激活`TodoPageComponent`，将`:id`参数值设置为`2`。

注意我们在定义重定向时是如何指定`pathMatch: 'full'`的。

角路由器有两种匹配策略:

*   **前缀**:默认，当 URL **以**开始时匹配`path`的值
*   **full** :当 URL **等于**的值`path`时匹配。

我们可以创建以下路线:

```
// no pathMatch specified, so Angular Router applies
// the default `prefix` pathMatch
{
  path: '',
  redirectTo: 'todos'
} 
```

在这种情况下，Angular Router 应用默认的`prefix`路径匹配策略，每个 URL 都被重定向到`todos`，因为每个 URL **都以**开始，即`path`中指定的空字符串`''`。

我们只希望我们的主页被重定向到`todos`，所以我们添加了`pathMatch: 'full'`来确保只有**等于**的 URL 空字符串`''`才匹配:

```
{
  path: '',
  redirectTo: 'todos',
  pathMatch: 'full'
} 
```

要了解更多关于不同路由配置选项的信息，请查看关于[路由和导航](https://angular.io/guide/router)的官方角度文档。

最后，我们创建并导出一个角度模块`AppRoutingModule`:

```
@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule],
  providers: []
})
export class AppRoutingModule {
} 
```

创建路由模块有两种方法:

1.  `RouterModule.forRoot(routes)`:创建一个路由模块，包括路由器指令、路由配置**和路由器服务**
2.  `RouterModule.forChild(routes)`:创建一个路由模块，包括路由器指令、路由配置**，但不包括**路由器服务。

当您的应用程序有多个路由模块时，需要使用`RouterModule.forChild()`方法。

请记住，路由器服务负责应用程序状态和浏览器 URL 之间的同步。实例化与同一个浏览器 URL 交互的多个路由器服务会导致问题，因此在我们的应用程序中必须只有一个路由器服务实例，无论我们在应用程序中导入多少路由模块。

当我们导入使用`RouterModule.forRoot()`创建的路由模块时，Angular 将实例化路由器服务。当我们导入使用`RouterModule.forChild()`创建的路由模块时，Angular 将**而不是**实例化路由器服务。

因此，对于额外的路由模块，我们只能使用`RouterModule.forRoot()`一次，使用`RouterModule.forChild()`多次。

因为我们的应用程序只有一个路由模块，所以我们使用`RouterModule.forRoot()`:

```
imports: [RouterModule.forRoot(routes)] 
```

此外，我们还在`exports`属性中指定了`RouterModule`:

```
exports: [RouterModule] 
```

这确保了当`AppModule`导入`AppRoutingModule`时，我们不必在`AppModule`中再次显式导入`RouterModule`。

现在我们有了我们的`AppRoutingModule`，我们需要将它导入到我们的`AppModule`中来启用它。

### 导入路由配置

为了将我们的路由配置导入到我们的应用程序中，我们必须将`AppRoutingModule`导入到我们的主`AppModule`中。

让我们打开`src/app/app.module.ts`并将`AppRoutingModule`添加到`AppModule`的`@NgModule`元数据中的`imports`数组中:

```
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';
import { FormsModule } from '@angular/forms';
import { HttpModule } from '@angular/http';

import { AppComponent } from './app.component';
import { TodoListComponent } from './todo-list/todo-list.component';
import { TodoListFooterComponent } from './todo-list-footer/todo-list-footer.component';
import { TodoListHeaderComponent } from './todo-list-header/todo-list-header.component';
import { TodoDataService } from './todo-data.service';
import { TodoListItemComponent } from './todo-list-item/todo-list-item.component';
import { ApiService } from './api.service';
import { AppRoutingModule } from './app-routing.module';

@NgModule({
  declarations: [
    AppComponent,
    TodoListComponent,
    TodoListFooterComponent,
    TodoListHeaderComponent,
    TodoListItemComponent
  ],
  imports: [
    AppRoutingModule,
    BrowserModule,
    FormsModule,
    HttpModule
  ],
  providers: [TodoDataService, ApiService],
  bootstrap: [AppComponent]
})
export class AppModule {
} 
```

因为`AppRoutingModule`的`exports`属性中已经列出了`RoutingModule`，Angular 会在我们导入`AppRoutingModule`的时候自动导入`RoutingModule`，所以我们不用再显式导入`RouterModule`(虽然这样做不会有什么坏处)。

在我们可以在浏览器中尝试我们的更改之前，我们需要完成第三步，也是最后一步。

### 添加路由器出口

尽管我们的应用程序现在有了路由配置，我们仍然需要告诉 Angular Router 它可以将实例化的组件放在 DOM 中的什么位置。

当我们的应用程序被引导时，Angular 实例化`AppComponent`，因为`AppComponent`被列在`AppModule`的`bootstrap`属性中:

```
@NgModule({
  // ...
  bootstrap: [AppComponent]
})
export class AppModule {
} 
```

为了告诉 Angular Router 它可以在哪里放置组件，我们必须将`<router-outlet></router-outlet>`元素添加到`AppComponent`的 HTML 模板中。

元素告诉 Angular Router 它可以在哪里实例化 DOM 中的组件。

*如果你熟悉 [AngularJS 1.x router](https://docs.angularjs.org/api/ngRoute/service/%24route) 和 [UI-Router](https://github.com/angular-ui/ui-router) ，你可以考虑`<router-outlet></router-outlet>`的角度替代`ng-view`和`ui-view`。*

如果没有`<router-outlet></router-outlet>`元素，Angular Router 将不知道在哪里放置组件，只会呈现`AppComponent`自己的 HTML。

`AppComponent`当前显示待办事项列表。

但是我们现在希望`AppComponent`包含一个`<router-outlet></router-outlet>`，并告诉 Angular Router 在`AppComponent`中实例化另一个组件来显示待办事项列表，而不是让`AppComponent`显示待办事项列表。

为此，让我们使用 Angular CLI 生成一个新组件`TodosComponent`:

```
$ ng generate component Todos 
```

让我们也将所有的 HTML 从`src/app/app.component.html`移动到`src/app/todos/todos.component.html`:

```
<!-- src/app/todos/todos.component.html -->
<section class="todoapp">
  <app-todo-list-header
    (add)="onAddTodo($event)"
  ></app-todo-list-header>
  <app-todo-list
    [todos]="todos"
    (toggleComplete)="onToggleTodoComplete($event)"
    (remove)="onRemoveTodo($event)"
  ></app-todo-list>
  <app-todo-list-footer
    [todos]="todos"
  ></app-todo-list-footer>
</section> 
```

让我们也将所有逻辑从`src/app/app.component.ts`移到`src/app/todos/todos.component.ts`:

```
/* src/app/todos/todos.component.ts */
import { Component, OnInit } from '@angular/core';
import { TodoDataService } from '../todo-data.service';
import { Todo } from '../todo';

@Component({
  selector: 'app-todos',
  templateUrl: './todos.component.html',
  styleUrls: ['./todos.component.css'],
  providers: [TodoDataService]
})
export class TodosComponent implements OnInit {

  todos: Todo[] = [];

  constructor(
    private todoDataService: TodoDataService
  ) {
  }

  public ngOnInit() {
    this.todoDataService
      .getAllTodos()
      .subscribe(
        (todos) => {
          this.todos = todos;
        }
      );
  }

  onAddTodo(todo) {
    this.todoDataService
      .addTodo(todo)
      .subscribe(
        (newTodo) => {
          this.todos = this.todos.concat(newTodo);
        }
      );
  }

  onToggleTodoComplete(todo) {
    this.todoDataService
      .toggleTodoComplete(todo)
      .subscribe(
        (updatedTodo) => {
          todo = updatedTodo;
        }
      );
  }

  onRemoveTodo(todo) {
    this.todoDataService
      .deleteTodoById(todo.id)
      .subscribe(
        (_) => {
          this.todos = this.todos.filter((t) => t.id !== todo.id);
        }
      );
  }
} 
```

现在我们可以将`src/app/app.component.html`中`AppComponent`的模板替换为:

```
<router-outlet></router-outlet> 
```

我们还可以从`src/app/app.component.ts`中的`AppComponent`的类中删除所有过时的代码:

```
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css'],
})
export class AppComponent {

} 
```

最后，我们在`src/app/app-routing.module.ts`中更新我们的`todos`路线来实例化`TodosComponent`而不是`AppComponent`:

```
const routes: Routes = [
  {
    path: '',
    redirectTo: 'todos',
    pathMatch: 'full'
  },
  {
    path: 'todos',
    component: TodosComponent
  }
]; 
```

现在，当我们的应用程序被引导时，Angular 实例化`AppComponent`并找到一个`<router-outlet></router-outlet>`，Angular Router 可以在这里实例化并激活组件。

让我们在浏览器中尝试我们的更改。

通过运行以下命令启动开发服务器和后端 API:

```
$ ng serve
$ npm run json-server 
```

然后将浏览器导航至`http://localhost:4200`。

Angular Router 读取路由器配置，自动将我们的浏览器重定向到`http://localhost:4200/todos`。

如果您检查页面上的元素，您会看到`TodosComponent`并没有呈现在`<router-outlet></router-outlet>`内部，而是就在它旁边:

```
<app-root>

    <!-- Angular Router finds router outlet -->
    <router-outlet></router-outlet>

    <!-- and places the component right next to it, NOT inside it -->
    <app-todos></app-todos>
</app-root> 
```

我们的应用程序现在启用了路由。厉害！

### 添加通配符路由

当您将浏览器导航到`http://localhost:4200/unmatched-url`，并打开浏览器的开发工具时，您会注意到 Angular Router 将以下错误记录到控制台:

```
Error: Cannot match any routes. URL Segment: 'unmatched-url' 
```

为了优雅地处理不匹配的 URL，我们需要做两件事:

1.  创建`PageNotFoundComponent`(如果你愿意，你可以给它起不同的名字)来显示一条友好的消息，表明无法找到所请求的页面
2.  告诉 Angular Router 当没有路由匹配请求的 URL 时显示`PageNotFoundComponent`。

让我们从使用 Angular CLI 生成`PageNotFoundComponent`开始:

```
$ ng generate component PageNotFound 
```

然后在`src/app/page-not-found/page-not-found.component.html`中编辑其模板:

```
<p>We are sorry, the requested page could not be found.</p> 
```

接下来，我们使用`**`作为路径添加通配符路由:

```
const routes: Routes = [
  {
    path: '',
    redirectTo: 'todos',
    pathMatch: 'full'
  },
  {
    path: 'todos',
    component: AppComponent
  },
  {
    path: '**',
    component: PageNotFoundComponent
  }
]; 
```

`**`匹配任何 URL，包括子路径。

现在，如果您将浏览器导航到`http://localhost:4200/unmatched-url`，就会显示`PageNotFoundComponent`。

请注意，通配符路由必须是路由配置中的最后一个路由，它才能按预期工作。

当 Angular Router 将请求 URL 与路由器配置匹配时，它会在找到第一个匹配项后立即停止处理。

所以如果我们把路线的顺序改成这样:

```
const routes: Routes = [
  {
    path: '',
    redirectTo: 'todos',
    pathMatch: 'full'
  },
  {
    path: '**',
    component: PageNotFoundComponent
  },
  {
    path: 'todos',
    component: AppComponent
  }
]; 
```

那么`todos`将永远不会到达，并且`PageNotFoundComponent`将被显示，因为通配符路由将首先被匹配。

我们已经做了很多，所以让我们快速回顾一下到目前为止我们已经完成的工作:

*   我们设置了角路由器
*   我们为应用程序创建了路由配置
*   我们将`AppComponent`重构为`TodosComponent`
*   我们将`<router-outlet></router-outlet>`添加到了`AppComponent`的模板中
*   我们添加了一个通配符路由来优雅地处理不匹配的 URL。

接下来，我们将创建一个解析器，使用 Angular Router 从后端 API 获取现有的 todos。

## 使用角度路由器解析数据

在本系列第 3 部分中，我们已经学习了如何使用 Angular HTTP 服务从后端 API 获取数据。

目前，当我们将浏览器导航到`todos` URL 时，会发生以下情况:

1.  角路由器匹配`todos` URL
2.  角路由器激活`TodosComponent`
3.  角度路由器将 DOM 中的`TodosComponent`放在`<router-outlet></router-outlet>`旁边
4.  浏览器中显示的`TodosComponent`带有一个空的待办事项数组
5.  todos 是从`TodosComponent`的`ngOnInit`处理程序中的 API 获取的
6.  浏览器中的`TodosComponent`用从 API 获取的 todos 更新。

如果在步骤 5 中加载待办事项需要三秒钟，则在步骤 6 中显示实际待办事项之前，将向用户呈现三秒钟的空待办事项列表。

如果`TodosComponent`的模板中包含以下 HTML:

```
<div *ngIf="!todos.length">
  You currently do not have any todos yet.
</div> 
```

然后，在显示实际的待办事项之前，用户会看到该消息三秒钟，这可能会完全误导用户，并导致用户在实际数据进入之前导航离开。

我们可以给`TodosComponent`添加一个加载器，在加载数据时显示一个微调器，但是有时我们可能无法控制实际的组件，例如当我们使用第三方组件时。

要修复这种不想要的行为，我们需要发生以下情况:

1.  角路由器匹配`todos` URL
2.  角度路由器从 API 获取 todos
3.  角路由器激活`TodosComponent`
4.  角度路由器将 DOM 中的`TodosComponent`放在`<router-outlet></router-outlet>`旁边
5.  `TodosComponent`显示在浏览器中，带有从 API 获取的 todos。

这里，`TodosComponent`直到来自 API 后端的数据可用时才显示。

这正是解决方案可以为我们做的。

要让 Angular Router 在激活`TodosComponent`之前解析 todos，我们必须做两件事:

1.  创建一个从 API 获取 todos 的`TodosResolver`
2.  当激活`todos`路径中的`TodosComponent`时，告诉角度路由器使用`TodosResolver`获取 todos。

通过将解析器附加到`todos`路径，我们要求角度路由器在`TodosComponent`被激活之前首先解析数据。

因此，让我们创建一个解析器来获取我们的 todos。

### 正在创建 TodosResolver

Angular CLI 没有生成解析器的命令，所以让我们手动创建一个新文件`src/todos.resolver.ts`并添加以下代码:

```
import { Injectable } from '@angular/core';
import { ActivatedRouteSnapshot, Resolve, RouterStateSnapshot } from '@angular/router';
import { Observable } from 'rxjs/Observable';
import { Todo } from './todo';
import { TodoDataService } from './todo-data.service';

@Injectable()
export class TodosResolver implements Resolve<Observable<Todo[]>> {

  constructor(
    private todoDataService: TodoDataService
  ) {
  }

  public resolve(
    route: ActivatedRouteSnapshot,
    state: RouterStateSnapshot
  ): Observable<Todo[]> {
    return this.todoDataService.getAllTodos();
  }
} 
```

我们将解析器定义为实现`Resolve`接口的类。

*`Resolve`接口是可选的，但是让我们的 TypeScript IDE 或编译器通过要求我们实现一个`resolve()`方法来确保我们正确地实现了这个类。*

当 Angular Router 需要使用解析器解析数据时，它调用解析器的`resolve()`方法，并期望`resolve()`方法返回一个值、一个承诺或一个可观察值。

如果`resolve()`方法返回一个承诺或一个可观测的角度路由器将等待承诺或可观测的完成，然后激活路由的组件。

当调用`resolve()`方法时，Angular Router 方便地传入激活的路由快照和路由器状态快照，为我们提供可能需要解析数据的数据访问(如路由参数或查询参数)。

`TodosResolver`的代码非常简洁，因为我们已经有了一个`TodoDataService`来处理与 API 后端的所有通信。

我们在构造函数中注入`TodoDataService`,并使用它的`getAllTodos()`方法获取`resolve()`方法中的所有 todos。

resolve 方法返回一个类型为`Todo[]`的可观察对象，因此 Angular Router 将在路由的组件被激活之前等待可观察对象完成。

现在我们有了解析器，让我们配置 Angular Router 来使用它。

### 通过路由器解析 todos

要让角度路由器使用解析器，我们必须在路由配置中将它附加到路由上。

让我们打开`src/app-routing.module.ts`并将我们的`TodosResolver`添加到`todos`路线中:

```
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { PageNotFoundComponent } from './page-not-found/page-not-found.component';
import { TodosComponent } from './todos/todos.component';
import { TodosResolver } from './todos.resolver';

const routes: Routes = [
  {
    path: '',
    redirectTo: 'todos',
    pathMatch: 'full'
  },
  {
    path: 'todos',
    component: TodosComponent,
    resolve: {
      todos: TodosResolver
    }
  },
  {
    path: '**',
    component: PageNotFoundComponent
  }
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule],
  providers: [
    TodosResolver
  ]
})
export class AppRoutingModule {
} 
```

我们导入`TodosResolver`:

```
import { TodosResolver } from './todos.resolver'; 
```

还将其作为解析器添加到`todos`路线中:

```
{
  path: 'todos',
  component: TodosComponent,
  resolve: {
    todos: TodosResolver
  }
} 
```

这告诉 Angular Router 使用`TodosResolver`解析数据，并将解析器的返回值指定为路由数据中的`todos`。

可以从`ActivatedRoute`或`ActivatedRouteSnapshot`访问路线数据，我们将在下一节中看到。

您可以使用路线的`data`属性将静态数据直接添加到路线数据中:

```
{
  path: 'todos',
  component: TodosComponent,
  data: {
    title: 'Example of static route data'
  }
} 
```

您还可以使用路径的`resolve`属性中指定的解析器添加动态数据:

```
resolve: {
  path: 'todos',
  component: TodosComponent,
  resolve: {
    todos: TodosResolver
  }
} 
```

你也可以同时做这两件事:

```
resolve: {
  path: 'todos',
  component: TodosComponent,
  data: {
    title: 'Example of static route data'
  }
  resolve: {
    todos: TodosResolver
  }
} 
```

一旦来自`resolve`属性的解析器被解析，它们的值就与来自`data`属性的静态数据合并，并且所有数据都作为路线的数据可用。

Angular Router 使用角度依赖注入来访问解析器，因此我们必须确保通过将`TodosResolver`添加到`AppRoutingModule`的`@NgModule`元数据中的`providers`属性，向 Angular 的依赖注入系统注册了`TodosResolver`:

```
@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule],
  providers: [
    TodosResolver
  ]
})
export class AppRoutingModule {
} 
```

当您将浏览器导航到`http://localhost:4200`时，Angular Router 现在:

1.  将 URL 从`/`重定向到`/todos`
2.  看到`todos`路线在其`resolve`属性中定义了`TodosResolver`
3.  从`TodosResolver`运行`resolve()`方法，等待结果并将结果分配给路线数据中的`todos`
4.  激活`TodosComponent`。

如果您打开开发人员工具的 network 选项卡，您会看到 todos 现在从 API 中提取了两次。一次由角度路由器执行，一次由`TodosComponent`中的`ngOnInit`处理器执行。

所以 Angular Router 已经从 API 获取了 todos，但是`TodosComponent`仍然使用它自己的内部逻辑来加载 todos。

在下一节中，我们将更新`TodosComponent`以使用 Angular Router 解析的数据。

### 使用解析的数据

让我们打开`app/src/todos/todos.component.ts`。

`ngOnInit()`处理程序目前直接从 API 获取 todos:

```
public ngOnInit() {
  this.todoDataService
    .getAllTodos()
    .subscribe(
      (todos) => {
        this.todos = todos;
      }
    );
} 
```

既然 Angular 路由器使用`TodosResolver`获取 todo，我们想要从路由数据而不是 API 获取`TodosComponent`中的 todo。

要访问路线数据，我们必须从`@angular/router`导入`ActivatedRoute`:

```
import { ActivatedRoute } from '@angular/router'; 
```

并使用角度依赖性注入来获得激活路线的句柄:

```
constructor(
  private todoDataService: TodoDataService,
  private route: ActivatedRoute
) {
} 
```

最后，我们更新`ngOnInit()`处理程序，从路线数据而不是 API 中获取 todos:

```
public ngOnInit() {
  this.route.data
    .map((data) => data['todos'])
    .subscribe(
      (todos) => {
        this.todos = todos;
      }
    );
} 
```

`ActivatedRoute`将路线数据公开为可观察数据，因此我们的代码几乎不变。

我们用`this.route.data.map((data) => data['todos'])`替换`this.todoDataService.getAllTodos()`，其余代码保持不变。

如果您将浏览器导航到`localhost:4200`并打开网络选项卡，您将不再看到两个 HTTP 请求从 API 获取 todos。

任务完成！我们已经成功地将角度路由器集成到我们的应用中！

在我们结束之前，让我们运行我们的单元测试:

```
ng serve 
```

一个单元测试失败:

```
Executed 11 of 11 (1 FAILED)
TodosComponent should create FAILED
    'app-todo-list-header' is not a known element 
```

当`TodosComponent`被测试时，测试床不知道`TodoListHeaderComponent`，因此 Angular 抱怨它不知道`app-todo-list-header`元素。

为了修复这个错误，让我们打开`app/src/todos/todos.component.spec.ts`并将`NO_ERRORS_SCHEMA`添加到`TestBed`选项中:

```
beforeEach(async(() => {
  TestBed.configureTestingModule({
    declarations: [TodosComponent],
    schemas: [
      NO_ERRORS_SCHEMA
    ]
  })
    .compileComponents();
})); 
```

现在 Karma 显示了另一个错误:

```
Executed 11 of 11 (1 FAILED)
TodosComponent should create FAILED
    No provider for ApiService! 
```

让我们为测试平台选项添加必要的提供程序:

```
beforeEach(async(() => {
  TestBed.configureTestingModule({
    declarations: [TodosComponent],
    schemas: [
      NO_ERRORS_SCHEMA
    ],
    providers: [
      TodoDataService,
      {
        provide: ApiService,
        useClass: ApiMockService
      }
    ],
  })
    .compileComponents();
})); 
```

这又引发了另一个错误:

```
Executed 11 of 11 (1 FAILED)
TodosComponent should create FAILED
    No provider for ActivatedRoute!! 
```

让我们为`ActivatedRoute`再添加一个提供者到测试平台选项中:

```
beforeEach(async(() => {
  TestBed.configureTestingModule({
    declarations: [TodosComponent],
    schemas: [
      NO_ERRORS_SCHEMA
    ],
    providers: [
      TodoDataService,
      {
        provide: ApiService,
        useClass: ApiMockService
      },
      {
        provide: ActivatedRoute,
        useValue: {
          data: Observable.of({
            todos: []
          })
        }
      }
    ],
  })
    .compileComponents();
})); 
```

我们为`ActivatedRoute`的提供者分配一个包含可观察数据属性的模拟对象，以公开`todos`的测试值。

现在单元测试成功通过了:

```
Executed 11 of 11 SUCCESS 
```

太棒了。为了将我们的应用程序部署到生产环境中，我们现在可以运行:

```
ng build --aot --environment prod 
```

我们将生成的`dist`目录上传到我们的托管服务器。多甜蜜啊。

我们在这篇文章中已经讨论了很多，所以让我们回顾一下我们所学到的。

## 摘要

在第[篇文章](https://www.sitepoint.com/angular-2-tutorial/)中，我们学习了如何:

*   使用 Angular CLI 初始化我们的 Todo 应用程序
*   创建一个`Todo`类来表示个人待办事项
*   创建一个`TodoDataService`服务来创建、更新和删除 todos
*   使用`AppComponent`组件显示用户界面
*   将我们的应用程序部署到 GitHub 页面

在[的第二篇文章](https://www.sitepoint.com/understanding-component-architecture-angular/)中，我们重构了`AppComponent`，将其大部分工作委托给:

*   显示待办事项列表
*   显示单个待办事项的`TodoListItemComponent`
*   创建新待办事项的`TodoListHeaderComponent`
*   显示还剩多少待办事项。

在第三篇文章中，我们学习了如何:

*   创建一个模拟 REST API 后端
*   将 API URL 存储为环境变量
*   创建一个`ApiService`来与 REST API 通信
*   更新`TodoDataService`以使用新的`ApiService`
*   更新`AppComponent`来处理异步 API 调用
*   创建一个`ApiMockService`来避免运行单元测试时真正的 HTTP 调用。

在第四篇文章中，我们了解到:

*   为什么应用程序可能需要路由
*   JavaScript 路由器是什么
*   什么是角路由器，它是如何工作的，它能为你做什么
*   如何为我们的应用程序设置角度路由器和配置路由
*   如何告诉 Angular Router 在 DOM 中何处放置组件
*   如何优雅地处理未知的网址
*   如何使用解析器让角路由器解析数据？

本文的所有代码都可以在 [GitHub](https://github.com/sitepoint-editors/angular-todo-app/tree/part-4) 获得。

在第五部分中，我们将实现身份验证来防止对应用程序的未授权访问。

所以，请继续关注更多，一如既往，欢迎在评论中留下你的想法和问题！

### 推荐课程

![](img/cba76132dc82ef2ac08afc0e875076ed.png)[Online Courses for Angular and TypeScript](https://ultimateangular.com/)Todd MottoExpert-led online AngularJS, Angular and TypeScript training courses for individuals and teams. **Use coupon code 'SITEPOINT' at checkout to get 25% off**.[](https://ultimateangular.com/)[](https://ultimateangular.com/)

## 分享这篇文章