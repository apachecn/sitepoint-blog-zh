# 使用 Silex 集成弹性搜索

> 原文：<https://www.sitepoint.com/integrate-elasticsearch-silex/>

在上一篇文章中，我开始探索 Drupal 7 和 [Elasticsearch](http://www.elasticsearch.org/) 引擎之间的集成。我们的目标是看看如何将这些开源技术结合起来，以实现一个高性能的应用程序，充分利用两者的优势。如果你刚刚加入我们，你应该看看[这个资源库](https://github.com/upchuk/d7-elastic-silex)，里面包含了这些文章的相关代码。

![esdrupalsilex](img/f9050a0dd97ce34bc9d51f63c350e2d8.png)

我们现在将创建一个小型 Silex 应用程序，它直接从 Elasticsearch 读取数据并返回给用户。

## Silex 应用程序

Silex 是一个很棒的 PHP 微框架，由 Symfony 项目的幕后人员开发。事实上，它主要使用 Symfony 组件，但层次更简单。让我们看看如何使用 Silex 应用程序快速入门。

方法不止一个。您可以将它作为依赖项添加到现有的基于 composer 的项目中:

```
"silex/silex": "~1.2",
```

或者，您甚至可以使用创建者提供的一个漂亮的小框架来创建一个新项目:

```
composer.phar create-project fabpot/silex-skeleton
```

不管你的项目是如何设置的，为了访问 Elasticsearch，我们需要使用它的 [PHP SDK](http://www.Elastic.org/guide/en/Elastic/guide/current/index.html) 。需要添加到 Composer:

```
"elasticsearch/elasticsearch": "~1.0",
```

如果我们想用 Twig 输出数据，我们也需要这个(当然如果还没有的话):

```
"symfony/twig-bridge": "~2.3"
```

为了使用 SDK，我们可以将其作为服务公开给小型 Silex 依赖注入容器 [Pimple](https://github.com/silexphp/Pimple) (比听起来容易得多)。根据我们的项目是如何建立的，我们可以在很多地方这样做(参见[库](https://github.com/upchuk/d7-elastic-silex)中的例子)。但基本上，在我们实例化新的 Silex 应用程序后，我们可以添加以下内容:

```
$app['elasticsearch'] = function() {
  return new Client(array());
};
```

这在我们的应用程序中创建了一个名为`elasticsearch`的新服务，它实例化了一个 Elasticsearch `Client`类的对象。别忘了我们需要*在顶部使用*那个类:

```
use Elasticsearch\Client;
```

现在，无论在哪里，我们都可以通过简单地引用`$app`对象中的属性来获得 Elasticsearch 客户端:

```
$client = $app['elasticsearch'];
```

## 连接到 Elasticsearch

在上一篇文章中，我们已经设法将节点数据放入到`node`索引中，每个节点类型都给出了一个 Elasticsearch 文档类型的名称。例如，这将返回所有文章节点类型:

```
http://localhost:9200/node/article/_search
```

我们还看到了如何为我们的 Elasticsearch SDK 实例化一个客户端。现在是时候以某种方式使用它了。一种方法是创建控制器:

```
<?php

namespace Controller;

use Silex\Application;
use Symfony\Component\HttpFoundation\Response;

class NodeController {

  /**
   * Shows a listing of nodes.
   *
   * @return \Symfony\Component\HttpFoundation\Response
   */
  public function index() {
    return new Response('Here there should be a listing of nodes...');
  }

  /**
   * Shows one node
   *
   * @param $nid
   * @param \Silex\Application $app
   * @return mixed
   */
  public function show($nid, Application $app) {
    $client = $app['elasticsearch'];
    $params = array(
      'index' => 'node',
      'body' => array(
        'query' => array(
          'match' => array(
            'nid' => $nid,
          ),
        ),
      )
    );

    $result = $client->search($params);
    if ($result && $result['hits']['total'] === 0) {
      $app->abort(404, sprintf('Node %s does not exist.', $nid));
    }

    if ($result['hits']['total'] === 1) {
      $node = $result['hits']['hits'];
      return $app['twig']->render('node.html.twig', array('node' => reset($node)));
    }
  }
}
```

根据你如何组织你的 Silex 应用程序，这个控制器类有很多地方可以去。在我的例子中，它位于`src/Controller`文件夹中，由 Composer 自动加载。

我们还需要创建一个映射到这个控制器的路由。同样，有几种不同的方法来处理这个问题，但是在我的例子中，我有一个位于`src/`文件夹中的`routes.php`文件，它是`index.php`中所必需的:

```
<?php

use Symfony\Component\HttpFoundation\Response;

/**
 * Error handler
 */
$app->error(function (\Exception $e, $code) {
  switch ($code) {
    case 404:
      $message = $e->getMessage();
      break;
    default:
      $message = 'We are sorry, but something went terribly wrong. ' . $e->getMessage();
  }

  return new Response($message);
});

/**
 * Route for /node
 */
$app->get("/node", "Controller\\NodeController::index");

/**
 * Route /node/{nid} where {nid} is a node id
 */
$app->get("/node/{nid}", "Controller\\NodeController::show");
```

那么在我上面的例子中会发生什么呢？首先，我为应用程序定义了一个错误处理程序，这样我就可以看到被捕获的异常并将它们打印在屏幕上。没什么大不了的。接下来，我定义了两条路由，它们映射到我之前定义的两个控制器方法。但是为了简洁起见，我只举例说明了预期的`show()`方法可能做的事情:

*   获取 Elasticsearch 客户端
*   构建 Elasticsearch 查询参数(类似于我们在 Drupal 环境中所做的)
*   执行查询
*   检查结果，如果找到了一个节点，就用一个 Twig 模板渲染它，并将节点数据传递给它。
*   如果没有找到结果，用 404 中止这个过程，它调用我们的错误处理程序来处理上面声明的 HTTP 代码。

如果你想学习这个例子，请记住，要使用 Twig，你需要将它注册到你的应用程序中。如果您已经通过 composer 将它放在供应商文件夹中，这并不困难。

实例化 Silex 应用程序后，可以注册提供者:

```
$app->register(new TwigServiceProvider());
```

确保*使用*顶部的类:

```
use Silex\Provider\TwigServiceProvider;
```

并通过一些基本配置将其添加为服务:

```
$app['twig'] = $app->share($app->extend('twig', function ($twig, $app) {
  return $twig;
}));
$app['twig.path'] = array(__DIR__.'/../templates');
```

现在，您可以在应用程序的`templates/`文件夹中创建模板文件。为了学习更多关于设置 Silex 应用程序的知识，我鼓励你阅读这篇关于框架的介绍。

继续我们的控制器示例，这里我们有几个输出节点数据的模板文件。

在一个`page.html.twig`文件中:

```
<!DOCTYPE html>
	<html>
	<head>
	    {% block head %}
	        <title>{% block title %}{% endblock %} - My Elasticsearch Site</title>
	    {% endblock %}
	</head>
	<body>
	<div id="content">{% block content %}{% endblock %}</div>
	</body>
	</html>
```

在控制器中用于渲染的`node.html.twig`文件中:

```
{% extends "page.html.twig" %}

{% block title %}{{ node._source.title }}{% endblock %}

{% block content %}

    <article>
        <h1>{{ node._source.title }}</h1>

        <div id="content">

            {% if node._source.field_image %}
                <div class="field-image">
                    {% for image in node._source.field_image %}
                        <img src="{{ image.url }}" alt="img.alt"/>
                    {% endfor %}
                </div>
            {% endif %}

            {% if node._source.body %}
                <div class="field-body">
                    {% for body in node._source.body %}
                        {{ body.value|striptags('<p><div><br><img><a>')|raw }}
                    {% endfor %}
                </div>
            {% endif %}
        </div>
    </article>

{% endblock %}
```

这只是在浏览器中打印节点数据的一些基本模板(否则就不那么有趣了)。我们有一个基本文件和一个扩展文件，将节点标题、图像和正文输出到屏幕上。

或者，您也可以借助于`JsonResponse`类从控制器返回一个 JSON 响应:

```
use Symfony\Component\HttpFoundation\JsonResponse;
```

从您的控制器返回一个新的实例，并将值传递给它:

```
return new JsonResponse($node);
```

您可以轻松地构建这样的 API。但是现在，这应该已经起作用了。将浏览器指向`http://localhost/node/5`，您应该会看到来自 Drupal 的 node 5 的数据(如果有的话)。有一个很大的不同:它要快得多。没有引导，主题层，数据库查询等。另一方面，除了使用 Silex/Symfony 组件自己构建之外，您也没有任何有用的东西。这可能是一件好事，也可能是一件坏事，取决于你所从事的项目类型。但关键是你可以选择为这个积分画一些线，并决定它的范围。

一种方法是用 Twig 甚至 Angular.js 构建整个前端，用 Silex 作为 API 后端。另一种方法是在一个 Drupal 页面上使用 Silex/Elasticsearch，并且只用于更好的内容搜索。介于两者之间的某个地方可能会使用这样的解决方案，用于专门处理大量数据的 Drupal 站点的整个部分(如视频商店或其他)。这取决于你。

## 结论

在本文中，我们已经看到了如何快速设置一个 Silex 应用程序，并使用它从 Elasticsearch 返回一些数据。我们的目标不是学习这些技术是如何工作的，而是探索集成它们的选择。起点是 Drupal 网站，它可以作为一个完美的内容管理系统，如果构建得当，可以高度扩展。在那里管理的数据可以转储到由 Elasticsearch 支持的高性能数据存储中，并在 Silex(一个精简而快速的 PHP 框架)的帮助下为最终用户再次检索。

## 分享这篇文章