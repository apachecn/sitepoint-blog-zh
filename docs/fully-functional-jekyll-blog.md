# 全功能 Jekyll 博客

> 原文：<https://www.sitepoint.com/fully-functional-jekyll-blog/>

![Blog (блог). Красное слово на белом фоне](img/55e548e4c5a4381c582734f9922d444a.png)

总有一天，入门教程是不够的。这不是那些教程之一。阅读完本文后，您将能够创建一个功能完整的 Jekyll 博客，包括分页和搜索功能。准备好看看哲基尔有多棒吧。

## 观众

**这是为了:**

*   对 Jekyll 有基本了解的人(注:如果你不看文档。很容易掌握基本的。)
*   知道如何使用文本编辑器
*   可以做`gem install`
*   非常擅长 HTML 和 JavaScript
*   了解[液体模板](http://liquidmarkup.org/)(同样…如果不了解，请查看文档)

## 资源

**如果你在任何时候迷失或困惑，这些链接应该可以帮你:**

*   [杰基尔文件](http://jekyllrb.com/docs/home/)
*   [堆栈溢出](http://stackoverflow.com/questions/tagged/jekyll)
*   谷歌

## 走吧。

为了确保我们都在同一页上，我将使用 Jekyll 版本 2.5.3。所以，如果你还没有，你应该`gem install jekyll -v 2.5.3`。

我们将*而不是*使用`jekyll new`命令来创建这个博客。对于想体验 Jekyll 博客的人来说，这很棒，但我觉得它会影响学习体验。相反，让我们首先创建一个新的 git 存储库，并向其中添加一个 ***config.yml*** 文件。这个 **config.yml** 是站点所有配置的存放处。该文件中的变量可以通过全局`site`变量访问(如`site.foo`)。

```
$ mkdir jekyll-blog
$ cd jekyll-blog
$ git init
$ git checkout -b gh-pages # for GitHub pages support
$ echo "2.2.2"; > .ruby-version # sets Ruby version
$ touch _config.yml
```

YAML 档案:

```
# _config.yml
title: A Sample Blog # your blog's name
author: Jesse Herrick # your name
baseurl: /blog/ # we'll get to this later
exclude: # things to exclude from the build process
  - README.md
  - .ruby-version
  - Gemfile
  - Gemfile.lock
```

现在，一些 git 来确保我们开始正确:

```
$ git add .
$ git commit -m "Initial blog configuration"
$ git push -u origin gh-pages # you should create a GitHub repo if you haven't yet
```

很酷的东西。我们的`master`分支不是我们的部署分支，`gh-pages`将担任这个角色。

在我们开始之前，让我们看看我们的博客想要什么:

*   帖子的索引页
*   个人帖子的页面
*   搜索帖子的能力

我们需要一些实际的帖子来测试，所以我用 Lorem Ipsum 生成器生成了一些。为了您的方便，您可以在这里得到它们。把这些放到一个名为 **_posts** 的目录下。

Jekyll 中的每个页面都需要一个默认模板，所以在创建文章索引页面之前先创建一个。这让我们不必写所有必须的东西:

```
$ mkdir _layouts
$ touch _layouts/default.html
```

和 HTML:

```
# _layouts/default.html

<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width,initial-scale=1">
    <title>{% if page.title %}{{ page.title }} &&middot; {% endif %}{{ site.title }}</title>
    {% include libs.html %}
  </head>
  <body>
    {{ content }}
  </body>
</html>
```

您会注意到在`title`标签中，我们使用了一个`if`语句:

```
{% if page.title %}{{ page.title }} &&middot; {% endif %}
```

这说明:如果定义了页面变量`title`，那么在分隔符旁边呈现它。如果不是，那么什么都不会显示。

我们还使用了`include` liquid 标签来获取一个名为**libs.html**的外部 HTML 文件，该文件位于**includes**目录中，包含我们的库。可以将每个模板分割成更小的模板，但是，在某一点上，这会比它的价值更耗费时间。

```
# _includes/libs.html

<!-- Stylesheets -->
<link href='//fonts.googleapis.com/css?family=Arvo:400,400italic,700' rel='stylesheet' type='text/css'>
<link href="{{ '/css/style.css' | prepend: site.baseurl }}">

<!-- JS -->
<script src="//ajax.googleapis.com/ajax/libs/angularjs/1.3.15/angular.min.js"></script>
<script src="{{ '/js/app.js' | prepend: site.baseurl }}"></script>
```

太棒了。请注意，我们使用了液体过滤器`prepend`，将我们站点先前定义的基本 URL 前置。它被定义为`/blog/`，这样，如果网站托管在像`http://johndoe.github.io/blog/`这样的 URL 上，资产就可以正确加载。这很有帮助，因为我们的默认布局将应用于所有页面。

现在让我们制作索引页。

索引页面需要显示整个职位，每 3 个职位分页(我们有 4)。此外，它应该有链接到个人的帖子页面。哲基尔让这变得超级简单。

```
# index.html

---
title: Home
layout: default
---

<h1>{{ site.author }}'s Blog</h1>

<section class="posts">
{% for post in site.posts %}
  <li><date>{{ post.date | date: "%B %-d, %Y"}}<a href="{{ post.url | prepend: site.baseurl }}">{{ post.title }}
{% endfor %}
</section>
```

这只是一个简单明了的索引页面，在文章上有一个经典的`for..in`循环。

## 搜索

AngularJS `ng-repeat`指令对于帖子的快速实时搜索来说是完美的。但是首先，我们需要某种 API 供 Angular 使用。Jekyll 可以在纯液体中为我们做到这一点。因为站点是静态生成的，所以我们的“API”中的数据也不需要动态生成。因此，要生成 API，只需使用以下代码:

```
# posts.json
---
layout: null
---

{
  "posts": [
    {% for post in site.posts %}{
      "title": "{{ post.title }}",
      "url": "{{ post.url | prepend: site.baseurl }}",
      "date": "{{ post.date | date: "%B %-d, %Y" }}",
      "raw_date": "{{ post.date }}"
    }{% unless forloop.last %},{% endunless %}
    {% endfor %}
  ]
}
```

它非常简单，为我们的博客生成了一个很好的 API，看起来像这样:

```
{
  "posts": [
    {
      "title": "Sample 4",
      "url": "/2015/05/20/Sample-4.html",
      "date": "May 20, 2015",
      "raw_date": "2015-05-20 00:00:00 -0400"
    },
    {
      "title": "Sample 3",
      "url": "/2015/05/18/Sample-3.html",
      "date": "May 18, 2015",
      "raw_date": "2015-05-18 00:00:00 -0400"
    },
    {
      "title": "Sample 1",
      "url": "/2015/05/17/Sample-1.html",
      "date": "May 17, 2015",
      "raw_date": "2015-05-17 00:00:00 -0400"
    },
    {
      "title": "Sample 2",
      "url": "/2015/05/15/Sample-2.html",
      "date": "May 15, 2015",
      "raw_date": "2015-05-15 00:00:00 -0400"
    }
  ]
}
```

那么我们如何在博客中使用它来增加搜索功能呢？

```
# js/app.js

angular.module('JekyllBlog', [])
  .controller('SearchCtrl', ['$scope', '$http', function($scope, $http) {
    $http.get('/posts.json').success(function(data) {
      $scope.posts = data.posts;
    });
  }]);
```

现在，将它添加到索引页面，但是有一个问题:Jekyll 将解析 AngularJS 的括号。

```
<p>{{wow.something}}</p>

<!-- is parsed into... -->
<p></p>
```

这是因为 Jekyll 通过 Liquid 运行所有模板，它假定(也应该这样)所有括号都是 Liquid 模板的一部分。所以，我们必须解决这个问题。一个选项是我写的这个插件，但是因为我们计划部署到 GitHub 页面，自定义插件不是一个选项。我们将不得不使用 Liquid 的`{% raw %}`标签(相信我，这并不好玩)。

```
# index.html

<div class="posts" ng-controller="SearchCtrl">
  <input type="search" class="search" ng-model="query">
  <ul>
    <li ng-repeat="post in posts | filter:query ">
      <date ng-bind="post.date"></date>
      <a href="{% raw %}{{ post.url }}{% endraw %}" ng-bind="post.title"></a>
    </li>
  </ul>
</div>
```

这里的特色酱是`ng-repeat`和一个`filter`的组合。我们基本上是告诉 Angular 显示与`query`范围变量匹配的`posts`数组(别名为`post`)中的每一项。您会注意到，在列表的顶部，我们添加了一个由`ng-model`绑定到`query`范围变量的搜索输入。然后，我们只是重复使用前面在`{% for post in posts %}`中使用的相同输出，但是使用 Jekyll“API”。

## 没有 JS 的人怎么办？

如果你真的需要迎合那些没有启用 JavaScript 的人，你可以使用一点`ng-cloak`魔法。

```
# css/styles.css (as recommended by: https://docs.angularjs.org/api/ng/directive/ngCloak)
[ng\:cloak], [ng-cloak], [data-ng-cloak], [x-ng-cloak], .ng-cloak, .x-ng-cloak {
  display: none !important;
}
```

```
# index.html 

<div ng-cloak class="posts" ng-controller="SearchCtrl">
  <input type="search" class="search" ng-model="query">
  <ul>
    <li ng-repeat="post in posts | filter:query ">
      <date ng-bind="post.date"></date>
      <a href="{% raw %}{{ post.url }}{% endraw %}" ng-bind="post.title"></a>
    </li>
  </ul>
</div>

<div ng-show class="posts">
  <ul>
    {% for post in posts %}
    <li><date>{{ post.date | date: "%B %-d, %Y"}}<a href="{{ post.url | prepend: site.baseurl }}">{{ post.title }}</a></li>
    {% endfor %}
  </ul>
</div>
```

这通过一些有趣的逻辑工作。首先，AngularJS' `ng-cloak`指令被一些 CSS 样式检测到，并且在 AngularJS 加载之前是隐藏的。因此，如果 JS 被禁用，则不会显示任何内容。

接下来，`ng-show`指令被添加到 Jekyll 生成的帖子列表中。这是因为 AngularJS 默认`ng-show`为`false`，这意味着只有 AngularJS 没有加载时 Jekyll 列表才会显示。

## 帖子呢？

每个帖子都有一个链接，但是我们还没有真正讨论过当你点击那个链接时会发生什么。现在，它显示了文章的降价呈现为 HTML，但我们想要更多。首先，让我们为所有的帖子添加一个默认布局，因为我们在撰写新帖子时不必担心这个问题:

```
# _config.yml

... other config ...
defaults:
  -
    scope:
      path: ""
      type: "posts"
    values:
      layout: "post"
```

这意味着`layout: post`将被添加到所有的`post`类型文件中。现在我们需要创建这个帖子布局:

```
$ touch _layouts/post.html
```

这个布局只需要一个标题和一个呈现文章的地方:

```
# _layouts/post.html -->

<article>
  <h2>{{ page.title }}</h2>
  Written by <strong>{{ site.author }}</strong><date>{{ page.date | date: "%B %-d, %Y" }}</date>.

  {{ content }}
</article>
```

如果你熟悉 Rails，`{{ content }}`就像 `in terms of layouts. In this case, it is yielding to content passed in each post. Notice that we also used `page.title`和`page.date`而不是`post.title`等等。因为我们调用的是一个只传递给页面(布局)而不通过迭代的变量。`

 `现在，如果你访问一个帖子链接，一个漂亮的标题和一些关于那个页面上的帖子的元信息就会显示出来。

## 结论

你有它！一个功能齐全的 Jekyll 博客。再多一点风格，你就可以随心所欲地写作了。如果你想了解更多关于 Jekyll 能做什么的信息，请查阅相关文档。博客快乐！` 

## `分享这篇文章`