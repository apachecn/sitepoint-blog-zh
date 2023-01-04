# 用角度和弹性搜索建立一个食谱搜索网站

> 原文：<https://www.sitepoint.com/building-recipe-search-site-angular-elasticsearch/>

你有没有想过在应用程序中内置搜索功能？在过去，你可能会发现自己与 Solr 争论不休，或者在 T2 Lucene T3 的基础上构建自己的搜索服务——如果你幸运的话。但是，自 2010 年以来，有一种更简单的方法:[弹性搜索](http://elasticsearch.org/)。

Elasticsearch 是一个基于 Lucene 的开源存储引擎。它不仅仅是一个搜索引擎；这是一个真正的文档库，尽管它强调搜索性能，而不是一致性或持久性。这意味着，对于许多应用程序，你可以使用 Elasticsearch 作为你的整个后端。应用程序，例如…

## 构建食谱搜索引擎

在这篇文章中，你将学习如何使用 Elasticsearch 和 [AngularJS](http://angularjs.org/) 来创建一个食谱搜索引擎，就像 OpenRecipeSearch.com[的那个一样。为什么是食谱？](http://openrecipesearch.com)

1.  OpenRecipes 的存在，让我们的工作变得容易多了。
2.  为什么不呢？

OpenRecipes 是一个开源项目，它从许多食谱网站上搜集食谱，然后以一种方便的 JSON 格式提供下载。这对我们来说太棒了，因为 Elasticsearch 也使用 JSON。然而，我们必须让 Elasticsearch 运行起来，然后才能向它提供所有这些食谱。

[下载 Elasticsearch](http://www.elasticsearch.org/overview/elkdownloads/) ，解压到你喜欢的任何目录。接下来，打开一个终端，`cd`到刚才解压的目录，运行`bin/elasticsearch`(Windows 上的`bin/elasticsearch.bat`)。哒哒！你刚刚开始了你自己的弹性搜索实例。当你跟着走的时候，让它跑着。

Elasticsearch 的一个伟大特性是它的开箱即用的 RESTful 后端，这使得它很容易从许多环境中进行交互。我们将使用 [JavaScript 驱动](https://github.com/elasticsearch/elasticsearch-js)，但是你可以使用[你喜欢的](https://github.com/elasticsearch)；无论哪种方式，代码看起来都非常相似。如果你喜欢，可以参考这个[随手参考](http://adambard.com/blog/elasticsearch-rosetta-stone/)(免责声明:我写的)。

现在，你需要一份 [OpenRecipes 数据库](http://openrecip.es/)的拷贝。它只是一个充满 JSON 文档的大文件，所以直接写一个快速 Node.js 脚本就可以把它们放进去。为此，您需要获得 JavaScript Elasticsearch 库，所以运行`npm install elasticsearch`。然后，创建一个名为`load_recipes.js`的文件，并添加以下代码。

```
var fs = require('fs');
var es = require('elasticsearch');
var client = new es.Client({
  host: 'localhost:9200'
});

fs.readFile('recipeitems-latest.json', {encoding: 'utf-8'}, function(err, data) {
  if (err) { throw err; }

  // Build up a giant bulk request for elasticsearch.
  bulk_request = data.split('\n').reduce(function(bulk_request, line) {
    var obj, recipe;

    try {
      obj = JSON.parse(line);
    } catch(e) {
      console.log('Done reading');
      return bulk_request;
    }

    // Rework the data slightly
    recipe = {
      id: obj._id.$oid, // Was originally a mongodb entry
      name: obj.name,
      source: obj.source,
      url: obj.url,
      recipeYield: obj.recipeYield,
      ingredients: obj.ingredients.split('\n'),
      prepTime: obj.prepTime,
      cookTime: obj.cookTime,
      datePublished: obj.datePublished,
      description: obj.description
    };

    bulk_request.push({index: {_index: 'recipes', _type: 'recipe', _id: recipe.id}});
    bulk_request.push(recipe);
    return bulk_request;
  }, []);

  // A little voodoo to simulate synchronous insert
  var busy = false;
  var callback = function(err, resp) {
    if (err) { console.log(err); }

    busy = false;
  };

  // Recursively whittle away at bulk_request, 1000 at a time.
  var perhaps_insert = function(){
    if (!busy) {
      busy = true;
      client.bulk({
        body: bulk_request.slice(0, 1000)
      }, callback);
      bulk_request = bulk_request.slice(1000);
      console.log(bulk_request.length);
    }

    if (bulk_request.length > 0) {
      setTimeout(perhaps_insert, 10);
    } else {
      console.log('Inserted all records.');
    }
  };

  perhaps_insert();
});
```

接下来，使用命令`node load_recipes.js`运行脚本。160，000 条记录之后，我们就有了一个完整的食谱数据库。如果您手头有`curl`的话，请查看一下:

```
$ curl -XPOST http://localhost:9200/recipes/recipe/_search -d '{"query": {"match": {"_all": "cake"}}}'
```

现在，你可能可以使用`curl`搜索食谱，但是如果全世界都喜欢你的食谱搜索，你就需要…

## 构建食谱搜索用户界面

这就是 Angular 的用武之地。我选择 Angular 有两个原因:一是因为我想这么做，二是因为 Elasticsearch 的 JavaScript 库附带了一个实验性的 Angular 适配器。我将把设计作为一个练习留给读者，但是我将向您展示 HTML 的重要部分。

现在就开始搜索棱角分明、富有弹性的产品。我推荐[鲍尔](http://bower.io/)，但是你也可以直接下载。打开您的`index.html`文件，将它们插入您通常放置 JavaScript 的任何地方(我自己更喜欢在结束的`body`标签之前，但那完全是另一回事):

```
<script src="path/to/angular/angular.js"></script>
<script src="path/to/elasticsearch/elasticsearch.angular.js"></script>
```

现在，让我们停下来想想我们的应用程序将如何工作:

1.  用户输入一个查询。
2.  我们将查询作为搜索发送给 Elasticsearch。
3.  我们检索结果。
4.  我们为用户呈现结果。

下面的代码示例显示了我们的搜索引擎的关键 HTML，其中包含角度指令。如果你从来没用过 Angular，那也没关系。要理解这个例子，您只需要知道一些事情:

1.  以`ng`开头的 HTML 属性是角度指令。
2.  角度应用的动态部分用一个`ng-app`和一个`ng-controller`括起来。`ng-app`和`ng-controller`不需要在同一个元素上，但是可以。
3.  HTML 中所有其他对变量的引用都是指我们将在 JavaScript 中遇到的`$scope`对象的属性。
4.  `{{}}`中包含的部分是模板变量，就像 Django/Jinja2/Liquid/Mustache 模板一样。

```
<div ng-app="myOpenRecipes" ng-controller="recipeCtrl">

  <!-- The search box puts the term into $scope.searchTerm
       and calls $scope.search() on submit -->
  <section class="searchField">
    <form ng-submit="search()">
      <input type="text" ng-model="searchTerm">
      <input type="submit" value="Search for recipes">
    </form>
  </section>

  <!-- In results, we show a message if there are no results, and
       a list of results otherwise. -->
  <section class="results">
    <div class="no-recipes" ng-hide="recipes.length">No results</div>

    <!-- We show one of these elements for each recipe in $scope.recipes.
         The ng-cloak directive prevents our templates from showing on load. -->
    <article class="recipe" ng-repeat="recipe in recipes" ng-cloak>
      <h2>
        <a ng-href="{{recipe.url}}">{{recipe.name}}</a>
      </h2>
      <ul>
        <li ng-repeat="ingredient in recipe.ingredients">{{ ingredient }}</li>
      </ul>

      <p>
        {{recipe.description}}
        <a ng-href="{{recipe.url}}">... more at {{recipe.source}}</a>
      </p>
    </article>

    <!-- We put a link that calls $scope.loadMore to load more recipes
         and append them to the results.-->
    <div class="load-more" ng-hide="allResults" ng-cloak>
      <a ng-click="loadMore()">More...</a>
    </div>
  </section>
```

现在，我们可以开始编写 JavaScript 了。我们将从模块开始，我们在上面决定将其称为`myOpenRecipes`(通过`ng-app`属性)。

```
/**
 * Create the module. Set it up to use html5 mode.
 */
window.MyOpenRecipes = angular.module('myOpenRecipes', ['elasticsearch'],
  ['$locationProvider', function($locationProvider) {
    $locationProvider.html5Mode(true);
  }]
);
```

对于那些刚接触 Angular 的人来说，`['$locationProvider', function($locationProvider) {...}]`业务是我们告诉 Angular 我们希望它将`$locationProvider`传递给我们的处理函数，这样我们就可以使用它了。这个依赖注入系统消除了我们依赖全局变量的需要(除了我们刚刚创建的全局`angular`和`MyOpenRecipes`)。

接下来，我们将编写控制器，命名为`recipeCtrl`。我们需要确保初始化模板中使用的`recipes`、`allResults`和`searchTerm`变量，并提供`search()`和`loadMore()`作为动作。

```
/**
 * Create a controller to interact with the UI.
 */
MyOpenRecipes.controller('recipeCtrl', ['recipeService', '$scope', '$location', function(recipes, $scope, $location) {
  // Provide some nice initial choices
  var initChoices = [
      "rendang",
      "nasi goreng",
      "pad thai",
      "pizza",
      "lasagne",
      "ice cream",
      "schnitzel",
      "hummous"
  ];
  var idx = Math.floor(Math.random() * initChoices.length);

  // Initialize the scope defaults.
  $scope.recipes = [];        // An array of recipe results to display
  $scope.page = 0;            // A counter to keep track of our current page
  $scope.allResults = false;  // Whether or not all results have been found.

  // And, a random search term to start if none was present on page load.
  $scope.searchTerm = $location.search().q || initChoices[idx];

  /**
   * A fresh search. Reset the scope variables to their defaults, set
   * the q query parameter, and load more results.
   */
  $scope.search = function() {
    $scope.page = 0;
    $scope.recipes = [];
    $scope.allResults = false;
    $location.search({'q': $scope.searchTerm});
    $scope.loadMore();
  };

  /**
   * Load the next page of results, incrementing the page counter.
   * When query is finished, push results onto $scope.recipes and decide
   * whether all results have been returned (i.e. were 10 results returned?)
   */
  $scope.loadMore = function() {
    recipes.search($scope.searchTerm, $scope.page++).then(function(results) {
      if (results.length !== 10) {
        $scope.allResults = true;
      }

      var ii = 0;

      for (; ii < results.length; ii++) {
        $scope.recipes.push(results[ii]);
      }
    });
  };

  // Load results on first run
  $scope.loadMore();
}]);
```

您应该从 HTML 中识别出`$scope`对象上的所有内容。请注意，我们实际的搜索查询依赖于一个名为`recipeService`的神秘对象。服务是 Angular 的一种方式，它提供可重用的工具来完成像与外部资源对话这样的事情。可惜 Angular 不提供`recipeService`，只能自己写了。它看起来是这样的:

```
MyOpenRecipes.factory('recipeService', ['$q', 'esFactory', '$location', function($q, elasticsearch, $location) {
  var client = elasticsearch({
    host: $location.host() + ':9200'
  });

  /**
   * Given a term and an offset, load another round of 10 recipes.
   *
   * Returns a promise.
   */
  var search = function(term, offset) {
    var deferred = $q.defer();
    var query = {
      match: {
        _all: term
      }
    };

    client.search({
      index: 'recipes',
      type: 'recipe',
      body: {
        size: 10,
        from: (offset || 0) * 10,
        query: query
      }
    }).then(function(result) {
      var ii = 0, hits_in, hits_out = [];

      hits_in = (result.hits || {}).hits || [];

      for(; ii < hits_in.length; ii++) {
        hits_out.push(hits_in[ii]._source);
      }

      deferred.resolve(hits_out);
    }, deferred.reject);

    return deferred.promise;
  };

  // Since this is a factory method, we return an object representing the actual service.
  return {
    search: search
  };
}]);
```

我们的服务非常简单。它公开了一个方法`search()`，允许我们向 Elasticsearch 发送一个查询，在所有字段中搜索给定的术语。你可以看到，在`query`的正文中传递了对`search` : `{"match": {"_all": term}}`的调用。`_all`是一个特殊的关键字，让我们搜索所有领域。相反，如果我们的查询是`{"match": {"title": term}}`，我们将只看到标题中包含搜索词的食谱。

结果按照“得分”递减的顺序返回，这是 Elasticsearch 根据关键字频率和位置对文档相关性的猜测。对于更复杂的搜索，我们可以调整分数的相对权重(例如，标题中的命中比描述中的命中更有价值)，但默认情况下似乎没有它也能做得很好。

您还会注意到搜索接受一个`offset`参数。由于结果是有序的，如果需要，我们可以通过告诉 Elasticsearch 跳过前 *n* 个结果来获取更多的结果。

## 关于部署的一些注意事项

部署有点超出了本文的范围，但是如果您想让您的菜谱搜索上线，您需要小心。Elasticsearch 没有用户或权限的概念。如果您想防止任何人添加或删除食谱，您需要找到一些方法来防止访问您的 Elasticsearch 实例上的 REST 端点。例如，[OpenRecipeSearch.com](http://openrecipesearch.com)在 Elasticsearch 前使用 nginx 作为代理，阻止外界访问除`recipes/recipe/_search`以外的所有端点。

## 恭喜你，你已经完成了食谱搜索

现在，如果你在浏览器中打开`index.html`，你应该会看到一个没有样式的食谱列表，因为我们的控制器会在页面加载时为你随机获取一些。如果你输入一个新的搜索，你会得到 10 个与你搜索的内容相关的结果，如果你点击页面底部的“更多…”会出现更多的食谱(如果确实有更多的食谱要取)。

这就是全部了！你可以在 [GitHub](https://github.com/adambard/angular-elasticsearch-demo) 上找到运行这个项目所需的所有文件。

## 分享这篇文章