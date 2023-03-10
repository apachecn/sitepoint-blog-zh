# 以 TDD 方式升级 Sylius:探索 PhpSpec

> 原文：<https://www.sitepoint.com/upgrading-sylius-tdd-way-exploring-phpspec/>

*这篇文章由[克里斯多佛·皮特](https://www.sitepoint.com/author/assertchris)进行了同行评议。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

关于开发和测试 Sylius 新特性的[帖子介绍了 Sylius 中使用的三种类型的测试——](https://www.sitepoint.com/sylius-and-cutting-your-teeth-on-tdd/)[PHPUnit](https://www.sitepoint.com/blog/)、 [Phpspec](https://www.sitepoint.com/bdd-in-laravel-getting-started-with-behat-and-phpspec/) 和 [Behat](https://www.sitepoint.com/bdd-in-laravel-getting-started-with-behat-and-phpspec/) 。

在这一部分，我们将扩展一些核心类来表示颜色编码的库存状态。首先，我们将处理后端部分。在后续的文章中，我们将使用 Behat 测试视觉变化。请按照[上一篇文章](https://www.sitepoint.com/sylius-and-cutting-your-teeth-on-tdd/)中的说明，启动并运行一个工作实例。

* * *

![Sylius logo](img/483b11ce0472dbb2536a1248896b7044.png)

Sylius 拥有优秀的[库存管理解决方案](http://docs.sylius.org/en/latest/bundles/SyliusInventoryBundle/index.html)。然而，总有一些调整的空间。如果你查看产品列表(管理/产品),没有关于可用库存的信息。查看产品的变体，我们可以看到库存数据，无论是否被跟踪，如果被跟踪，还可以看到库存商品的总数。如果能在产品列表页面上看到这类信息，那就太好了。此外，库存水平是全有或全无，例如，绿色标签表示“现有 10”或红色标签表示“现有 0”。介于两者之间的东西怎么样，比如一个黄色的标签“现有 3 个”来表示库存不足？然后，商店管理员可以决定是时候补货了。

## 扩展产品变体和产品模型

我们希望扩展 Sylius 提供的`ProductVariant`和`Product`模型的行为，这样我们可以在查看产品时看到关于库存可用性的额外信息。

### 创建捆绑包

首先，我们创建`src/AppBundle/AppBundle.php`文件，并在`app/AppKernel.php`中注册它。

```
<?php

// src/AppBundle/AppBundle.php

namespace AppBundle;

use Symfony\Component\HttpKernel\Bundle\Bundle;

class AppBundle extends Bundle
{
} 
```

```
<?php

// app/AppKernel.php

public function registerBundles()
{
    $bundles = [
        // ...
        new AppBundle\AppBundle(),
    ];
} 
```

接下来，我们通过将新的包添加到`composer.json`的“自动加载”部分来通知自动加载器。

```
{
  // ...
  "autoload": {
    "psr-4": {
      // ...
      "AppBundle\\": "src/AppBundle"
    }
  }
  // ...
} 
```

然后，我们运行`composer dump-autoload`来重新生成自动加载器信息。现在，我们可以开始编写一些 SpecBDD 测试了。

### 编写 SpecBDD 测试

我们的测试将集中在我们的`Product`和`ProductVariant`类如何帮助我们实现我们为实现上述特性而设定的目标。Phpspec 需要了解我们的测试。我们打开`phpspec.yml.dist`，在“套房”下添加以下内容:

```
AppBundle: { namespace: AppBundle\Entity, psr4_prefix: AppBundle\Entity, spec_path: src/AppBundle/Entity, src_path: src/AppBundle/Entity } 
```

现在清空缓存可能比较好:`php bin/console cache:clear`。接下来要做的事情是制定规范，我们在其中编写类的预期行为，Phpspec 能够从一开始就帮助我们。`describe`命令是起点，它创建一个规范类:

```
php bin/phpspec desc AppBundle/Entity/ProductVariant 
```

结果是:

```
Specification for AppBundle\Entity\ProductVariant created in src/AppBundle/Entity/spec/ProductVariantSpec.php 
```

让我们运行它。

```
php bin/phpspec run src/AppBundle/Entity/spec/ProductVariantSpec.php 
```

规范中描述的类`ProductVariant`不存在，但是 Phpspec 将为我们创建它。创建好之后，我们可以重复上面的两个步骤来获得我们的`Product`类。

```
php bin/phpspec desc AppBundle/Entity/Product
php bin/phpspec run src/AppBundle/Entity/spec/ProductSpec.php 
```

Sylius 已经有了这两个类，所以我们需要在 AppBundle 中扩展它们。因为 Sylius 经常使用接口，所以我们也创建接口。

```
<?php

// src/AppBundle/Entity/ProductInterface.php

namespace AppBundle\Entity;

use Sylius\Component\Core\Model\ProductInterface as BaseProductInterface;

interface ProductInterface extends BaseProductInterface
{
} 
```

```
<?php

// src/AppBundle/Entity/ProductVariantInterface.php

namespace AppBundle\Entity;

use Sylius\Component\Core\Model\ProductVariantInterface as BaseProductVariantInterface;

interface ProductVariantInterface extends BaseProductVariantInterface
{
} 
```

```
<?php

// src/AppBundle/Entity/Product.php

namespace AppBundle\Entity;

use Sylius\Component\Core\Model\Product as BaseProduct;
use AppBundle\Entity\ProductInterface;

class Product extends BaseProduct implements ProductInterface
{
} 
```

让我们用我们想要引入的`$reorderLevel`属性来更新生成的`ProductVariant`类。

```
<?php

// src/AppBundle/Entity/ProductVariant.php

namespace AppBundle\Entity;

use AppBundle\Entity\ProductVariantInterface as ProductVariantInterface;
use Sylius\Component\Core\Model\ProductVariant as BaseProductVariant;

class ProductVariant extends BaseProductVariant implements ProductVariantInterface
{
    const REORDER_LEVEL = 5;

    /**
     * @var int
     */
    private $reorderLevel;

} 
```

### 覆盖 Sylius 类

让我们通知 Sylius 我们的类，这样它们将被用来代替 Sylius 提供的类。我们将以下内容添加到`app/config/config.yml`:

```
sylius_product:
    resources:
        product:
            classes:
                model: AppBundle\Entity\Product
        product_variant:
            classes:
                model: AppBundle\Entity\ProductVariant 
```

我们在`ProductVariant`类中添加了一个默认为 5 的`$reorderLevel`属性。由于不同的型号会有所不同，我们需要修改`product_variant`表，这样我们也可以存储这些信息。既然西利乌斯使用了教义 ORM，那就走这条路。为了扩展现有的表定义，让我们创建`src/AppBundle/Resources/config/doctrine/ProductVariant.orm.yml`，并添加以下内容:

```
AppBundle\Entity\ProductVariant:
    type: entity
    table: sylius_product_variant
    fields:
        reorderLevel:
            column: reorder_level
            type: integer
            nullable: true 
```

该字段的关键字是模型上的属性名称。我们必须指定列名，否则原则默认为“reorderLevel”的属性名。如果属性是一个单词，例如“level”，则该列可以不在此配置中。

接下来，让我们创建`src/AppBundle/Resources/config/doctrine/Product.orm.yml`并添加这个:

```
AppBundle\Entity\Product:
    type: entity
    table: sylius_product 
```

它是空的，但是我们说我们的扩展产品模型仍然在未修改的表中存储数据。

### 更新数据库

让我们运行以下代码:

```
php bin/console doctrine:migrations:diff 
```

该命令在`app/migrations`中生成一个迁移类。它通常是最后一个，因为名称中包含时间戳。它包含改变`product_variant`表的 SQL 语句。在实际项目中，生成的代码可能不够，但是您可以编辑文件以满足您的要求。要迁移:

```
php bin/console doctrine:migrations:migrate 
```

如果由于某种原因失败，请尝试以下方法:

```
php bin/console doctrine:schema:update --force 
```

### 编写更多 SpecBDD 测试

现在我们已经配置好了模型和数据库，让我们回到测试中来。我们的前两个例子现在应该给出绿色的结果，因为类已经被创建了。我们来确认一下:

```
php bin/phpspec run -fpretty --verbose src/AppBundle/Entity/spec/ProductVariantSpec.php 
```

```
<?php

// src/AppBundle/Entity/spec/ProductVariantSpec.php

namespace spec\AppBundle\Entity;

use AppBundle\Entity\ProductVariant;
use PhpSpec\ObjectBehavior;
use Prophecy\Argument;

class ProductVariantSpec extends ObjectBehavior
{
    function it_is_initializable()
    {
        $this->shouldHaveType(ProductVariant::class);
    }
} 
```

`$this`表示一个特殊的对象，其行为与我们的`ProductVariant`类完全一样。然而，它只有我们在例子中指定的属性和方法。让我们向 spec 类添加一个方法。

```
function it_has_reorder_level_by_default()
{
   $this->getReorderLevel()->shouldReturn(ProductVariant::REORDER_LEVEL);
} 
```

我们希望`ProductVariant`的再订购级别值默认为 5。记住我们的`ProductVariant`类还是空的。让我们试着运行规范。测试很顺利地失败了，并给出了一个提示:

```
Do you want me to create
  `AppBundle\Entity\ProductVariant::getReorderLevel()` for you? 
```

![Running Phpspec and getting confirmation for auto-creating a method](img/b4de9186354ea3ccdad26df8df222097.png)

如果我们确认，`getReorderLevel()`方法在类中被剔除。Phpspec 期望找到返回一个整数的`getReorderLevel()`,表示库存阈值，低于该阈值时应该补货。在该方法中，我们所要做的就是返回`$reorderLevel`属性。

通常，`return $this->reorderLevel;`就足够了，但是我们的商店里已经有产品了，所以我们需要找到一种方法来获得默认的再订购水平值。一种方法是:

```
/**
 * @return int
 */
public function getReorderLevel()
{
    return !empty($this->reorderLevel) ? $this->reorderLevel : self::REORDER_LEVEL;
} 
```

测试通过。我们可以用`public function getReorderLevel();`更新`ProductVariantInterface`。接下来，我们要确保我们可以设置再订购级别。下面是一个例子:

```
function it_has_reorder_level()
{
    $this->setReorderLevel(10);
    $this->getReorderLevel()->shouldReturn(10);
} 
```

当我们运行它时，Phpspec 将知道没有`setReorderLevel()`方法，并提供创建它。按如下方式更新创建的方法:

```
/**
 * @param int $reorderLevel
 */
public function setReorderLevel($reorderLevel)
{
    $this->reorderLevel = $reorderLevel;
} 
```

我们运行它，它是绿色的。别忘了更新界面。现在，我们要检查一个项目是否可以重新订购，也就是说，它是否应该被标记为足够低，以便进行补充。必须对其进行跟踪，并且现有数量必须低于或等于再订购水平:

```
function it_is_reorderable_when_tracked_and_available_stock_is_at_reorder_level()
{
    $this->setTracked(true);
    $this->setOnHand(3);
    $this->setReorderLevel(4);
    $this->isReorderable()->shouldReturn(true);
} 
```

当我们运行规范时，它会找到除了在`ProductVariant`类中为我们创建的`isReorderable()`之外的所有方法。

![Another offer to create a method](img/b5d751b2551ae2e2beae7229c1ffce6d.png)

在该方法中，我们需要检查变体是否可跟踪，以及现有数量是否小于或等于再订购水平。

```
public function isReorderable()
{
  return $this->isTracked() && ($this->getOnHand() <= $this->getReorderLevel());
} 
```

所有测试都通过了。我们用`public function isReorderable();`更新一下界面吧。您可能还想添加一个测试，看看可用库存何时超过再订购水平:它不应该是可再订购的。

```
function it_is_not_reorderable_when_tracked_and_available_stock_is_greater_than_reorder_level()
{
    $this->setTracked(true);
    $this->setOnHand(10);
    $this->setReorderLevel(4);
    $this->isReorderable()->shouldReturn(false);
} 
```

现在运行规范应该会得到类似这样的结果:

![Five passing tests](img/e26b4eae70483d8a77c3c64105627ca1.png)

我们完成了`ProductVariant`。我们将对产品中的变体做类似的事情，但方式略有不同。我们写的每个例子都需要一个产品，为每个例子创建一个至少有两个变体的产品是很乏味的。

在 Phpspec 中，我们可以编写一个在每个示例之前被调用的`let()`方法和运行之后被调用的`letGo()`方法。在`src/AppBundle/Entity/spec/ProductSpec.php`中，我们将添加以下内容，同时记住在导入`ProductVariantInterface`时将`use AppBundle\Entity\ProductVariantInterface as VariantInterface;`放在类的顶部。

```
function let(VariantInterface $firstVariant, VariantInterface $secondVariant)
{
    $firstVariant->setOnHand(4);
    $firstVariant->getOnHand()->willReturn(4);
    $firstVariant->setReorderLevel(4);
    $firstVariant->getReorderLevel()->willReturn(4);
    $firstVariant->isTracked()->willReturn(true);
    $firstVariant->isReorderable()->willReturn(true);
    $firstVariant->setProduct($this)->shouldBeCalled();
    $this->addVariant($firstVariant);

    $secondVariant->setOnHand(10);
    $secondVariant->getOnHand()->willReturn(10);
    $secondVariant->setReorderLevel(3);
    $secondVariant->getReorderLevel()->willReturn(3);
    $secondVariant->isTracked()->willReturn(true);
    $secondVariant->isReorderable()->willReturn(false);
    $secondVariant->setProduct($this)->shouldBeCalled();
    $this->addVariant($secondVariant);
} 
```

现在，我们可以继续编写示例，并确保我们的测试每一步都是绿色的:

```
php bin/phpspec run -fpretty --verbose src/AppBundle/Entity/spec/ProductSpec.php 
```

我们要确保的第一件事是产品的现有量是产品中现有变量的总和。在`ProductSpec.php`中，我们加上这个例子:

```
function it_gets_products_on_hand_as_sum_of_product_variants_on_hand()
{
    $this->getVariants()->shouldHaveCount(2);
    $this->getOnHand()->shouldReturn(14);
} 
```

当然，当你运行它时，`Product`类没有一个`getOnHand()`方法，所以我们要求 Phpspec 在我们实现它之前为我们创建它。

```
public function getOnHand()
{
    $onHand = 0;
    foreach ($this->getVariants() as $variant) {
        if ($variant->isTracked()) {
            $onHand += $variant->getOnHand();
        }
    }

    return $this->onHand = $onHand;
} 
```

我们不能忘记在`ProductInterface`上也加上`public function getOnHand();`。

最后，当产品的至少一个变型是可再订购的时，该产品是可再订购的。示例如下:

```
function it_recognizes_when_to_reorder_stock() {
    $this->isReorderable()->shouldReturn(true);
} 
```

同样，我们让 Phpsepc 创建了`isReorderable()`方法。让我们用`public function isReorderable();`来更新`ProductInterface`。完整的实现应该是:

```
public function isReorderable() {
    $variants = $this->getVariants();
    $trackedVariants = $variants->filter(function (ProductVariantInterface $variant) {
        return $variant->isReorderable() === true;
    });
    return $this->reorderable = count($trackedVariants) > 0;
} 
```

我们还应该能够通过类似的逻辑来检查产品是否被跟踪——至少它的一个变体必须是可跟踪的。Phpspec 的例子很简单，因为我们得到的两个变量都是可跟踪的。

```
function it_recognizes_product_as_tracked_if_at_least_one_variant_is_tracked() {
    $this->isTracked()->shouldReturn(true);
} 
```

我们现在将运行规范，并用以下代码更新生成的`isTracked()`方法:

```
public function isTracked()
{
    $variants = $this->getVariants();
    $trackedVariants = $variants->filter(
        function (ProductVariantInterface $variant) {
            return $variant->isTracked() === true;
        }
    );

    return $this->tracked = count($trackedVariants) > 0;
} 
```

一切都好。也许我们应该添加一个例子，确保如果没有跟踪到变量，`isTracked()`将返回`false`。我们的`let()`方法中的两种变体都已经被跟踪了。然而，如果我们像在`let()`方法中那样传入两个具有匹配名称的变量，我们可以覆盖新示例中对象的行为。

```
function it_recognizes_product_as_not_tracked_if_no_variant_is_tracked(
  VariantInterface $firstVariant,
  VariantInterface $secondVariant
) {
    $firstVariant->isTracked()->willReturn(false);
    $secondVariant->isTracked()->willReturn(false);
    $this->isTracked()->shouldReturn(false);
} 
```

我们也可以和布尔人一起玩。例如，如果我们将`$this->isTracked()->shouldReturn(false);`更改为`$this->isTracked()->shouldReturn(true);`，我们会得到如下消息:

```
AppBundle/Entity/Product
    recognizes product as not tracked if no variant is tracked
        expected true, but got false. 
```

让我们把它改回`false`，所以我们从所有的绿色继续。这就结束了我们对`Product`和`ProductVariant`类的测试。在稍后的`Product`中，我们需要一些东西，比如一个`getOnHold()`方法，它检查一个相似方法的变体。我们应该以如下的类结束:

```
<?php

// src/AppBundle/Entity/Product.php

namespace AppBundle\Entity;

use Sylius\Component\Core\Model\Product as BaseProduct;
use AppBundle\Entity\ProductVariantInterface as ProductVariantInterface;

class Product extends BaseProduct implements ProductInterface
{
    /**
     * @var int
     */
    protected $onHold = 0;

    /**
     * @var int
     */
    protected $onHand = 0;

    /**
     * @var bool
     */
    protected $tracked = false;

    /**
     * @var bool
     */
    protected $reorderable = false;

    public function isTracked()
    {
        $variants = $this->getVariants();
        $trackedVariants = $variants->filter(
            function (ProductVariantInterface $variant) {
                return $variant->isTracked() === true;
            }
        );

        return $this->tracked = count($trackedVariants) > 0;
    }

    public function getOnHand()
    {
        $onHand = 0;
        foreach ($this->getVariants() as $variant) {
            if ($variant->isTracked() === true) {
                $onHand += $variant->getOnHand();
            }
        }

        return $this->onHand = $onHand;
    }

    public function getOnHold()
    {
        $onHold = 0;
        foreach ($this->getVariants() as $variant) {
            if ($variant->isTracked()) {
                $onHold += $variant->getOnHold();
            }
        }

        return $this->onHold = $onHold;
    }

    public function isReorderable()
    {
        $variants = $this->getVariants();
        $trackedVariants = $variants->filter(
            function (ProductVariantInterface $variant) {
                return $variant->isReorderable() === true;
            }
        );

        return $this->reorderable = count($trackedVariants) > 0;
    }
} 
```

## 结论

我们现在已经学会了如何以正确的方式扩展 Sylius **的核心功能——在开发过程中进行彻底的测试。我们添加了一些新特性，允许 Phpspec 为它们生成存根，并在继续开发之前让所有测试从红色变成绿色。**

在这个“扩展 Sylius”系列的最后一部分，我们将与 Behat 一起工作，测试我们的特性所引起的变化的视觉输出。敬请期待！

## 分享这篇文章