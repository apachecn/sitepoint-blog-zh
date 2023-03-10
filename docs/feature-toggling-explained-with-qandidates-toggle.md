# 用 Qandidate 的切换来解释功能切换

> 原文：<https://www.sitepoint.com/feature-toggling-explained-with-qandidates-toggle/>

版本控制系统中经常使用的开发工作流是特性分支。这个想法是，我们在主功能之外的分支中开发新功能。在一个特性被测试并准备好发布之后，它被合并回主分支或者发布分支以进行部署。这种方法有助于我们开发新功能，而不会干扰主要代码库。

然而，开发一个特性分支可能比正常的发布周期要长得多。这将使得合并分支更加困难，因为我们将不得不处理可能的合并、逻辑或者依赖冲突。

![Feature toggling](img/a427b737d61f377bc1e1ace07954698f.png)

## 功能切换

广泛用作特征分支替代的技术之一是**特征切换**。特征切换(或特征翻转)的作用类似于开/关开关。他们让我们在主分支上继续开发，同时不向公众公开部分开发的和有风险的特性。我们可以确保我们的功能与应用程序中的现有功能完全兼容。

我们可以暂时隐藏一个部分构建的或者有风险的特性(发布切换)，或者将已经完成的稳定特性限制到某个用户组(业务切换)。这两种类型的切换以相同的方式实现，但目的不同。有了发布切换，我们对用户隐藏了未完成的特性，除了开发和 QA 团队。当特征变得稳定时，这些切换将会退出。它们通常在代码级别进行管理。

通过使用 business toggles，我们可以使某个功能对某个用户群可用，或者我们可以在某些情况下完全禁用它——想想网上商店销售，你网站上的圣诞节主题，等等。他们通常需要一个类似仪表板的界面来查看现有开关的状态，并能够关闭和打开它们。

许多大型网站都使用功能切换，包括 Flickr、脸书、Disqus、Etsy、Reddit、Gmail 和网飞。

[Martin Fowler](http://martinfowler.com/bliki/FeatureToggle.html) 写了一篇关于特性切换的好文章，涵盖了优缺点以及如何正确使用它们。

在本教程中，我们将学习如何使用由 [Quandidate 实验室](http://labs.qandidate.com/blog/2014/08/18/a-new-feature-toggling-library-for-php/)开发的 PHP 库 [Toggle](https://github.com/qandidate-labs/qandidate-toggle) 创建特性切换。

## Qandidate 切换

基本思想是在运行时基于一些条件激活和停用一个特性。例如，我们可以为一周前在我们的系统中注册了帐户的用户激活一个功能。

Toggle 由几个组件组成。

### 切换管理器

顾名思义，切换管理器负责管理特征切换，包括存储切换、检索切换、检查切换状态等。

切换管理器可以用两种不同的方式存储特征切换。一种方法是内存中的集合，这是一个包装了一组开关的类。这个类公开了一些在集合中存储和检索切换的方法。第二种存储方法使用 Redis 来存储和检索触发器。

存储类在实例化时作为依赖项注入到切换管理器中。在本教程中，我们将使用内存中的集合来存储切换。

```
<?php
// ...
$manager = new ToggleManager(new InMemoryCollection()); 
```

### 触发器

在 Toggle 中，每个特征开关都是一个带有名称**和一组条件的对象。我们根据这些条件评估运行时值，以决定是否应该启用或禁用特性切换。**

```
<?php

// ...

$conditions = [$condition1, $condition2];
$toggle = new Toggle('newFeature', $conditions); 
```

### 经营者

运算符是切换条件的构造块。如果我们将它们与编程语言中的操作符进行比较，它们就像是`if`语句中的逻辑表达式。开箱即用支持以下运算符:

*   大于
*   更大的同等
*   不到
*   小于等于
*   插入物
*   百分率

要创建操作员，请执行以下操作:

```
<?php
// ...
$operator = new LessThan(1000) 
```

在前面的代码中，运算符应用于小于`1000`的值。运营商自己什么都不做。它们应该与条件对象一起使用。

这个想法现在可能变得有点混乱，但是当我们在下面的部分中把它放在一起时，它就变得很清楚了。

### 情况

如前所述，每个触发器必须满足**一个**或**多个**条件，才能被激活。什么是切换中的条件？

条件是一个对象，它接受一个任意的**键**和一个**操作符**对象:

```
<?php

// ...

$operator = new LessThan(100);
$conditions = [];

// This condition is met when user_id is less or equal than 100
$conditions[] = new OperatorCondition('user_id', $operator);

$toggle = new Toggle('newFeature', $conditions); 
```

### 语境

运算符、条件和切换对象创建在运行时激活功能切换的指令。所以基本上什么都不会发生，直到我们根据条件检查一些值。

`Context`是一个允许我们为切换条件赋值的对象。根据我们的用例，这些值可以是从 ID 号到任何值。我们使用`set()`方法向`Context`对象添加值:

```
<?php

// ...

$operator = new LessThan(100);
$conditions = [];
$conditions[] = new OperatorCondition('user_id', $operator);

$toggle = new Toggle('newFeature', $conditions);

$context = new Context();
$context->set('user_id', 67); 
```

在上面的例子中,`user_id`指的是我们的条件键。

现在我们有了条件和运行时值，我们可以使用管理器的`active()`方法来检查切换的状态:

```
<?php
$manager = new ToggleManager(new InMemoryCollection());

$operator = new LessThan(100);

$conditions = [];
$conditions[] = new OperatorCondition('user_id', $operator);

$toggle = new Toggle('newFeature', $conditions);

$context = new Context();
$context->set('user_id', 100);

$manager->add($toggle);

if ($manager->active('newFeature', $context)) {
    echo 'The newFeature toggle is enabled';
} 
```

`active`接受两个参数。第一个参数是我们想要检查其状态的开关的名称。第二个参数是上下文对象。`active()`检查集合中是否存在这样的切换。因此，它根据相应的条件计算上下文值，并返回一个布尔值。

请注意，在评估之前，我们需要使用`add()`方法将切换添加到管理器中。

不使用管理器的`active()`方法，我们可以直接使用 toggle 对象本身的`activeFor()`:

```
<?php

// ...

// Conditions here

$toggle = new Toggle('newFeature', $conditions);
if ($toggle->activeFor($context)) {
    echo 'The toggle is active';
    // Do something special here.
}
// ... 
```

`activeFor()`接受一个`Context`对象作为参数。

## 动作切换

我们将使用 composer 来安装 Toggle 及其依赖项。为此，首先我们在 web 根目录中创建一个目录，并在其中运行以下命令:

```
composer require qandidate/toggle 
```

现在，让我们创建一个文件，并将其命名为`ToggleConfig.php`。我们将把所有的切换定义保存在这个文件中。最后，我们将评估切换的状态，并将其作为数组返回。

对于此示例，让我们创建一个在晚上 8 点前启用的功能切换:

```
<?php
//ToggleConfig.php

use Qandidate\Toggle\Context;
use Qandidate\Toggle\Operator\LessThan;
use Qandidate\Toggle\OperatorCondition;
use Qandidate\Toggle\Toggle;
use Qandidate\Toggle\ToggleCollection\InMemoryCollection;
use Qandidate\Toggle\ToggleManager;

$manager = new ToggleManager(new InMemoryCollection());

//-------------------------------------------------
//   Toggle for featureOne
//-------------------------------------------------

$operator  = new LessThan(20); // < 20

$conditions = [];
$conditions[] = new OperatorCondition('time', $operator); // time < 20

$toggle = new Toggle('featureOne', $conditions);

// Adding the toggle to the collection
$manager->add($toggle);

$context = new Context();
$context->set('time', (int) date('G'));

//----------------------------------------------------------

// Return the status array

return array(   
    'featureOne' => $manager->active('featureOne', $context),
); 
```

在前面的代码中，我们首先将所有必需的类导入到脚本中。我们实例化了`ToggleManager`并注入了`InMemoryCollection`作为存储介质。然后我们添加了一个带有一个条件的开关。当当前小时数小于 20(晚上 8 点)时，满足条件。

最后，我们评估了切换状态，并将其作为数组返回。

要使用特性切换，让我们创建另一个名为`index.php`的文件:

```
<?php

require_once 'vendor/autoload.php';

$toggles = require 'ToggleConfig.php';

if ($toggles['featureOne']) {
    echo 'The toggle is active';
    // Do something special here.
} 
```

我们将`ToggleConfig.php`的输出(这是一个 toggle 状态数组)赋给了`$toggles`变量。

## 对框架使用切换

当我们在野外部署一个部分开发的特性时，我们必须在 UI 组件中隐藏对它的访问，以及通向所述特性的功能的所有入口点。

例如，我们将在 Laravel 项目中使用切换特性来保护一些 UI 组件和一组 URL。然而，这个想法也适用于其他框架。

启动一个 [Laravel 框架项目](http://laravel.com/docs/5.1)，然后继续。

首先，让我们安装 Toggle。在我们的 Laravel 项目的根目录中，我们运行:

```
composer require qandidate/toggle 
```

### 创建特征切换

特性切换可以在任何地方定义，只要它们是在我们的应用程序引导时加载的。一个好的方法是在一个中间件类中定义所有这些。

为了创建中间件，在终端中，我们将目录切换到我们的 Laravel 安装目录，并运行以下命令:

```
php artisan make:middleware TogglesMiddleware 
```

因此，名为`ToggleMiddleware.php`的中间件类将在我们的`app/Http/Middleware`目录中创建。我们需要定义这个类的`handle()`方法中的所有开关。

在这个例子中，让我们创建一个特性切换来授予某个用户组访问权限。例如，ID 号在 100 以下的用户——早期用户。

为了在我们的应用程序中使用切换状态，我们将使用 Laravel 的`Config`服务。

```
<?php

namespace App\Http\Middleware;

use Closure;
use Config;
use Qandidate\Toggle\Context;
use Qandidate\Toggle\Operator\LessThan;
use Qandidate\Toggle\OperatorCondition;
use Qandidate\Toggle\Toggle;
use Qandidate\Toggle\ToggleCollection\InMemoryCollection;
use Qandidate\ToggleManager;

class TogglesMiddleware
{
    /**
     * Handle an incoming request.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Closure  $next
     * @return mixed
     */
    public function handle($request, Closure $next)
    {

       /*
        * Toggle for NewFeature
        */
        $manager = new ToggleManager(new InMemoryCollection())

        $operator  = new LessThan(100); // < 100
        $condition = new OperatorCondition('uid', $operator);  // uid < 100

        $toggle = new Toggle('newFeature', [$condition]);

        $context = new Context();

        // Here we use the User model to get user information
        $context->set('uid', User::get()->getId());   

        // Storing the statuses in the Config service
        Config::set('toggles.newFeature', $manager->active($toggle, $context));

        return $next($request);
    }
} 
```

下一步，我们需要注册中间件。由于我们希望在对应用程序的每个 HTTP 请求期间运行中间件，我们将其注册为一个**全局**中间件。为此，我们将中间件的类名添加到我们的`app/Http/Kernel.php`类的`$middleware`属性中:

```
<?php
/**
     * The application's global HTTP middleware stack.
     *
     * @var array
     */
    protected $middleware = [

        //...
        'App\Http\Middleware\TogglesMiddleware',
    ]; 
```

### 隐藏 UI 组件

要隐藏与我们的功能相关的 UI 组件，我们有两个选项。我们可以从视图中访问`Config`服务并检查切换的状态，或者我们可以在控制器中执行所有的逻辑，然后向视图传递一个标志。根据最佳 MVC 实践，后一种方法似乎更合适。

所以在我们的控制器里:

```
<?php

// ...

// Our business logic here.

$showFeature = \Config::get('toggles.newFeature');
View::render('users.dashboard', array(
    'showNewFeature' => $showFeature,
));

// ... 
```

在视图内部:

```
<div class="row">
    <div class="col-md-3">Feature one</div>
    <div class="col-md-3">Feature one</div>
    @if ($showFeature)
        <div class="col-md-3">Feature one</div>
    @endif

</div> 
```

在前面的代码中，我们使用 blade 的`@if`语句来检查标志是`true`还是`false`。因此，只有当`showFeature`被设置为`true`时，最后的`<div>`元素才会被渲染。

### 保护网址

如果我们的新特性包含一个 API 或者任何我们应该对用户隐藏的控制器，我们也需要保护他们。为此，我们在中间件之前创建了一个*，但是这一次我们没有将它注册为一个全局中间件，而是将它分配给一组特定的路由。在中间件的 handle 方法中，我们检查 toggle 的状态，如果它被禁用，我们抛出一个 404 错误。*

另一种方法是，我们在控制器类的开头放置一个逻辑屏障，如果开关关闭，则抛出一个`404`错误。然而，使用这种方法，即使切换是关闭的，请求也被分派到我们的控制器。

对于这个例子，我们采用第一种方法。让我们从创建中间件开始:

```
php artisan make:middleware APIToggleMiddleware 
```

结果，在`app/Http/Middleware`目录中创建了一个`APIToggleMiddleware.php`文件。由于全局中间件在特定于路由的中间件之前运行，我们将使用我们在全局中间件中创建的开关。我们之所以没有首先在特定于路由的中间件中定义触发器，是因为我们可能在所有控制器中都需要这些触发器。

```
<?php
namespace App\Http\Middleware;

use Config;
use Closure;

class APIToggleMiddleware
{
    /**
     * Handle an incoming request.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Closure  $next
     * @return mixed
     */
    public function handle($request, Closure $next)
    {
        if (Config::get('toggles.newFeature')) {
            abort(404, 'Page Not Found!');
        }
        return $next($request);
    }
} 
```

在前面的代码中，我们使用了`Config`服务来检索`newFeature`切换的状态。如果是假的，我们简单地抛出一个`404`错误。

为了注册这个中间件，我们将中间件的类名添加到`app/Http/Kernel.php`内的`$routeMiddleware`属性中。`$routeMiddleware`是一个关联数组，存储所有特定于路由的中间件，因此，我们也需要为我们的中间件指定一个键。在我们的例子中:`api-toggle`:

```
<?php
/**
 * The application's route middleware.
 *
 * @var array
 */
 protected $routeMiddleware = [
     'auth' => \App\Http\Middleware\Authenticate::class,
     'auth.basic' => \Illuminate\Auth\Middleware\AuthenticateWithBasicAuth::class,
     'guest' => \App\Http\Middleware\RedirectIfAuthenticated::class,
     'api-toggle' => '\App\Http\MiddleWare\APIToggleMiddleware',
 ]; 
```

现在，我们可以使用我们的`app/http/routes.php`文件中的中间件作为`before`中间件:

```
<?php

Route::group('new-feature/api', ['middleware' => 'api-toggle'], function(){
     Route::get('new-feature/', function(){...});
     Route::get('new-feature/create', function(){...});
     Route::put('new-feature/{id}', function(){...});
}); 
```

这样一来，`new-feature/api`下的所有路线将只对 ID 号在 100 以下的用户开放。

## 切换策略

默认情况下，当至少满足一个条件时，功能切换处于活动状态，但这可以使用 toggle 提供的现成切换策略进行调整。

为此，我们需要在创建切换对象时指定所需的策略:

```
<?php

// ...
$toggle = new Toggle('newFeature', $conditions, Toggle::STRATEGY_AFFIRMATIVE);
// ... 
```

### 肯定的

这是 Toggle 用于检查切换条件的默认策略。在这种情况下，必须满足至少一个条件才能激活切换。

```
<?php

// ...

$manager = new ToggleManager(new InMemoryCollection());

$conditions   = [];
$conditions[] = new OperatorCondition('user_id', new LessThan(100));
$conditions[] = new OperatorCondition('age',     new GreaterThan(21));

$toggle = new Toggle('newFeature', $conditions, Toggle::STRATEGY_AFFIRMATIVE);

$manager->add($toggle);

$context = new Context();
$context->set('user_id', 100)
        ->set('age', 20);

if ($toggle->activeFor($context)) {
    echo 'The toggle is active';
    // Do something here
} 
```

由于满足其中一个条件(`user_id`)，上述切换将被启用。

### 多数

在多数策略中，必须满足多数条件。例如，如果我们在一个触发器中有三个条件，其中至少有两个条件必须满足。

```
<?php

// ...

$manager = new ToggleManager(new InMemoryCollection());

$conditions   = [];
$conditions[] = new OperatorCondition('user_id', new LessThan(42));
$conditions[] = new OperatorCondition('age',     new GreaterThan(24));
$conditions[] = new OperatorCondition('height',  new GreaterThan(5.7));

$toggle = new Toggle('newFeature', $conditions, Toggle::STRATEGY_MAJORITY);

$manager->add($toggle);

$context = new Context();
$context->set('user_id', 41);
        ->set('age', 25);
        ->set('height', 5.6);

if ($toggle->activeFor($context)) {
    echo 'The toggle is active.'
    // Do something special here
} 
```

在前面的代码中，将启用切换，因为满足三个条件中的两个(`user_id`和`age`)。

### 全体一致的

在具有一致策略的切换中，必须满足所有条件:

```
<?php

// ...

$manager = new ToggleManager(new InMemoryCollection());

$conditions   = [];
$conditions[] = new OperatorCondition('user_id', new LessThan(42));
$conditions[] = new OperatorCondition('age',     new GreaterThan(24));
$conditions[] = new OperatorCondition('height',  new GreaterThan(5.7));

$toggle = new Toggle('newFeature', $conditions, Toggle::STRATEGY_MAJORITY);

$manager->add($toggle);

$context = new Context();
$context->set('user_id', 41);
        ->set('age', 25);
        ->set('height', 5.6);

if ($toggle->activeFor($context)) {
    echo 'The toggle is active.'
    // Do something special here
} 
```

在上面的示例中，切换将被禁用，因为根据上下文的值，第三个条件(`height`)不满足。

## 切换状态

每个功能切换可以有三种状态:活动、非活动和有条件活动。当一个切换被手动设置为激活时，无论条件如何，它将总是激活的。如果切换设置为非活动状态，它将始终处于非活动状态。第三种状态是有条件激活的，其中触发的状态将取决于条件。这是默认状态。

要手动更改切换的状态，我们使用 toggle 对象的`activate()`方法。此方法采用的参数是开关的状态，可以是下列常量之一:

*   `Toggle::CONDITIONALLY_ACTIVE`(默认状态)
*   `Toggle::ACTIVE`
*   `Toggle::INACTIVE`

```
<?php

// Conditions here

$toggle->activate(Toggle::CONDITIONALLY_ACTIVE);
$manager->add($toggle);

$context = new Context();
$context->set('user_id', 41);
        ->set('age', 25);
        ->set('height', 5.6);

if ($toggle->activeFor($context)) {
        echo 'The toggle is active.'
        // Do something special here
} 
```

## 使用数组或 YAML 创建切换

到目前为止，我们已经实例化了不同的对象(操作符、条件和开关)来创建特性开关。如果我们更喜欢配置而不是代码，我们也可以使用数组和 YAML 文件来创建特性切换。在这种方法中，我们将所有条件和操作符定义为多维关联数组或 YAML 对象。因此，我们使用`InMemoryCollectionSerializer`服务(它是切换库的一部分)来自动创建所有必要的操作符、条件和切换。

要使用数组定义切换:

```
<?php

// ...

// Array value
$data = [
    'some-feature' => [
        'name' => 'newFeatureToggle',
        'conditions' => [
            [
                'name' => 'operator-condition',
                'key'  => 'user_id',
                'operator' => ['name' => 'greater-than', 'value' => 41],
            ],
        ],
        'status' => 'conditionally-active',
    ],
]; 
```

正如我们所看到的，我们有一系列的开关。在每个数组中，我们有两个元素:`name`和`condition`。`name`是开关的名称，条件是一组条件。在`conditions`子数组中，我们需要指定条件的类别(OperatorCondition)，但采用烤肉串格式:`operator-condition`。同样的规则也适用于我们在配置数组中使用的其他类名。我们还需要定义条件键(在本例中是`user_id`)和操作符。

为了创建切换，我们使用`InMemoryCollectionSerializer`反序列化上面的数组，如下所示:

```
<?php

use Qandidate\Toggle\Context;
use Qandidate\Toggle\ToggleManager;
use Qandidate\Toggle\Serializer\InMemoryCollectionSerializer;

// $data array ( 
// ...
// );

$serializer = new InMemoryCollectionSerializer();
$collection = $serializer->deserialize($data);
$manager    = new ToggleManager($collection);

$context = new Context();
$context->set('user_id', 42);

if ($manager->active('some-feature', $context)) {
    echo 'The toggle is Active';
    // Do something special here
} 
```

Yaml 模式几乎相同，但需要 Symfony/yaml 包。更多信息，请参考[示例](https://github.com/qandidate-labs/qandidate-toggle/blob/master/examples/from-yaml.php)。

## 包扎

特性切换让我们可以直接在主分支上开发，而不必处理合并冲突。它们还帮助我们向最终用户隐藏未完成的和有风险的特性，并且我们总是可以在代码出错的情况下回滚。然而，如果我们不谨慎使用它们，从长远来看，它们会成为一场噩梦。

我们需要确定我们真的需要一个功能切换。如果我们尽可能地使用特性切换，即使它们是不需要的，最终我们会有一堆几乎不可追踪的切换分散在我们的代码库中。这将增加我们在没有注意到的情况下将有缺陷的功能暴露给公众的机会。

虽然功能切换对于一次性的可轻松删除的切换(如圣诞节主题、特别促销、临时配置文件功能等)来说非常棒，但在开发复杂的功能时，应该将它们作为第二选择。相反，我们应该看看我们是否可以在每个发布周期中开发和部署我们的升级。

同样，我们应该总是在不再需要特性切换时立即将其移除。这包括删除中间件类和定义它们的配置文件。

也就是说，Qandidate Toggle 是一个 PHP 库，它使得管理特性切换比以前容易得多。我们希望您喜欢本教程，在本教程中，我们从一个基本示例开始，并将其扩展到一个 Laravel 环境中。

你如何进行功能切换？让我们知道！如果您有任何问题或意见，请在下面留下！

## 分享这篇文章