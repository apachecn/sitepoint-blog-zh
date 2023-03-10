# Atlas 初探——交付的 ORM

> 原文：<https://www.sitepoint.com/a-first-look-at-atlas-the-orm-that-delivers/>

*这篇文章由[保罗·m·琼斯](https://www.sitepoint.com/author/pmjones/)和[斯科特·莫利纳里](https://www.sitepoint.com/author/smolinari/)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

> ***“[atlas or m](https://github.com/atlasphp/Atlas.Orm)是一个为你的持久模型(不是你的领域模型)实现的数据映射器】“***

根据定义，数据映射器在对象和数据库之间移动数据，并将它们相互隔离。使用数据映射器，内存中的对象甚至不需要知道数据库的存在。它不需要知道 SQL 接口或数据库模式；它甚至不需要领域层知道它的存在！

这可能会让我们认为，在 [Atlas](https://github.com/atlasphp/Atlas.Orm) 中，持久层完全脱离了数据库，但事实并非如此。Atlas 使用术语`Record`来表示它的对象不是领域实体。地图集记录是被动的；不是活动记录。与大多数 ORM 不同，它的对象表示持久性模型，而不是域模型。请将它视为表示数据如何存储，而不是真实世界的表示。

![Illustration of the god Atlas carrying a big globe](img/54115647da5bbf01e0683f4a8aa898d5.png)

背后的推理是什么？

地图集的创造者[保罗·琼斯](https://www.sitepoint.com/author/pmjones/)，根据[迈赫迪·哈利利](http://www.mehdi-khalili.com/orm-anti-patterns-part-4-persistence-domain-model/)的这篇文章改编。想法是创建一个替代 [*活动记录*](https://www.sitepoint.com/yii-2-0-activerecord-explained/) 模式(一个携带数据和行为的对象，本质上连接到数据库)。

引用作者的话:

> “我想要一个活动记录的替代品，它可以让你像使用活动记录一样容易地开始你的持久模型，然后根据需要更容易地重构一个更丰富的领域模型。”

亚多拉斯有自己的一套与众不同的特征。

*   **无注释**——前提是代码要在代码里，不在注释里。
*   **无迁移或数据库修改逻辑**–由于 Atlas 与数据库是分离的(并非完全分离，作为一个整体，软件包需要与数据库交互才能来回移动数据)，因此它只作为模式的一个模型而没有其创建者和管理者是有意义的。
*   **没有延迟加载**–创造者认为延迟加载是有用的，但最终太麻烦了。
*   **没有数据类型抽象**–数据库类型尽可能公开和可用。
*   **复合键支持**–Atlas 支持复合主键和复合外键。

现在我们对 Atlas 有了一个大致的了解，也知道它代表了什么，让我们来看看它的使用情况。

## 装置

因为在撰写本文时，Atlas 仍在开发中，所以我们将安装最新、最先进的版本。此外，为了便于开发，我们将安装 CLI 包。留给我们的是下面的`composer.json`文件:

```
{
    "require": {
        "atlas/orm": "@dev"
    },
    "require-dev": {
        "atlas/cli": "@dev"
    },
    "autoload": {
        "psr-4": {
            "App\\": "src/App/"
        }
    }
} 
```

我们也有一些 [PSR-4](https://www.sitepoint.com/battle-autoloaders-psr-0-vs-psr-4/) 用于自动装载，我们将在以后使用。

## 设置它

前面，我们说过 Atlas 不像一个模式创建者和管理者。这意味着我们没有创建数据库的工具。在这个例子中，我们从头开始创建一个新的简单的数据库，但是 Atlas 将与您已经存在的任何数据库一起工作。

我们将使用一个非常简单的数据库设计。我们将有单独的数据库表中的产品和类别。使用 [MySQL](https://www.sitepoint.com/how-to-install-mysql/) ，让我们创建我们的数据库:

```
CREATE DATABASE atlasorm;
USE DATABASE atlasORM;
CREATE TABLE `category` (`category_id` int(11) NOT NULL, `name` varchar(255) NOT NULL) ENGINE=InnoDB DEFAULT CHARSET=latin1;
CREATE TABLE `products` (`product_id` int(11) NOT NULL, `name` varchar(255) NOT NULL, `category_id` int(11) NOT NULL) ENGINE=InnoDB DEFAULT CHARSET=latin1;
ALTER TABLE `category` ADD PRIMARY KEY (`category_id`);
ALTER TABLE `products` ADD PRIMARY KEY (`product_id`);
ALTER TABLE `category` MODIFY `category_id` int(11) NOT NULL AUTO_INCREMENT;
ALTER TABLE `products` MODIFY `product_id` int(11) NOT NULL AUTO_INCREMENT; 
```

之后，我们向两个表中添加一些值:

```
INSERT INTO `category` (`category_id`, `name`) VALUES (1, 'Steel'), (2, 'Wood'), (3, 'Leather');
INSERT INTO `products` (`product_id`, `name`, `category_id`) VALUES (1, 'Nails', 1), (2, 'Pipe', 1), (3, 'Chair', 2), (4, 'Screwdriver', 1); 
```

有了这些信息，我们现在可以设置我们的 ORM 了。Atlas 生成的代码很少。为了生成框架类(表类和映射器类)，我们首先必须创建一个`.php`文件，在其中保存我们的数据库连接信息。这样，生成器将知道它应该生成什么属性。

```
<?php
// connection.php
return ['mysql:dbname=testdb;host=localhost', 'username', 'password']; 
```

然后，让我们运行`atlas-skeleton`生成器。`atlas-skeleton`生成器是一个 CLI 工具，它将使用`connection.php`文件上的数据库连接信息、类名和给定的名称空间来生成框架类。请注意，我们必须为数据库中的每个表运行生成器。

```
./vendor/bin/atlas-skeleton.php --dir=./src/App/DataSource --conn=/path/to/connection.php --table=products App\\DataSource\\Products
./vendor/bin/atlas-skeleton.php --dir=./src/App/DataSource --conn=/path/to/connection.php --table=category App\\DataSource\\Category 
```

运行`atlas-skeleton`命令后，我们可以检查`/src/App/Datasource`文件夹，看到`products`和`category`的骨架都被创建了。这两个框架都包含两个文件。一个几乎为空的`Mapper`文件和一个包含数据库表描述的`Table`类。这就是我们需要的所有设置。

现在，让我们终于看到它的行动！

## CRUD 操作

设置好一切后，让我们专注于使用我们的 ORM。为此，我们需要一个`Atlas`实例，我们使用`AtlasContainer`类创建一个。

```
<?php

require_once __DIR__ . '/vendor/autoload.php';

use Atlas\Orm\Mapper\Mapper;
use Atlas\Orm\AtlasContainer;
use App\DataSource\Category\CategoryMapper;
use App\DataSource\Products\ProductsMapper;

$atlasContainer = new AtlasContainer('mysql:host=host;dbname=atlasorm','username', 'password' );
$atlasContainer->setMappers([ProductsMapper::CLASS, CategoryMapper::CLASS]);

$atlas = $atlasContainer->getAtlas(); 
```

这里没有什么特别的，我们自动加载我们的供应商类，包括我们的映射器，并用我们的`Products`和`Category`映射器信息创建一个`Atlas`实例。

### 阅读

要从我们的数据库中读取一个`Record`或一个`RecordSet`,我们可以使用以下代码:

```
$categoryRecord = $atlas->fetchRecord(CategoryMapper::CLASS, '2'); 
```

这将返回一条记录，即 ID 为 2 的记录。对于记录集:

```
$categoryRecordSet = $atlas
    ->select(CategoryMapper::CLASS)
    ->orderBy(['category_id DESC'])
    ->limit(10)
    ->fetchRecordSet(); 
```

这将获取按`category_id`排序的最后 10 个类别的记录集。

### 创建、更新和删除

对于创建、更新和删除，Atlas 使用相同的原理。获取记录并对其进行操作:

```
// start insert
$newCategory = $atlas->newRecord(CategoryMapper::CLASS);
$newCategory->name = "Bone";

// create a transaction
$transaction = $atlas->newTransaction();
$transaction->insert($newCategory);

// execute the transaction
$ok = $transaction->exec();
if ($ok) {
    echo "Transaction success.";
} else {
    echo "Transaction failure. ";
} 
```

正如我们所见，创建非常简单。对于更新和删除，我们可以使用相同的原则:

```
//Start updating
$newCategory = $atlas->fetchRecord(CategoryMapper::CLASS, '2');
$newCategory->name = "Wood";

// create a transaction
$transaction = $atlas->newTransaction();

// plan work for the transaction
$transaction->update($newCategory);
//$transaction->delete($newCategory);

// execute the transaction plan
$ok = $transaction->exec();
if ($ok) {
    echo "Transaction success.";
} else {
    echo "Transaction failure. ";
}
//End updating 
```

在这种情况下，我们可以使用`update`和新的表值来更新一条记录，或者使用`delete`来删除同一条记录。

### 关于关系的快速笔记

Atlas 支持四种关系:`OneToMany`、`ManyToOne`、`OneToOne`和`MantToMany`。要添加关系，我们必须将它们添加到映射器类中。

在我们的特殊情况下，让我们想象每个类别可以有许多产品。一对多关系。我们的`CategoryMapper`应该是这样的:

```
<?php

namespace App\DataSource\Category;

use Atlas\Orm\Mapper\AbstractMapper;
use App\DataSource\Products\ProductsMapper;

/**
 * @inheritdoc
 */
class CategoryMapper extends AbstractMapper
{
    /**
     * @inheritdoc
     */
    protected function setRelated()
    {
        $this->oneToMany('products', ProductsMapper::CLASS);
    }
} 
```

默认情况下，该关系将采用源表中的主键，并将其映射到目标表中的相应列。当然，在我们有多对一关系的情况下，这是不可能的。在那种情况下，将进行相反的操作。目标表上的主键将被映射到源表上的相应列。

因此，如果我们想获取我们的类别及其相关产品记录的记录集，我们可以使用`with()`方法，就像这样:

```
$newRecord = $atlas
    ->select(CategoryMapper::CLASS)
    ->with([
        'products'
    ])
    ->fetchRecordSet(); 
```

了解了基本的 CRUD 操作是如何工作的，我们现在可以看看一些使用 Atlas 的更实际的代码示例。

### 一些实际例子

有一个像 Atlas 这样的工具，并看到它是如何工作的，真是太棒了。但最终，我们希望看到它被用于更实际的场景。虽然我们不会构建一个完全成熟的应用程序，但我们会研究一些可能的用途。

有些操作我们每天都在使用，甚至没有注意到，比如获取信息、插入和删除数据库记录。使用 Atlas 会是什么样子？

```
<?php

require_once __DIR__ . '/vendor/autoload.php';

use Atlas\Orm\Mapper\Mapper;
use Atlas\Orm\AtlasContainer;
use App\DataSource\Category\CategoryMapper;
use App\DataSource\Products\ProductsMapper;

/**
* This function will create and return our Atlas instance set up
* with both our Mappers
*
* @return $atlasContainer
*/
function getAtlasContainer(){

    $atlasContainer = new AtlasContainer('mysql:host=localhost;dbname=atlasorm','root', '' );
    $atlasContainer->setMappers([ProductsMapper::CLASS, CategoryMapper::CLASS]);

    return $atlasContainer->getAtlas();
}

/**
* This function will return a RecordSet of all our products
*
* @return RecordSet
*/
function getAllProducts(){

    $atlas = getAtlasContainer();

    $productsRecordSet = $atlas
            ->select(ProductsMapper::CLASS)
            ->fetchRecordSet();

    return $productsRecordSet;
}

/**
* This function will return a Record of the Product with the specified id
*
* @param int 
* @return Record 
*/
function getProductById( $id ){

    $atlas = getAtlasContainer();

    $productRecord = $atlas->fetchRecord(ProductsMapper::CLASS, $id);

    return $productRecord;
}

/**
* This function will insert a new product Record
*
* @param string $product_name
* @param int $category_name 
*/
function addProduct( $product_name, $category_id ){

    $atlas = getAtlasContainer();

    //First we check if the category exists
    $categoryRecord = $atlas->fetchRecord(CategoryMapper::CLASS, $category_id);

    //if our category exists we will insert our product
    if( $categoryRecord ){
        //Start insert
        $newProduct = $atlas->newRecord(ProductsMapper::CLASS);
        $newProduct->name = $product_name;
        $newProduct->category_id = $category_id;

        // create a transaction
        $transaction = $atlas->newTransaction();
        $transaction->insert($newProduct);

        // execute the transaction
        $ok = $transaction->exec();
        if ($ok) {
            echo "Transaction success.";
        } else {
            // get the exception that was thrown in the transaction
            $exception = $transaction->getException();
            $work = $transaction->getFailure();
            echo "Transaction failure. ";
            echo $work->getLabel() . ' threw ' . $exception->getMessage();
        }
        //End insert
    }else{
        echo 'The category is not valid.';
    }
}

/**
* This function will delete a product Record
*
* @param id $product_id
*/
function deleteProduct( $product_id ){

    $atlas = getAtlasContainer();

    //First, lets check if the product with $product_id exists
    $productRecord = $atlas->fetchRecord(ProductsMapper::CLASS, $product_id);

    if( $productRecord ){
        //Delete the product
        $transaction = $atlas->newTransaction();
        $transaction->delete($productRecord);

        // execute the transaction
        $ok = $transaction->exec();
        if ($ok) {
            echo "Transaction success.";
        } else {
            // get the exception that was thrown in the transaction
            $exception = $transaction->getException();
            $work = $transaction->getFailure();
            echo "Transaction failure. ";
            echo $work->getLabel() . ' threw ' . $exception->getMessage();
        }
    }else{
        echo 'The product you are trying to delete does not exist.';
    }
} 
```

使用 Atlas 的一个非常基本的操作框架(没有 OOP 和现代编程概念，因为这只是一个演示)。

### 警告

最后，让我们看看两个警告。

*   Atlas 使用**代码生成**。如果您更改一个已经生成了代码的数据库表并重新生成它，mapper 类将不会被覆盖。如果您在 mapper 类中编写了自定义代码，这将特别有帮助。

*   Atlas 是一个正在进行中的工作，1.0.0-alpha1 是最近才发布的。使用它时要非常小心，尤其是当您试图在生产环境中使用它时，因为突破性的更改可能会影响您的项目。

## 结论

Atlas 背后的概念是原创的，很容易理解和使用。没有注释和很少的代码生成是非常强的卖点，尤其是如果你，就像我一样，喜欢让你的代码尽可能地去耦和独立。

你觉得阿特拉斯怎么样？你能转一下吗？

## 分享这篇文章