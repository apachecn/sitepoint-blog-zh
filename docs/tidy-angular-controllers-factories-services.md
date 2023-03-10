# 用工厂和服务整理你的角度控制器

> 原文：<https://www.sitepoint.com/tidy-angular-controllers-factories-services/>

大约在五六年前，jQuery 曾经统治着 web 的客户端。它读起来像简单的英语，很容易安装，而且学习曲线很平坦，幼儿可以在上面骑三轮车。然而，伴随着这种便捷的访问而来的是一系列的问题。jQuery 使得拼凑“有效”的东西变得容易，但这是以最佳实践、可维护性和可伸缩性为代价的。

然后，框架大战开始了，很快每个人都吵着要尝试最新最棒的框架，这将为他们的应用带来承诺的结构和可伸缩性。其中一个框架是 AngularJS。现在，Angular 的学习曲线明显比 jQuery 陡峭，但我认为它已经达到了一个点，许多开发人员可以非常自信地建立一个基本的应用程序。也就是说，使用框架并不能自动解决应用程序设计的核心问题。在 AngularJS、EmberJS 或 React 等不可维护或不可扩展的框架中构建应用程序仍然是可能的——事实上，初学者甚至中级框架用户犯这种错误是很常见的。

## 事情怎么这么容易失控？

为了演示这种突然的复杂性如何发生在最基本的 AngularJS 应用程序中，让我们开始构建一个并观察我们可能会出错的地方。然后，稍后，我们将研究修复它的方法。

### 让我们创建一个简单的应用程序

我们将要创建的应用程序是一个为 [Dribbble](http://dribbble.com) 玩家设计的得分应用程序。我们将能够键入一个 Dribbble 用户的名字，并让他们添加到一个评分板。

**剧透**——你可以在这里看到最终产品[的工作实现。](http://codepen.io/Haizyfox/pen/CIzbh/)

首先创建一个包含以下内容的`index.html`文件:

```
<!DOCTYPE html>
<html>
  <head>
    <title>Angular Refactoring</title>
    <link href="http://netdna.bootstrapcdn.com/bootstrap/3.1.1/css/bootstrap.min.css" rel="stylesheet">
    <script src="http://cdnjs.cloudflare.com/ajax/libs/angular.js/1.2.15/angular.min.js"></script>
  </head>
  <body>
    <div>
      <div class="panel panel-default">
        <div class="panel-heading">Dribbble Player Scores</div>
        <div class="panel-body">
          <p>Add Dribbble players to see how they rank:</p>
          <div class="form-inline">
            <input class="form-control" type="text" />
            <button class="btn btn-default">Add</button>
          </div>
        </div>
        <ul class="list-group">
          ...
        </ul>
      </div>
    </div>
  </body>
</html>
```

### 创建我们的 AngularJS 应用

如果你以前编写过 Angular 应用程序，接下来的几个步骤你应该相当熟悉。首先，我们将创建一个`app.js`文件，在这里我们将实例化我们的 AngularJS 应用程序:

```
var app = angular.module("dribbbleScorer", []);
```

现在我们将把它包含在我们的`index.html`文件中。我们还将在我们的`<html>`标签中添加`ng-app="dribbbleScorer"`属性来引导 Angular 应用程序。

```
<html ng-app="dribbbleScorer">
  <head>
    <title>Angular Refactoring</title>
    <link href="http://netdna.bootstrapcdn.com/bootstrap/3.1.1/css/bootstrap.min.css" rel="stylesheet">
    <script src="http://cdnjs.cloudflare.com/ajax/libs/angular.js/1.2.15/angular.min.js"></script>
    <script src="app.js"></script>
  </head>
  ...
```

既然我们的应用程序已经设置和引导，我们可以开始处理我们的应用程序的业务逻辑。

### 让它工作

是时候实际实现我们的应用程序了。请记住，我们正以“让它发挥作用”的方式来处理这个问题，因为这通常是我们面临的现实。就像人们可能急于用 jQuery 添加一个点击处理程序一样，Angular 用户通常会找到一条最快的路径来使用一个应用程序:`ng-controller`。让我们看看它是如何工作的。

在`app.js`中，我们将定义一个控制器和一些虚拟玩家数据:

```
var app = angular.module("dribbbleScorer", []);

app.controller("DribbbleController", function($scope) {
  $scope.players = ["Tom", "Dick", "Harry"];
});
```

在`index.html`中，我们将使用`ng-controller`插入我们的控制器，我们将编辑我们的`ul`列表来循环播放玩家，并在`li`中显示他们每个人:

```
<body>
  <!-- Add our DribbbleController -->
  <div ng-controller="DribbbleController">
    ...
    <ul class="list-group">
      <!-- Loop over players using ng-repeat -->
      <li class="list-group-item" ng-repeat="player in players">
        {{player}}
      </li>
    </ul>
    ...
  </div>
</body>
```

如果你保存这两个文件，并在浏览器中打开`index.html`,你应该会看到一个列表，上面有三个名字:汤姆、迪克和哈利。很简单，到目前为止，很干净。

### 实现表单

接下来，让我们让表单工作。我们需要一个变量作为输入字段的`ng-model`，并且我们需要一个按钮的点击处理程序。点击处理器将需要添加我们的输入到当前的球员名单。

在`index.html`中，将模型和点击处理程序添加到我们的表单中:

```
<div ng-controller="DribbbleController">
  ...
  <div class="form-inline">
    <input class="form-control" type="text" ng-model="newPlayer" />
    <button class="btn btn-default" ng-click="addPlayer(newPlayer)">Add</button>
  </div>
  ...
</div>
```

接下来，我们将在`app.js`中实现这两件事:

```
app.controller("DribbbleController", function($scope) {
  $scope.newPlayer = null; // Our model value is null by default
  $scope.players = ["Tom", "Dick", "Harry"];

  // Adds a player to the list of players
  $scope.addPlayer = function(player) {
    $scope.players.push(player); 
  }
});
```

在浏览器中测试它。键入一个名称，单击 Add 按钮，它应该会出现在列表中。使用 AngularJS 控制器很容易就能让某些东西快速工作。

### 从 Dribbble 获取数据

现在，让我们从 Dribbble 中获取玩家信息，而不是仅仅使用虚拟玩家名。我们将更新我们的`addPlayer()`函数，将玩家名字发送到 Dribbble 的 API，并将结果推送到列表中:

```
app.controller("DribbbleController", function($scope, $http) {
  $scope.newPlayer = null; // Our model value is null by default
  $scope.players = ["Tom", "Dick", "Harry"];

  // Fetches a Dribbble player and adds them to the list
  $scope.addPlayer = function(player) {
    $http.jsonp(
      'http://api.dribbble.com/players/' + player + '?callback=JSON_CALLBACK'
    ).success(function(dribbble_player){
      $scope.players.push(dribbble_player.name);
    }).error(function(){
      // handle errors
    }); 
  }
});
```

记得首先将`$http`服务注入到您的控制器中。Dribbble API 是基于 JSONP 的，所以我们需要使用`$http.jsonp()`方法，并在 URL 中添加`?callback=JSON_CALLBACK`，以允许 Angular 自动处理我们的响应。剩下的很简单。在我们的成功回调中，我们将玩家的名字推入列表。继续，在浏览器中尝试一下。

### 移除玩家

让我们在玩家行中添加一个删除按钮。首先，对`index.html`进行如下修改。

```
<ul class="list-group">
  <!-- Loop over players using ng-repeat -->
  <li class="list-group-item" ng-repeat="player in players">
    {{player}}
    <a href="" ng-click="removePlayer(player)">
      <i class="glyphicon glyphicon-remove pull-right"></i>
    </a>
  </li>
</ul>
```

然后，在`app.js`中进行这些更改:

```
app.controller("DribbbleController", function($scope, $http) {
  ...
  $scope.removePlayer = function(player) {
    $scope.players.splice($scope.players.indexOf(player), 1);
  };
});
```

你现在应该可以在你的列表中添加和删除玩家了。

### 使用`player`对象

在开始重构之前，是时候创建应用程序的最后一部分了。我们将为我们的玩家创建一个任意的“评论分数”和“喜欢分数”。但是首先，我们需要将我们的播放器字符串转换成对象，这样它们就可以有属性，然后我们可以在 DOM 中显示这些属性。让我们更新`app.js`以使用从 Dribbble 返回的实际玩家对象:

```
app.controller("DribbbleController", function($scope, $http) {
  $scope.newPlayer = null; // Our model value is null by default
  $scope.players = []; // We'll start with an empty list

  // Fetches a Dribbble player and adds them to the list
  $scope.addPlayer = function(player) {
    $http.jsonp(
      'http://api.dribbble.com/players/' + player + '?callback=JSON_CALLBACK'
    ).success(function(dribbble_player){
      $scope.players.push(dribbble_player); // Here we add the dribbble_player object to the list
    }).error(function(){
      // handle errors
    }); 
  };
});
```

接下来，让我们更新 DOM 以使用播放器的属性:

```
<ul class="list-group">
  <!-- Loop over players using ng-repeat -->
  <li class="list-group-item" ng-repeat="player in players">
    <!-- We use player.name here instead of just player -->
    {{player.name}}
    <a href="" ng-click="removePlayer(player)">
      <i class="glyphicon glyphicon-remove pull-right"></i>
    </a>
  </li>
</ul>
```

此时，应用程序应该仍能正常运行。

### 计算分数

让我们将分数信息添加到 DOM 中，然后我们将在 JavaScript 文件中实现它:

```
<ul class="list-group">
  <li class="list-group-item" ng-repeat="player in players">
    {{player.name}} L: {{likeScore(player)}} C:{{commentScore(player)}}
    <a href="" ng-click="removePlayer(player)">
      <i class="glyphicon glyphicon-remove pull-right"></i>
    </a>
  </li>
</ul>
```

我们将通过从玩家收到的评论数中减去玩家给出的评论来任意计算分数，同样地(请原谅双关语)计算他们给出的喜欢数和收到的喜欢数。我们将按如下方式实现:

```
app.controller("DribbbleController", function($scope, $http){
  ...

  $scope.likeScore = function(player) {
    return player.likes_received_count - player.likes_count;
  };

  $scope.commentScore = function(player) {
    return player.comments_received_count - player.comments_count;
  };
});
```

重新加载页面，添加一些玩家，您应该会看到每个玩家的 Like (L)分数和 Comment (C)分数。

### 看那个控制器！

现在，我们的应用程序运行良好，但看看我们创建的控制器的大小和复杂性就知道了！在一个理想的世界里，控制器应该只关心它自己:控制应用程序不同部分之间的通信。在这里，我们的控制器负责所有的事情。

```
app.controller("DribbbleController", function($scope, $http) {
  $scope.newPlayer = null; // Our model value is null by default
  $scope.players = []; // We'll start with an empty list

  // Fetches a Dribbble player and adds them to the list
  $scope.addPlayer = function(player) {
    $http.jsonp(
      'http://api.dribbble.com/players/' + player + '?callback=JSON_CALLBACK'
    ).success(function(dribbble_player) {
      $scope.players.push(dribbble_player); // Here we add the dribbble_player object to the list
    }).error(function() {
      // handle errors
    }); 
  };

  $scope.removePlayer = function(player) {
    $scope.players.splice($scope.players.indexOf(player), 1);
  };

  $scope.likeScore = function(player) {
    return player.likes_received_count - player.likes_count;
  };

  $scope.commentScore = function(player) {
    return player.comments_received_count - player.comments_count;
  };
});
```

我们可以做得更好。

## 使用角工厂来抽象我们的关注点

添加和删除玩家是控制器中的两个概念。与其说控制器公开了这些功能，不如说它还负责这些功能的实现。如果控制器的`addPlayer()`功能只是把这个请求交给应用程序的另一部分，而这个部分可以处理**添加**玩家的细节，那不是更好吗？这就是 AngularJS 工厂的由来。

### 创建我们的工厂

如果我们从面向对象的角度考虑，我们正在处理一个 Dribbble player 对象。所以，让我们创建一个可以制造 Dribbble 播放器的工厂。为了方便起见，我们将在同一个`app.js`文件中实现它:

```
app.controller("DribbbleController", function($scope, $http) {
  ...
});

app.factory("DribbblePlayer", function() {
  // Define the DribbblePlayer function
  var DribbblePlayer = function(player) {
  };

  // Return a reference to the function
  return (DribbblePlayer);
});
```

你会注意到我们用大写语法定义了`DribbblePlayer`。这是因为它是一个构造函数。还要注意，构造函数接受一个播放器参数。当我们将这个工厂注入到我们的控制器中时，我们将能够调用`new DribbblePlayer(player)`,并让它返回一个自己配置给那个播放器的构造实例。

让我们给`DribbblePlayer`构造函数添加一个初始化函数来设置一些默认属性:

```
// We need to inject the $http service in to our factory
app.factory("DribbblePlayer",function($http) {
  // Define the DribbblePlayer function
  var DribbblePlayer = function(player) {
    // Define the initialize function
    this.initialize = function() {
      // Fetch the player from Dribbble
      var url = 'http://api.dribbble.com/players/' + player + '?callback=JSON_CALLBACK';
      var playerData = $http.jsonp(url);
      var self = this;

      // When our $http promise resolves
      // Use angular.extend to extend 'this'
      // with the properties of the response
      playerData.then(function(response) {
        angular.extend(self, response.data);  
      });
    };

    // Call the initialize function for every new instance
    this.initialize();
  };

  // Return a reference to the function
  return (DribbblePlayer);
});
```

这里有几件事需要注意:

我们将`self`变量定义为对`this`的引用，T1 在上下文中是构造的`DribbblePlayer`实例。我们这样做是为了让实例可以在 promise 的`then()`回调中扩展。

我们还使用`angular.extend()`将所有从 API 获得的 Dribbble players 属性添加到我们的`DribbblePlayer`实例中。这相当于:

```
playerData.then(function(response) {
  self.name = response.data.name;
  self.likes_count = response.data.likes_count;
  // etc
});
```

我们在定义它之后立即调用`this.initialize()`。这是为了模拟正常的 OOP 行为，其中定义一个构造函数或`initialize()`方法将导致该方法在创建该类的新实例时被执行。

### 使用工厂

是时候用我们的工厂了。我们需要将它注入到我们的控制器中，然后我们可以用它从控制器中抽象出一些责任:

```
...

// Inject DribbblePlayer into your controller and remove the $http service
app.controller("DribbbleController", function($scope, DribbblePlayer) {
  $scope.newPlayer = null;
  $scope.players = [];

  $scope.addPlayer = function(player) {
    // We can push a new DribbblePlayer instance into the list
    $scope.players.push(new DribbblePlayer(player));
    $scope.newPlayer = null;
  };
  ...
});
```

在你的浏览器中重新加载应用程序，它应该像以前一样工作。是不是很牛逼？

### 这到底是怎么回事？

概括地说，我们已经将我们的`DribbblePlayer`工厂注入到我们的控制器中。工厂允许我们创建`DribbblePlayer`构造函数的新实例。构造器的`initialize()`方法使用 player name 参数从 Dribbble 中获取玩家的详细信息，并将它们设置为实例的属性。最后，该实例是我们放入列表的内容。

我们根本不需要改变 DOM，因为它需要有`name`和`like_count`的对象，这正是我们给它的。

### 这真的值得吗？

绝对的！我们不仅简化了控制器，还分离了我们的关注点。我们的控制器不再关心添加玩家的实现。我们可以用`new DribbblePlayer()`替换`new BaseballSuperstar()`，我们只需要修改一行代码。此外，我们现在也可以抽象控制器的其他部分，使用可读性更强、可伸缩性更好的 OOP 方法。

让我们将`likeScore()`和`commentScore()`移入我们的工厂，并将它们设置为每个播放器实例上的方法，而不是接受播放器参数的函数:

```
...

  this.initialize = function(argument) {
    ...
  };

  this.likeScore = function() {
    return this.likes_received_count - this.likes_count;
  };

  this.commentScore = function() {
    return this.comments_received_count - this.comments_count;
  };
}
```

现在，每次我们调用`new DribbblePlayer(player)`时，我们得到的对象将有一个`likeScore()`方法和一个`commentScore()`方法。它们需要保持为函数而不是属性，以便在 Angular 的每个`$digest`周期中，它们将生成新值来表示`DribbblePlayer`模型中的任何潜在变化。

我们需要更新我们的 DOM 来反映这些变化:

```
<ul class="list-group">
  <li class="list-group-item" ng-repeat="player in players">
    <!-- We can now use player.likeScore instead of likeScore(player) -->
    {{player.name}} L: {{player.likeScore()}} C:{{player.commentScore()}}
    <a href="" ng-click="removePlayer(player)">
      <i class="glyphicon glyphicon-remove pull-right"></i>
    </a>
  </li>
</ul>
```

## 包扎

我试图证明，我们编写“让它工作”的代码是多么容易，而这些代码很快就会失控。我们最终得到了一个混乱的控制器，充满了功能和责任。然而，经过一些重构，我们的控制器文件现在看起来像这样:

```
app.controller("DribbbleController", function($scope, DribbblePlayer) {
  $scope.newPlayer = null;
  $scope.players = [];

  $scope.addPlayer = function(player) {
    $scope.players.push(new DribbblePlayer(player));
  };

  $scope.removePlayer = function(player) {
    $scope.players.splice($scope.players.indexOf(player), 1);
  };
});
```

它可读性更强，而且很少涉及自身——这就是重构的意义所在。我希望我已经为您提供了开始考虑构建 AngularJS 应用程序的更好方法所需的工具。重构快乐！

本教程的代码可以在 GitHub 上找到！

### 额外学分

我们当然改进了`addPlayer()`功能，但为什么就此止步呢？这里有几个我们可以做的其他改进:

*   将`$http`调用抽象成一个角度资源，以解耦持久性/资源。然后，您可以将该资源注入到您的工厂中，以便使用它。
*   创建一个`PlayerList`工厂来处理列表管理，包括添加、删除和排序。这样你可以抽象出`PlayerList.add()`和`PlayerList.remove()`后面的`push()`和`splice()`方法，这样你就不需要直接依赖控制器内部的实现。

## 分享这篇文章