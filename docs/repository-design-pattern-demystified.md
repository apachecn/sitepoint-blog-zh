# 仓库设计模式去神秘化

> 原文：<https://www.sitepoint.com/repository-design-pattern-demystified/>

### 什么是存储库设计模式？

简单地说，它是应用程序和数据源之间的代理层的实现。任何一方都不需要知道另一方来执行他们各自的工作，这允许我们有一个解耦的架构，这反过来有助于在没有硬依赖的情况下在大联盟中扩展应用程序。

### 你为什么要在乎？

让我们用一个例子来理解这一点。想象一下，我们正在建立一个销售桔子味糖果的网上商店。这是一个小商店，它保持本地库存，所以我们不需要任何花哨的东西。storefront 应用程序可以直接连接到数据库，并根据手头的库存数量在线接受订单。这很好，因为商店只有一个供应仓库，而且经营区域有限。但是如果这家店想扩大经营面积会怎么样呢？商店可能想扩展到另一个城市或全国各地，拥有一个中央库存系统将是如此繁琐。

现在，如果我们仍然使用数据模型，那么我们就有了一个紧密耦合的应用程序。店面应用程序需要知道它必须与之交互的每个数据源，这是一个糟糕的应用程序设计。这里店面应用程序的工作是允许客户订购糖果，应用程序不应该关心数据源，它不应该跟踪所有不同的数据源。这就是数据存储库发挥作用的地方。按照存储库设计模式，公共 API 通过一个接口公开，每个消费者(在本例中是我们的店面应用程序)使用该 API 与数据源进行对话。正在使用哪个数据源，或者数据源是如何连接的，这些都与应用程序无关。应用程序只关心它获得的数据和它发送要保存的数据。

一旦实现了存储库设计模式，就可以为每个数据源创建存储库。storefront 应用程序不再需要跟踪任何数据源，它只是使用存储库 API 来获取所需的数据。

### 这是灵丹妙药吗？

不，不是的。像每一种设计模式一样，它有起有落，有利有弊。

**优点:**

*   关注点分离；应用程序不需要知道或跟踪任何或所有数据源。
*   允许简单的单元测试，因为存储库被绑定到在运行时注入到类中的接口。
*   DRY(不要重复)设计，从数据源查询和获取数据的代码是不重复的。

**缺点:**

*   增加了另一个抽象层，这增加了一定程度的复杂性，对于小型应用程序来说，这是一个大材小用。

### 如何着手呢？

让我们用一个小代码示例来看看这一点。我将在示例中使用 Laravel 来利用其出色的依赖注入特性。如果你使用任何现代的 PHP 框架，那么它应该已经有了依赖注入/IoC 容器。实现存储库设计模式需要依赖注入，因为没有依赖注入，您将无法将数据存储库绑定到存储库接口，整个想法是编码到接口以避免硬耦合。如果您没有使用任何框架或者您选择的框架没有 IoC 容器，那么您可以使用现成的 IoC 容器(查看脚注)。

让我们继续吧。首先，我们在 Composer 中设置名称空间和自动加载。打开`composer.json`，为我们的名称空间添加 psr-4 自动加载(在`classmap`之后的`autoload`节点中)。

```
 "autoload":  {  "classmap":  [  "app/commands",  "app/controllers",  "app/models",  "app/database/migrations",  "app/database/seeds",  "app/tests/TestCase.php"  ],  "psr-4":  {  "RocketCandy\\":  "app/RocketCandy"  }  },
```

保存后，在终端中执行`composer dump-autoload -o`来注册新名称空间的自动加载。在`app/RocketCandy/Repositories/OrangeCandyRepository/`中创建`OrangeCandyRepository.php`。这将是我们的存储库接口。

```
<?php namespace  RocketCandy\Repositories\OrangeCandyRepository;  interface  OrangeCandyRepository  {  public  function get_list( $limit =  0, $skip =  0  );  public  function get_detail( $candy_id =  0  );  }
```

现在我们有了一个接口，我们可以创建一个存储库。在`app/RocketCandy/Repositories/OrangeCandyRepository/`中创建`CityAOrangeCandyRepository.php`。

```
<?php namespace  RocketCandy\Repositories\OrangeCandyRepository;  class  CityAOrangeCandyRepository  implements  OrangeCandyRepository  {  public  function get_list( $limit =  0, $skip =  0  )  {  //query the data source and get the list of  //candies  }  public  function get_detail( $candy_id =  0  )  {  //query the data source and get the details of  //candies  }  }
```

为了将`CityAOrangeCandyRepository`存储库绑定到`OrangeCandyRepository`接口，我们将利用 Laravel 的 IoC 容器。打开`app/start/global.php`并在文件末尾添加以下内容。

```
//OrangeCandyRepository  App::bind(  'RocketCandy\Repositories\OrangeCandyRepository\OrangeCandyRepository',  'RocketCandy\Repositories\OrangeCandyRepository\CityAOrangeCandyRepository'  );
```

***注:**我把 IoC 绑定放在`global.php`只是为了演示。理想情况下，这些应该放在一个单独的文件中，您可以将所有的 IoC 绑定放在这个文件中，并在`global.php`中加载该文件，或者您可以创建服务提供者来注册每个 IoC 绑定。这里可以阅读更多[。](http://laravel.com/docs/ioc)*

现在，我们可以继续通过接口使用存储库了。在我们的`CandyListingController.php`位于`app/controllers/`。

```
<?php use  RocketCandy\Repositories\OrangeCandyRepository\OrangeCandyRepository;  class  CandyListingController  extends  BaseController  {  /**
     * @var RocketCandy\Repositories\OrangeCandyRepository\OrangeCandyRepository
     */  protected $_orange_candy;  public  function __construct(  OrangeCandyRepository $orange_candy )  { $this->_orange_candy = $orange_candy;  }  }
```

在这里，我们将`OrangeCandyRepository`接口注入到我们的控制器中，并将其对象引用存储在一个类变量中，现在控制器中的任何函数都可以使用该变量来查询数据。由于我们将`OrangeCandyRepository`接口绑定到了`CityAOrangeCandyRepository`库，这就好像我们直接使用了`CityAOrangeCandyRepository`库本身。

所以现在，数据源的类型和种类是这里`CityAOrangeCandyRepository`唯一关心的。我们的应用程序只知道`OrangeCandyRepository`接口和它所公开的 API，实现它的每个存储库都必须遵守这些 API。存储库在运行时从 IoC 容器中解析出来，这意味着可以根据需要设置接口< = >存储库绑定，接口可以绑定到任何数据存储库，我们的应用程序不必担心数据源的变化，数据源现在可以是数据库、web 服务或跨维超级数据管道。

### 一种尺寸不适合所有人

正如我在上面的知识库设计模式的缺点中提到的，它给应用程序增加了一点复杂性。因此，如果您正在开发一个小应用程序，并且您没有看到它升级到可能调用多个数据源的大联盟，那么您最好不要实现它，而是坚持使用良好的旧数据模型。知道某事和知道何时使用那件事是不同的。这是一种非常方便的设计模式，无论是在创建应用程序时，还是在必须对应用程序进行维护或扩展(或缩减)时，都可以省去很多麻烦，但它并不是适用于所有应用程序的灵丹妙药。

我使用了 Laravel 特定的代码来演示上面的实现，但是它相当简单，与任何像样的 IoC 容器都很相似。有问题吗？请在下面的评论中畅所欲言。

### 脚注:

*   如果您的框架没有 IoC 容器库或者您没有使用框架，您可以使用以下 IoC 容器库:
    *   [Orno\Di](https://github.com/orno/di)
    *   [雷。Di](https://github.com/koriym/Ray.Di)
    *   [太阳](https://github.com/rdlowrey/Auryn)
    *   [骰子](https://github.com/TomBZombie/Dice)
    *   [桶](https://github.com/troelskn/bucket)
    *   [丁](https://github.com/marcelog/Ding)
*   推荐阅读:
    *   [领域驱动快速设计](http://www.infoq.com/resource/minibooks/domain-driven-design-quickly/en/pdf/DomainDrivenDesignQuicklyOnline.pdf)
    *   埃里克·埃文斯的领域驱动设计

## 分享这篇文章