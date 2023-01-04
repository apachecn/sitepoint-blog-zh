# 您的第一次 Drupal 8 迁移

> 原文：<https://www.sitepoint.com/your-first-drupal-8-migration/>

迁移是 Drupal 生态系统中最成熟的模块之一。以至于在 Drupal 8 中，决定将它的一些功能移植并添加到 Drupal core 中。一个重要原因是主要版本之间的传统升级被 Drupal 6 或 7 内容和配置到 Drupal 8 的迁移所取代。

![Drupal 8 logo](img/f9690f1e98b563da0de2ec95432340dd.png)

然而，并不是所有的迁移模块的功能都被转移到了核心模块中。相反，我们在核心`migrate`模块中有基本框架，在核心`migrate_drupal`模块中有从 Drupal 6 和 7 升级的功能。剩下的可以在很多 contrib 模块中找到。最重要的是[迁移工具](https://www.drupal.org/project/migrate_tools)，其中包含 drush 命令和运行迁移的 UI。此外，[迁移源 CSV](https://www.drupal.org/project/migrate_source_csv) 、[迁移源 XML](https://www.drupal.org/project/migrate_source_xml) 和[迁移源 JSON](https://www.drupal.org/project/migrate_source_json) 模块为最常用的迁移源类型提供插件。

在本文中，我们将通过将一些内容迁移到节点实体来了解 Drupal 8 中的迁移工作。为了简单起见，我们处理的数据驻留在与 Drupal 安装相同的数据库中的表中。如果你想看到最终的代码，你可以在[这个库](https://github.com/upchuk/d8-demo-modules/tree/master/demo)中查看。

## Drupal 8 迁移理论

Drupal 8 中的迁移结构由三个主要部分组成:源、过程和目的地，这三个部分都是使用新的 [Drupal 8 插件系统](https://www.sitepoint.com/drupal-8-custom-plugin-types/)构建的。这三个部分形成一个管道。source 插件表示我们正在迁移的原始数据，并负责交付其中的各个行。这些数据被传递给一个或多个流程插件，这些插件对每个行字段执行任何需要的操作。最后，一旦流程插件完成数据准备，目标插件将数据保存到 Drupal 实体中(内容或配置)。

创建一个迁移需要使用这样的插件(通过扩展或者直接使用核心类)。然后，所有这些都集中到一个特殊的迁移配置实体中。这是作为模块配置提供的，在模块首次启用时导入，或者可以使用模板构造[(这种情况我们今天不探讨)。迁移配置实体提供对所使用的主要插件的引用+关于迁移的附加元数据。](https://www.drupal.org/node/2186747)

## 电影迁移

现在让我们开始写几个迁移。我们的数据模型如下:我们的数据库中有两个表:`movies`和`movies_genres`。前者有一个 ID、名称和描述，而后者有一个 ID、名称和电影 ID，它们映射到第一个表中的电影。为了简单起见，这种映射是一对一的，以避免第三表的复杂性。下面是 MySQL 脚本，您可以使用它来创建这些表并填充一些测试记录(如果您想继续的话):

```
CREATE TABLE `movies` (
  `id` int(11) unsigned NOT NULL AUTO_INCREMENT,
  `name` varchar(255) DEFAULT NULL,
  `description` text,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=3 DEFAULT CHARSET=utf8;

CREATE TABLE `movies_genres` (
  `id` int(11) unsigned NOT NULL AUTO_INCREMENT,
  `movie_id` int(11) DEFAULT NULL,
  `name` varchar(255) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=5 DEFAULT CHARSET=utf8;

INSERT INTO `movies` (`id`, `name`, `description`)
VALUES
	(1, 'Big Lebowsky', 'My favorite movie, hands down.'),
	(2, 'Pulp fiction', 'Or this is my favorite movie?');

INSERT INTO `movies_genres` (`id`, `movie_id`, `name`)
VALUES
	(1, 1, 'Comedy'),
	(2, 1, 'Noir'),
	(3, 2, 'Crime'); 
```

我们想要实现的是将电影迁移到基本的 Drupal 文章节点中，将流派迁移到标签词汇表的分类术语中(文章节点通过一个字段引用标签词汇表)。自然，我们也希望这种迁移反映出电影和流派之间的联系。

### 体裁

让我们首先关注一下类型迁移，因为在 Drupal 中，电影将依赖于它们(它们将引用它们)。

在我们模块的`config/install`文件夹中，我们需要以下名为`migrate.migration.genres.yml`的配置实体文件:

```
id: genres
label: Genres
migration_group: demo
source:
  plugin: genres
  key: default
destination:
  plugin: entity:taxonomy_term
process:
  vid:
    plugin: default_value
    default_value: tags
  name: name 
```

此配置的前三个元素是迁移 ID、标签和它所属的组。下面三个键负责前面提到的迁移管道的三个主要部分。后面三个我们分开来说。

#### 来源

在 source 键下，我们指定迁移应该使用哪个插件来表示和交付源数据(在我们的例子中是 ID 为`genres`的插件)。`key`键用于指定我们的源查询应该使用哪个数据库(即我们的数据所在的位置)。

因此，在我们模块的`Plugin/migrate/source`文件夹中，让我们为我们的流派创建基于 SQL 的源代码插件:

**Genres.php**

```
namespace Drupal\demo\Plugin\migrate\source;

use Drupal\migrate\Plugin\migrate\source\SqlBase;

/**
 * Source plugin for the genres.
 *
 * @MigrateSource(
 *   id = "genres"
 * )
 */
class Genres extends SqlBase {

  /**
   * {@inheritdoc}
   */
  public function query() {
    $query = $this->select('movies_genres', 'g')
      ->fields('g', ['id', 'movie_id', 'name']);
    return $query;
  }

  /**
   * {@inheritdoc}
   */
  public function fields() {
    $fields = [
      'id' => $this->t('Genre ID'),
      'movie_id' => $this->t('Movie ID'),
      'name' => $this->t('Genre name'),
    ];

    return $fields;
  }

  /**
   * {@inheritdoc}
   */
  public function getIds() {
    return [
      'id' => [
        'type' => 'integer',
        'alias' => 'g',
      ],
    ];
  }
} 
```

由于我们使用的是 SQL source，我们需要有自己的 source 插件类来提供一些关于如何读取数据的信息。使用 core 现有的一个是不够的。`query()`方法创建对流派数据的查询，`fields()`方法定义每个单独的行字段，`getIds()`方法指定充当唯一 ID 的源行字段。这里没有复杂的事情发生。

我们从`SqlBase`开始扩展，其中寻找名为`key`的插件配置元素，以了解应该在哪个数据库上运行查询。在我们的例子中，是默认的，正如我们在迁移配置实体中详细描述的。

这就是我们简单的源代码插件所需要的。

#### 目的地

对于迁移目的地，我们使用默认的核心分类术语目的地插件，它为我们处理一切。不需要指定更多的东西。

#### 过程

在迁移的`process`键下，我们列出了我们想要填充的每个目标字段，以及一个或多个将源行字段转换为目标字段数据的流程插件。因为我们希望类型是标签词汇表的所有术语，所以对于`vid`字段，我们使用了`default_value`插件，该插件接受一个`default_value`键，该键指示每条记录将具有的值。因为所有的都在同一个词汇表中，这对我们来说很好。

最后，对于术语名称字段，我们可以简单地指定源行字段名称，而不需要显式的插件名称。这将在幕后使用`get`插件，该插件简单地从源获取数据，并将其不加修改地复制到目的地。

有关如何在管道中链接多个流程插件的更多信息，或者您可以从 core 获得的其他此类插件，我建议您查看文档。

### 电影

既然我们的类型已经可以导入了，让我们来看看与前一个(`config/install`)位于同一文件夹中的 movies 迁移配置:

**migrate . migration . movies . yml**

```
id: movies
label: Movies
migration_group: demo
source:
  plugin: movies
  key: default
destination:
  plugin: entity:node
process:
  type:
    plugin: default_value
    default_value: article
  title: name
  body: description
  field_tags:
    plugin: migration
    migration: genres
    source: genres
migration_dependencies:
  required:
    - genres 
```

我们注意到与以前相同的元数据，迁移的三个主要部分(源、过程和目的地)，以及在成功运行迁移之前需要满足的显式依赖关系。

#### 来源

像之前一样，我们来看看`movies`源码插件，和`genres`源码插件(`Plugin/migrate/source`)位于同一个地方:

**Movies.php**:

```
namespace Drupal\demo\Plugin\migrate\source;

use Drupal\migrate\Plugin\migrate\source\SqlBase;
use Drupal\migrate\Row;

/**
 * Source plugin for the movies.
 *
 * @MigrateSource(
 *   id = "movies"
 * )
 */
class Movies extends SqlBase {

  /**
   * {@inheritdoc}
   */
  public function query() {
    $query = $this->select('movies', 'd')
      ->fields('d', ['id', 'name', 'description']);
    return $query;
  }

  /**
   * {@inheritdoc}
   */
  public function fields() {
    $fields = [
      'id' => $this->t('Movie ID'),
      'name' => $this->t('Movie Name'),
      'description' => $this->t('Movie Description'),
      'genres' => $this->t('Movie Genres'),
    ];

    return $fields;
  }

  /**
   * {@inheritdoc}
   */
  public function getIds() {
    return [
      'id' => [
        'type' => 'integer',
        'alias' => 'd',
      ],
    ];
  }

  /**
   * {@inheritdoc}
   */
  public function prepareRow(Row $row) {
    $genres = $this->select('movies_genres', 'g')
      ->fields('g', ['id'])
      ->condition('movie_id', $row->getSourceProperty('id'))
      ->execute()
      ->fetchCol();
    $row->setSourceProperty('genres', $genres);
    return parent::prepareRow($row);
  }
} 
```

我们有和以前一样的三个必需的方法，它们做同样的事情:查询和定义数据。然而，这里我们也使用了`prepareRow()`方法来改变行数据和可用字段。目的是选择与当前行(电影)匹配的电影类型的 ID。该值被填充到一个名为`genres`的新源字段中，稍后我们将看到它是如何用于保存标记分类术语引用的。

#### 目的地

在这种情况下，我们使用节点实体目的地插件，我们不需要更多。

#### 过程

在文章节点上有四个我们希望用电影数据填充的字段。首先，对于节点`type`,我们使用与之前分类法词汇表相同的技术，并将`article`设置为默认值。第二和第三，对于标题和正文字段，我们映射电影名称和描述源字段不变。

最后，对于 tags 字段，我们使用了[迁移过程插件](https://www.drupal.org/node/2149801),它允许我们将流派的 ID(我们之前添加到`genres`源行字段中)转换成其对应的分类术语的 ID。这个插件通过检查流派的迁移映射和读取这些 id 来完成这项工作。这就是为什么类型迁移也被标记为电影导入的依赖项。

## 激活和运行迁移

现在我们有了两个迁移配置实体和所有相关的插件，是时候第一次启用我们的模块并让 Drupal 导入配置了。如果您的模块已经启用，请将其卸载，然后再次启用。这将使配置导入发生。

此外，为了通过 Drush 运行迁移(这是推荐的方式)，安装[迁移工具](https://www.drupal.org/project/migrate_tools)模块。然后剩下要做的就是使用命令来迁移或回滚电影和流派。

要查看可用的迁移及其状态:

```
drush migrate-status 
```

要导入所有迁移:

```
drush migrate-import --all 
```

要回滚所有迁移:

```
drush migrate-rollback --all 
```

## 结论

现在我们有了它——一个简单的迁移来说明我们现在如何在 Drupal 8 中导入、跟踪和回滚迁移。我们已经看到了插件系统是如何被用来表示所有这些不同的功能组件的，以及迁移定义是如何被转变成将这些元素组合在一起的配置的。

然而，还有很多东西需要学习。您可以使用不同的源插件，例如 CSV 或 JSON 格式的数据，复杂的流程插件(或者自己编写)，甚至可以为任何数据结构定制目标插件。祝你好运！

## 分享这篇文章