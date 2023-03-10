# Sylius 和在 TDD 上切齿

> 原文：<https://www.sitepoint.com/sylius-and-cutting-your-teeth-on-tdd/>

*这篇文章由[克里斯多佛·皮特](https://www.sitepoint.com/author/assertchris)和[托马斯·彭特](https://www.sitepoint.com/author/tpunt)进行了同行评议。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

* * *

![Sylius logo](img/483b11ce0472dbb2536a1248896b7044.png)

Sylius 是一个基于 [Symfony](http://www.symfony.com) 的电子商务应用/框架。它号称 [100%的代码覆盖率](http://lakion.com/blog/testing-and-bdd-in-symfony-sylius-interview-by-fernando-arconada)，这对于那种规模的 PHP 应用程序来说是令人印象深刻的。在本文中，我们将浏览不同种类的可用测试，并尝试一些测试和行为驱动开发(TDD/BDD)。参见 [Sylius 安装指南第](http://docs.sylius.org/en/latest/book/installation/index.html)页的说明，因为本文假设您有一个带有示例数据的工作安装，并且您可以运行 [Behat](http://behat.org/) 、 [PHPUnit](http://phpunit.de) 和 [phpspec](http://www.phpspec.net) 测试。

在 web 根目录下，有一个`src`文件夹，其中保存了所有 Sylius 相关的代码。这允许您在应用程序开发中使用`app`文件夹，而不会不必要地冒犯 Sylius。由于我们对测试驱动开发感兴趣(首先，在编写代码之前编写失败的测试),所以让我们以 Sylius 的方式深入研究一下。

我们从建立测试数据库开始。

```
php bin/console doctrine:database:create --env=test
php bin/console doctrine:schema:create --env=test 
```

## Sylius 测试的类型

下面这些工具的一些基础知识已经在本帖的[中介绍过了，但是我们将在 Sylius 示例中重述它们，以保持主题一致。](https://www.sitepoint.com/bdd-in-laravel-getting-started-with-behat-and-phpspec/)

### PHPUnit

Sylius 附带了许多 [PHPUnit](https://phpunit.de/) 功能测试。配置文件`phpunit.xml.dist`位于 web 根目录中，单元测试位于`tests`文件夹中。从我们应用程序的根开始，让我们在`tests/Controller/CountryApiTest.php`运行测试:

```
./vendor/phpunit/phpunit/phpunit -c ./phpunit.xml.dist tests/Controller/CountryApiTest 
```

![PHPUnit in action](img/79756ebf9075db67eaadeece3eb236a8.png)

该命令由 3 部分组成 PHPUnit 的路径、我们的配置文件和单元测试类。打开`tests/Controller/CountryApiTest.php`看一看班级。它扩展了 JsonApiTestCase，我们可以追溯到 ApiTestCase 和 WebTestCase，这是 Symfony 框架包的一部分。

### Phpspec

行为驱动开发(BDD)源于测试驱动开发(TDD)，关注用户如何与应用程序交互以及应用程序如何运行。SpecBDD 是 BDD 的一部分，它考虑的是应用程序的各个小部分如何协同工作来完成任务。

Sylius 安装有`phpspec`，这是完成此操作所需的工具。在您的根目录中，还有一个`phpspec.yml.dist`配置文件。规范是用 PHP 类编写的，可能被分组到套件中。

记住，Sylius 是一个很大的应用程序，所以有很多文件。打开`src/Sylius/Component/Order/spec/Model/OrderItemSpec.php`。

首先要注意的是，无论文件夹结构有多深，你都可以在`spec`文件夹中找到规范，并且测试应用的源代码很容易找到。如果你看一下`spec`文件夹的层次，你会看到`Model`，在它里面是一个`OrderItem`类。那个班的规格是`spec/Model/OrderItemSpec.php`。比较一下这些功能，你就可以看出它们是如何联系在一起的。

当运行 phpspec 时，使用`--verbose`选项可以得到更多的输出，使用`-fpretty`可以得到更漂亮的输出。

```
./bin/phpspec run -fpretty --verbose src/Sylius/Component/Order/spec/Model/OrderItemSpec.php 
```

![PHPSpec in action in Sylius](img/dc4409ffcc53bfbb17fccd976ef03352.png)

### 贝哈特

StoryBDD 是 BDD 硬币的第二面，它以叙述的方式呈现行为，将注意力集中在应用程序中应该发生的更大画面上。Behat 是让 StoryBDD 成为可能的主要工具。你运行 Behat，它分析称为特性的特殊文件，并检查你的网站的行为是否与这些文件中的描述相匹配。需要其他工具来模拟或仿真浏览器的功能。 [Mink](http://mink.behat.org/) 是 Behat 为此需要的库，而类似 Laravel 的东西将使用[优秀的黄昏](https://www.sitepoint.com/laravel-dusk-intuitive-and-easy-browser-testing-for-all/)。

你写的行为描述是用一种叫做[小黄瓜](http://docs.behat.org/en/v2.5/guides/1.gherkin.html)的格式。每个行为被称为一个*场景*，一个特征文件可以有多个场景。文件的扩展名必须是`feature`。

Sylius 带有一个`behat.yml.dist`配置文件和一个`features`文件夹，文件夹中有组织特征文件的子文件夹。配置文件和功能文件夹必须在同一级别。打开`features/order/managing_orders/browsing_orders.feature`看看结构。

让我们来测试一下这个特性:

```
./bin/behat features/order/managing_orders/browsing_orders.feature 
```

![Feature being tested](img/06f4100f77328f15c23eedb80d67e6e2.png)

参见 Laravel:Behat 和 PhpSpec 入门中的 [BDD，获得关于这个主题的另一篇精彩文章。](https://www.sitepoint.com/bdd-in-laravel-getting-started-with-behat-and-phpspec/)

## 让我们做 TDD

我们将以测试驱动的方式修改订单列表页面。登录管理部分，`admin/login`。左上角的图标看起来很完美，但是让我们试着把它换成别的。Sylius 使用[语义 UI](http://semantic-ui.com) 作为其前端。看一下[图标集](http://semantic-ui.com/elements/icon.html)。假设我们想用“购物篮”图标替换“商店”。查看订单列表页面的源代码，我们想从`<i class="circular shop icon"></i>`转到`<i class="circular shopping basket icon"></i>`。

### 步骤 1:添加功能

我们不是从一张白纸开始，所以我们走西利乌斯的路。与订单相关的特性在`features\order\managing_orders`中，所以我们在那里创建一个文件，将其命名为`browsing_orders_with_visual_display.feature`，并添加:

```
@viewing_page_icon
Feature: Browsing orders page with icon
    In order to identify orders page
    As an Administrator
    I want to be able to see an icon on the orders page 
    Background:
        Given the store operates on a single channel in "United States"
        And the store has a product "PHP T-Shirt"
        And the store ships everywhere for free
        And the store allows paying with "Cash on Delivery"
        And there is a customer "john.doe@gmail.com" that placed an order "#00000022"
        And the customer bought a single "PHP T-Shirt"
        And the customer chose "Free" shipping method to "United States" with "Cash on Delivery" payment
        And I am logged in as an administrator

    @ui
    Scenario: Seeing an icon on the page
        When I browse orders
        Then the "shopping basket" icon should be visible on the page 
```

`Background`为测试做准备。要做到这一点，它需要包括渠道、产品、送货方式、客户详细信息以及希望查看页面的用户。幸运的是西利乌斯已经为我们做了基础工作。

### 步骤 2:添加页面对象

Sylius 允许我们在一个`etc\behat\services\pages.xml`文件中创建在 Behat 容器中定义的新页面对象。我们将在`src\Sylius\Behat\Page\Admin\Order\`中创建一个接口和实现。

```
<container xmlns="http://symfony.com/schema/dic/services" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://symfony.com/schema/dic/services http://symfony.com/schema/dic/services/services-1.0.xsd">
    <parameters>
        <parameter key="sylius.behat.page.admin.order.order_page.class">Sylius\Behat\Page\Admin\Order\OrderPage
        </parameter>
    </parameters>

    <services>
        <service id="sylius.behat.page.admin.order.order_page" class="%sylius.behat.page.admin.order.order_page.class%"
                 parent="sylius.behat.page.admin.order.index" public="false">
        </service>

    </services>
</container> 
```

我们正在通知行为[服务容器](http://symfony.com/doc/current/service_container.html)关于我们的`OrderPage`类。让我们创建`src/Sylius/Behat/Page/Admin/Order/OrderPageInterface.php`:

```
<?php

// src/Sylius/Behat/Page/Admin/Order/OrderPageInterface.php

namespace Sylius\Behat\Page\Admin\Order;

use Sylius\Behat\Page\SymfonyPageInterface;
use Sylius\Behat\Page\Admin\Crud\IndexPageInterface;

interface OrderPageInterface extends IndexPageInterface
{

    /**
     * Match icons by class.
     *
     * @param string $class
     *  The class to match icons against.
     *
     * @return mixed
     *  The matched icons or false.
     */
     public function findIcons($class);

} 
```

然后，`src/Sylius/Behat/Page/Admin/Order/OrderPage.php`:

```
<?php

// src/Sylius/Behat/Page/Admin/Order/OrderPage.php

namespace Sylius\Behat\Page\Admin\Order;

use Behat\Mink\Exception\ElementNotFoundException;
use Behat\Mink\Session;
use Sylius\Behat\Page\SymfonyPage;
use Sylius\Behat\Service\Accessor\TableAccessorInterface;
use Symfony\Component\Routing\RouterInterface;
use Sylius\Behat\Page\Admin\Crud\IndexPage;

class OrderPage extends IndexPage implements OrderPageInterface
{

    /**
     * @inheritDoc
     */
    public function findIcons($class)
    {
        $foundIcons = $this->getElement('icon')->find('css', '.' . implode('.', explode(' ', $class)));
        if (!$foundIcons) {
                throw new ElementNotFoundException($this->getSession(), 'Icons with class(es) ' . $class);
            }

        return $foundIcons;
    }

    /**
     * @inheritDoc
     */
    protected function getDefinedElements()
    {
        return array_merge(parent::getDefinedElements(), [
            'icon' => '.icon',
        ]);
    }
} 
```

Sylius 定义了超过 120 个元素(类和 id)来标识页面上的不同事物，但显然没有标识图标。(搜索`src\Sylius\Behat`寻找它们)。我们需要找到`icon`类，所以我们在`getDefinedElements()`方法中定义自己的类。我们的`findIcons()`方法从我们的特性中寻找我们传入的类。当图标有不止一个名字时，比如“购物篮”，我们需要找到两个名字。

### 步骤 3:为要运行的测试添加一个上下文

上下文是也作为服务提供的类。从`src/Sylius/Behat/Resources/config/services.xml`开始，你会发现`<import resource="services/contexts.xml" />`在顶部附近。在随后的导入之后，您将到达`src/Sylius/Behat/Resources/config/services/contexts/`，在这里已经配置了一些上下文类别(`cli.xml`、`domain.xml`、`hook.xml`、`setup.xml`、`transform.xml`和`ui.xml`)。我们在找那个`ui.xml`人。打开它。找到`sylius.behat.context.ui.admin.managing_orders`服务，我们将我们的服务添加到它的正下方:

```
<service id="sylius.behat.context.ui.admin.viewing_page_icon" class="Sylius\Behat\Context\Ui\Admin\ViewingPageIconContext">
    <argument type="service" id="sylius.behat.page.admin.order.order_page" />
    <tag name="fob.context_service" />
</service> 
```

注意服务`id`和`class`之间的关系。点用反斜杠和类名的骆驼大小写代替。

创建`src\Sylius\Behat\Context\Ui\Admin\ViewingPageIconContext.php`并添加以下内容:

```
<?php

// src/Sylius/Behat/Context/Ui/Admin/ViewingPageIconContext.php

namespace Sylius\Behat\Context\Ui\Admin;

use Behat\Behat\Context\Context;
use Sylius\Behat\Page\Admin\Order\OrderPageInterface;
use Webmozart\Assert\Assert;
use Behat\Behat\Tester\Exception\PendingException;

class ViewingPageIconContext implements Context
{
    /**
     * @var OrderPageInterface
     */
    private $orderPage;

    /**
     * @param OrderPageInterface $orderPage
     */
    public function __construct(OrderPageInterface $orderPage)
    {
        $this->orderPage = $orderPage;
    }

    /**
     * @Then /^the "([^"]*)" icon should be visible on the page$/
     */
    public function theIconShouldBeVisibleOnThePage($class)
    {
        $icons = $this->orderPage->findIcons($class);
        Assert::eq(count($icons), 1, sprintf('Icon with class, %s, not found', $class));
    }
} 
```

我们将我们的`OrderPageInterface`注入到这个类中，以便当 Behat 找到我们的场景时，我们能够从注入的类中调用正确的方法。我们期望只有一个图标，我们断言它。

让我们进行测试。失败是因为还没有购物篮图标。现在，我们可以继续进行更改了。打开`src/Sylius/Bundle/AdminBundle/Resources/config/routing/order.yml`，将`icon: cart`改为`icon: shopping basket`。运行测试，它应该是绿色的。

![A passing icon test](img/d9bc31ae5eb5b48e61b565678b3efa7a.png)

接下来，我们做一些其他的改变。我们店是单渠道业务，不需要渠道栏目。其次，我们希望表格行根据州有不同的背景。为此，我们将在进行更改之前编写另一个测试。取消管理部分的一两个订单，这样我们就有不同的状态。

*   用这个更新我们的特征文件`features\order\managing_orders\browsing_orders_with_visual_display.feature`:

    ```
    @ui
    Scenario: Checking row of a cancelled order
        When I view the summary of the order "#00000022"
        And I cancel this order
        When I browse orders
        Then this order should have order payment state "Cancelled"
        And the row should be marked with the order payment state "Cancelled" 
    ```

*   我们还将更新`OrderPageInterface`和`OrderPage`:

    ```
    // src/Sylius/Behat/Page/Admin/Order/OrderPageInterface.php

    /**
         * Match table rows by class.
         *
         * @param string $class
         *  The class to match table rows against.
         *
         * @return mixed
         *  The matched rows or false.
         */
    public function findTableRows($class); 
    ```

    ```
    // src/Sylius/Behat/Page/Admin/Order/OrderPage.php

    /**
         * @inheritDoc
         */
    public function findTableRows($class)
    {
            $foundRows = $this->getElement('table')->find('css', '.' . $class);
            if (!$foundRows) {
                throw new ElementNotFoundException($this->getSession(), 'Rows with class ' . $class);
            }
            return $foundRows;
    } 
    ```

    Sylius 中已经定义了 table 元素，所以我们只使用它。我们找到传入该类的所有行。当你运行测试时，它是红色的。现在，我们可以进行必要的更改。

*   覆盖在`src/Sylius/Bundle/AdminBundle/Resources/config/grids/order.yml`中定义的`sylius_admin_order`网格中的“状态”和“通道”列。

    *   创建`app\config\grids.yml`并添加以下内容:

    ```
     sylius_grid:
            grids:
                sylius_admin_order:
                    fields:
                        channel:
                            enabled: false
                        state:
                            enabled: false 
    ```

    *   通过在`app\config\config.yml`中导入该文件来告知 Sylius:

    ```
     - { resource: "grids.yml" } 
    ```

*   将`src\Sylius\Bundle\UiBundle\Resources\views\Macro\table.html.twig`复制到`app\Resources\SyliusUiBundle\views\Macro\table.html.twig`，在`{% macro row(grid, definition, row) %}`语句中，将`<tr class="item">`替换为`<tr class="item {{ row.state | lower }}">`。我们所要做的就是在这里为输出类添加 CSS。

*   将`src\Sylius\Bundle\AdminBundle\Resources\views\Order\Label\PaymentState\awaiting_payment.html.twig`复制到`app\Resources\SyliusAdminBundle\views\Order\Label\PaymentState\awaiting_payment.html.twig`。我们希望图标和标签是大写的。将文件更改为:

    ```
    <span class="ui olive label">
        {{ value|trans|upper }}
    </span> 
    ```

*   将`src\Sylius\Bundle\AdminBundle\Resources\views\Order\Label\PaymentState\cancelled.html.twig`复制到`app\Resources\SyliusAdminBundle\views\Order\Label\PaymentState\cancelled.html.twig`。将文件更改为:

    ```
    <span class="ui yellow label">
        {{ value|trans|upper }}
    </span> 
    ```

*   将`src\Sylius\Bundle\AdminBundle\Resources\views\Order\Label\ShippingState\cancelled.html.twig`复制到`app\Resources\SyliusAdminBundle\views\Order\Label\ShippingState\cancelled.html.twig`。我们做的和上面一样:

    ```
    <span class="ui yellow label">
        {{ value|trans|upper }}
    </span> 
    ```

*   将`src\Sylius\Bundle\AdminBundle\Resources\views\Order\Label\ShippingState\ready.html.twig`复制到`app\Resources\SyliusAdminBundle\views\Order\Label\ShippingState\ready.html.twig`。我们做的和上面一样:

    ```
    <span class="ui blue label">
        {{ value|trans|upper }}
    </span> 
    ```

*   将`src\Sylius\Bundle\UiBundle\Resources\views\_stylesheets.html.twig`复制到`app\Resources\SyliusUiBundle\views\_stylesheets.html.twig`。在这里，我们将自定义 CSS 文件添加到默认文件的下面:`<link rel="stylesheet" href="{{ asset('assets/admin/css/style.css') }}">`。有其他的方法可以做到这一点，但是这是保持我们的变更隔离的最快的方法。

*   添加`assets/admin/css/style.css`我们宏伟的 CSS。

    ```
    .order-row.new {
        background-color: #c5effd;
    }

    .order-row.cancelled {
        background-color: #fbe7af;
    } 
    ```

再次运行测试。都是绿色的。

![](img/74d7fcb8fdcdd0a704384d073b00ae03.png)

刷新订单页面，但您可能还需要先清除缓存。取消订单行现在有不同的背景颜色。

最后，让我们添加一个测试来断言通道列不再存在。

1.  用这个更新我们的特征文件`features\order\managing_orders\browsing_orders_with_visual_display.feature`:

    ```
    Scenario: Not seeing a column on the page
        When I browse orders
        Then the "channel" column should not be visible on the page 
    ```

2.  用以下内容更新`OrderPageInterface.php`:

    ```
    // src/Sylius/Behat/Page/Admin/Order/OrderPageInterface.php

    /**
     * Get table headers.
     *
     * @return mixed
     *  The array of headers keys or empty array.
     */
    public function findTableHeaders(); 
    ```

3.  在`OrderPage.php`中实现新方法:

    ```
    // src/Sylius/Behat/Page/Admin/Order/OrderPage.php

    /**
     * {@inheritdoc}
     */
    public function findTableHeaders() {
        $headers = $this->getTableAccessor()
            ->getSortableHeaders($this->getElement('table'));
        return is_array($headers) ? array_keys($headers) : array();
    } 
    ```

    Sylius 附带了一个`TableAccessor`类，其中有很多在测试中处理表格的有用方法。默认的表格标题是可排序的，我们简单地调用获取页面上所有可排序的`th`元素的方法。

4.  更新我们的功能上下文`ViewingPageIconContext.php`:

    ```
     // src\Sylius\Behat\Context\Ui\Admin\ViewingPageIconContext.php

      /**
       * @Then /^the "([^"]*)" column should not be visible on the page$/
       */
      public function theColumnShouldNotBeVisibleOnThePage($column) {
        $foundHeaders = $this->orderPage->findTableHeaders();
        Assert::false(in_array(strtolower($column), $foundHeaders), sprintf('%s column not found', $column));
      } 
    ```

我们得到订单页面上的所有表头。标题元素的数组以名称为关键字，例如日期、客户、渠道、号码、支付状态、发货状态等。表示列。我们检查键以确认我们的列不再在页面上。

![A passing Behat test](img/d5d8a8ed356dd9fba3bb90f1f35fc968.png)

## 结论

这些都是旨在说明 TDD 过程的小步骤——想想你想要实现什么，写测试来检查什么时候完成，测试失败，进行必要的修改，测试通过。

我们通过编写几个 Behat 测试来演示这个过程，从而探索了 StoryBDD。尽管这些都是简单的例子，但是同样的原则也适用于更复杂的事情或者使用 SpecBDD 或 PHPUnit 测试。

测试时你采取哪种方法？只有 TDD？只有 BDD？两者都有？也许[BDD DD](https://www.sitepoint.com/testing-frenzy-can-we-bdd-test-the-units/)？让我们知道！

## 分享这篇文章