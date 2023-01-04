# StackPHP 解释道

> 原文：<https://www.sitepoint.com/stackphp-explained/>

今天我们来看看[stack HP](https://stackphp.com/)并试着理解这是怎么一回事。虽然这篇文章会有一些代码，但这篇文章将是相当理论化的，因为我们有兴趣了解 StackPHP 实际上是什么，它来自哪里以及它为什么有用。

![StackPHP Logo](img/a94c4099b976fb1bc0ba984d624480b0.png)

正如[Stack HP](https://stackphp.com/)项目的首页所说，Stack 是*一个用于编写 HttpKernelInterface 中间件*的约定。但是，为了真正理解这个定义，我们必须先了解一些概念。最后，我们还将通过一些示例代码来说明我们在 StackPHP 上下文中学习到的概念。这通常会让事情更容易理解。

## 什么是 HttpKernelInterface？

这个 [HttpKernelInterface](https://api.symfony.com/2.5/Symfony/Component/HttpKernel/HttpKernelInterface.html) 可以被定义为不止一个东西。在最基本的层面上，它是位于 [Symfony HttpKernel 组件](https://symfony.com/doc/current/components/http_kernel/introduction.html)内部的一个 PHP 接口。但不仅仅如此。它是一种现代的 PHP 模式，允许我们以面向对象的方式处理 HTTP 请求，而不是处理超全局变量和输出函数，如`echo`或`header`。

但这实际上意味着什么呢？

该接口提供了一个名为`handle()`的方法，该方法接受一个`$request`，并预期返回一个`$response`。后者基本上是 HTTP 规范的包装对象。例如，在 Symfony 中， [HttpFoundation 组件](https://symfony.com/doc/current/components/http_foundation/introduction.html)负责创建和管理这些。所以可以想象，`$request`对象包含关于当前用户请求的数据，而`$response`对象负责输出处理请求的结果。但这已经是题外话了。

该接口并不真正关心`handle()`方法内部发生了什么。这是实现它的类的责任。例如，Symfony 内核使用[事件调度器组件](https://symfony.com/doc/current/components/event_dispatcher/introduction.html)来进行管理。它调度许多事件，这些事件的侦听器处理大部分繁重的工作。这保持了事物的抽象和分离。

但是回到正题，你可以把`HttpKernelInterface`看作一个非常简单的面向对象模式，它帮助组织对传入请求的处理，目的是把它变成一个响应。

## 什么是装饰者模式？

像许多其他编程模式一样，装饰模式是一种解决特定问题的面向对象的方式。在这种情况下，问题涉及到在一个类中扩展功能，而没有实际修改或实际扩展该类。解决方案是*用额外需要的功能来装饰*。

让我们快速地看一个代码示例，因为这是理解这种模式的最简单的方法。

假设您有这个`Action`接口和类:

```
interface ActionInterface {
  public function trigger();
}

class Action implements ActionInterface {

  private function performAction() {
    return 'Action performed';
  }

  public function trigger() {
    return $this->performAction();
  }

}
```

并且您希望能够提供这个类的多个不同版本，这些版本在`trigger()`方法中包含额外的功能。您可以扩展该类，这在某些情况下是一种有效的方法。然而，如果您最终需要类似多重继承(从多个类继承)的东西，这也会产生不同的问题。PHP 5.4 引入了[特征](https://www.sitepoint.com/using-traits-in-php-5-4/)来帮助解决这些问题。

你还可以用另一个实现相同接口的类来修饰它，向它添加你的功能，然后委托回原来的那个。这里有一个非常简单的例子:

```
class NotifiedAction implements ActionInterface {

  private $action;

  public function __construct(ActionInterface $action) {
    $this->action = $action;
  }

  public function notifyUser() {
    // Logic for user notification
  }

  public function trigger() {
    $performed = $this->action->trigger();
    $this->notifyUser();
    return $performed;
  }

}
```

如你所见，我们现在有了一个`NotifiedAction`类，它实现了同一个接口，并把同一个接口作为构造函数的参数。这就像参数对象周围的装饰器，因为它包装了参数对象并从外部添加了它的功能(在这种情况下，在动作执行后发送通知)。

装饰者必须实现相同接口的原因是为了确保它与被装饰的接口具有相同的公共 API。换句话说，这些对象需要或多或少可以互换。这实际上是装饰模式的主要缺点，因为用多种方法包装接口会变得相当笨拙。但是让我们回到我们的例子。

现在，您有两个选项可以用`ActionInterface`触发动作:

```
$action = new Action();
$action->trigger();
```

和

```
$action = new NotifiedAction(new Action());
$action->trigger();
```

你可以继续创造装饰者，把他们像俄罗斯娃娃一样包在一起:

```
$action = new EmailNotifiedAction(new NotifiedAction(new Action()));
$action->trigger();
```

这是装饰模式的一个非常简单的例子。您可以拥有稍微复杂一点的结构，在其中进行一些进一步的抽象，以减少样板代码。但这对我们的目的来说已经足够了。

## 什么是 StackPHP 中间件？

现在我们已经对什么是 HttpKernelInterface 和 decorator 模式有了基本的了解，是时候看看什么是 StackPHP 中间件了。

正如我在开始提到的，StackPHP 是

> 构成 HttpKernelInterface 中间件的约定。

换句话说，StackPHP 是一个构建遵循少量规则的中间件的约定，其中之一就是它们对 HttpKernelInterface 的实现。但是在这种情况下，什么是中间件呢？

中间件可以理解为添加到运行在用户和服务器之间(请求和响应之间)的管道中的一部分功能。机架中间件是中间件的 StackPHP 定义的一个非常接近的例子。由于它提供了一种实现这种管道的合理模式，HttpKernelInterface 是包装中间件的一个很好的选择。

更具体地说，堆栈中间件是一个简单的 PHP 对象，它遵循三个简单的规则:

1.  实现 HttpKernelInterface
2.  修饰实现此接口的另一个对象
3.  向`handle()`方法添加功能，并委托给原始对象

因此，需要记住的重要一点是，StackPHP 不是一个框架或库。它甚至没有任何代码*本身*(除了几个**可选** [工具](https://stackphp.com/toolbox/)帮你实现约定)。

相反，StackPHP 项目的目标是促进一种框架不可知的方式来构建 HTTP 级别的代码。它认为，与框架特定的或集成的代码相反，HTTP 级代码可以堆叠到现有的应用程序中，这也使它更加可共享和普遍兼容。目前，有许多 PHP 开发框架使用 Symfony HttpKernel 组件，并且 Stack 已经在使用了。Symfony、Silex、Laravel 5 和 Drupal 8 只是几个例子，希望这个数字在未来会增加。

## 堆栈中间件看起来像什么？

如果我们应用我们在前两节中学到的知识，我们现在应该完全理解如何构建一个堆栈中间件。我们需要编写一个类来修饰一个 HttpKernelInterface 对象，并在委托给被修饰的对象之前或之后添加我们自己的功能。因此，让我们创建一个死简单堆栈中间件，它包装一个 Symfony 应用程序，并在实际向用户返回响应之前执行一个自定义操作。

基本上，这是 Symfony 应用程序的前端控制器:

```
$kernel = new AppKernel('prod', false);
$request = Request::createFromGlobals();
$response = $kernel->handle($request);
$response->send();
$kernel->terminate($request, $response);
```

`AppKernel`实现了 HTTPKernelInterface，因此有一个`handle()`方法，我们将一个`Request`对象传递给它，并期望从它那里得到一个`Response`对象。甚至不用关心这个方法在内部做什么，我们可以用我们自己的中间件来修饰它，并添加新的功能:

```
class CustomAppKernel implements HttpKernelInterface {

  /**
   * @var \Symfony\Component\HttpKernel\HttpKernelInterface
   */
  private $kernel;

  public function __construct(HttpKernelInterface $kernel) {
    $this->kernel = $kernel;
  }

  /**
   * {@inheritdoc}
   */
  public function handle(Request $request, $type = HttpKernelInterface::MASTER_REQUEST, $catch = TRUE) {
    // Perform whatever task we want before handling the request

    // Delegate to the decorated kernel to handle the request as it normally would
    $response = $this->kernel->handle($request, $type, $catch);

    // Perform some more tasks after the response has been created

    // Return the Response
    return $response;
  }
}
```

在`CustomAppKernel`中，你可以看到装饰模式在起作用。我们包装一个 HttpKernelInterface 对象，执行一些逻辑，然后委托给修饰对象。我们的 Symfony 应用前端控制器现在看起来像这样:

```
$kernel = new CustomAppKernel(new AppKernel('prod', false));
$request = Request::createFromGlobals();
$response = $kernel->handle($request);
$response->send();
```

仅此而已。我们已经创建了一个堆栈中间件。如果你想看更多的例子，请随意查看 StackPHP 网站上的中间件页面。

您将注意到的一件事是遗漏了对`terminate()`方法的调用。如果我们也想这样做，我们的中间件也需要实现`TerminableInterface`。或者，我们可以使用 [StackBuilder](https://github.com/stackphp/builder) 来帮助我们将中间件堆叠在一起，这也为实现这个接口的内核提供了支持。

## 堆栈生成器

StackBuilder 是一个小型的库，可以帮助你构建一个中间件树。通过提供一个简单的 API *向导*，它基本上消除了手工实例化和修饰中间件的痛苦。那么它是如何工作的呢？

让我们回到上面的例子:

```
$kernel = new CustomAppKernel(new AppKernel('prod', false));
$request = Request::createFromGlobals();
$response = $kernel->handle($request);
$response->send();
```

有了 StackBuilder，我们可以这样做:

```
$kernel = new AppKernel('prod', false);
$stack = (new Stack\Builder())
    ->push('CustomAppKernel');

$kernel = $stack->resolve($kernel);
```

使用`StackBuilder`对象上的`push()`方法，我们可以将中间件添加到堆栈中。在这个例子中，我们用`CustomAppKernel`中间件包装我们的`AppKernel`。当您有多个想要堆叠在一起的中间件时，这真的变得很有价值。你所要做的就是继续链接`push()`方法:

```
$stack = (new Stack\Builder())
    ->push('CustomAppKernel')
    ->push('AnotherAppKernel')
    ->push('YetAotherAppKernel);
```

所有这些都将一个接一个地包装原始的 HttpKernelInterface 对象。而`resolve()`方法处理实际的堆叠。所以现在当你在堆栈内核上调用`handle()`方法时，你基本上是在调用`CustomAppKernel`的`handle()`方法，它包装了`AnotherAppKernel`，后者包装了`YetAnotherAppKernel`，后者包装了原始的`AppKernel`。

此外，`resolve()`返回的堆栈内核也实现了`TerminableInterface`，因此有了可用的`terminate()`方法。所以如果我们调用它，它会委托给栈中每个实现这个接口的中间件的`terminate()`方法。

## 结论

在本文中，我们研究了 StackPHP，并试图理解它的全部内容。我们首先讨论了 HttpKernelInterface，它是堆栈约定试图实现的核心。我们还解释了装饰模式，以便更好地理解如何在实践中遵循这些约定。然后，我们创建了一个小的中间件来观察这一切。最后，我们看了一下 StackBuilder，它是 Stack 创建者提供的实用工具，使堆叠中间件的过程变得轻而易举。

你有没有构建自己的 StackPHP 中间件？请在评论中告诉我们。

u = swader