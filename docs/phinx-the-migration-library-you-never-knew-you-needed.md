# phinx——您从来不知道自己需要的迁移库

> 原文：<https://www.sitepoint.com/phinx-the-migration-library-you-never-knew-you-needed/>

您正在构建一个应用程序，并且需要与您的团队共享数据库的结构。毕竟，您希望每个人都尽快启动并运行。你是做什么的？表结构的 SQL 转储？你可以…但是这太原始了——导入起来也很耗时！通常情况下，[数据库迁移](https://en.wikipedia.org/wiki/Schema_migration)就是答案。

在本教程中，我们将介绍一个名为 [Phinx](https://phinx.org) 的用于构建和执行数据库迁移的与框架无关的包。

![Phinx logo](img/292f549cef34cbe803f840342183d7ab.png)

## 拔靴带

首先，让我们用 [Composer](https://www.sitepoint.com/php-dependency-management-with-composer/) 将 Phinx 安装到项目中:

```
composer require robmorgan/phinx --dev 
```

按照 Composer 的默认值，Phinx 二进制文件将被安装到`vendor/bin`文件夹中。然后可以通过运行以下命令来执行它:

```
php vendor/bin/phinx 
```

Phinx 需要一个`phinx.yml`文件来读取数据库配置，然后才能做任何有意义的事情。为了生成它，我们运行:

```
php vendor/bin/phinx init 
```

## 配置

生成的文件如下所示:

```
paths:
    migrations: %%PHINX_CONFIG_DIR%%/db/migrations
    seeds: %%PHINX_CONFIG_DIR%%/db/seeds

environments:
    default_migration_table: phinxlog
    default_database: development
    production:
        adapter: mysql
        host: localhost
        name: production_db
        user: root
        pass: ''
        port: 3306
        charset: utf8

    development:
        adapter: mysql
        host: localhost
        name: development_db
        user: root
        pass: ''
        port: 3306
        charset: utf8

    testing:
        adapter: mysql
        host: localhost
        name: testing_db
        user: root
        pass: ''
        port: 3306
        charset: utf8 
```

Phinx 支持任意数量的“数据库”，尽管这些应该被认为是一个数据库的版本，而不是几个不同的版本。然而，有时可能需要使用完全不同的数据库。

例如， [nofw](https://github.com/swader/nofw) 就是这种情况，它在以前的版本中仍然使用 gate keeper——一种安全但笨拙开发的用户授权包。Gatekeeper 需要自己的`phinx.yml`文件(带有自定义迁移路径),并且不提供更改所用文件的选项，同时还需要自己的用户数据库。这打破了“让我们在已经使用 Gatekeeper 的项目中使用 Phinx”的想法。

对于这种情况，Phinx 提供了`-c`选项，告诉启动器使用哪个`phinx.yml`文件。注意，Phinx 也支持`json`和`php`文件格式，但是我们在这里将关注默认的`yml`文件格式，并为我们的示例数据库创建一个单独的文件。

```
mv phinx.yml my-phinx.yml 
```

这只是重命名默认文件，并为“特殊”包(如 Gatekeeper)腾出空间。如果 Phinx [支持多种迁移路径](https://github.com/robmorgan/phinx/issues/232)那就太好了，但是我们会尽我们所能。

在我们创建第一个迁移之前，我们需要填写`my-phinx.yml`中的凭证。关于[宅基地改良](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)，以下就足够了:

```
paths:
    migrations: db/migrations

environments:
    default_migration_table: phinxlog
    default_database: development
    production:
        adapter: mysql
        host: localhost
        name: production_db
        user: username
        pass: 'password'
        port: 3306
        charset: utf8

    development:
        adapter: mysql
        host: localhost
        name: homestead
        user: homestead
        pass: 'secret'
        port: 3306
        charset: utf8 
```

## 第一次迁移

假设我们有一个需要内置以下功能的应用程序:

*   一种在应用程序范围内发送消息的方式，以便所有用户都可以看到它们
*   将文件上传到系统中的方法，以及将这些文件附加到上述消息中的方法
*   一种定义标签和标记上传文件的方法(在其他实体中，稍后应该很容易添加)，因此可以对文件进行排序和搜索。

这个模型可能看起来像这样:

![An entity-relationship diagram of the above features](img/5780ad02ee96b8f9975d103f1d1b598e.png)

通过 MySQL Workbench 转换为 SQL，可以直接导入数据库:

```
-- MySQL Workbench Forward Engineering

SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0;
SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0;
SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='TRADITIONAL,ALLOW_INVALID_DATES';

-- -----------------------------------------------------
-- Schema mydb
-- -----------------------------------------------------

-- -----------------------------------------------------
-- Schema mydb
-- -----------------------------------------------------
CREATE SCHEMA IF NOT EXISTS `mydb` DEFAULT CHARACTER SET utf8 ;
USE `mydb` ;

-- -----------------------------------------------------
-- Table `mydb`.`tag`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `mydb`.`tag` (
  `id` INT UNSIGNED NOT NULL AUTO_INCREMENT,
  `name` VARCHAR(45) NOT NULL,
  `description` TEXT NULL,
  `context` VARCHAR(25) NULL,
  `created` TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  `created_by` INT UNSIGNED NOT NULL,
  `visibility` TINYINT(1) UNSIGNED NOT NULL DEFAULT 1,
  PRIMARY KEY (`id`),
  UNIQUE INDEX `name_creator_visibile` (`created_by` ASC, `name` ASC, `visibility` ASC),
  INDEX `context_key` (`context` ASC))
ENGINE = InnoDB;

-- -----------------------------------------------------
-- Table `mydb`.`tag_relation`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `mydb`.`tag_relation` (
  `tag_id` INT UNSIGNED NOT NULL,
  `entity_id` INT UNSIGNED NOT NULL,
  `entity_type` VARCHAR(45) NOT NULL,
  `created` TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  `created_by` INT UNSIGNED NOT NULL,
  PRIMARY KEY (`tag_id`, `entity_id`, `entity_type`),
  INDEX `tag_key` (`tag_id` ASC),
  INDEX `entity_key` (`entity_id` ASC, `entity_type` ASC),
  CONSTRAINT `tag_id_fk`
    FOREIGN KEY (`tag_id`)
    REFERENCES `mydb`.`tag` (`id`)
    ON DELETE CASCADE
    ON UPDATE NO ACTION)
ENGINE = InnoDB;

-- -----------------------------------------------------
-- Table `mydb`.`file`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `mydb`.`file` (
  `id` INT UNSIGNED NOT NULL AUTO_INCREMENT,
  `name` VARCHAR(255) NOT NULL,
  `path` TEXT NOT NULL,
  `created_by` INT UNSIGNED NOT NULL,
  `created` TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  `updated` TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  `updated_by` INT UNSIGNED NOT NULL,
  `access` VARCHAR(6) NOT NULL DEFAULT 'public',
  PRIMARY KEY (`id`),
  INDEX `creator` (`created_by` ASC))
ENGINE = InnoDB;

-- -----------------------------------------------------
-- Table `mydb`.`message`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `mydb`.`message` (
  `id` INT UNSIGNED NOT NULL AUTO_INCREMENT,
  `created_by` INT UNSIGNED NOT NULL,
  `created` TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  `content` TEXT NOT NULL,
  `attachments` TEXT NULL,
  PRIMARY KEY (`id`))
ENGINE = InnoDB;

SET SQL_MODE=@OLD_SQL_MODE;
SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS;
SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS; 
```

虽然这使得第一步非常简单，但它使得后续的更改几乎不可能。我们会在后面讨论原因。从长远来看，更健康的方法是使用迁移。

### 最佳实践

在继续之前，让我们讨论一下最佳实践。

普遍的共识是一个迁移文件应该只包含每个文件的一个变更。创建表时，每个表一次迁移。当随后添加列时，每个文件的一组*列改变*。添加外键时，甚至每个表一个约束。

同样，虽然 Phinx 确实支持迁移文件中的纯 SQL 语句，所以从技术上讲，工作台的输出可以直接复制到迁移中，但是最好使用 Phinx 自己的语法来使底层数据库引擎在以后可以替换。

记住这一点，让我们开始吧。

### 创建迁移

首先，我们将创建表。

```
php vendor/bin/phinx create Tag
php vendor/bin/phinx create File
php vendor/bin/phinx create Message 
```

注意，我们只有一个与标记相关的迁移，尽管标记特性使用了两个表，从上面的模型中可以明显看出。这是因为在这个初始阶段，创建不能同时独立运行的表是可以的。还要注意功能的顺序——要发送消息，我们需要能够附加文件。要附加文件，我们需要能够标记它们。因此，首先安装标记，其次是文件，最后是消息是有意义的。

该命令执行后，Phinx 将在`db/migrations`中创建三个文件，每个文件以创建的日期和时间开始，以迁移名称结束，例如`20160508205010_tag.php`。这些文件将扩展`AbstractMigration`类，并包含类似如下的模板代码:

```
< ?php

use Phinx\Migration\AbstractMigration;

class Tag extends AbstractMigration
{
    /**
     * Change Method.
     *
     * Write your reversible migrations using this method.
     *
     * More information on writing migrations is available here:
     * http://docs.phinx.org/en/latest/migrations.html#the-abstractmigration-class
     *
     * The following commands can be used in this method and Phinx will
     * automatically reverse them when rolling back:
     *
     *    createTable
     *    renameTable
     *    addColumn
     *    renameColumn
     *    addIndex
     *    addForeignKey
     *
     * Remember to call "create()" or "update()" and NOT "save()" when working
     * with the Table class.
     */
    public function change()
    {

    }
} 
```

注意，虽然 Phinx 确实支持您在其他迁移工具中看到的标准`up`和`down`方法，但它默认使用能够自动逆转迁移的`change`，从而消除了编写单独的`down`过程的需要。

现在让我们通过修改`change()`方法来创建标签迁移，如下所示:

```
 public function change()
    {
        $tag = $this->table('tag');
        $tag
            ->addColumn('name', 'string', ['limit' => 45, 'null' => false])
            ->addColumn('description', 'text')
            ->addColumn('context', 'string', ['limit' => 25])
            ->addColumn('created', 'timestamp', ['null' => false, 'default' => 'CURRENT_TIMESTAMP'])
            ->addColumn('created_by', 'integer', ['signed' => false, 'null' => false])
            ->addColumn('visibility', 'boolean', ['null' => false, 'signed' => false, 'default' => 1])
            ;

        $tag->addIndex(['name', 'created_by', 'visibility'], ['unique' => true, 'name' => 'name_creator_visible']);
        $tag->addIndex(['context']);

        $tag->create();

        $tagRelation = $this->table('tag_relation', array('id' => false, 'primary_key' => array('tag_id', 'entity_id', 'entity_type')));
        $tagRelation
            ->addColumn('tag_id', 'integer', ['null' => false])
            ->addColumn('entity_id', 'integer', ['null' => false, 'signed' => false])
            ->addColumn('entity_type', 'string', ['limit' => 45, 'null' => false])
            ->addColumn('created', 'timestamp', ['null' => false, 'default' => 'CURRENT_TIMESTAMP'])
            ->addColumn('created_by', 'integer', ['signed' => false, 'null' => false])
        ;

        $tagRelation->addIndex(['tag_id']);
        $tagRelation->addIndex(['entity_id', 'entity_type'], ['name' => 'entity']);

        $tagRelation->addForeignKey('tag_id', 'tag', 'id', array('delete'=> 'CASCADE', 'update'=> 'NO_ACTION'));

        $tagRelation->create();
    } 
```

分解一下，我们首先定义`tag`表，然后根据文档添加所有的列，最后为了更好的度量，在那里放入一些索引。注意在`tag`表中没有`id`列。这是因为 Phinx 会自动创建一个名为`id`的自动递增主键，除非被告知要这样做。

标签表的第一个索引叫做`name_creator_visible`。索引中列的顺序[很重要](http://www.ovaistariq.net/17/mysql-indexes-multi-column-indexes-and-order-of-columns/)。第二个索引放在`context`列，允许我们通过上下文搜索所有标签——这是我们在选择适用于某个实体的标签时经常使用的。

接下来，我们制作`tag_relation`表。如前所述，Phinx 创建了一个名为`id`的自动递增主键，这就是为什么我们需要在表的初始定义中禁用它。在同一行中，我们定义了一个由`tag_id`、`entity_id`和`entity_type`组成的可选主键。这确保了一个给定的实体只能附加一个标签的一个副本。添加列照常进行，然后是再次创建索引的时候了。`tag_id`让我们快速找到带有给定标签的所有实体，而`entity_id`和`entity_type`的组合让我们快速列出给定实体上的所有标签。

最后，我们创建一个简单的外键，将`tag_id`字段绑定到`tag`表的`id`字段，这样，如果标签本身从系统中删除，对应于所述`tag_id`的`tag_relation`行也会被删除。

我们的初始标签迁移现在已经准备好了。让我们来测试一下。我们通过以下方式启动迁移:

```
php vendor/bin/phinx migrate -c my-phinx.yml 
```

可选地，我们可以提供一个`-e X`标志，其中 X 是我们的目标环境。在这种情况下，这是不必要的，因为我们的`my-phinx.yml`文件将`development`标注为默认数据库(因此也是默认环境)。

果然，执行之后，我们的表就在那里了:

![CLI output of successful migration execution](img/c2e323f25cb3af97ae64ece26ffd13e1.png)

![Tables visible in SequelPro](img/eba46175905b3289b4118d7ff372d4a7.png)

让我们也快速记录一下`file`和`message`迁移。

```
// File
    public function change()
    {

        $file = $this->table('file');

        $file
            ->addColumn('name', 'string', ['limit' => 255, 'null' => false])
            ->addColumn('path', 'text', ['null' => false])
            ->addColumn('access', 'string', ['limit' => 6, 'null' => false, 'default' => 'public'])
            ->addColumn('created', 'timestamp', ['null' => false, 'default' => 'CURRENT_TIMESTAMP'])
            ->addColumn('created_by', 'integer', ['signed' => false, 'null' => false])
            ->addColumn('updated', 'timestamp', ['null' => false, 'default' => 'CURRENT_TIMESTAMP'])
            ->addColumn('updated_by', 'integer', ['signed' => false, 'null' => false])
        ;

        $file->addIndex(['created_by'], ['name' => 'creator']);
        $file->addIndex(['access'], ['name' => 'accessibility']);

        $file->create();

    } 
```

```
// Message
    public function change()
    {

        $message = $this->table('message');

        $message
            ->addColumn('content', 'text', ['null' => false])
            ->addColumn('attachments', 'text')
            ->addColumn('created', 'timestamp', ['null' => false, 'default' => 'CURRENT_TIMESTAMP'])
            ->addColumn('created_by', 'integer', ['signed' => false, 'null' => false])

        ;

        $message->addIndex(['created_by'], ['name' => 'creator']);

        $message->create();

    } 
```

### 解决纷争

但是，如果我们现在运行 migrate 命令，我们会注意到什么也没有发生。不会出现新的数据库。怎么回事？

当 Phinx 执行`tag`迁移时，它在数据库的`phinxlog`表中创建一个条目，记录最后执行的迁移。由于此时消息和文件的迁移文件已经存在(但为空)，它们被标记为已迁移，因此在本次运行中被忽略，因为本次运行已经“正式”完成。首先，我们得把*回滚*。回滚会撤消上次迁移。

```
php vendor/bin/phinx rollback -c my-phinx.yml 
```

![A failed rollback error message saying it cannot find the table in question](img/91dde6fc1ad5d824692c29184de32d41.png)

啊哦！现在怎么办？

当迁移第一次运行时，文件和消息迁移文件是空的。Phinx 读取迁移的`change`方法来计算撤销过程(例如，它将`create table`转换为`drop table`)，由于它现在发现文件中提到了以前没有的表，它很困惑——没有这样的表！

有几种方法可以解决这个问题:

1.  编写迁移时要小心。要么一次全部写入，然后进行迁移，要么逐个创建和写入，然后在每个都完成后进行迁移。
2.  注释两个新的`change`方法的内容，保存，运行 rollback，然后取消注释。
3.  手动移除`phinxlog`、`tag`和`tag_relation`桌子，因为这是我们第一次设置东西，我们不能做任何损坏。
4.  使用`up`和`down`方法，而不是`change`——这将在回滚时使用`down`方法，它可以是空的，也可以包含一个`drop table`命令。

我们将采用选项 2。

首先，注释两个`change`方法的内容。然后，运行:

```
php vendor/bin/phinx rollback -c my-phinx.yml -t XXXXXXXX 
```

…其中`XXXXXXXX`是您想要回滚到的迁移前面的数字，如果您想要跳过其中几个迁移的话。省略这个数字只是回滚一次，直到最后一次已知的迁移，所以在没有`-t`的情况下运行几次也是一样的。

最后，我们可以运行`migrate`并将其全部导入。首先，我们取消了`change`方法的注释。然后:

```
php vendor/bin/phinx migrate -c my-phinx.yml 
```

![Successful migration of all three files](img/32f0559cd17ddd1d7c1f014be2b605d1.png)

成功！三个表都已经创建好了！

## 后续迁移和版本控制

那么，我们现在如何进行更改和后续迁移呢？假设我们希望`message`表:

*   包含一个字段，该字段可以注册已经*看到*消息的用户帐户的所有`user_id`值。
*   包含一个主题，而不仅仅是一个主体。
*   可以在主题和正文上快速搜索(全文索引——仅适用于 MySQL)

编写 SQL 并以这样一种原始形式提交它，对于从头开始设置我们正在开发的应用程序的人来说仍然很简单，但是当一个人已经有了数据库，对表结构进行更改就是迁移的真正威力开始显现的地方。

我们创建一个新的迁移:

```
php vendor/bin/phinx create MessageSeen -c my-phinx.yml 
```

在新创建的`message_seen`类中，我们将`change`方法改为:

```
 public function change()
    {

        $message = $this->table('message');

        $message->addColumn('seen_by', 'text');
        $message->addColumn('subject', 'text');

        if ($this->getAdapter()->getAdapterType() === 'mysql') {
            $message->addIndex('subject', ['type' => 'fulltext']);
            $message->addIndex('content', ['type' => 'fulltext']);
        }

        $message->update();
    } 
```

请注意，在创建表时，我们第一次像以前一样添加了列，因此没有什么新的内容。但是我们使用了一个技巧来发现我们的适配器是否属于`mysql`类型，这意味着我们支持全文索引。定义好一切之后，我们在表上执行`update`命令。如果我们现在运行默认的 migrate 命令，应该会应用更改。

```
php vendor/bin/phinx migrate -c my-phinx.yml 
```

果然，变化就在那里:

![Additional changes have been applied to the table and are now visible in the database](img/a740c9d63955c1dea18f5e909b814b44.png)

## 结论

Phinx 是一个非常实际且易于使用的包，其功能类似于某些框架中的功能，但完全独立。它为我们提供了在不编写原始 SQL 的情况下创建、销毁和修改数据库表的能力，这不仅使编写和修改版本变得更加容易，而且还让我们能够在以后将底层数据库适配器从 MySQL 切换到 Postgre！

你写过最复杂的迁移是什么？你认为这种方法会有什么问题吗？请在评论中告诉我们！

## 分享这篇文章