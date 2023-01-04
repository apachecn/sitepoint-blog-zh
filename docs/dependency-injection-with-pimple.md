# 依赖注射伴丘疹

> 原文：<https://www.sitepoint.com/dependency-injection-with-pimple/>

在应用程序开发中，我们尝试创建独立的模块，以便在未来的项目中重用代码。但是，很难创建提供有用功能的完全独立的模块；除非管理得当，否则它们的依赖性会导致维护上的噩梦。这就是依赖注入派上用场的地方，因为它让我们能够注入代码正常运行所需的依赖，而无需将它们硬编码到模块中。

Pimple 是一个简单的依赖注入容器，它利用 PHP 的闭包以可管理的方式定义依赖。在本文中，我们将研究硬编码依赖的问题，依赖注入如何解决这些问题，以及如何使用 Pimple 使利用依赖注入的代码更易于维护。

## 具体依赖关系的问题

在编写应用程序时，我们使用了许多 PHP 类。一个类可能需要调用一个或多个其他类的方法来提供预期的功能，所以我们说第一个类依赖于其他类。例如:

```
<?php
class A
{
    public function a1() {
        $b = new B();
        $b->b1();
    }
}
```

类`A`依赖于类`B`。如果类`B`不可用，那么上述代码将不会工作。

此外，每当我们在一个类中硬编码创建一个对象时，我们就对该类产生了具体的依赖。具体的依赖是编写可测试代码的一个障碍。更好的方法是向类`A`提供类`B`的对象。这些对象可以通过`A`的构造函数或 setter 方法提供。

在我们继续之前，让我们先来看一个更现实的场景。

如今，在社交网站上分享内容非常普遍，大多数网站都在自己的网站上显示自己的社交资料。假设我们有一个名为`SocialFeeds`的类，它从 Twitter、脸书、Google+等社交网站生成提要。创建单独的类来处理这些服务。这里我们将看看与 Twitter 接口的类，`TwitterService`。

`SocialFeeds`类使用`TwitterService`请求一个 Twitter 提要。`TwitterService`与数据库交互，检索访问 API 的特定用户令牌。令牌被传递给`OAuth`类，后者使用提供的令牌检索提要，并将其返回给`SocialFeeds`类。

```
<?php
class SocialFeeds
{
    public function getSocialFeeds() {
        $twService = new TwitterService();
        echo $twService->getTweets();
    }
}
```

```
<?php
class TwitterService
{
    public function getTweets() {
        $db = new DB();
        $query = "Query to get user token from database";
        $token = $db->getQueryResults($query);

        $oauth = new OAuth();
        return $oauth->requestTwitterFeed($token);
    }
}
```

```
<?php
class OAuth
{
    public function requestTwitterFeed($token) {
        // Retrieve and return twitter feed using the token    		
    }
}
```

```
<?php
class DB
{
    public function getQueryResults($query) {
        // Get results from database and return token
    }
}
```

很明显`SocialFeeds`依赖于`TwitterService`。但是`TwitterService`依赖于`DB`和`OAuth`，所以`SocialFeeds`间接依赖于`DB`和`OAuth`。

那么问题是什么呢？`SocialFeeds`依赖于三个类的具体实现，所以如果没有其他类的实际实现，就不可能将`SocialFeeds`作为一个单独的单元进行测试。或者，假设我们想要使用不同的数据库或不同的 OAuth 提供者。在这种情况下，我们必须在整个代码中用新的类替换现有的类。

## 修复具体的依赖关系

这些依赖性问题的解决方案非常简单，只需在必要时动态提供对象，而无需使用具体的实现。有两种注入依赖的技术:基于构造函数的依赖注入和基于设置器的注入。

### 基于构造函数的注入

使用基于构造函数的依赖注入，依赖对象是在外部创建的，并作为参数传递给类的构造函数。我们可以将这些对象赋给类变量，并在类中的任何地方使用。`SocialFeeds`类的基于构造函数的注入如下所示:

```
<?php
class SocialFeeds
{
    public $twService;

    public function __construct($twService) {
        $this->twService = $twService;
    }

    public function getSocialFeeds() {
        echo $this->twService->getTweets();
    }
}
```

`TwitterService`的实例作为对象传递给构造函数。`SocialFeeds`仍然依赖于`TwitterService`，但是现在我们可以自由地提供不同版本的 Twitter 服务提供商，甚至是一个用于测试的模拟对象。`DB`和`OAuth`等级的定义与`TwitterService`类似。

```
<?php
$db = new DB();
$oauth = new OAuth();
$twService = new TwitterService($db, $oauth);
$socialFeeds = new SocialFeeds($twService);
$socialFeeds->getSocialFeeds();
```

### 基于 Setter 的注入

对于基于 setter 的注入，对象是通过 setter 方法而不是构造函数来提供的。下面是对`SocialFeeds`类的依赖注入的基于 setter 的实现:

```
<?php
class SocialFeeds
{
    public $twService;

    public function getSocialFeeds() {
        echo $this->twService->getTweets();
    }

    public function setTwitterService($twService) {
        $this->twService = $twService;
    }
}
```

包括`DB`和`OAuth`的初始化代码现在看起来是这样的:

```
<?php
$db = new DB();
$oauth = new OAuth();
$twService = new TwitterService();
$twService->setDB($db);
$twService->setOAuth($oauth);

$socialFeeds = new SocialFeed();
$socialFeeds->setTwitterService($twService);
$socialFeeds->getSocialFeeds();
```

### 构造函数与设置器注入

由您决定是选择基于构造函数的注入还是基于 setter 的注入。当需要所有依赖项来实例化类时，基于构造函数的注入是合适的。当在每个场合都不需要依赖时，基于 Setter 的注入是合适的。

**优势**

*   构造函数——一个类的所有依赖关系都可以通过查看该类的构造函数来识别
*   Setter——添加一个新的依赖项就像添加一个新的 setter 方法一样简单，不会破坏现有的代码

**缺点**

*   构造函数——添加新的依赖项会增加构造函数的参数；现有代码需要在整个应用程序中更新，以提供新的依赖关系
*   setter——我们必须手动搜索必要的依赖项，因为它们在任何地方都没有指定

有了依赖注入和各种注入技术的知识，是时候看看 Pimple 了，看看它是如何适应的。

## 丘疹在 DI 中的作用

您可能想知道，当我们已经可以使用前面提到的技术注入依赖项时，为什么还需要 Pimple。要回答这个问题，我们需要看看干原理。

> 不要重复你自己(DRY)是软件开发的一个原则，旨在减少各种信息的重复，在多层架构中特别有用。DRY 原则被表述为“在一个系统中，每一项知识都必须有一个单一的、明确的、权威的表述—[维基百科](http://en.wikipedia.org/wiki/Don%27t_repeat_yourself "Don't repeat yourself - Wikipedia, the free encyclopedia")

考虑基于构造函数的注入示例。每当我们需要一个`SocialFeed`类的对象时，我们必须重复实例化和传递其依赖关系的整个设置过程。按照 DRY 的说法，应该避免这样的代码，以防止维护上的麻烦。Pimple 充当了定义这种依赖关系的容器，以避免重复。

让我们看一个简单的例子来了解 Pimple 是如何工作的。

```
<?php
$container = new Pimple();
$container['class_name'] = 'Test';
$container['object_name'] = function ($c) {
    return new $c['class_name']();
};

$testClass = $container['object_name'];
```

创建一个`Pimple`实例作为存储依赖关系的容器。它实现了 SPL `ArrayAccess`接口，因此使用它非常类似于使用数组。首先，我们定义了一个键，它保存了我们想要的任意类的名称。然后我们定义了一个闭包来返回作为服务的指定类的实例。注意，`$c`将被传递一个容器的实例，所以我们可以随意引用其他已定义的键；通过`$c`变量，每个定义的参数或对象在闭包中都是可用的。现在，每当我们需要一个类的实例时，我们可以引用键来检索对象。

让我们把`SocialFeeds`的例子转换成 Pimple。官方 Pimple 站点上的例子展示了基于构造函数的注入，所以这里我们将说明基于 setter 的注入。请记住，我们在前面定义的任何 setter 方法或代码都不需要修改就可以使用 Pimple——我们只是封装了逻辑。

```
<?php
$container = new Pimple();
$container['oauth'] = function($c) {
	return new OAuth();
};
$container['db'] = function($c) {
	return new DB();
};
$container['tweet_service'] = function($c) {
	$twService = new TwitterService();
	$twService->setDB($c['db']);
	$twService->setOauth($c['oauth']);
	return $twService;
};
$container['social_feeds'] = function($c) {
	$socialFeeds = new SocialFeeds();
	$socialFeeds->setTwitterService($c['tweet_service']);
	return $socialFeeds;
};

$socialFeeds = $container['social_feeds'];
$socialFeeds->getSocialFeeds();
```

`DB`和`OAuth`类都是独立的模块，所以我们在闭包里直接返回它们的一个新实例。然后，我们使用基于 setter 的注入向`TwitterService`类添加依赖项。我们已经在容器中添加了`DB`和`OAuth`类，所以我们可以使用`$c['db']`和`$c['oauth']`在函数中直接访问它们。

现在，依赖关系作为服务封装在容器中。每当我们想使用不同的`DB`类或不同的`OAuth`服务时，我们只需替换容器语句中的类，一切都会完美地工作。使用 Pimple，您只需在一个地方添加新的依赖项。

## 高级粉刺用法

在上面的场景中，无论何时请求，Pimple 都会从闭包返回每个类的新实例。在某些情况下，我们需要使用相同的对象，而不需要每次都初始化新的实例，例如连接到数据库就是一个很好的例子。

Pimple 提供了使用共享对象返回相同实例的能力，这样做需要我们通过如下所示的`share()`方法指定闭包:

```
<?php
$container['db'] = $container->share(function ($c) {
    return new DB();
});
```

此外，到目前为止，我们已经在 Pimple 容器中的一个位置定义了所有的依赖项。但是请考虑这样一种情况，我们需要服务及其依赖项，但是配置方式与原始方式略有不同。例如，假设我们需要访问一个 ORM 来获得`TwitterService`类的某些功能。我们不能改变现有的闭包，因为这会迫使所有现有的功能都使用 ORM。

Pimple 提供了方法`extend()`来动态修改现有的闭包，而不影响原来的实现。考虑以下代码:

```
<?php
$container['tweet_service'] = $container->extend('tweet_service', function($twSservice, $c) {
	$twService->setDB(new ORM());
	return $twService;
});
```

现在我们能够在特殊的场景中使用不同的扩展版本的`tweet_service`。第一个参数是服务的名称，第二个是访问对象实例和容器的函数。

的确，`extend()`是一种动态添加依赖项以适应不同情况的强大方法，但是要确保将服务的扩展版本限制到最小，因为它会增加重复代码的数量。

## 摘要

管理依赖关系是 web 应用程序开发中最基本也是最困难的任务之一。我们可以通过构造函数和 setter 方法使用依赖注入来有效地管理它们。依赖注入也有自己的麻烦，Pimple 通过提供一个轻量级的容器来创建和存储对象依赖，解决了这个问题。

欢迎在下面的评论中分享你在项目中管理依赖关系的经验，以及你对 Pimple 作为依赖注入容器的看法。

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章