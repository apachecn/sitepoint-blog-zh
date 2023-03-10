# 夹具适配器–不带服务器的 Ember.js

> 原文：<https://www.sitepoint.com/fixture-adapters-ember-js-server/>

如今，JavaScript MVC 框架已经成为许多前端 web 开发人员的热门选择。但是大多数时候，您会发现您的应用程序需要与一些后端 web 服务进行通信。频繁调用此服务会降低您的开发时间。这可能会令人沮丧，尤其是如果你依赖于你无法控制的第三方服务。

为了解决这个问题，Ember.js 提供了一个有用的特性，叫做**夹具适配器**。Fixture 适配器允许您用本地存储的虚拟数据模拟对服务器的任何可能的调用。一旦开发完成，您就可以换出 fixtures，使您的应用程序能够调用服务器来获取真实数据。在本教程中，我们将构建一个示例博客应用程序，展示 Ember 的夹具适配器。

## 介绍

我们的博客应用程序将有几个屏幕。将有一个主页列出文章，另一个用于查看单个文章。现在我们知道我们在构建什么，让我们开始吧。

## `index.html`文件

在应用程序的根目录下创建一个`index.html`文件，并添加以下代码。这是一个简单的框架，包括 Ember.js 及其依赖项、Handlebars 和 jQuery。

```
<!DOCTYPE html>
<html>
<head>
  <title>Developing with Ember.js Using Fixture Adapters</title>
  <script src="http://cdnjs.cloudflare.com/ajax/libs/jquery/1.10.2/jquery.min.js"></script>
  <script src="http:////cdnjs.cloudflare.com/ajax/libs/handlebars.js/1.1.2/handlebars.min.js"></script>
  <script src="http://cdnjs.cloudflare.com/ajax/libs/ember.js/1.5.1/ember.min.js"></script>
  <script src="http://cdnjs.cloudflare.com/ajax/libs/ember-data.js/1.0.0-beta.7/ember-data.js"></script>
</head>
<body>
  <h1>Welcome to our Ember.js blog</h1>
</body>
</html>
```

## 引导应用程序

创建一个新的`main.js`文件，并将下面的代码粘贴到其中。这将引导一个 Ember.js 应用程序的新实例，并为您定义两条路线。

```
App = Ember.Application.create();

App.Router.map(function() {
  this.resource('articles', { path: 'articles' }, function() {
    this.route('article', { path: '/:article_id' });
  });
});
```

## 在主页上列出文章

接下来，我们需要在主页上列出我们的文章。为此，请完成以下步骤。

### 重定向至文章列表

为了确保文章出现在主页上，将下面的代码粘贴到您的`main.js`文件中。这将创建一个索引路径，并确保无论何时访问主页，用户都会被自动重定向到文章页面。

```
App.IndexRoute = Ember.Route.extend({
  redirect: function() {
    this.transitionTo('articles');
  }
});
```

### 创建商品列表路径

接下来，创建文章列表 route 并为其提供一些数据。将这段代码粘贴到`main.js`文件中。这条路线有趣的地方在于，我们返回一个简单的 JavaScript 文章数组作为路线的模型。

```
var articles = [
  {
    id: 1,
    title: 'Article 1',
    body: 'This is my first article'
  },
  {
    id: 2,
    title: 'Article 2',
    body: 'This is my secondarticle'
  },
  {
    id: 3,
    title: 'Article 3',
    body: 'This is my third article'
  }
];

App.ArticlesIndexRoute = Ember.Route.extend({
  model: function() {
    return articles;
  }
});
```

### 创建文章列表模板

现在我们的路由有了一个模型，将下面的模板添加到`index.html`文件的`h1`头之后。

```
<script type="text/x-handlebars" id="articles/index">
  <h2>Articles</h2>
  {{#each}}
    <p>{{#link-to 'articles.article' this}}{{title}}{{/link-to}}</p>
  {{/each}}
</script>
```

现在访问主页会显示文章链接列表。当你点击其中一个链接，你将被带到一个单独的文章页面。不过，您会注意到，文章细节丢失了。这是因为我们没有为一篇文章添加模板。让我们接下来做那件事。

## 显示单篇文章

将下面的模板添加到`index.html`文件中的`articles/index`模板之后。

```
<script type="text/x-handlebars" id="articles/article">
  <h2>{{title}}</h2>
  <p>{{body}}</p>
</script>
```

现在，当我们访问主页并点击一篇文章链接时，您应该会看到一个专门的文章页面，上面有该文章的标题和内容。

如果刷新页面，您会注意到应用程序已损坏。原因是当您通过 URL 访问该路由时，没有为该路由提供任何模型。

有两种方法可以解决这个问题。我们既可以使用夹具适配器，也可以为单个商品创建一个明确的路径。因为我们将演示夹具适配器，所以我们将展示如何使用适配器来修复它。

## 使用夹具适配器

我们的应用程序可以通过返回一个数组作为路由的模型来列出模拟文章，这很好。然而，当我们的应用程序增长时，这种方法会变得难以管理。当我们准备使用后端服务器时，将阵列换成真实数据会变得很困难。这就是夹具适配器的用处。为了让我们的应用程序使用适配器，让我们先做几件事。

### 创建文章模型

将以下内容插入到您的`main.js`文件中。

```
App.Article = DS.Model.extend({
  title: DS.attr(),
  body: DS.attr()
});
```

### 添加装置

接下来，在前面的代码之后立即添加此代码:

```
App.Article.FIXTURES = [
  {
    id: 1,
    title: 'Article 1',
    body: 'This is my first article'
  },
  {
    id: 2,
    title: 'Article 2',
    body: 'This is my secondarticle'
  },
  {
    id: 3,
    title: 'Article 3',
    body: 'This is my third article'
  }
];
```

此外，删除您之前创建的`articles`数组，因为我们将不再使用它。

### 启用夹具

接下来，添加下面的代码来指示 Ember.js 在我们从数据存储中请求文章时使用 fixture。

```
App.ArticleAdapter = DS.FixtureAdapter.extend();
```

### 来自夹具的查询

最后，我们需要编辑文章列表路径。更改此代码:

```
App.ArticlesIndexRoute = Ember.Route.extend({
  model: function() {
    return articles;
  }
});
```

对此:

```
App.ArticlesIndexRoute = Ember.Route.extend({
  model: function() {
    return this.store.find('article');
  }
});
```

现在，当您访问主页时，您应该看不到任何区别，因为我们使用的是完全相同的数据，唯一的区别是它现在来自一个设备。

## 结论

这使我们结束了我们的简短教程，强调了 Ember.js 夹具适配器的使用。我们开发的博客应用程序只展示了它们所能实现的一小部分。当你决定切换到后端服务器时，它变得毫不费力。在某些情况下，转换就像换出一行代码一样简单。

在我开发 Ember.js 应用程序的所有时间里，我还没有发现一个应用程序的开发不能受益于使用适配器。

除了这个简单的博客应用程序之外，我鼓励您通过尝试以下内容来拓展 fixture 适配器的功能。

*   实现向文章添加评论的能力。
*   在 fixtures 中创建一对多和多对多的关系。
*   添加创建和编辑文章的功能。

我希望这篇文章是使用夹具适配器的一个很好的起点。要了解更多信息，请访问 [Ember 文档](http://emberjs.com/guides/models/the-fixture-adapter/)。你也可以在这里查看我们博客应用[的完整版本](http://codepen.io/Lilmls/pen/gGval)。

如果您之前使用过夹具适配器，请分享您在使用时的想法和经验。

## 分享这篇文章