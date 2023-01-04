# 快速提示:使用一次性数据库测试 Symfony 应用程序

> 原文：<https://www.sitepoint.com/quick-tip-testing-symfony-apps-with-a-disposable-database/>

测试与数据库交互的代码是一件非常痛苦的事情。一些开发人员模仿数据库抽象，因此不测试实际的查询。其他人会为开发环境创建一个测试数据库，但是当涉及到持续集成和维护这个数据库的状态时，这也是一件痛苦的事情。

![Symfony logo](img/2ee97df5b613acecba0787f696eb5c2f.png)

内存数据库是这些选项的替代方案。因为它们只存在于应用程序的内存中，所以它们确实是一次性的，非常适合测试。幸运的是，这些很容易用使用教条的 Symfony 应用程序来设置。在此之后，请尝试阅读我们的 Symfony 功能测试指南，了解如何测试应用程序的端到端行为。

## Symfony 环境配置

Symfony 框架最强大的特性之一是能够用自己独特的配置创建不同的环境。Symfony 开发人员可能会忽略这个特性，尤其是在这里研究的鲜为人知的测试环境中。

Symfony 指南[掌握和创建新环境](http://symfony.com/doc/current/cookbook/configuration/environments.html)解释了框架如何处理不同环境的配置，并展示了一些有用的例子。设置一次性测试数据库需要编辑的配置文件是`app/config/config_test.php`。当在测试套件中访问应用程序时，将使用测试环境加载内核，并处理这个配置文件。

## 带有教条的内存数据库

[SQLite3](https://www.sqlite.org/) 支持[内存数据库](https://www.sqlite.org/inmemorydb.html)，非常适合测试。使用这些方法，可以通过向正常运行的数据库发送 SQL 查询来测试应用程序，从而消除了用预定义的行为繁琐地模仿存储库类的需要。数据库在测试开始时是新的，在测试结束时被彻底销毁。

要覆盖默认的原则连接配置，需要将下面几行添加到测试环境配置文件中。如果在应用程序中配置了多个学说连接，则可能需要稍作调整以匹配。

```
# app/config/config_test.yml

doctrine:
    dbal:
        driver:  pdo_sqlite
        memory:  true
        charset: UTF8 
```

## 在测试类中使用数据库

当在测试类中使用这个崭新的内存数据库时，必须首先构建模式。这意味着从实体中创建表，并加载测试套件所需的任何设备。

下面的类可以作为数据库的基础，完成大部分的工作。它与强制运行教条模式更新控制台命令的效果相同。

```
<?php

namespace Tests\AppBundle;

use Doctrine\ORM\EntityManager;
use Doctrine\ORM\Tools\SchemaTool;
use Symfony\Component\HttpKernel\KernelInterface;

class DatabasePrimer
{
    public static function prime(KernelInterface $kernel)
    {
        // Make sure we are in the test environment
        if ('test' !== $kernel->getEnvironment()) {
            throw new \LogicException('Primer must be executed in the test environment');
        }

        // Get the entity manager from the service container
        $entityManager = $kernel->getContainer()->get('doctrine.orm.entity_manager');

        // Run the schema update tool using our entity metadata
        $metadatas = $entityManager->getMetadataFactory()->getAllMetadata();
        $schemaTool = new SchemaTool($entityManager);
        $schemaTool->updateSchema($metadatas);

        // If you are using the Doctrine Fixtures Bundle you could load these here
    }
} 
```

如果要求实体管理器测试一个类，则必须使用初级工具:

```
<?php

namespace Tests\AppBundle;

use Symfony\Bundle\FrameworkBundle\Test\KernelTestCase;
use Tests\AppBundle\DatabasePrimer;

class FooTest extends KernelTestCase
{
    public function setUp()
    {
        self::bootKernel();

        DatabasePrimer::prime(self::$kernel);
    }

    public function testFoo()
    {
        $fooService = self::$kernel->getContainer()->get('app.foo_service');

        // ...
    }
} 
```

在上面的例子中，容器被用来获取被测试的服务。如果这个服务依赖于实体管理器，那么它将由在`setUp`方法中准备好的同一个实体管理器来构造。如果需要更多的控制，可能是为了模仿另一个依赖项，那么总是可以从容器中检索到实体管理器，并用它来手动实例化需要测试的类。

使用 [Doctrine Fixtures Bundle](http://symfony.com/doc/current/bundles/DoctrineFixturesBundle/index.html) 用测试数据填充数据库也是一个好主意，但这将取决于您的用例。

## 分享这篇文章