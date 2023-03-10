# 了解 Drupal 的 EntityFieldQuery

> 原文：<https://www.sitepoint.com/understanding-drupals-entityfieldquery/>

## 介绍

当构建复杂的 web 应用程序时，您最终将不得不与数据库进行交互。要在 Drupal 中检索数据，可以使用提供的数据库抽象层，这需要一些 SQL 知识才能正确使用。Drupal 7 提供了 EntityFieldQuery API，它允许您从 Drupal 获取有关实体的信息，而无需实际构建 SQL 查询。在本文中，让我们看看如何使用 EntityFieldQuery API 从 Drupal 获取数据并在我们的模块中使用它。

## EntityFieldQuery 类

EntityFieldQuery 类用于在 Drupal 中查找符合某些条件的实体。EntityFieldQuery 位于 Drupal 安装中的 includes/entity.inc 文件中。这个类有不同的方法来指定实体类型和特定的条件，基于这些条件我们可以过滤我们想要获取的数据。我们将在本文中详细讨论这些方法。
在使用 EntityFieldQuery 时，您将遵循的基本用法首先是创建该类的对象。一旦创建了对象，您将向它添加一些条件，然后调用 execute 方法来获取结果。

通用模板如下

```
$entityquery = new EntityFieldQuery();
/// Set some conditions 
$result = $query->execute ();
```

我们现在要创建一个 Drupal 模块，它将安装 3 个新的 Drupal 节点类型:产品、电影和书籍，以及一个显示我们结果的模块。你可以在本文的[中看到如何创建节点类型](https://www.sitepoint.com/creating-a-new-drupal-node-type/)

该模块的代码是

*entityquery.info*

```
name = entityquery
description =  Shows how to use entity query to fetch data from drupal
package =  Entity  Query  Example core =  7.x
```

*entityquery.install*

```
<?php
/**
 * Implement hook_install().
 */
function entityquery_install() {
    node_types_rebuild();
    $types = node_type_get_types();
    node_add_body_field($types['product']);
    node_add_body_field($types['movies']);
    node_add_body_field($types['books']);
}
```

*entityquery.module*

```
<?php
/**
 * Implement hook_node_info()
 */
function entityquery_node_info() {
    return array(
        'product' => array(
            'name' => t('Product'),
            'base' => 'product',
            'description' => t('You can define new Products here'),
            'has_title' => TRUE,
            'title_label' => t('Product title')
         ),
        'movies' => array(
            'name' => t('Movies'),
            'base' => 'movies',
            'description' => t('You can define new Movies here'),
            'has_title' => TRUE,
            'title_label' => t('Movie title')
         ),
        'books' => array(
            'name' => t('Books'),
            'base' => 'Books',
            'description' => t('You can define new Books here'),
            'has_title' => TRUE,
            'title_label' => t('Books title')
         )
    );
}

/**
 * Implement hook_form()
 */
function product_form($node, $form_state) {
    return node_content_form($node, $form_state);
}

/**
 * Implement hook_form()
 */
function movies_form($node, $form_state) {
    return node_content_form($node, $form_state);
}

/**
 * Implement hook_form()
 */
function books_form($node, $form_state) {
    return node_content_form($node, $form_state);
}

/**
 * Implement hook_block_info().
 */
function entityquery_block_info() {
  $blocks = array();

  $blocks['entityqueryblock'] = array(
    'info' => t('A block to display results from entityquery'),
  );

  return $blocks;
}

/**
 * Implement hook_block_view().
 */
function entityquery_block_view($block_name = '') {
  if ($block_name == 'entityqueryblock') {
    $content ='';
    $block = array(
      'subject' => t('A block to display results from entityquery'),
      'content' => $content,
    );
    return $block;
  }
}
```

将这个模块放在 modules 文件夹中，如果一切顺利，您将能够在模块列表中看到 entityquery 模块，如下所示。

![](img/737ae8b2f09eac5f0ad9f2105311ef20.png)

安装完模块后，您应该能够在 Drupal 管理面板的 Add content 部分看到新的节点类型，如下所示

![](img/b386fbdae2e1cd24a2d255354149dcbd.png)

### 使用 EntityFieldQuery 执行简单查询

一旦我们设置了使用 EntityFieldQuery 的基本模块，让我们开始编写一些查询来从 Drupal 获取数据。我们将编写的第一个查询是使用 EntityFieldQuery 获取所有节点。然后我们将使用它来显示块中节点的标题。

如前所述，首先要做的是创建一个 EFQ 的实例。要获取一种类型的实体，你必须给它添加`entity_type`条件。在这种情况下，我们希望获取节点，因此其代码如下:

```
$query = new EntityFieldQuery();
$query->entityCondition('entity_type', 'node');
```

使用函数`entityCondition`设置实体条件，其中我们将实体类型设置为节点。一旦我们设置了实体条件，我们就可以执行查询了。节点 id 以数组的形式返回到结果的节点键中。显示节点的完整代码如下:

```
$query = new EntityFieldQuery();
    $query
    ->entityCondition('entity_type', 'node');

    $result = $query->execute();
    $nodes = array();
    if (isset($result['node'])) {
      $nids = array_keys($result['node']);
      $nodes = node_load_multiple($nids);
    }

    $list = array();
    foreach ($nodes as $node) {
      $options = array('absolute' => TRUE);
      $url = url('node/' . $node->nid, $options);
      $list[] = '<a href='.$url.'>'.$node->title.'</a>';
    }

    $theme_args = array('items' => $list, 'type' => 'ol');
    $content = theme('item_list', $theme_args);

    $block = array(
      'subject' => t('A block to display results from entityquery'),
      'content' => $content,
    );
    return $block;
```

现在，如果您去查看您的块，您应该能够看到其中的所有节点，如下所示

![](img/5baface674df1d63a76ddbc9d6f37751.png)

您现在应该尝试添加不同的节点，如电影和书籍，并检查它们是否显示在块中。在上面的代码中，一旦我们从 EntityFieldQuery 获得了节点 id，我们就使用`node_load_multiple`加载节点并显示它们。

### 向 EntityFieldQuery 添加实体条件

您可以添加实体条件以仅显示特定类型的节点。如果我们只想显示该类型节点的“产品”,我们将使用的查询是:

```
$query = new EntityFieldQuery();
    $query
    ->entityCondition('entity_type', 'node')
    ->entityCondition('bundle', 'product'); 

    $result = $query->execute();
```

现在，如果我们检查我们的块，它将只显示产品:

![](img/df71ac3a753703cd7deaa5fd64ce6117.png)

我们甚至可以使用`entityCondition`指定一个节点类型数组来获取多种类型的节点。要从数据库中获取所有产品和电影:

```
$query = new EntityFieldQuery();
    $query
    ->entityCondition('entity_type', 'node')
    ->entityCondition('bundle', array('product', 'movies')); 

    $result = $query->execute();
```

### 向 EntityFieldQuery 添加属性条件

我们甚至可以在查询中添加属性条件。这取决于您要查询的实体类型。在大多数情况下，属性条件将基于您正在查询的实体类型的字段。例如，您可以查询已发布或由特定用户编写的节点等。

使用`propertyCondition`只显示已发布节点的查询如下

```
 $query = new EntityFieldQuery();
    $query
    ->entityCondition('entity_type', 'node')
    ->propertyCondition('status', 1);

    $result = $query->execute();
```

## 向 EntityFieldQuery 添加字段条件并排序

字段条件特定于实体上存在的字段。因此，假设我们想找到所有在正文中带有“折扣”一词的产品，我们可以使用字段条件来实现。我们甚至可以使用`propertyOrderBy`函数对结果进行排序。

如果我们希望产品和电影的正文中有“折扣”,按其创建的降序排列，查询将如下:

```
 $query = new EntityFieldQuery();
    $query
    ->entityCondition('entity_type', 'node')
    ->entityCondition('bundle', array('product', 'movies'))
    ->propertyCondition('status', 1)
    ->fieldCondition('body', 'value', 'discount', 'CONTAINS')
    ->propertyOrderBy('created', 'DESC');

    $result = $query->execute();
```

该查询的输出如下所示

![](img/fda6f8ec6f3e61e2d57f53c1534fd11e.png)

### 扩展 EntityFieldQuery 类

有时，您可能需要在许多地方构建相同的查询。一个很好的抽象方法是扩展 EntityFieldQuery 类并创建自己的子类。

假设您希望构建一个查询，以便按创建日期的升序获取所有活动用户:

```
class ActiveUsersEntityFieldQuery extends EntityFieldQuery {

  public function __construct() {

    $this
      ->entityCondition('entity_type', 'user')
      ->propertyCondition('status', 1)
      ->propertyOrderBy('created', 'ASC');

  }

}
```

现在，您可以在任何地方使用该查询，如下所示:

```
$activeUsers = new ActiveUsersEntityFieldQuery();
$result2 = $activeUsers->execute();
```

## 结论

Drupal 中的许多模块需要您从数据库中获取实体内容。你可以直接使用 Drupal 数据库层，但是你至少要有 SQL 的工作知识，这可能更容易出错。`EntityFieldQuery`类是 Drupal 核心的一部分，你可以很容易地使用它而不依赖于其他模块。祝您创建下一个 Drupal 模块愉快！

## 分享这篇文章