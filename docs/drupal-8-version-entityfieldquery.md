# EntityFieldQuery 的 Drupal 8 版本

> 原文：<https://www.sitepoint.com/drupal-8-version-entityfieldquery/>

![](img/a935c48e877a70a6250c9604c8474c63.png)

尽管 Drupal 7 core 缺乏处理其全新实体系统的适当方式(我们目前依赖于伟大的[实体](https://drupal.org/project/entity)模块)，但它确实给了我们 [EntityFieldQuery](https://api.drupal.org/api/drupal/includes%21entity.inc/class/EntityFieldQuery/7) 。对于那些不知道的人来说，`EntityFieldQuery`是一个非常强大的查询类，用于以编程方式搜索 Drupal 实体(节点、用户等)。

它提供了许多方法，使得基于条件(如字段值或类属性)查询实体变得容易。如果你不知道它是如何工作的，请随意查看这个[文档页面](https://drupal.org/node/1343708)或[这个关于这个主题的很棒的教程](https://www.sitepoint.com/understanding-drupals-entityfieldquery/)。

在这篇文章中，我将讨论 Drupal 8 中用于查询实体的内容。没有更多的`EntityFieldQuery`，但是有一个`entity.query`服务将为给定的实体类型实例化一个查询对象(并且实现了[\ Drupal \ Core \ Entity \ Query \ Query interface](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Entity%21Query%21QueryInterface.php/interface/QueryInterface/8))。我们可以通过 [\Drupal](https://api.drupal.org/api/drupal/core%21lib%21Drupal.php/function/Drupal%3A%3AentityQuery/8) 名称空间或者使用依赖注入来静态访问这个服务。

首先，我们将查看查询节点实体，然后我们将了解如何加载它们。同样的技术也适用于其他内容实体(用户、评论等)，也适用于配置实体，这真的很酷。

## 实体查询服务

如前所述，我们可以通过两种方式访问用于查询实体的`entity.query`服务。静态地，我们可以这样做:

```
$query = \Drupal::entityQuery('node');
```

代替`node`，我们可以指定任何其他实体类型的机器名，我们在`$query`变量中得到的是我们实体类型的查询对象。在`\Drupal`名称空间上的`entityQuery()`静态方法是使用`entity.query`服务实现这一点的捷径。

或者(也是强烈推荐的方法)是使用[依赖注入](http://www.webomelette.com/drupal-8-dependency-injection-service-container-and-all-jazz)。

如果您可以访问容器，就可以从那里加载服务，然后获得正确的查询对象:

```
$entity_query_service = $container->get('entity.query');
$query = $entity_query_service->get('node');
```

如您所见，我们在`entity_query`服务上使用了`get()`方法来实例化实体类型的查询对象，并将机器名作为参数传递。

## 查询实体

让我们展示几个使用这个对象查询节点实体的例子。

一个非常简单的查询返回发布的节点:

```
$query = \Drupal::entityQuery('node')
    ->condition('status', 1);

$nids = $query->execute();
```

`$nids`将是一个实体 id(在我们的例子中是节点 id)的数组，由修订 id(如果实体类型启用了修订)或实体 id(如果没有)作为关键字。让我们看一个添加更多属性条件和字段条件的示例:

```
$query = \Drupal::entityQuery('node')
    ->condition('status', 1)
    ->condition('changed', REQUEST_TIME, '<')
    ->condition('title', 'cat', 'CONTAINS')
    ->condition('field_tags.entity.name', 'cats');

$nids = $query->execute();
```

在这个查询中，我们检索在当前时间之前最后更新的所有已发布节点的节点 id，这些节点在标题**中包含单词`cat`，在`field_tags`中有一个分类术语`cats`作为引用。**

正如你所看到的，在`propertyCondition`和`fieldCondition`之间已经没有区别了(就像在 D7 中有`EntityFieldQuery`)。此外，我们可以包含基于引用实体的条件，将`entity.(column)`附加到实体引用字段名上。

需要注意的重要一点是，我们在`condition()`方法中也有`langcode`参数，通过它我们可以指定查询中应该包含节点的什么翻译。例如，我们可以检索节点 id，它在一种语言的字段中包含一个特定的值，但在另一种语言的同一字段中包含另一个值。

关于`condition()`方法[的更多信息，你应该查阅 API 文档](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Entity%21Query%21QueryInterface.php/function/QueryInterface%3A%3Acondition/8)。

接下来我们要看的是使用条件组(包括`AND`和`OR`)进行更强大的查询:

```
$query = \Drupal::entityQuery('node')
    ->condition('status', 1)
    ->condition('changed', REQUEST_TIME, '<');

$group = $query->orConditionGroup()
    ->condition('title', 'cat', 'CONTAINS')
    ->condition('field_tags.entity.name', 'cats');

$nids = $query->condition($group)->execute();
```

在上文中，我们修改了之前的查询，以便检索标题中包含`cat`字符串或者在`field_tags`字段中引用了术语`cats`的节点。为此，我们创建了一个[或条件组](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Entity%21Query%21QueryInterface.php/function/QueryInterface%3A%3AorConditionGroup/8)对象，然后将它作为条件传递给查询。我们还可以将多个条件组合在一个[和条件组](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Entity%21Query%21QueryInterface.php/function/QueryInterface%3A%3AandConditionGroup/8)中。

在`QueryInterface`上有许多其他方法可以扩展查询(比如排序、范围等)。我鼓励您在文档中查看它们，并用它们进行实验。但是现在，让我们快速看一下如何处理结果集。

## 加载实体

正如我上面提到的，我们一直使用的查询对象上的`execute()`方法返回一个实体 id 数组。假设我们现在必须加载那些实体对象并使用它们。我们如何做到这一点？

在 Drupal 7 中，我们有一个 [entity_load()](https://api.drupal.org/api/drupal/includes%21common.inc/function/entity_load/7) 函数，我们向它传递一个 id 数组，它将返回一个对象数组。在 Drupal 8 中，保留了这个助手函数，您可以用几乎相同的方式使用它，除了一次只能用于一个实体:

```
$node = entity_load('node', $nids[1]);
```

返回值是一个节点对象。要加载多个节点，可以使用 [entity_load_multiple()](https://api.drupal.org/api/drupal/core%21includes%21entity.inc/function/entity_load_multiple/8) 函数:

```
$nodes = entity_load_multiple('node', $nids);
```

然后返回以 id 为关键字的实体对象数组。

一个额外的信息是，这两个函数都是相关实体的存储管理器的包装器。它们基本上静态地检索存储管理器，然后对其分别调用 [load()](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Entity%21EntityStorageInterface.php/interface/EntityStorageInterface/8) 和 [loadMultiple()](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21Entity%21EntityStorageInterface.php/function/EntityStorageInterface%3A%3AloadMultiple/8) 方法:

静态地，您可以做类似的事情:

```
$node_storage = \Drupal::entityManager()->getStorage('node');

// Load multiple nodes
$node_storage->loadMultiple($ids);
// Load a single node
$node_storage->load($id);
```

但是更好的是，您可以使用依赖注入并从容器中检索存储类:

```
$node_storage = $container->get('entity.manager')->getStorage('node');
```

然后继续装货。如果可能的话，使用依赖注入通常是推荐的方法，比如在一个类中工作。这使得测试您的类更加容易，并且更好地将它与应用程序的其余部分分离。

## 结论

在本文中，我们看到了如何在 Drupal 8 中查询和加载实体。D7 `EntityFieldQuery`类经过了彻底的改进，变成了用于查询内容和配置实体的健壮 API。我们已经看到了查询内容实体，但是系统对配置实体的工作方式是一样的。这对于新的 Drupal 8 实体系统来说是一个小小的胜利。

我们还看到了如何基于这些查询中产生的 id 加载实体，以及执行这些操作的包装器函数背后的实际内容。接下来，我们将在 Drupal 8 中定义我们自己的内容实体类型。为了复习我们在 Drupal 7 中是如何做的，你可以查看这些关于这个主题的文章。

## 分享这篇文章