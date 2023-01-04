# PHP 中的 Elasticsearch 简介

> 原文：<https://www.sitepoint.com/introduction-to-elasticsearch-in-php/>

在本教程中，我们将看看 [Elasticsearch](https://www.elastic.co/products/elasticsearch) 以及如何在 PHP 中使用它。Elasticsearch 是一个基于 Apache Lucene 的开源搜索服务器。我们可以用它来执行超快的全文和其他复杂的搜索。它还包含一个 REST API，允许我们轻松地发出创建、删除、更新和检索数据的请求。

![ElasticSearch Logo](img/1a500d5253f0a029bf9af33025452009.png)

## 安装弹性搜索

本教程将假设你正在使用基于 Debian 的环境，就像下面安装说明中的[这个](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)。

要安装 Elasticsearch，我们首先需要安装 Java。默认情况下，它在 Ubuntu 使用的库中不可用，所以我们需要添加一个。

```
sudo add-apt-repository ppa:webupd8team/java
sudo apt-get update
```

一旦完成，我们就可以安装 Java 了。

```
sudo apt-get install oracle-java8-installer
```

接下来，我们用`wget`下载 Elasticsearch。

```
wget https://download.elasticsearch.org/elasticsearch/elasticsearch/elasticsearch-1.5.2.tar.gz
```

目前，最新的稳定版本是 1.5.2，这就是我们上面使用的版本。如果你想确保你得到的是最新版本，看看 [Elasticsearch 下载页面](https://www.elastic.co/downloads/elasticsearch)。

然后，我们提取并安装。

```
mkdir es
tar -xf elasticsearch-1.5.2.tar.gz -C es
cd es
./bin/elasticsearch
```

当我们在浏览器中访问`http://localhost:9200`时，我们得到类似如下的内容:

```
{
  "status" : 200,
  "name" : "Rumiko Fujikawa",
  "cluster_name" : "elasticsearch",
  "version" : {
    "number" : "1.5.2",
    "build_hash" : "62ff9868b4c8a0c45860bebb259e21980778ab1c",
    "build_timestamp" : "2015-04-27T09:21:06Z",
    "build_snapshot" : false,
    "lucene_version" : "4.10.4"
  },
  "tagline" : "You Know, for Search"
}
```

## 使用弹性搜索

现在我们可以开始玩 Elasticsearch 了。首先，让我们安装 PHP 官方的 Elasticsearch 客户端。

```
composer require elasticsearch/elasticsearch
```

接下来，让我们用下面的代码创建一个新的 php 文件，用于测试，这样我们就可以使用 Elasticsearch 客户端了。

```
<?php
require 'vendor/autoload.php';

$client = new Elasticsearch\Client();
```

### 索引文档

可以通过在客户端调用`index`方法来索引新文档。这个方法接受一个数组作为它的参数。该数组应该包含`body`、`index`和`type`作为它的键。`body`是一个包含要索引的数据的数组。`index`是您想要索引特定文档的位置(对应于传统 RDBMS 中的数据库)。最后，`type`是您想赋予文档的类型，您想如何对文档进行分类。就像 RDBMS 国土中的表。这里有一个例子:

```
$params = array();
$params['body']  = array(
  'name' => 'Ash Ketchum',
  'age' => 10,
  'badges' => 8 
);

$params['index'] = 'pokemon';
$params['type']  = 'pokemon_trainer';

$result = $client->index($params);
```

如果您打印出`$result`,您会得到类似如下的内容:

```
Array
(
    [_index] => pokemon
    [_type] => pokemon_trainer
    [_id] => AU1Bn51W5l_vSaLQKPOy
    [_version] => 1
    [created] => 1
)
```

在上面的例子中，我们没有为文档指定 ID。如果没有指定，Elasticsearch 会自动分配一个唯一的 ID。让我们尝试为另一个文档分配一个 ID:

```
$params = array();
$params['body']  = array(
  'name' => 'Brock',
  'age' => 15,
  'badges' => 0 
);

$params['index'] = 'pokemon';
$params['type']  = 'pokemon_trainer';
$params['id'] = '1A-000';

$result = $client->index($params);
```

当我们打印出`$result`:

```
Array
(
    [_index] => pokemon
    [_type] => pokemon_trainer
    [_id] => 1A-001
    [_version] => 1
    [created] => 1
)
```

当索引文档时，我们不局限于一维数组。我们也可以索引多维的:

```
$params = array();
$params['body']  = array(
  'name' => 'Misty',
  'age' => 13,
  'badges' => 0,
  'pokemon' => array(
    'psyduck' => array(
      'type' => 'water',
      'moves' => array(
        'Water Gun' => array(
          'pp' => 25,
          'power' => 40
        )
      ) 
    )
  ) 
);

$params['index'] = 'pokemon';
$params['type']  = 'pokemon_trainer';
$params['id'] = '1A-002';

$result = $client->index($params);
```

我们可以深入到我们想要的深度，但当我们用 Elasticsearch 索引数据时，我们仍然需要观察数据的适当存储(不要太深入，保持它的结构化和逻辑性，等等)，就像我们在 RDBMS 设置中所做的那样。

### 搜索文档

我们可以使用`get`或`search`方法在特定索引中搜索现有文档。这两者的主要区别在于，当您已经知道文档的 ID 时，通常会使用`get`方法。它也用于只获取一个文档。另一方面，`search()`方法用于搜索多个文档，您可以使用文档中的任何字段进行查询。

#### 得到

首先，我们从`get`方法开始。就像`index`方法一样，它接受一个数组作为它的参数。该数组应该包含您想要查找的文档的`index`、`type`和`id`。

```
$params = array();
$params['index'] = 'pokemon';
$params['type'] = 'pokemon_trainer';
$params['id'] = '1A-001';

$result = $client->get($params);
```

上面的代码将返回以下内容:

```
Array
(
    [_index] => pokemon
    [_type] => pokemon_trainer
    [_id] => 1A-001
    [_version] => 1
    [found] => 1
    [_source] => Array
        (
            [name] => Brock
            [age] => 15
            [badges] => 0
        )

)
```

#### 使用特定字段进行搜索

`search`方法的数组参数需要有`index`、`type`和`body`键。`body`是我们指定查询的地方。首先，这里有一个例子，说明我们如何使用它来返回所有 15 年前的文档。

```
$params['index'] = 'pokemon';
$params['type'] = 'pokemon_trainer';
$params['body']['query']['match']['age'] = 15;

$result = $client->search($params);
```

这将返回以下内容:

```
Array
(
    [took] => 177
    [timed_out] => 
    [_shards] => Array
        (
            [total] => 5
            [successful] => 5
            [failed] => 0
        )

    [hits] => Array
        (
            [total] => 1
            [max_score] => 1
            [hits] => Array
                (
                    [0] => Array
                        (
                            [_index] => pokemon
                            [_type] => pokemon_trainer
                            [_id] => 1A-001
                            [_score] => 1
                            [_source] => Array
                                (
                                    [name] => Brock
                                    [age] => 15
                                    [badges] => 0
                                )

                        )

                )

        )

)
```

让我们来分析一下结果:

*   `took`–请求完成所用的毫秒数。
*   `timed_out`–如果请求超时，则返回`true`。
*   `_shards`–默认情况下，Elasticsearch 将数据分成 5 份。如果`total`和`successful`的值是 5，那么每个碎片当前都是健康的。你可以在这个 [Stackoverflow 线程](http://stackoverflow.com/questions/15694724/shards-and-replicas-in-elasticsearch)中找到更详细的解释。
*   `hits`包含结果。

不过，我们上面使用的方法只允许我们进行第一级深度的搜索。如果我们要进一步深入，我们必须使用`bool`查询。为此，我们将`bool`指定为`query`的一个项目。然后，我们可以通过使用`.`遍历我们想要的字段，从第一级字段开始向下到我们想要用作查询的字段。

```
$params['index'] = 'pokemon';
$params['type']  = 'pokemon_trainer';
$params['body']['query']['bool']['must'][]['match']['pokemon.psyduck.type'] = 'water';
$result = $client->search($params);
```

#### 使用数组搜索

我们可以使用数组作为查询进行搜索(以匹配几个值)，方法是指定`bool`项，接着是`must`、`terms`，然后是我们希望用于查询的字段。我们指定一个数组，包含我们想要匹配的值。在下面的例子中，我们选择了一个`age`等于 10 和 15 的文档。

```
$params['index'] = 'pokemon';
$params['type']  = 'pokemon_trainer';

$params['body']['query']['bool']['must']['terms']['age'] = array(10, 15);
```

此方法只接受一维数组。

#### 过滤搜索

接下来，让我们做一个过滤搜索。要使用过滤搜索，我们必须指定`filtered`项并设置我们想要为特定字段返回的范围。在下面的例子中，我们使用`age`作为字段。我们正在选择年龄大于或等于(gte) 11 但小于或等于(LTE)20 的文档。

```
$params['index'] = 'pokemon';
$params['type']  = 'pokemon_trainer';
$params['body']['query']['filtered']['filter']['range']['age']['gte'] = 11;
$params['body']['query']['filtered']['filter']['range']['age']['lte'] = 20;
$result = $client->search($params);
```

#### 或与和

在 RDBMS 领域，我们习惯于使用 and 和 OR 关键字来指定两个或多个条件。我们也可以用过滤搜索的方式进行弹性搜索。在下面的例子中，我们使用`and`过滤器来选择年龄为 10 岁、徽章数量为 8 的文档。仅返回与该标准匹配的文档。

```
$params['index'] = 'pokemon';
$params['type']  = 'pokemon_trainer';

$params['body']['query']['filtered']['filter']['and'][]['term']['age'] = 10;
$params['body']['query']['filtered']['filter']['and'][]['term']['badges'] = 8;

$result = $client->search($params);
```

如果你想选择其中任何一个，你可以使用`or`来代替。

```
$params['body']['query']['filtered']['filter']['or'][]['term']['age'] = 10;
$params['body']['query']['filtered']['filter']['or'][]['term']['badges'] = 8;
```

#### 极限结果

通过指定`size`字段，可以将结果限制为特定的数字。这里有一个例子:

```
$params['body']['query']['filtered']['filter']['and'][]['term']['age'] = 10;
$params['body']['query']['filtered']['filter']['and'][]['term']['badges'] = 8;
$params['size'] = 1;
```

这将返回第一个结果，因为我们将结果限制在一个文档中。

#### 页码

在 RDBMS 领域，我们有极限和偏移。在弹性搜索中，我们有`size`和`from`。`from`允许我们指定结果集中第一个结果的索引。文档是零索引的。因此，对于每页 10 个结果，如果我们的大小为 10，那么每当用户导航到下一页时，我们就给`from`值加 10。

```
$params['index'] = 'pokemon';
$params['type']  = 'pokemon_trainer';

$params['size'] = 10;
$params['from'] = 10; // <-- will return second page
```

### 更新文档

要更新文档，我们首先需要获取文档的旧数据。为此，我们像前面一样指定`index`、`type`和`id`，然后调用`get`方法。当前数据可以在`_source`项中找到。我们所要做的就是用新值更新当前字段，或者向该项添加新字段。最后，我们使用与 get 方法相同的参数调用`update`方法。

```
$params = array();
$params['index'] = 'pokemon';
$params['type'] = 'pokemon_trainer';
$params['id'] = '1A-001';
$result = $client->get($params);

$result['_source']['age'] = 21; //update existing field with new value

//add new field
$result['_source']['pokemon'] = array(
  'Onix' => array(
    'type' => 'rock',
    'moves' => array(
      'Rock Slide' => array(
        'power' => 100,
        'pp' => 40
      ),
      'Earthquake' => array(
        'power' => 200,
        'pp' => 100
      )
    )
  )
);

$params['body']['doc'] = $result['_source'];

$result = $client->update($params);
```

这将返回类似于以下内容的内容:

```
Array
(
    [_index] => pokemon
    [_type] => pokemon_trainer
    [_id] => 1A-001
    [_version] => 2
)
```

请注意，每次您调用`update`方法时，`_version`都会递增，不管实际上是否已经更新了内容。

您可能想知道为什么我们在文档中有一个版本，或者甚至想在 Elasticsearch 中有一个功能允许我们获取文档的以前版本。不幸的是，事实并非如此。版本仅仅作为一个计数器，记录文档被更新的次数。

### 删除文档

通过调用`delete`方法可以删除文档。该方法接受包含`index`、`type`和`id`的数组作为其参数。

```
$params = array();
$params['index'] = 'pokemon';
$params['type'] = 'pokemon_trainer';
$params['id'] = '1A-001';

$result = $client->delete($params);
```

这将返回以下内容:

```
Array
(
    [found] => 1
    [_index] => pokemon
    [_type] => pokemon_trainer
    [_id] => 1A-001
    [_version] => 7
)
```

请注意，如果您试图使用`get`方法获取一个已删除的文档，您将会得到一个错误。

## 结论

在本文中，我们了解了如何使用官方的 Elasticsearch 客户端在 PHP 中使用 Elasticsearch。具体来说，我们已经了解了如何索引新文档、搜索文档、对结果分页以及删除文档。

总的来说，Elasticsearch 是向 PHP 应用程序添加搜索功能的好方法。如果你想了解更多关于如何将 Elasticsearch 集成到你的 PHP 应用程序中，你可以看看丹尼尔·希波什的系列文章[如何将 Elasticsearch 与 Drupal 和 Silex](https://www.sitepoint.com/series/a-silex-and-elasticsearch-app-powered-by-drupal-7-for-content-management/) 集成。

但是，如果您更喜欢自动解决方案，而不是在应用程序中添加深度搜索功能，请参见本系列的。

## 分享这篇文章