# 带角度 2 和 Redux 的数据流

> 原文：<https://www.sitepoint.com/data-flows-angular-2-redux/>

本文是微软网站开发系列的一部分。感谢您对使 SitePoint 成为可能的合作伙伴的支持。

*这是 Angular 2 系列的第三部。你可以在这里阅读第二部分[。](https://www.sitepoint.com/angular-2-components-inputs-outputs)*

本文的代码可以在 Github [这里](https://github.com/DevelopIntelligenceBoulder/angular2-samples)找到。浏览到 src/data-flows 文件夹，找到适用的源代码。这个运行在 [Azure](https://azure.microsoft.com/?WT.mc_id=16550-DEV-sitepoint-article86) 节点上的应用可以在[这里](http://angular2-samples.azurewebsites.net/data-flows/index.html)找到。

## Angular 2 中的数据绑定

数据绑定可能是一个敏感的话题。在 Angular 1 之前，它主要是为编译和服务器渲染语言或 UI 框架(如 Silverlight 和 Flex)保留的。当 Angular 1 出现时，我们对数据绑定在 UI 中提供的“魔力”感到惊讶。

如果你读过 Angular 2，你可能听说过诸如“不变性”和“单向数据流”之类的东西。但是这些和 Angular 有什么关系呢？他们和 NgModel 合作吗？当一切都朝一个方向流动时，我们如何管理应用程序的状态。

本文首先介绍如何像处理 Angular 1 一样处理 Angular 2 数据绑定。然后，我们将讨论如何转换为单向数据流，并为此进行必要的修改。

## 简单的角度 1 应用程序

我们会打造我能想到的最基础的`Todo` app。没有样式，只是一个允许用户键入一个`Todo`并按 Enter 键提交的输入。然后是一个待办事项列表，可以将它们标记为已完成。

最终产品看起来像这样

![Simple basic Todo app in Angular 1 and Angular 2](img/76ab8f8ff3e967dd648f092e2a6d5f7f.png)

[微软 Edge](https://www.microsoft.com/en-us/windows/microsoft-edge/?WT.mc_id=16550-DEV-sitepoint-article86) 中的浏览器截图

在 Angular 1 中构建这个应该是相对简单的。我们有一个简单的 HTML 表单，带有一个`ng-submit`(所以我们的表单处理`Enter`键)，一个控制器方法，将我们输入的文本转换成一个新的`Todo`，以及一种切换已完成和未完成待办事项的方法。

标记如下所示:

```
 <h2>Angular 1 Todo App</h2>
<div ng-controller="AddTodo as addTodo">
	<form ng-submit="addTodo.addTodo(todo)">
	<input ng-model="todo" placeholder="Create a Todo"/>
	</form>
</div>
<div ng-controller="TodoList as todoList">
	<ul>
	<li ng-repeat="todo in todoList.todos"
	ng-click="todoList.toggleTodo(todo)"
	ng-class="{completed: todo.completed}"
	>{{todo.title}}</li>
	</ul>
</div> 
```

有一个简单的服务来管理呈现的 Todos 列表。该服务还处理添加和切换每个待办事项的完成状态:

```
 function TodoService () {
	return {
		// Default a couple Todos for initial rendering
		todos: [
			{title: "Read the todo list", completed: true},
			{title: "Look at the code", completed: false}
		],
		add: function (todo) {
			if (!todo.length) {
				return;
			}
			this.todos.push({title: todo, completed: false});
		},
		toggle: function (todo) {
			todo.completed = !todo.completed;
		}
	}
} 
```

当用户键入`<input>`时，他们将添加到“todo”模型属性中。点击 Enter 将提交表单并在`AddTodo`控制器上运行`addTodo()`，如下所示:

```
 functionAddTodoController (TodoService) {
	this.todoService = TodoService;
}

AddTodoController.prototype.addTodo = function (todo) {
	this.todoService.add(todo);
} 
```

最后，Todo 列表呈现在`<ul>`标签中，每个 Todo 都可以运行`toggleTodo()`方法。`TodoList`控制器看起来是这样的:

```
 functionTodoListController (TodoService) {
	this.todoService = TodoService;
	this.todos = this.todoService.todos;
}

TodoListController.prototype.toggleTodo = function (todo) {
	this.todoService.toggle(todo);
} 
```

### 角度 1 总结

虽然这个非常基本的 Angular 1 应用程序可能不是您为生产而编写的(我建议使用指令和/或路由器将视图绑定到控制器，而不是使用`ng-controller`)，但它提供了一个简单的示例，使用基本的数据绑定和脏检查来呈现 Todos 列表。这是一个很好的起点，因为它涉及到 Angular 1 中常见的数据流场景。

## 转换成角度 2

在 Angular 2 中，我们可以用与 Angular 1 相同的方式构建一个应用程序。我们可以使用双向数据绑定(实际上只是两个单向数据绑定，但最终结果是双向数据绑定)和一个服务来管理我们的数据。当数据发生变化时，我们可以依靠脏检查来重新呈现 UI。

那么用 Angular 2 会有什么好处呢？好吧，在看下面的代码样本时，我们会看到一个更清晰的关注点分离。使用组件，我们将应用程序构建成一棵自顶向下的树，每个组件中的功能都可以很容易地推理出来。

以下是我们应用程序的基本组件:

```
 // TodoApp (app.ts)
import {Component, DoCheck} from 'angular2/core';
import {AddTodo, TodoList, Header} from './components';
import {TodoService} from './services/todos';

@Component({
	selector: 'TodoApp',
	template: '
		<Header></Header>
		<AddTodo></AddTodo>
		<TodoList></TodoList>
	',
	directives: [AddTodo, TodoList, Header],
	// Add the TodoService so all children components 

	// have access to the same TodoService instance.
	providers: [TodoService]
})
export class TodoApp {} 
```

这是一个非常基本的组件，但是需要注意的关键部分是组件装饰器中使用的`providers`属性。`TodoService`没有被这个基础组件直接使用，但是将它添加到`providers`列表中意味着任何子组件都将收到`TodoService`的相同实例(为任何子组件创建一个单例)。

注意在`AddTodo`组件中我们是如何注入`TodoService`的，但是它不包含在 providers 属性中。Angular 2 将查找注入器链，直到找到顶层的`TodoService`并注入那个:

```
 // TodoService (services/todos.ts)
import {Injectable} from 'angular2/core';
import {TodoModel} from '../models/todo';

@Injectable()
export class TodoService {
	todos: TodoModel[];
	constructor () {
		this.todos = [
			{title: "Read the todo list", completed: true},
			{title: "Look at the code", completed: false}
		]; 
	}

	add (todo: String): void {
		if (!todo.length) {
			return;
		}
		this.todos.push({title: todo, completed: false});
	}

	toggle (todo: TodoModel): void {
		todo.completed = !todo.completed;
	}
} 
```

现在，`TodoList`获得了对同一个`TodoService`实例的引用，并使用它来呈现 Todos 列表:

```
 // TodoList (components/todoList.ts)
import {Component, Input} from 'angular2/core';
import {Todo} from './todo';
import {TodoService} from '../services/todos';

@Component({
	selector: 'TodoList',
	template: '
		<ul class="list-group">
		<Todo *ngFor="#todo of todos" [todo]="todo"></Todo>
		</ul>
	',
	directives: [Todo]
})
export class TodoList {
	todos;
	constructor (todoService: TodoService) {
		this.todos = todoService.todos;
	}
} 
```

最后，Todo 组件本身呈现 Todo 文本。它还连接了一个`click`事件来切换 Todo 的状态:

```
 // Todo (components/todo.ts)
import {Component, Input} from 'angular2/core';
import {TodoService} from '../services/todos';

@Component({
	selector: 'Todo',
	template: '
		<li [ngClass]="{completed: todo.completed}"
		(click)="toggle(todo)"
		class="list-group-item">
		{{todo.title}}
		</li>
	'
})
export class Todo {
	@Input() todo;
	constructor (public todoService: TodoService) {}
	toggle (todo) {
		this.todoService.toggle(todo);
	}
} 
```

## 角度 2 总结

虽然实现方式不同，但 Angular 1 和 Angular 2 实现方式之间的数据流和概念是相同的。

服务管理正在呈现的待办事项列表。同一服务还管理添加和切换 Todos。两者都使用 TodoService 的单一实例，因此应用程序的所有组件总是引用同一组数据。我们改变了 todo 的数组(以及数组中的单个 todo ),并依靠脏检查来重新渲染。

总的来说，这是一种不错的构建应用程序的方式。但是随着应用程序的增长，组件和它们所呈现的数据之间的依赖关系会变得很麻烦。它会使对应用程序的推理变得困难，并且依赖数据突变和脏检查会使测试变得更加困难。

接下来，我们将研究构建这个简单应用程序的第三种方法:使用单向数据流。

## 单向数据流概述

如果您在过去一两年中一直关注 JavaScript 领域，那么您可能听说过使用单向数据流来管理应用程序。有一些很好的资源解释了什么是单向数据流，但从高层次来看，这个想法是所有数据都来自同一个地方，并被推送到使用它的孩子。考虑为任务列表应用程序设置初始状态或数据存储。您可以定义诸如什么用户登录、应该呈现什么任务等等。当使用单向数据流时，这种初始状态设置看起来没有太大的不同。

当您想要更新一项任务时，这种差异就出现了。我们将向顶级数据存储发送一条消息，告诉它更新其对象图中的某些内容，而不是就地更改(突变)数据模型。更新发生，应用程序被重新呈现。

重新呈现应用程序是单向数据流的关键部分。您可以把它想象成整个应用程序为发生的每一个变化而重新呈现。在现实世界中，这不是高性能的，因此只有应用程序中看到变化的部分才会被重新呈现。

这个概念很大程度上是由 [React](https://facebook.github.io/react/) 框架推广的。有许多库可以用来实现单向数据流，但是在 React 社区中有一个很受欢迎的库，在 Angular 2 社区中越来越受欢迎，它被称为 [Redux](http://redux.js.org/) 。

*对于单向数据流架构的概述，你可以看到[这篇文章](http://survivejs.com/webpack_react/react_and_flux/)描述了脸书的 [Flux 架构](https://facebook.github.io/flux/)。*

## 用于状态管理的 Redux 库

Redux 库有几个基本原则，我们将在构建这个应用程序时看到。我不会深入讨论它们，因为有[个很棒的文档描述它们](http://redux.js.org/docs/introduction/ThreePrinciples.html)，但是我会快速概述一下(直接从上面链接的文章开始):

### 真理的单一来源

整个应用程序的状态存储在单个存储区的对象树中。

```
 console.log(store.getState());

/* Prints
{
	visibilityFilter: 'SHOW_ALL',
	todos: [
		{
			text: 'Consider using Redux',
			completed: true,
		}, 
		{
			text: 'Keep all state in a single tree',
			completed: false
		}
	]
}
*/ 
```

### 状态为只读

改变状态的唯一方法是发出一个动作，一个描述发生了什么的对象。

```
 store.dispatch({
	type: 'COMPLETE_TODO',
	index: 1
})

store.dispatch({
	type: 'SET_VISIBILITY_FILTER',
	filter: 'SHOW_COMPLETED'
}) 
```

### 变化是由纯函数产生的

为了指定状态树是如何被动作转换的，你需要编写纯 reducers。

纯函数是返回值仅由其输入值决定的函数，没有可观察到的副作用(来自函数式编程的[:纯函数](https://www.sitepoint.com/functional-programming-pure-functions/))。

缩减器(也称为缩减函数)是一个接受一个累加值并返回一个新累加值的函数。它们用于将一组值缩减为一个值(来自 redux.js.org 的[词汇表](http://redux.js.org/docs/Glossary.html#reducer))。

todos 还原剂样品:

```
 function todos(state = [], action) {
	switch (action.type) {
		case 'ADD_TODO':
			return [
				...state,
				{
					text: action.text,
					completed: false
				}
			]

		case 'COMPLETE_TODO':
			return [
				...state.slice(0, action.index),
				Object.assign({}, state[action.index], {
					completed: true
				}),
				...state.slice(action.index + 1)
			]

		default:
			return state
	}
} 
```

## 单向数据流的角度 2

现在，我们已经看到了使用双向数据流在 Angular 1 和 Angular 2 中构建的示例应用程序，让我们使用单向数据流在 Angular 2 中重新构建相同的应用程序。

我们将使用 Redux 库来管理状态。我们需要创建商店，并为我们的 Todos 数组传入一个 reducer。它看起来像这样:

```
 // todo reducer (reducers/todos.ts)
import {TodoModel} from '../models/todo';

const initialState = [
	{title: "Read the todo list", completed: true},
	{title: "Look at the code", completed: false}
];

export function todos (state: TodoModel[] = initialState, action) {
	// Redux reducers typically use a switch statement 
	// to determine how to handle a given action
	switch (action.type) {
		case 'ADD_TODO':
			return [
				...state,
				{
					title: action.title,
					completed: false
				}
			]

		case 'TOGGLE_TODO':
			let todo = state[action.index];
			let toggledTodo = Object.assign({}, todo, {
				completed: !todo.completed
			}); 
			return [
				...state.slice(0, action.index),
				toggledTodo,
				...state.slice(action.index + 1)
			]

		default:
			return state;
	}
} 
```

让我们来分解这个缩减器的各个部分，因为应用程序的大部分逻辑都包含在其中。

### 默认状态

Redux 调用`createState`并接收到上面的 reducer 时，会调用带有`undefined`状态和动作参数的 reducer 函数。所以我们需要返回默认状态。因此:

```
 // Variable to store the first two default Todos
const initialState = [
	{title: "Read the todo list", completed: true},
	{title: "Look at the code", completed: false}
];

// Defaulting the "state" parameter to "initialState". The 
// Redux createState() function will call todos() with undefined 
// "state" param making the default state "initalState" value
export function todos (state: TodoModel[] = initialState, action) {
	switch (action.type):
		...

		// No action, return the state as-is
		default:
			return state;
} 
```

### 添加待办事项

当我们分派类型为`ADD_TODO`的动作时，我们需要返回一个新的状态数组，并附加上新的 Todo。为什么要返回一个新的数组，而不是将新的 Todo 推送到现有的数组上？如果你记得 Redux 的三个核心原则，第一个是状态是只读的。所以我们必须返回一个新的数组。但是数组中的 Todos 可以通过引用返回，因为它们没有变异。

为了方便起见，我们使用 ES6 语法用旧数组的内容生成一个新数组，并附加新的 Todo:

```
 case'ADD_TODO':
	return [
		...state,
		{
			title: action.title,
			completed: false
		}
	] 
```

### 切换待办事项

从概念上来说，切换 Todo 就是将`completed`属性从 true 更改为 false 或者从 false 更改为 true。然而，我们知道我们不能改变状态树中的任何东西。所以我们需要返回一个新的对象。

多亏了 ES6，我们有了`Object.assign(target, ...sources)`将源参数的属性赋给目标参数。[关于`Object.assign`的很好的解释可以在 MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign) 上找到。

```
 case'TOGGLE_TODO':
    let todo = state[action.index];
    let toggledTodo = Object.assign({}, todo, {
	completed: !todo.completed
    });
    return [
	...state.slice(0, action.index),
	toggledTodo,
	...state.slice(action.index + 1)
    ] 
```

所以切换 Todo 的结果是一个新数组。除了我们实际切换的 Todo 之外，数组的元素基本上没有变化(引用的值相同)。切换的 Todo 将是 Todo 的新实例，其值与旧 Todo 的值相同，但切换了 completed 属性。

### 接上电线

现在我们需要连接这个缩减器来创建一个状态树。有很多方法可以做到这一点，但是出于本文的目的，我将创建一个`TodoStore`模块。该模块提供了对状态的访问，并发布了分派动作来更新状态树的函数。

```
 // TodoStore (store.ts)
import {combineReducers, createStore, Store} from 'redux';
import {todos} from './reducers/todos';

// Create the store using the todos reducer
export const TodoStore: Store = createStore(todos);

// Publish action to add a new Todo
export const addTodo: Function = (title: String) : void => {
	// Dispatch will publish an action of type 'ADD_TODO', which
	// runs through the reducer and gets caught in the 
	// case 'ADD_TODO': block
	TodoStore.dispatch({
		type: 'ADD_TODO',
		title
	})
}

// Publish action to toggle a Todo
export const toggleTodo: Function = (index: Number) : void => {
	TodoStore.dispatch({
		type: 'TOGGLE_TODO',
		index
	})
} 
```

现在我们需要在我们的应用程序中使用这个 TodoStore。由于这是一个非常简单的应用程序，我们基本上只有一个顶级的 TodoApp 组件，我们可以将数据从根组件向下传递给所有的子组件。

在根组件的构造函数中，我们可以抓住 TodoStore。关键是我们需要订阅对 TodoStore 的更改，这是通过`subscribe()`方法完成的。任何时候 TodoStore 发生变化，我们都可以重新分配引用它的组件变量。这意味着任何时候商店改变，身份检查`(===)`将显示对象是不同的，Angular 将更新视图。

新组件现在看起来像这样:

```
 // TodoApp (app.ts)
import {Component} from 'angular2/core';
import {AddTodo, TodoList, Header} from './components';
import {TodoStore} from './store';

@Component({
	selector: 'TodoApp2',
	template: '
		<Header></Header>
		<AddTodo></AddTodo>
		<TodoList [todos]="todos"></TodoList>
	',
	directives: [AddTodo, TodoList, Header]
})
export class TodoApp {
	store = TodoStore;
	todos = this.store.getState();

	constructor () {
		this.store.subscribe(() => {
			this.todos = this.store.getState();
		})
	}
} 
```

注意,`todos`属性最初被设置为默认状态。然后，每次存储发生变化时，我们都会获得新的状态，并将其赋回 todos 属性。属性`todos`的值被传递给`TodoList`组件。

```
 // TodoList (components/todoList.ts)
import {Component, Input} from 'angular2/core';
import {Todo} from './todo';

@Component({
	selector: 'TodoList',
	template: '
		<ul class="list-group">
		<Todo *ngFor="#todo of todos; #i = index;" [todo]="todo" [index]="i"></Todo>
		</ul>
	',
	directives: [Todo]
})
export class TodoList {
	@Input() todos;
} 
```

除了将索引传递给 Todo 组件之外，这里没有什么大的变化。这是处理对正确 Todo 的引用的一种方式。另一种方法是将 id 分配给每个 Todo，并传递 id 而不是数组引用。在生产应用程序中，我们倾向于使用 IDs。

```
 // Todo (components/todo.ts)
import {Component, Input} from 'angular2/core';
import {toggleTodo} from '../store';

@Component({
	selector: 'Todo',
	template: '
		<li [ngClass]="{completed: todo.completed}"
		(click)="toggleTodo(index)"
		class="list-group-item">
		{{todo.title}}
		</li>
	'
})
export class Todo {
	@Input() todo;
	@Input() index;

	// Pass through to the TodoStore
	toggleTodo = toggleTodo;
} 
```

`Todo`组件现在可以引用来自`TodoStore`模块的`toggleTodo`动作。所以每次用户点击一个 Todo，一个动作就会被调度，状态树就会被重新创建。

最后一个操作是添加新的 Todo。类似于切换 Todo，我们将切换委托给 TodoStore:

```
 // AddTodo (components/addTodo.ts)
import {Component, Output, EventEmitter} from 'angular2/core';
import {addTodo} from '../store';

@Component({
	selector: 'AddTodo',
	template: '
		<form (submit)="addTodo(newTodo)" class="panel panel-default">
		<input class="form-control" [(ngModel)]="newTodo"/>
		</form>
	'
})
export class AddTodo {
	addTodo = addTodo;
} 
```

## 摘要

这个应用程序给出了一个 Angular 2 中单向数据流的简单例子。如果我们要构建一个更大、更复杂的应用程序，我们可以遵循类似的模式。创建一个或多个 reducer，将它们添加到我们的数据存储中，并提供可用于更新存储的操作。整个应用程序的状态可以用这个单一的状态对象来表示。

考虑将此应用于更大的应用程序，这个概念与 Angular 2 应用程序的结构非常相似。状态树中的一个节点代表组件树中的一个节点。状态树中的子组件将代表组件树中的子组件。通过使用只读数据，Angular 的脏检查周期可以针对身份检查而不是深度对象图进行优化。

## 更多的 Web 开发实践

本文是微软和 [DevelopIntelligenceon](http://www.developintelligence.com/) 的 web 开发系列的一部分，实用的 JavaScript 学习、开源项目和互操作性最佳实践，包括[微软 Edge](http://blogs.windows.com/msedgedev/2015/05/06/a-break-from-the-past-part-2-saying-goodbye-to-activex-vbscript-attachevent/?WT.mc_id=16550-DEV-sitepoint-article86) 浏览器和新的 [EdgeHTML 渲染引擎](http://blogs.windows.com/msedgedev/2015/02/26/a-break-from-the-past-the-birth-of-microsofts-new-web-rendering-engine/?WT.mc_id=16550-DEV-sitepoint-article86)。

[DevelopIntelligence](http://www.developintelligence.com/) 为技术团队和组织提供讲师指导的 [JavaScript 培训](http://www.developintelligence.com/catalog/javascript-training)、 [AngularJS 培训](http://www.developintelligence.com/catalog/web-development-training/angularjs)和其他 [Web 开发培训](http://www.developintelligence.com/catalog/web-development-training)。

我们鼓励您在 dev.microsoftedge.com 使用免费工具进行跨浏览器和设备测试，包括 Windows 10 的默认浏览器 Microsoft Edge:

*   [扫描你的网站，寻找过时的库、布局问题和可访问性](http://dev.modern.ie/tools/staticscan/?utm_source=SitePoint&utm_medium=article86&utm_campaign=SitePoint)
*   [下载适用于 Mac、Linux 和 Windows 的免费虚拟机](http://dev.modern.ie/tools/vms/windows/?utm_source=SitePoint&utm_medium=article86&utm_campaign=SitePoint)
*   [跨浏览器检查网络平台状态，包括微软 Edge 路线图](https://dev.modern.ie/platform/status/?utm_source=SitePoint&utm_medium=article86&utm_campaign=SitePoint)
*   [在您自己的设备上远程测试 Microsoft Edge](https://remote.modern.ie/?utm_source=SitePoint&utm_medium=article86&utm_campaign=SitePoint)

从我们的工程师和传道者那里获得更深入的学习:

*   **互操作性最佳实践** ( [系列](https://channel9.msdn.com/Blogs/BeLux-Developer/Riding-the-Modern-Web-5-things-to-consider-as-a-web-developer?WT.mc_id=16550-DEV-sitepoint-article86)):
    *   [如何避免浏览器检测](https://channel9.msdn.com/Blogs/BeLux-Developer/Riding-the-Modern-Web-Avoiding-Browser-Detection?WT.mc_id=16550-DEV-sitepoint-article86)
    *   [使用 CSS 前缀的最佳实践](https://channel9.msdn.com/Blogs/BeLux-Developer/Riding-the-Modern-Web-CSS-Vendor-Prefixes?WT.mc_id=16550-DEV-sitepoint-article86)
    *   [保持你的 JS 框架&库更新](https://channel9.msdn.com/Blogs/BeLux-Developer/Riding-the-Modern-Web-Dealing-with-JavaScript-Libraries?WT.mc_id=16550-DEV-sitepoint-article86)
    *   [构建插件免费网络体验](https://channel9.msdn.com/Blogs/BeLux-Developer/Riding-the-Modern-Web-Dealing-with-Plugins?WT.mc_id=16550-DEV-sitepoint-article86)
*   GitHub 上的编码实验室:跨浏览器测试和最佳实践
*   哇，我可以在 Mac 电脑上测试 Edge & IE 浏览器& Linux！(来自雷伊·班戈)
*   [在不破坏网络的情况下推进 JavaScript】(来自 Christian Heilmann)](http://channel9.msdn.com/Events/WebPlatformSummit/2015/Advancing-JavaScript-without-breaking-the-web?WT.mc_id=16550-DEV-sitepoint-article86)
*   [用 WebGL 释放 3D 渲染](https://channel9.msdn.com/Events/WebPlatformSummit/2015/Unleash-3D-rendering-with-WebGL-and-Microsoft-Edge?WT.mc_id=16550-DEV-sitepoint-article86)(来自 David Catuhe)
*   托管网络应用和网络平台创新

我们的社区开源项目:

*   伏龙。JS (跨设备远程 JavaScript 测试)
*   [manifoldJS](http://manifoldjs.com/?WT.mc_id=16550-DEV-sitepoint-article86) (部署跨平台托管的 web 应用)
*   [babylonJS](http://babylonjs.com/?WT.mc_id=16550-DEV-sitepoint-article86) (轻松制作 3D 图形)

更多免费工具和后端 web 开发工具:

*   [Visual Studio 代码](https://code.visualstudio.com/?WT.mc_id=16550-DEV-sitepoint-article86)(用于 Mac、Linux 或 Windows 的轻量级代码编辑器)
*   [Visual Studio 开发基础知识](https://www.visualstudio.com/en-us/products/visual-studio-dev-essentials-vs.aspx?WT.mc_id=16550-DEV-sitepoint-article86)(基于订阅的免费培训和云优势)
*   [用节点编码。JS](https://www.microsoftvirtualacademy.com/en-US/training-courses/building-apps-with-node-js-jump-start-8422?WT.mc_id=16550-DEV-sitepoint-article86) 与[在蔚蓝云上试用](https://azure.microsoft.com/en-us/pricing/free-trial/?WT.mc_id=16550-DEV-sitepoint-article86)

## 分享这篇文章