# 在 Drupal 7 中向视图显示表

> 原文：<https://www.sitepoint.com/exposing-tables-views-drupal-7/>

在本文中，您将学习如何将 Drupal 模块创建的表暴露给视图。你为什么想这么做？Views 是一个用于查询和显示信息的强大模块，在处理模块生成的定制数据时，它可以为您省去很多麻烦。

本教程的重点将放在没有被抽象为 Drupal 实体的数据上，而是简单地存储在一个表中。将自定义实体暴露给视图是我在另一个 Sitepoint.com 系列中涉及的一个主题，所以如果你也是这种情况，我建议你仔细阅读一下—[这里的](https://www.sitepoint.com/author/dsipos/)是我的文章列表。

我在[这个库](https://bitbucket.org/Swader/views-tables)中建立了一个名为`expose`的模块，如果你想了解更多，你可以克隆、跟随甚至构建它。master 分支包含一个 clean 模块，它简单地创建了我将在本文中使用的表来说明这个过程。`exposed-table`分支包含我在下面经历的一个接一个提交的步骤。

由于[模式定义](https://bitbucket.org/upchuk/views-tables/src/523272f9cfc86a155917b9341455ca28292162aa/expose.install?at=exposed-table)在模块中，我在此不再赘述。不过，我会给你一个简短的 MySQL 命令来生成几个虚拟记录，让你玩玩。希望这个命令能够提供足够的信息来描述表结构:

```
INSERT INTO `exposed` (`id`, `name`, `deadline`, `node_id`)
VALUES
	(1, 'Danny', 1399477939, 1),
	(2, 'Peter', 1399477957, 2);
```

## 向 Drupal 视图显示表

我们需要采取两个主要步骤来将我们的`exposed`表暴露给视图:让视图知道我们的模块及其视图相关文件，并告诉它我们的表的结构。第二步是复杂的一步，您可以进行不同程度的定制。

不过先说第一件事，实现 [hook_views_api()](https://api.drupal.org/api/views/views.api.php/function/hook_views_api/7) :

```
/**
 * Implements hook_views_api().
 */
function expose_views_api()
{
    return array(
        'api' => 3,
        'path' => drupal_get_path('module', 'expose') . '/includes/views',
    );
}
```

这是一个非常简单的实现，其中我们告诉模块使用哪个视图 API，以及视图相关的包含文件将被放置的位置。`hook_views_api()`实现放在`.module`文件中。

接下来，是时候实现 [hook_views_data()](https://api.drupal.org/api/views/views.api.php/function/hook_views_data/7) 了，这次是在一个名为`expose.views.inc`的文件中，该文件位于我们上面指定的文件夹(`/includes/views/`)中:

```
/**
 * Implements hook_views_data().
 */
function expose_views_data()
{

    $data = array();

    $data['exposed']['table']['group'] = t('Exposed');

    $data['exposed']['table']['base'] = array(
        'title' => t('Exposed'),
        'help' => t('Contains records we want exposed to Views.'),
    );

    return $data;

}
```

在这个实现中，我们在一个大数组中告诉视图关于我们的表的所有信息，我们在最后返回这个数组。首先，我们需要指定我们的表所属的组。这将用于标识 UI 中表格字段的来源。接下来，我们告诉视图`exposed`表是一个基表(因此有了`base`索引)。这意味着我们可以创建一个视图，使用这个表作为主查询。如您所见，对于这两条信息，`exposed`键实际上是数据库中表的名称。

这是前两步，我们已经可以看到 UI 中的一些变化。清除缓存并添加新视图。您会注意到，在`Content`、`User`等旁边，现在有了`Exposed`以及一种数据类型。这是从我们将表定义为基表时开始的。但是，如果您现在继续编辑视图，您将在`Fields`部分看到一个中断的处理程序，并且没有一个表列可以作为字段找到。接下来也向视图声明它们。

## 菲尔茨

既然 Views 知道了我们的表，那么让我们告诉它如何处理表的列来进行显示、过滤和排序。实现这一点的方法是指定关于每一列的一些元数据(如标题和描述)以及对于给定的任务，哪个视图处理程序用于该字段。让我们来看看如何对表中的前三列进行操作:`id`、`name`、`deadline`。在`hook_views_data()`实现中，在`$data`返回之前粘贴以下内容:

```
// The ID field
$data['exposed']['id'] = array(
    'title' => t('ID'),
    'help' => t('The record ID.'),
    'field' => array(
        'handler' => 'views_handler_field_numeric',
    ),
    'sort' => array(
        'handler' => 'views_handler_sort',
    ),
    'filter' => array(
        'handler' => 'views_handler_filter_numeric',
    ),
);

// The Name field
$data['exposed']['name'] = array(
    'title' => t('Name'),
    'help' => t('The record name.'),
    'field' => array(
        'handler' => 'views_handler_field',
    ),
    'sort' => array(
        'handler' => 'views_handler_sort',
    ),
    'filter' => array(
        'handler' => 'views_handler_filter_string',
    ),
);

// The Deadline field
$data['exposed']['deadline'] = array(
    'title' => t('Deadline'),
    'help' => t('The record deadline.'),
    'field' => array(
        'handler' => 'views_handler_field_date',
    ),
    'sort' => array(
        'handler' => 'views_handler_sort_date',
    ),
    'filter' => array(
        'handler' => 'views_handler_filter_date',
    ),
);
```

看起来很多，但不是。对于每个字段，我们在`exposed`数组中创建一个新数组(这是我们的新表)，它的键以表列命名。然后在一些基本信息(`title`和`help`)之后，我们告诉视图这三个操作中的每一个使用哪个处理程序:`field`、`sort`和`filter`。第一个用于显示字段，而后两个是不言自明的。

但是什么是处理程序呢？处理程序是位于视图模块的`handlers`文件夹中的视图类，*处理*这些操作。这里我们基本上需要指定应该使用哪一个类。例如，对于一个常规整数字段，我们将使用面向整数的处理程序进行显示，常规处理程序用于排序，整数处理程序用于过滤。对于截止日期，我们有日期处理程序。如果 Views 提供的缺省值不够用(可能是这种情况)，您可以扩展这些类，更改一些逻辑，并使用新的定制处理程序。

现在，您可以保存、清除缓存并刷新视图。你应该能够添加字段，过滤器和排序。同时，视图知道如何根据包含的数据类型处理每个字段。

## 连接

您可能已经注意到，表中还有一列我们还没有涉及到:即`node_id`。这是数据库中的一个整数，我想我们可以这样描述它。但是创建显示节点 id 的清单不会很有用。相反，这些 id 应该在关系中使用(表连接)，并从各自的节点中检索数据。为此，我们需要做两件事:告诉视图我们的表可以连接哪些其他表，并为`node_id`列设置适当的处理程序。

首先，让我们确保视图知道`exposed`表可以与`node`表连接。在`hook_views_date()`实现内部，在描述字段之前，粘贴以下内容:

```
$data['exposed']['table']['join'] = array(
    'node' => array(
        'left_field' => 'nid',
        'field' => 'node_id',
    ),
);
```

所以我们基本上添加了一个新的索引`join`,其中下面的数组键表示它将连接的表。`left_field`是目标表上的字段(`node`)，而`field`是当前(`exposed`)表上的字段。这可能会引起混淆，因为通常列名是相同的。

接下来，我们需要定义`node_id`字段，就像我们对其余部分所做的那样，但是需要一点额外的信息，关系(以及参数):

```
// The Node ID field
$data['exposed']['node_id'] = array(
    'title' => t('Node ID'),
    'help' => t('The record node ID.'),
    'field' => array(
        'handler' => 'views_handler_field_node',
    ),
    'sort' => array(
        'handler' => 'views_handler_sort',
    ),
    'filter' => array(
        'handler' => 'views_handler_filter_numeric',
    ),
    'relationship' => array(
        'base' => 'node',
        'field' => 'node_id',
        'handler' => 'views_handler_relationship',
        'label' => t('Node'),
    ),
    'argument' => array(
        'handler' => 'views_handler_argument_node_nid',
        'numeric' => TRUE,
        'validate type' => 'nid',
    ),
);
```

该字段的显示处理程序取自代表核心节点模块(在`modules/node`文件夹中找到)的视图模块实现。当在基于`node`表的视图上显示节点 ID 时，视图也使用这个处理程序。

过滤和排序的处理程序和以前一样，因为我们处理的是一个普通的整数。我们也可以对此进行过滤和排序。然而，我们也有一个`relationship`键，通过它我们可以指定连接表、字段和处理程序。`views_handler_relationship`是一个类，它不仅可以显示连接表中的字段，还可以对它们进行过滤和排序。

最后，为了更好地衡量，我还包含了`argument`键，通过它我们可以根据节点 ID 处理参数(上下文过滤器)。用于此的处理程序也是特定于节点 ID 的，就像视图模块实现它一样。对于其他字段，您也可以使用一些通用的参数处理程序。

因此，请清空您的缓存并刷新视图。现在，您应该能够基于节点 ID 添加关系了。然后，您将获得许多新的节点相关字段，您可以添加、过滤和排序这些字段。此外，您现在可以为这个字段添加一个上下文过滤器，并根据上下文中传递的 ID 动态地过滤视图。

## 结论

如您所见，无论是作为站点构建者还是开发者，使用视图都很容易。通过几个简单的步骤，我们成功地将自定义表中的数据暴露给视图。现在我们可以用它来显示、过滤和排序我们的数据，以及访问相关的 Drupal 实体。这确实是一个非常可插拔的系统，如果您对现有的处理程序所能做的不满意，可以随意扩展那些接近的处理程序，并根据您的数据对它们进行调整，然后在适当的字段定义中引用新的类。祝你好运！

## 分享这篇文章