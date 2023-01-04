# JavaScript MV*框架的剖析

> 原文：<https://www.sitepoint.com/anatomy-javascript-mv-framework/>

快速学习 JavaScript MV*框架的关键是将它们分解成一系列特性。MV*应用的主要特性是路由、数据绑定、模板/视图、模型和数据访问。在这篇文章中，我将描述这些特性，并展示每个特性的 AngularJS、Backbone 和 Ember 代码示例。你将开始具体理解这些框架试图帮助你完成什么，并意识到它们更多的是相似而不是不同。事实上，显而易见的是，大多数框架大量借鉴了其他框架的成功之处。

不要太在意理解每一行代码。现在，试着去欣赏它们是多么的相似，以及它们能为你的项目解决的问题。

## 按指定路线发送

路由至少将您的 URL 映射到一个函数，但有时甚至会实现一个完整的“状态机”设计模式来管理视图中的状态转换。如果你曾经在服务器端 MVC 框架中使用过路由器，比如 Rails、CodeIgniter、CakePHP、ASP.NET MVC 等等。，那么您可以把 JavaScript MV*路由器想象成同样的东西，但是在客户端上以 JavaScript 运行。

你可能想知道这是如何工作的，在旧的浏览器上也能工作吗？URL 中散列标签之后的所有内容都被认为是路由，但是，如果配置了 HTML 推送状态支持(在大多数框架中只有一行代码),那么没有与路由匹配的散列的 URL 将在客户端被拦截，并运行 JavaScript。

足够的细节让我们看看一些代码。

### 主干示例

以下是 Backbone.js 中路由的一个简单示例:

[https://jsfiddle.net/cmckeachie/WSX5E/embedded/js,html,result/light](https://jsfiddle.net/cmckeachie/WSX5E/embedded/js,html,result/light)

注意这个`AppRouter`物体。路线被映射到功能。这些函数只是创建一个视图对象，该对象管理一个 DOM 片段，并在 URL 改变时将它添加到页面中。`Backbone.history.start()`告诉 Backbone 开始监听 URL 的变化。

### 角度示例

以下是 AngularJS 布线的一个简单示例:

[https://jsfiddle.net/cmckeachie/mtV62/embedded/js,html,result/light](https://jsfiddle.net/cmckeachie/mtV62/embedded/js,html,result/light)

AngularJS 示例与主干示例非常相似，除了路由映射到`templateUrl`和控制器功能。

### 余烬示例

下面是 Ember 中路由的一个简单示例:

[https://jsfiddle.net/cmckeachie/QZW8T/embedded/js,html,result/light](https://jsfiddle.net/cmckeachie/QZW8T/embedded/js,html,result/light)

同样，除了 Ember.js 之外，与其他的非常相似，路由器的“资源”对象的第一个参数是一个`routeName`，第二个是 URL。这些参数的顺序一开始让我很困惑，直到有人指出 path 参数是可选的，并且经常可以按照约定进行设置，就像示例中的 about 页面一样。此外，这个简单的路由示例需要 Ember 模板，但是我将在后面的部分中介绍它们。现在，知道模板被放置在`{{outlet}}`中就足够了。

## 数据绑定

数据绑定允许在视图中更新模型数据中的变化和/或在模型中自动更新视图中的变化，而无需额外的代码。单向数据绑定通常表示对模型的更改会传播到视图。双向数据绑定增加了视图变化立即显示在模型上的能力。数据绑定消除了开发人员编写的大量样板代码，使开发人员能够专注于应用程序中的独特问题。

### 角度示例

下面是 AngularJS 中双向数据绑定的一个简单例子。在输入字段中键入内容将在欢迎消息后显示输入的文本。

[https://jsfiddle.net/cmckeachie/Y8Jg6/embedded/html,js,result/light](https://jsfiddle.net/cmckeachie/Y8Jg6/embedded/html,js,result/light)

### 主干示例

Backbone 没有自动数据绑定，但是手动绑定是可能的。在实践中，我发现单向数据绑定非常有用，它可以在模型发生变化时更新视图。从视图到模型真实用例的数据绑定不太常见。

下面是一个简单的例子，其中实现了代码来绑定两种方式。

[https://jsfiddle.net/cmckeachie/7fGrD/embedded/js,html,result/light](https://jsfiddle.net/cmckeachie/7fGrD/embedded/js,html,result/light)

总之，您将侦听模型上的更改事件，并调用视图的 render 属性来让模型更新视图。类似地，您在一个输入上监听`keyup`,通过用 jQuery 从输入中获取值并在模型上设置它来改变模型，让视图更新模型。这个例子应该让您感受到数据绑定工作需要多少代码。同样值得注意的是，有许多插件增加了对 Backbone 数据绑定的支持。

### 余烬示例

Ember 中的数据绑定如下所示:

[https://jsfiddle.net/cmckeachie/gXcfL/embedded/js,html,result/light](https://jsfiddle.net/cmckeachie/gXcfL/embedded/js,html,result/light)

Ember 使用熟悉的手柄进行模板化，但是框架还包括“输入助手”来绑定常见的表单输入字段。在这个例子中，花括号`{{`代替了输入中的尖括号`<`,并且`name`属性没有引号，所以助手知道要绑定它。

## 模板/视图

模板可以是整个 HTML 页面，但更常见的是包含用于动态数据的数据绑定占位符表达式的较小 HTML 片段。它们可以是无逻辑的，认为视图中应该有很少甚至没有逻辑，而其他的允许你直接在模板中嵌入 JavaScript。模板可以基于 DOM 并使用 DOM 动态插入动态数据，也可以基于字符串，将 HTML 视为字符串并替换动态部分。

让我们看一些例子。

### 角度示例

下面是 AngularJS 中的一个简单模板示例。

[https://jsfiddle.net/cmckeachie/aq676/embedded/js,html,result/light](https://jsfiddle.net/cmckeachie/aq676/embedded/js,html,result/light)

您会注意到这与前面的路由示例非常相似，只是添加了一些数据绑定，以显示模板如何在您的应用程序中发挥作用。模板都包含在主 HTML 文件的`script`标签中，以使该示例在 jsfiddle.net 中易于理解和工作，但是在配置`$routeProvider`时，通过给`templateUrl`属性一个有效的文件路径，模板可以在 AngularJS 中的视图外部。

在关注性能的大规模应用中，处理模板的首选方式是在编译时将 AngularJS 模板连接并注册到 Angular `$templateCache`中，执行一个构建任务，如[这个](https://www.npmjs.org/package/grunt-angular-templates)。

### 余烬示例

下面是 Ember 中模板的一个例子。

[https://jsfiddle.net/cmckeachie/79e6c/embedded/js,html,result/light](https://jsfiddle.net/cmckeachie/79e6c/embedded/js,html,result/light)

Ember route 是一个告诉模板应该显示哪个模型的对象。我认为它是您的模板和资源(URL)的最基本的控制器，其主要工作是加载模型。如果你需要更有趣的东西并存储应用程序状态，那么你需要一个控制器。

### 主干示例

现在，让我们看一个主干模板的简单例子。

[https://jsfiddle.net/cmckeachie/hvqjG/embedded/js,html,result/light](https://jsfiddle.net/cmckeachie/hvqjG/embedded/js,html,result/light)

这是对 routing 示例的修改，但不是将 HTML 硬编码在 view 对象的 template 属性中，而是将标记放在 HTML 页面中具有`id`属性的`script`标记内(浏览器忽略不识别类型的脚本标记，如 text/template，因此不会显示或执行模板)。为了获得模板(HTML 片段)，我们使用 jQuery 选择器通过`script`标签的`id`找到元素，获取`innerHTML`，然后将 HTML 分配给视图对象的模板属性(它只是一个字符串)。

## 模型

模型是通常被称为业务对象、领域对象或实体的客户端版本。一般来说，客户端 MV*框架中模型背后的思想是为应用中的数据以及应该与该数据一起封装的任何行为建立一个中心点。这种模型可以与服务器端 MVC 加 jQuery 架构形成对比，后者的模型数据通常存储在 DOM 中。通过拥有一个模型，目标是从 DOM 中移除数据和状态，并将其放在一个可以重用的公共位置。

### 主干示例

模型保存数据，将数据保存在 DOM 之外，并发出类似于`change`的事件，这允许许多视图做出相应的反应，并在任何需要的地方更新用户界面。这给了你一个事实的来源，不是用户界面。

[https://jsfiddle.net/cmckeachie/8AH3t/embedded/js,html,result/light](https://jsfiddle.net/cmckeachie/8AH3t/embedded/js,html,result/light)

我修改了前面的数据绑定示例，添加了一个新的模板和视图来查看同一个 Person 模型对象。以前，我动态地声明了 Person 模型以保持简单，但现在我添加了对`Backbone.Model.extend()`的调用，以演示如何为一个模型创建原型，该模型可以像经典语言中的类一样反复使用。注意两个视图是如何监听同一个人员模型对象(变更事件)并更新它们自己的。有了这个单一的数据源，对特定 DOM 元素的大量调用就可以封装在它们自己整洁的视图中，一个模型就可以满足所有需求。

### 角度示例

AngularJS 中存在一个模型的想法，即你的应用程序中的状态是真实的，但 Angular 允许你使用普通的旧 JavaScript 对象作为你的模型，然后用指令`ng-model`将观察器添加到视图中数据绑定的任何属性中。然后，这些观察器会自动提醒应用程序中绑定到同一模型的其他部分，这些 DOM 元素知道如何更新自己。

下面是更新后的 AngularJS 数据绑定示例，展示了视图的两个更新部分。

[https://jsfiddle.net/cmckeachie/k5GqX/embedded/html,js,result/light](https://jsfiddle.net/cmckeachie/k5GqX/embedded/html,js,result/light)

## 数据存取

数据访问是关于如何为应用程序获取和保存数据。一般来说，框架假设您正在调用一个返回 JSON 的 API。

### 角度示例

AngularJS 以两种不同的方式处理数据。首先，通过`$http`以与 jQuery 的`$.ajax`功能非常相似的方式提供对手动 Ajax 调用的支持。此外，如果你的后端是一个严格的 RESTful 服务，AngularJS 提供了一个`$resource`类，使得对 RESTful 服务的调用非常简洁。

#### `$http`举例

```
app.factory('myService', function($http) {
  return {
    getFooOldSchool: function(callback) {
      $http.get('foo.json').success(callback);
    }
  };
});

app.controller('MainCtrl', function($scope, myService) {
  myService.getFooOldSchool(function(data) {
    $scope.foo = data;
  });
});
```

#### `$resource`举例

```
//create a todo
var todo1 = new Todo();
todo1.foo = 'bar';
todo1.something = 123;
todo1.$save();

//get and update a todo
var todo2 = Todo.get({id: 123});
todo2.foo += '!';
todo2.$save();

//delete a todo
Todo.$delete({id: 123});
```

### 主干示例

Backbone 假设您正在与 RESTful API 进行交互，但是如果没有，它允许您覆盖一个方法`Backbone.sync()`。您告诉您的模型资源在服务器上的位置(URL)，然后您就可以调用`save()`。

```
var UserModel = Backbone.Model.extend({
  urlRoot: '/user',
  defaults: {
    name: '',
    email: ''
  }
});
var user = new Usermodel();
// Notice that we haven't set an `id`
var userDetails = {
  name: 'Craig',
  email: 'craigmc@funnyant.com'
};
// Because we have not set an `id` the server will call
// POST /user with a payload of {name:'Craig', email: 'craigmc@funnyant.com'}
// The server should save the data and return a response containing the new `id`
user.save(userDetails, {
  success: function (user) {
    alert(user.toJSON());
  }
});
```

### 余烬示例

Ember 拥有 Ember Data，从技术上讲，它不是核心框架的一部分，但它致力于提供一个更健壮的数据持久性/数据存储故事。它提供了许多您在服务器端 ORM(如 ActiveRecord)中可以找到的工具，但它是专门为浏览器中独特的 JavaScript 环境而设计的。在撰写本文时，Ember 核心团队即将发布 1.0 版，但还没有，许多 Ember 项目只是简单地使用 jQuery 中的`$.ajax`方法，就像 AngularJS 在上面的例子中使用`$http`一样。

## 结论

这篇文章按照特性对 JavaScript MV*框架进行了分类，以深入了解这些框架提供了哪些功能，并让读者意识到它们实际上非常相似。一旦你理解了框架的特性以及它们是如何组合在一起的，那么快速学习多种框架并为你的项目找到合适的框架就变得容易多了。

## 分享这篇文章