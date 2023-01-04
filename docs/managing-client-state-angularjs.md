# 在 AngularJS 中管理仅客户端状态

> 原文：<https://www.sitepoint.com/managing-client-state-angularjs/>

AngularJS 等 JavaScript 框架中的视图模型可以不同于服务器上的域模型——视图模型甚至不必存在于服务器上。因此，视图模型可以具有仅客户端状态，例如“动画开始”和“动画结束”或“拖动”和“丢弃”。这篇文章将集中讨论使用 Angular 的`$resource`服务创建和保存视图模型时的状态变化。

对于一个`$resource`消费者来说，例如一个控制器，设置状态实际上非常容易，如下所示。

```
angular.module('clientOnlyState.controllers')
    .controller('ArticleCtrl', function($scope, $resource, ArticleStates /* simple lookup */) {
        var Article = $resource('/article/:articleId', { articleId: '@id' });

        var article = new Article({ id: 1, title: 'A title', author: 'M Godfrey' });
        article.state = ArticleStates.NONE; // "NONE"

        $scope.article = article;

        $scope.save = function() {
            article.state = ArticleStates.SAVING; // "SAVING"

            article.$save(function success() {
                article.state = ArticleStates.SAVED; // "SAVED"
            });
        };
    });
```

这种方法适用于包含单个使用者的应用程序。想象一下，对于多个消费者来说，复制这些代码是多么的无聊和容易出错！但是，如果我们可以将状态更改逻辑封装在一个地方会怎么样呢？

### `$resource`服务

让我们从把我们的`Article`资源变成一个可注入的服务开始。让我们在第一次创建一个`Article`时，将最简单的状态设置添加为 NONE。

```
angular.module('clientOnlyState.services')
    .factory('Article', function($resource, ArticleStates) {

        var Article = $resource('/article/:articleId', { articleId: '@id' });

        // Consumers will think they're getting an Article instance, and eventually they are...
        return function(data) {
            var article = new Article(data);
            article.state = ArticleStates.NONE;
            return article;
        }
    });
```

检索和保存呢？我们希望`Article`作为`$resource`服务出现在消费者面前，所以它必须始终如一地工作。我在 John Resig 的优秀著作《JavaScript Ninja 的秘密》中学到的一项技术在这里非常有用——函数包装。这是他的实现，直接提升为一个可注入的角度服务。

```
angular.module('clientOnlyState.services')
    .factory('wrapMethod', function() {
        return function(object, method, wrapper) {
            var fn = object[method];

            return object[method] = function() {
                return wrapper.apply(this, [fn.bind(this)].concat(
                    Array.prototype.slice.call(arguments))
                );
            };
        }
    });
```

这允许我们包装`Article`的`save`和`get`方法，并在前后做一些不同/额外的事情:

```
angular.module('clientOnlyState.services')
    .factory('Article', function($resource, ArticleStates, wrapMethod) {
        var Article = $resource('/article/:articleId', { articleId: '@id' });

        wrapMethod(Article, 'get', function(original, params) {
            var article = original(params);

            article.$promise.then(function(article) {
                article.state = ArticleStates.NONE;
            });

            return article;
        });

        // Consumers will actually call $save with optional params, success and error arguments
        // $save consolidates arguments and then calls our wrapper, additionally passing the Resource instance
        wrapMethod(Article, 'save', function(original, params, article, success, error) {
            article.state = ArticleStates.SAVING;

            return original.call(this, params, article, function (article) {
                article.state = ArticleStates.SAVED;
                success && success(article);
            }, function(article) {
                article.state = ArticleStates.ERROR;
                error && error(article);
            });
        });

        // $resource(...) returns a function that also has methods
        // As such we reference Article's own properties via extend
        // Which in the case of get and save are already wrapped functions
        return angular.extend(function(data) {
            var article = new Article(data);
            article.state = ArticleStates.NONE;
            return article;
        }, Article);
    });
```

我们的控制器因此开始变得更瘦，并且完全不知道状态是如何设置的。这很好，因为控制器也不应该关心。

```
angular.module('clientOnlyState.controllers')
    .controller('ArticleCtrl', function($scope, Article) {
        var article = new Article({ id: 1, title: 'A title', author: 'M Godfrey' });

        console.log(article.state); // "NONE"

        $scope.article = article;

        $scope.save = function() {
            article.$save({}, function success() {
                console.log(article.state); // "SAVED"
            }, function error() {
                console.log(article.state); // "ERROR"
            });
        };
    });
```

### 封装优势

我们已经尽了最大努力来封装控制器外部的状态变化，但是我们获得了什么好处呢？

我们的控制器现在可以利用传递了新旧状态的观察监听器来设置消息。它还可以执行本地翻译，如下所示。

```
angular.module('clientOnlyState.controllers')
    .controller('ArticleCtrl', function($scope, Article, ArticleStates) {
        var article = new Article({ id: 1, title: 'A title', author: 'M Godfrey' });

        var translations = {};
        translations[ArticleStates.SAVED] = 'Saved, oh yeah!';
        translations['default'] = '';

        $scope.article = article;

        $scope.save = function() {
            article.$save({});
        };

        $scope.$watch('article.state', function(newState, oldState) {
            if (newState == ArticleStates.SAVED && oldState == ArticleStates.SAVING) {
                $scope.message = translations[newState];
            } else {
                $scope.message = translations['default'];
            }
        });
    });
```

考虑一下，指令和过滤器构成了应用程序的 API。HTML 视图使用这个 API。API 的可组合性越强，重用的潜力就越大。过滤器能提高新旧观察的可组合性吗？

### 通过滤镜作曲是灵丹妙药吗？

我的想法是这样的。表达式的每个部分都可以重用。

```
<p>{{article.state | limitToTransition:"SAVING":"SAVED" | translate}}</p>
```

从 Angular 1.3 开始，过滤器可以利用`$stateful`属性，但是强烈建议不要使用它，因为 Angular 不能缓存基于输入参数值调用过滤器的结果。因此，我们应该将状态参数传递给`limitToTransition`(先前状态)和`translate`(可用翻译)。

```
angular.module('clientOnlyState.filters')

    .filter('limitToTransition', function() {
        return function(state, prevState, from, to) {
            if(prevState == from && state == to)
                return to;

            return '';
        };
    })

    .filter('translate', function() {
        return function(text, translations) {
            return translations[text] || translations['default'] || '';
        };
    });
```

因此，我们需要对`Article`稍作修改:

```
function updateState(article, newState) {
    article.prevState = article.state;
    article.state = newState;
};

wrapMethod(Article, 'get', function(original, params) {
    var article = original(params);
    article.$promise.then(function(article) {
        updateState(article, ArticleStates.NONE);
    });
    return article;
});
```

最终结果不太漂亮，但仍然非常强大:

```
<p>{{article.state | limitToTransition : article.prevState : states.SAVING : states.SAVED | translate : translations}}</p>
```

我们的控制器又变得更精简了，特别是如果你考虑到翻译可以被提取到一个可注入的服务中:

```
angular.module('clientOnlyState.controllers')
    .controller('ArticleCtrl', function($scope, Article, ArticleStates) {
        var article = new Article({ id: 1, title: 'A title', author: 'M Godfrey' });

        // Could be injected in...
        var translations = {};
        translations[ArticleStates.SAVED] = 'Saved, oh yeah!';
        translations['default'] = '';

        $scope.article = article;
        $scope.states = ArticleStates;
        $scope.translations = translations;

        $scope.save = function() {
            article.$save({});
        };
    });
```

## 结论

将视图模型提取到可注入服务中有助于我们扩展应用程序。这篇文章中给出的例子非常简单。考虑一个允许货币对交易的应用程序(例如英镑对美元、欧元对英镑等)。).每个货币对代表一种产品。在这样的应用程序中，可能有数百种产品，每种产品都接收实时价格更新。价格更新可能高于或低于当前价格。应用程序的一部分可能关心连续两次上涨的价格，而另一部分可能关心刚刚下跌的价格。能够观察这些价格变化状态极大地简化了应用程序的各个消费部分。

我提出了一种基于新旧值的替代观看方法，即过滤。这两种技术都是完全可以接受的——事实上，当我开始研究这篇文章时，我就想到了观察。过滤是完井后发现的潜在改进。

我很想看看我介绍的技术是否能帮助你扩展 Angular 应用。任何和所有的反馈都将在评论中收到！

在研究这篇文章时创建的代码样本也可以在 GitHub 上找到。

## 分享这篇文章