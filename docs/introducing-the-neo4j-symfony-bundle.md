# Neo4j Symfony 捆绑包简介

> 原文：<https://www.sitepoint.com/introducing-the-neo4j-symfony-bundle/>

*这篇文章由 [Wern Ancheta](https://www.sitepoint.com/author/wancheta) 和 [Christophe Willemsen](https://www.sitepoint.com/author/ikwattro) 进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

* * *

## 为什么是图表？

无论从哪个角度看，都没有不相关的信息——人、事件、地点、事物、文档、应用程序以及与它们相关的信息都是紧密相连的。随着数据量的增长，其连接的数量和动态性也在增长。如果您过去曾尝试在任何数据库中存储和查询这种高度关联的半结构化数据，您可能会遇到很多挑战。

![Example graph](img/20705bbe4886982d0469cee232263123.png)

## 原生图数据库中的标记属性图

Neo4j 正是为了处理这种真实世界的信息而构建的，而不会影响您可以拥有的与实体相关的连接的数量和类型。这是一个开源的 NoSQL 数据库，它使用带标签的属性图将您的域模型(图)的实体存储为**节点**，将它们的连接存储为**关系**，每个节点都可以有任意的属性。

![Nodes and releationships with properties](img/80b1e5e386283c6dc4c4173ba0643fa0.png)

Neo4j 不仅仅是另一个数据库之上的一个图形层，而是一个成熟的事务性(ACID)数据库，从管理磁盘上的记录页到提供一个可伸缩的、安全的集群，应有尽有。作为原生图形数据库，它使用专用的数据结构来高效地存储和查询高度关联的数据。与其他数据库不同，(复杂)连接查询在查询时不会重复计算。相反，实体之间的关系是直接存储的。在查询过程中，数据库引擎可以使用直接记录指针进行常量时间查找。

## 开放密码查询语言

这不仅仅扩展到建模或存储，甚至 Neo4j 附带的 Cypher graph 查询语言也专注于图形模式，这次在您的查询中表示为 ASCII-art:`(dan:Person {name:"Dan"})-[:LOVES]->(ann:Person {name:"Ann"})`，这使得您的查询即使对于非开发人员也非常可读，例如下面是一个推荐查询(“像您这样的客户也购买了这个”):

```
MATCH (c:Customer)-[:BOUGHT]->(:Product)<-[:BOUGHT]-(o:Customer)-[:BOUGHT]->(reco:Product)
WHERE c.id = 123 AND NOT (c)-[:BOUGHT]->(reco)
RETURN reco.name, count(*) as frequency
ORDER BY frequency DESC LIMIT 10; 
```

## Symfony，PHP 快速开发框架

Symfony 是现代 PHP 框架的角色模型。该框架采用组件方法，已经存在了 11 年。从 Symfony 生态系统中，我们已经看到了 Composer、Twig、Swiftmailer、Assetic、Monolog 等项目。多亏了组件方法，其他项目和框架重用 Symfony 的代码变得很容易。像 Laravel、Silex、Sylius、Drupal、phpBB、eZ 等项目都在使用 Symfony 组件。

Symfony 成功的一个关键因素是框架的灵活性和易用性。Symfony 的标准版附带了作为默认数据库层抽象的原则，支持一些主要的数据库，如 MySQL 和 MongoDB。但是无论是数据库层还是原则都不是 Symfony 的主要公民。它们很容易被其他东西取代。

## Symfony Neo4j 捆绑包简介

为了提供 Neo4j 和 Symfony 之间的平滑集成，我们创建了 SymfonyNeo4jBundle。它通过 Graphaware 包装了优秀的 PHP 社区客户端，并创建了可靠的 Symfony 体验。由于 WebProfiler 的集成，您将看到所有的数据库调用、所有的查询及其结果。您甚至可以看到与数据库交互时抛出的任何异常。您还将获得关于每个数据库调用的详细统计信息。这使得调试应用程序更加容易。

这个包还集成了客户端事件和 Symfony 事件调度程序。现在，您可以创建事件订阅者来监听与 Neo4j 的交互，例如与 MonologBundle 集成来记录您的所有数据库查询。

这个包并不坚持你如何使用 Neo4j。使用 OGM 是可选的。高级 Neo4j 用户将完全控制客户端和执行什么样的密码。

### 类似 API 的学说

对于熟悉主义的开发者来说，他们会知道如何使用 GraphAware 的 OGM(对象图映射器)。OGM 有一个 EntityManager，它实现了 Doctrine 的 ObjectManager 接口，为您提供了“查找”、“删除”、“持久化”和“刷新”等功能。与 Doctrine 的 MySQL EntityManagers 相比，开发人员在使用 Neo4j 的 OGM 模型时会有完全相同的体验。

### 配置

与大多数现代框架一样，Symfony 将配置与代码分开。这是 Neo4jBundle 坚持的良好软件实践。它提供了轻松配置多个连接、多个客户端和多个实体管理器的能力。对于每个连接，您可以决定是使用 HTTP 还是新的二进制“bolt”协议。

由于 Symfony 的配置组件，您可以使用 Yaml、XML 或 PHP 来指定您的配置。默认配置将用默认用户名/密码建立一个到`127.0.0.1:7474`的连接。

## Symfony-Neo4j 软件包入门

在[安装软件包](https://github.com/neo4j-contrib/neo4j-symfony)后，您可以开始使用客户端

```
$client = $this->get('neo4j.client');
$results = $client->run('MATCH (n:Movie) RETURN n LIMIT 5');
foreach ($results->records() as $record) {
   $node = $record->get('n');
   echo $node->get('title'); // "The Matrix"
} 
```

如果你习惯于教条，你可能想使用 OGM。您需要向您的模型添加注释，以便 OGM 能够正确理解和映射属性。

```
use GraphAware\Neo4j\OGM\Annotations as OGM;

/**
 * @OGM\Node(label="User")
 */
class User
{
    /** @OGM\GraphId() */
    protected $id;

    /** @OGM\Property(type="string") */
    protected $name;

    /** @OGM\Property(type="int") */
    protected $age;

    // Getters and setters
} 
```

```
$em = $this->get('neo4j.entity_manager');

$bart = new User('Bart Johnson', 33);
$em->persist($bart);
$em->flush();

// Retrieve from database
$john = $em->getRepository(User::class)->findOneBy('name', 'John Doe');
echo $john->getAge(); 
```

下面是一个概要分析器的示例:

![Example of the Symfony profiler page](img/9dce6ef665b8ef62c7154e8a1ebab6c4.png)

## 关系和关系实体

与 Doctrine 和 MySQL 的主要区别在于，在 Neo4j 中，关系是一等公民。它们和节点本身一样重要。要创建一个人和一部电影之间的简单关系，您可以使用`@OGM\Relationship`注释。

```
use GraphAware\Neo4j\OGM\Annotations as OGM;
use GraphAware\Neo4j\OGM\Common\Collection;

/**
*
* @OGM\Node(label="Person")
*/
class Person
{
   /**
    * @var int
    *
    * @OGM\GraphId()
    */
   protected $id;

   // other code

   /**
    * @var Movie[]|Collection
    *
    * @OGM\Relationship(type="ACTED_IN", direction="OUTGOING", collection=true, mappedBy="actors", targetEntity="Movie")
    */
   protected $movies;

   public function __construct()
   {
       $this->movies = new Collection();
   }

   // other code

   /**
    * @return Movie[]|Collection
    */
   public function getMovies()
   {
       return $this->movies;
   }
} 
```

电影类有一个来自 Person 的传入关系。

```
use GraphAware\Neo4j\OGM\Annotations as OGM;
use GraphAware\Neo4j\OGM\Common\Collection;

/**
*
* @OGM\Node(label="Movie")
*/
class Movie
{
   /**
    * @var int
    *
    * @OGM\GraphId()
    */
   protected $id;

   // other code

   /**
    * @var Person[]|Collection
    *
    * @OGM\Relationship(type="ACTED_IN", direction="INCOMING", collection=true, mappedBy="movies", targetEntity="Person")
    */
   protected $actors;

   public function __construct()
   {
       $this->actors = new Collection();
   }

   // other code

   /**
    * @return Person[]|Collection
    */
   public function getActors()
   {
       return $this->actors;
   }
} 
```

关系本身也可以有属性。例如，如果用户对一部电影进行评级，那么该关系可能会有一个分数。这可以通过关系实体来实现。在 OGM 文档中阅读更多关于它们的信息。

## 示例项目

有一个示例项目可以用来测试这个包。使用以下步骤安装项目:

```
git clone git@github.com:neo4j-examples/movies-symfony-php-bolt.git
composer install 
```

导入数据夹具(https://neo4j.com/developer/example-project/#_data_setup)

```
php bin/console server:run 
```

请访问 http://127.0.0.1:8000/

Github 上的[库](https://github.com/neo4j-contrib/neo4j-symfony)中有更多关于这个包的信息。请就我们做得对的地方以及如何改进给我们意见和反馈。欢迎[提出问题](https://github.com/neo4j-contrib/neo4j-symfony/issues)或为项目做贡献。

## 资源:

*   [Symfony 捆绑包](https://github.com/neo4j-contrib/neo4j-symfony)
*   [Neo4j PHP 页面](http://neo4j.com/developer/php)
*   [PHP 驱动程序](https://github.com/graphaware/neo4j-php-client)
*   [GitHub](https://github.com/neo4j-contrib/neo4j-symfony)
*   [演示项目](https://github.com/neo4j-examples/movies-symfony-php-bolt)
*   [Neo4j-PHP-OGM](https://github.com/graphaware/neo4j-php-ogm)

## 分享这篇文章