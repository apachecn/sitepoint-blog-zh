# 用 Drupal 安装和集成 Elasticsearch

> 原文：<https://www.sitepoint.com/install-integrate-elasticsearch-drupal/>

在本教程中，我将探讨使用 Drupal 7 作为内容管理系统来支持另一个高性能应用程序的可能性。为了说明后者，我将使用 [Silex](https://www.sitepoint.com/introduction-silex-symfony-micro-framework/) PHP 微框架和 [Elasticsearch](http://www.Elastic.org/guide/en/Elastic/guide/current/index.html) 作为数据源。目标是创建一个概念证明，演示这三种技术的结合使用。

![esdrupalsilex](img/f9050a0dd97ce34bc9d51f63c350e2d8.png)

这篇文章附带了一个 [git 库](https://github.com/upchuk/d7-elastic-silex),您应该去看看，它包含了比教程本身更完整的代码。此外，如果您对正在使用的三个开源项目中的任何一个都不熟悉，我建议您访问上面的链接，并查看它们各自网站上的文档。

本教程将分为两部分，因为有相当多的地面覆盖。

在这一部分中，我们将在服务器上设置 Elasticsearch，并通过创建一个小的定制模块将它与 Drupal 集成，该模块将在 Elasticsearch 中插入、更新和删除 Drupal 节点。

在第二部分中，我们将创建一个小型 Silex 应用程序，它直接从 Elasticsearch 获取并显示节点数据，完全绕过 Drupal 安装。

## 弹性搜索

第一步是在服务器上安装 Elasticsearch。假设你使用的是 Linux，你可以遵循这个指南并设置它在服务器启动时运行。这里有许多配置选项可以设置。

需要记住的一件非常重要的事情是，Elasticsearch 没有访问控制，因此，一旦它在您的服务器上运行，它就可以通过(默认)9200 端口公开访问。为了避免出现问题，请确保在配置文件中取消对该行的注释:

```
network.bind_host: localhost
```

并添加以下一条:

```
script.disable_dynamic: true
```

这些选项确保 Elasticsearch 不能从外部访问，也不允许使用动态脚本。这些是建议您采取的安全措施。

## Drupal

下一步是在同一台服务器上设置 Drupal 站点。使用 [Elasticsearch 连接器 Drupal 模块](https://www.drupal.org/project/elasticsearch_connector)，您可以获得与 Elasticsearch 实例的一些集成:它附带了用于 Elasticsearch 的 PHP SDK、关于 Elasticsearch 实例的一些统计数据和一些其他有用的子模块。我会让你在闲暇时探索这些。

启用连接器模块后，在您的自定义模块中，您可以检索 Elasticsearch 客户端对象包装器来访问数据:

```
$client = elastic_connector_get_client_by_id('my_cluster_id');
```

这里，`my_cluster_id`是您赋予 Elasticsearch 集群的 Drupal 机器名(在`admin/config/elasticsearch-connector/clusters`)。`$client`对象现在将允许你执行各种操作，正如我在上面提到的文档中所展示的。

### 插入数据

我们需要做的第一件事是确保将一些 Drupal 数据插入到 Elasticsearch 中。现在坚持节点，我们可以写一个 [hook_node_insert()](https://api.drupal.org/api/drupal/modules!node!node.api.php/function/hook_node_insert/7) 实现，将每个新节点保存到 Elasticsearch。这里有一个例子，在一个名为`elastic`的定制模块中:

```
/**
 * Implements hook_node_insert().
 */
function elastic_node_insert($node) {
  $client = elasticsearch_connector_get_client_by_id('my_cluster_id');
  $params = _elastic_prepare_node($node);

  if ( ! $params) {
    drupal_set_message(t('There was a problem saving this node to Elasticsearch.'));
    return;
  }

  $result = $client->index($params);
  if ($result && $result['created'] === false) {
    drupal_set_message(t('There was a problem saving this node to Elasticsearch.'));
    return;
  }

  drupal_set_message(t('The node has been saved to Elasticsearch.'));
}
```

如您所见，我们实例化了一个客户机对象，用于索引来自节点的数据。你可能想知道`_elastic_prepare_node()`是什么:

```
/**
 * Prepares a node to be added to Elasticsearch
 *
 * @param $node
 * @return array
 */
function _elastic_prepare_node($node) {

  if ( ! is_object($node)) {
    return;
  }

  $params = array(
    'index' => 'node',
    'type' => $node->type,
    'body' => array(),
  );

  // Add the simple properties
  $wanted = array('vid', 'uid', 'title', 'log', 'status', 'comment', 'promote', 'sticky', 'nid', 'type', 'language', 'created', 'changed', 'revision_timestamp', 'revision_uid');
  $exist = array_filter($wanted, function($property) use($node) {
    return property_exists($node, $property);
  });
  foreach ($exist as $field) {
    $params['body'][$field] = $node->{$field};
  }

  // Add the body field if exists
  $body_field = isset($node->body) ? field_get_items('node', $node, 'body') : false;
  if ($body_field) {
    $params['body']['body'] = $body_field;
  }

  // Add the image field if exists
  $image_field = isset($node->field_image) ? field_get_items('node', $node, 'field_image') : false;
  if ($image_field) {
    $params['body']['field_image'] = array_map(function($img) {
      $img = file_load($img['fid']);
      $img->url = file_create_url($img->uri);
      return $img;
    }, $image_field);
  }

  return $params;
}
```

它只是我写的一个助手函数，负责“序列化”节点数据，并准备好插入到 Elasticsearch 中。这只是一个例子，绝对不是一个完整的或完全可扩展的例子。还假设相应的图像字段名称是`field_image`。需要注意的重要一点是，我们使用 type = `$node->type`将节点插入到`node`索引中。

### 更新数据

插入是不够的，我们还需要确保节点的变化在 Elasticsearch 中得到反映。我们可以通过一个 [hook_node_update()](https://api.drupal.org/api/drupal/modules!node!node.api.php/function/hook_node_update/7) 实现来做到这一点:

```
/**
 * Implements hook_node_update().
 */
function elastic_node_update($node) {
  if ($node->is_new !== false) {
    return;
  }

  $client = elasticsearch_connector_get_client_by_id('my_cluster_id');
  $params = _elastic_prepare_node($node);

  if ( ! $params) {
    drupal_set_message(t('There was a problem updating this node in Elasticsearch.'));
    return;
  }

  $result = _elastic_perform_node_search_by_id($client, $node);
  if ($result && $result['hits']['total'] !== 1) {
    drupal_set_message(t('There was a problem updating this node in Elasticsearch.'));
    return;
  }

  $params['id'] = $result['hits']['hits'][0]['_id'];
  $version = $result['hits']['hits'][0]['_version'];
  $index = $client->index($params);

  if ($index['_version'] !== $version + 1) {
    drupal_set_message(t('There was a problem updating this node in Elasticsearch.'));
    return;
  }

  drupal_set_message(t('The node has been updated in Elasticsearch.'));
}
```

我们再次使用 helper 函数为插入准备节点，但这次我们也在 Elasticsearch 中搜索节点，以确保我们正在更新而不是创建一个新节点。这发生在我编写的另一个助手函数中，作为一个例子:

```
/**
 * Helper function that returns a node from Elasticsearch by its nid.
 *
 * @param $client
 * @param $node
 * @return mixed
 */
function _elastic_perform_node_search_by_id($client, $node) {
  $search = array(
    'index' => 'node',
    'type' => $node->type,
    'version' => true,
    'body' => array(
      'query' => array(
        'match' => array(
          'nid' => $node->nid,
        ),
      ),
    ),
  );

  return $client->search($search);
}
```

您会注意到，我也要求 Elasticsearch 返回文档版本。这样我就可以检查文档是否已经根据我的请求进行了更新。

### 删除数据

我们需要的最后一个(目前)特性是当一个节点被删除时从 Elasticsearch 中删除数据的能力。 [hook_node_delete()](https://api.drupal.org/api/drupal/modules!node!node.api.php/function/hook_node_delete/7) 可以帮助我们:

```
/**
 * Implements hook_node_delete().
 */
function elastic_node_delete($node) {
  $client = elasticsearch_connector_get_client_by_id('my_cluster_id');

  // If the node is in Elasticsearch, remove it
  $result = _elastic_perform_node_search_by_id($client, $node);
  if ($result && $result['hits']['total'] !== 1) {
    drupal_set_message(t('There was a problem deleting this node in Elasticsearch.'));
    return;
  }

  $params = array(
    'index' => 'node',
    'type' => $node->type,
    'id' => $result['hits']['hits'][0]['_id'],
  );

  $result = $client->delete($params);
  if ($result && $result['found'] !== true) {
    drupal_set_message(t('There was a problem deleting this node in Elasticsearch.'));
    return;
  }

  drupal_set_message(t('The node has been deleted in Elasticsearch.'));
}
```

同样，我们在 Elasticsearch 中搜索节点，并使用返回的 ID 作为删除文档的标记。

请记住，在 Drupal hook 实现中使用如上所示的早期返回并不理想，除非这或多或少是它们需要的所有功能。如果您需要在这些钩子中执行其他不相关的任务，我建议将逻辑拆分成助手函数。

这足以让我们开始在 Drupal 上使用 Elasticsearch 作为一个非常简单的数据源。有了这些基本代码，您就可以导航到您的 Drupal 站点，开始创建一些节点，更新和删除它们。

检查 Elasticsearch 是否被填充的一个方法是禁用我上面提到的远程访问限制，您需要启用它。确保您只在您的本地、开发、环境中这样做。这样，您可以直接从浏览器执行 HTTP 请求，并从 Elasticsearch 获取 JSON 数据。

您可以通过以下网址快速搜索 Elasticsearch 中的所有节点:

```
http://localhost:9200/node/_search
```

…其中 localhost 指向您的本地服务器，9200 是默认的 Elasticsearch 端口。

仅适用于文章节点:

```
http://localhost:9200/node/article/_search
```

对于单个商品，通过自动生成的弹性搜索 id:

```
http://localhost:9200/node/article/AUnJgdPGGE7A1g9FtqdV
```

请继续查看 Elasticsearch 文档，了解您可以与它交互的所有令人惊叹的方式。

## 结论

在本文中，我们已经看到了如何开始将 Elasticsearch 与 Drupal 集成。显然，基于我们已经完成的哪怕是很小的事情，我们还可以做得更多。如果需要，我们可以将集成扩展到其他实体，甚至是 Drupal 配置。无论如何，我们现在在 Elasticsearch 中有一些 Drupal 数据，可以从外部应用程序中使用。

这个外部应用程序将是本教程第二部分的任务。我们将建立一个小型 Silex 应用程序，使用 Elasticsearch PHP SDK，直接从 Elasticsearch 读取 Drupal 数据。与上面的第 1 部分一样，我们不会一步一步地完成给定的任务，而是探索一种可以开始构建这种集成的方法。在那里见。

## 分享这篇文章