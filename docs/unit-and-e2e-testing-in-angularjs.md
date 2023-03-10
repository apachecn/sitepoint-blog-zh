# AngularJS 中的单元和端到端测试

> 原文：<https://www.sitepoint.com/unit-and-e2e-testing-in-angularjs/>

单元测试是一种帮助开发人员验证孤立代码片段的技术。当您想要确定一组组件集成在一起时，是否能按预期工作时，端到端测试(E2E)就开始发挥作用了。AngularJS 是一个现代的 JavaScript MVC 框架，提供了对单元测试和 E2E 测试的全面支持。在开发 Angular 应用程序的同时编写测试可以节省你大量的时间，否则你会浪费这些时间去修复意想不到的错误。本教程将解释如何将单元测试和 E2E 测试合并到一个角度应用程序中。本教程假设您熟悉 AngularJS 开发。您还应该熟悉构成角度应用程序的不同组件。

我们将使用 Jasmine 作为测试框架，Karma 作为测试运行程序。你可以使用 [Yeoman](http://yeoman.io/) 轻松地为你搭建一个项目，或者只是快速地从 GitHub 获取 [angular seed 应用](https://github.com/angular/angular-seed)。

如果您没有测试环境，只需遵循以下步骤:

1.  下载并安装 [Node.js](http://nodejs.org/) ，如果你还没有的话。
2.  使用 npm ( `npm install -g karma`)安装 Karma。
3.  从 GitHub 下载本教程的[演示 app](https://github.com/jsprodotcom/source/blob/master/angular-seed.zip) 并解压。

在解压后的应用程序中，您可以在`test/unit`和`test/e2e`目录中找到测试。要查看单元测试的结果，只需运行`scripts/test.bat`，这将启动 Karma 服务器。我们的主 HTML 文件是`app/notes.html`，可以在[http://localhost/angular-seed/app/notes . HTML](http://localhost/angular-seed/app/notes.html)访问。

## 单元测试入门

不要只看单元测试是如何编写的，让我们构建一个简单的 Angular 应用程序，看看单元测试如何适应开发过程。所以，让我们从一个应用程序开始，同时对不同的组件进行单元测试。在本节中，您将学习如何进行单元测试:

*   控制器
*   指令
*   过滤
*   工厂

我们将建立一个非常简单的待办事项记录应用程序。我们的标记将包含一个文本字段，用户可以在其中写一个简单的注释。当按下按钮时，便笺会添加到便笺列表中。我们将使用 HTML5 [本地存储](https://www.sitepoint.com/an-overview-of-the-web-storage-api/)来存储笔记。初始的 HTML 标记如下所示。Bootstrap 用于快速构建布局。

```
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
<html ng-app="todoApp">
  <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1"/>
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.2.2/angular.min.js" type="text/javascript"></script>
    <script src="//netdna.bootstrapcdn.com/bootstrap/3.0.3/js/bootstrap.min.js" type="text/javascript"></script>
    <link rel="stylesheet" href="//netdna.bootstrapcdn.com/bootstrap/3.0.3/css/bootstrap.min.css" type="text/css"/>
    <script type="text/javascript" src="js/app.js"></script>
    <style>
      .center-grey{
             background:#f2f2f2;
             margin-top:20;
      }
      .top-buffer {
              margin-top:20px; 
      }
      button{
              display: block; 
              width: 100%;
      }
    </style>
    <title>Angular Todo Note App</title>
  </head>
  <body>
    <div class="container center-grey" ng-controller="TodoController">
      <div class="row top-buffer" >
        <span class="col-md-3"></span>
        <span class="col-md-5">
          <input class="form-control" type="text" ng-model="note" placeholder="Add a note here"/> 
        </span>
        <span class="col-md-1">
          <button ng-click="createNote()" class="btn btn-success">Add</button>
        </span>
        <span class="col-md-3"></span>
      </div>
      <div class="row top-buffer" >
        <span class="col-md-3"></span>
        <span class="col-md-6">
          <ul class="list-group">
            <li ng-repeat="note in notes track by $index" class="list-group-item">
              <span>{{note}}</span>
            </li>
          </ul>
        </span>
        <span class="col-md-3"></span>
      </div>
    </div>
  </body>
</html>
```

在上面的标记中可以看到，我们的角度模块是`todoApp`，控制器是`TodoController`。输入文本被绑定到`note`模型。还有一个列表，显示所有已添加的笔记项目。此外，当点击按钮时，我们的`TodoController`的`createNote()`功能运行。现在让我们打开附带的`app.js`文件，创建模块和控制器。将以下代码添加到`app.js`。

```
var todoApp = angular.module('todoApp',[]);

todoApp.controller('TodoController', function($scope, notesFactory) {
  $scope.notes = notesFactory.get();
  $scope.createNote = function() {
    notesFactory.put($scope.note);
    $scope.note = '';
    $scope.notes = notesFactory.get();
  }
});

todoApp.factory('notesFactory', function() {
  return {
    put: function(note) {
      localStorage.setItem('todo' + (Object.keys(localStorage).length + 1), note);
    },
    get: function() {
      var notes = [];
      var keys = Object.keys(localStorage);

      for(var i = 0; i < keys.length; i++) {
        notes.push(localStorage.getItem(keys[i]));
      }

      return notes;
    }
  };
});
```

我们的`TodoController`使用一个名为`notesFactory`的工厂来存储和检索笔记。当`createNote()`函数运行时，它使用工厂将一个注释放入`localStorage`中，然后清除`note`模型。因此，如果我们要对`TodoController`进行单元测试，我们需要确保当控制器初始化时，`scope`包含一定数量的音符。运行示波器的`createNote()`功能后，音符的数量应该比上一次计数多 1。我们的单元测试代码如下所示。

```
describe('TodoController Test', function() {
  beforeEach(module('todoApp')); // will be run before each it() function

  // we don't need the real factory here. so, we will use a fake one.
  var mockService = {
    notes: ['note1', 'note2'], //just two elements initially
    get: function() {
      return this.notes;
    },
    put: function(content) {
      this.notes.push(content);
    }
  };

  // now the real thing: test spec
  it('should return notes array with two elements initially and then add one',
    inject(function($rootScope, $controller) { //injects the dependencies
      var scope = $rootScope.$new();

      // while creating the controller we have to inject the dependencies too.
      var ctrl = $controller('TodoController', {$scope: scope, notesFactory:mockService});

      // the initial count should be two
      expect(scope.notes.length).toBe(2);

      // enter a new note (Just like typing something into text box)
      scope.note = 'test3';

      // now run the function that adds a new note (the result of hitting the button in HTML)
      scope.createNote();

      // expect the count of notes to have been increased by one!
      expect(scope.notes.length).toBe(3);
    })
  );
});
```

### 说明

方法定义了测试套件。它只是说套件中包含哪些测试。在里面，我们有一个在每个`it()`函数运行之前执行的`beforeEach()`函数。`it()`函数是我们的测试规范，有实际的测试要进行。因此，在执行每个测试之前，我们需要加载我们的模块。

由于这是一个单元测试，我们不需要外部依赖。你已经知道我们的控制器依赖于`notesFactory`来处理笔记。因此，为了对控制器进行单元测试，我们需要使用一个模拟工厂或服务。这就是为什么我们创造了`mockService`，它只是模拟了真实的`notesFactory`，并具有相同的功能，`get()`和`put()`。我们的真工厂使用`localStorage`来存储注释，而假工厂使用底层数组。

现在让我们检查用于执行测试的`it()`函数。你可以看到它声明了两个依赖关系`$rootScope`和`$controller`，这两个依赖关系是 Angular 自动注入的。这两个服务分别是获取应用程序的根范围和创建控制器所必需的。

`$controller`服务需要两个参数。第一个是要创建的控制器的名称。第二个是表示控制器依赖关系的对象。`$rootScope.$new()`返回一个新的子作用域，这是我们的控制器所需要的。请注意，我们还将我们的假工厂实现传递给了控制器。

现在，`expect(scope.notes.length).toBe(2)`断言当控制器初始化时`scope.notes`正好包含两个音符。如果它有多于或少于两个音符，这个测试将失败。类似地，我们用一个新项目填充`note`模型，并运行`createNote()`函数，该函数将添加一个新的注释。现在`expect(scope.notes.length).toBe(3)`对此进行检查。因为一开始我们用两个条目初始化了我们的数组，在运行`createNote()`之后，它应该还有一个(三个条目)。你可以在 Karma 中看到哪些测试失败/成功。

### 测试工厂

现在我们要对工厂进行单元测试，以确保它按预期工作。`notesFactory`的测试用例如下所示。

```
describe('notesFactory tests', function() {
  var factory;

  // excuted before each "it()" is run.
  beforeEach(function() {
    // load the module
    module('todoApp');

    // inject your factory for testing
    inject(function(notesFactory) {
      factory = notesFactory;
    });

    var store = {
      todo1: 'test1',
      todo2: 'test2',
      todo3: 'test3'
    };

    spyOn(localStorage, 'getItem').andCallFake(function(key) {
      return store[key];
    });

    spyOn(localStorage, 'setItem').andCallFake(function(key, value) {
      return store[key] = value + '';
    });

    spyOn(localStorage, 'clear').andCallFake(function() {
      store = {};
    });

    spyOn(Object, 'keys').andCallFake(function(value) {
      var keys=[];

      for(var key in store) {
        keys.push(key);
      }

      return keys;
    });
  });

  // check to see if it has the expected function
  it('should have a get function', function() {
    expect(angular.isFunction(factory.get)).toBe(true);
    expect(angular.isFunction(factory.put)).toBe(true);
  });

  //check to see if it returns three notes initially
  it('should return three todo notes initially', function() {
    var result = factory.get();

    expect(result.length).toBe(3);
  });

  //check if it successfully adds a new item
  it('should return four todo notes after adding one more', function() {
    factory.put('Angular is awesome');

    var result = factory.get();
    expect(result.length).toBe(4);
  });
});
```

测试程序与`TodoController`相同，除了一些地方。记住，实际的工厂使用`localStorage`来存储和检索注释项。但是，由于我们是单元测试，我们不想依赖外部服务。所以，我们需要把像`localStorage.getItem()`和`localStorage.setItem()`这样的函数调用转换成假的来使用我们自己的存储，而不是使用`localStorage`的底层数据存储。`spyOn(localStorage, 'setItem').andCallFake()`确实如此。`spyOn()`的第一个参数指定感兴趣的对象，第二个参数表示我们想要监视的函数。`andCallFake()`给了我们一种方法来编写我们自己的函数实现。因此，在这个测试中，我们已经配置了`localStorage`函数来使用我们的定制实现。在我们的工厂中，我们还使用`Object.keys()`函数进行迭代，并获得音符的总数。因此，在这个简单的例子中，我们也可以监视`Object.keys(localStorage)`从我们自己的存储中返回密钥，而不是本地存储。

接下来，我们检查工厂是否包含所需的函数(`get()`和`put()`)。这是通过`angular.isFunction()`完成的。然后我们检查工厂最初是否有三个音符。在最后一个测试中，我们添加了一个新的音符，并断言它将音符计数增加了 1。

### 测试过滤器

现在，假设我们需要修改注释在页面上的显示方式。如果注释文本超过 20 个字符，我们应该只显示前 10 个。让我们为此编写一个简单的过滤器，命名为`truncate`，如下所示。

```
todoApp.filter('truncate', function() {
  return function(input,length) {
    return (input.length > length ? input.substring(0, length) : input );
  };
});
```

在标记中，它可以这样使用:

```
{{note | truncate:20}}
```

要对它进行单元测试，可以使用下面的代码。

```
describe('filter tests', function() {
  beforeEach(module('todoApp'));
  it('should truncate the input to 10 characters',
    //this is how we inject a filter by appending Filter to the end of the filter name
    inject(function(truncateFilter) {
      expect(truncateFilter('abcdefghijkl', 10).length).toBe(10);
    })
  );
});
```

前面的代码非常简单。请注意，您是通过将`Filter`附加到实际过滤器名称的末尾来注入过滤器的。那你就可以照常打电话了。

### 测试指令

让我们创建一个简单的指令，为它所应用的元素提供背景色。用 CSS 可以很容易地做到这一点。但是，为了演示指令的测试，让我们坚持以下内容:

```
todoApp.directive('customColor', function() {
  return {
    restrict: 'A',
    link: function(scope, elem, attrs) {
      elem.css({'background-color': attrs.customColor});
    }
  };
});
```

这可以应用于任何元素，例如`<ul custom-color="rgb(128, 128, 128)"></ul>`。测试代码如下所示。

```
describe('directive tests', function() {
    beforeEach(module('todoApp'));
  it('should set background to rgb(128, 128, 128)',
    inject(function($compile,$rootScope) {
      scope = $rootScope.$new();

      // get an element representation
      elem = angular.element("<span custom-color=\"rgb(128, 128, 128)\">sample</span>");

      // create a new child scope
      scope = $rootScope.$new();

      // finally compile the HTML
      $compile(elem)(scope);

      // expect the background-color css property to be desirabe one
      expect(elem.css("background-color")).toEqual('rgb(128, 128, 128)');
     })
  );
});
```

我们需要一个名为`$compile`(由 Angular 注入)的服务来实际编译和测试应用了指令的元素。`angular.element()`创建一个 jqLite 或 jQuery(如果有的话)元素供我们使用。然后，我们用一个 scope 编译它，它就可以被测试了。在这种情况下，我们期望`background-color` CSS 属性是`rgb(128, 128, 128)`。参考[这个文档](http://docs.angularjs.org/api/angular.element)来了解你可以在`element`上调用哪些方法。

## E2E 角测试

在 E2E 测试中，我们将一组组件装配在一起，并检查整个过程是否如预期那样工作。在我们的例子中，我们需要确保当用户在文本字段中输入内容并点击按钮时，它会被添加到`localStorage`中，并出现在文本字段下方的列表中。

这个 E2E 测试使用一个有角度的场景跑步者。如果你已经下载了演示 app，解压后可以看到`test/e2e`里面有一个`runner.html`。这是我们的场景运行程序文件。`scenarios.js`文件包含 e2e 测试(您将在这里编写测试)。写完测试之后，你可以运行[http://localhost/angular-seed/test/e2e/runner . html](http://localhost/angular-seed/test/e2e/runner.html)来查看结果。添加到`scenarios.js`的 E2E 测试如下所示。

```
describe('my app', function() {
  beforeEach(function() {
    browser().navigateTo('../../app/notes.html');
  });

  var oldCount = -1;

  it("entering note and performing click", function() {
    element('ul').query(function($el, done) {
      oldCount = $el.children().length;
      done();
    });

    input('note').enter('test data');

    element('button').query(function($el, done) {
      $el.click();
      done();
    });
  });

  it('should add one more element now', function() {
    expect(repeater('ul li').count()).toBe(oldCount + 1);
  });        
});
```

### 说明

由于我们正在执行一个完整的测试，我们应该首先导航到我们的主 HTML 页面，`app/notes.html`。这是通过`browser.navigateTo()`实现的。`element.query()`功能选择`ul`元素来记录最初有多少个音符项。该值存储在`oldCount`变量中。接下来，我们模拟通过`input('note').enter()`在文本字段中输入注释。请注意，您需要将模型名称传递给`input()`函数。在我们的 HTML 页面中，输入被绑定到`ng-model` `note`。因此，应该用它来标识我们的输入字段。然后我们点击按钮，检查它是否向列表中添加了新的注释(`li`元素)。我们通过比较新的计数(通过`repeater('ul li').count()`获得)和旧的计数来实现。

## 结论

AngularJS 的设计考虑到了可靠的 JavaScript 测试，并且支持测试驱动开发。所以，在开发的时候，一定要测试你的代码。这看起来很费时间，但实际上通过消除大部分以后会出现的错误节省了您的时间。

### 额外资源

1.  如果您的服务/工厂使用`http`服务来调用远程 API，您可以从它返回假数据用于单元测试。这里有一个[指南](http://docs.angularjs.org/api/ngMock.%24httpBackend)来做这个。
2.  Angular 网站上的这个文档有一些关于单元测试的好信息。
3.  如果你开始一个新的角度项目，考虑使用[量角器](https://github.com/angular/protractor)进行 E2E 测试。

## 分享这篇文章