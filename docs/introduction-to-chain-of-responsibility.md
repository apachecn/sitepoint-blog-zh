# 责任链介绍

> 原文：<https://www.sitepoint.com/introduction-to-chain-of-responsibility/>

在本文中，我们将解释和演示责任链模式。

![](img/f7efc94997e6575a5cac58f12cebf3e4.png)

## 定义

责任链是一个[行为设计模式](http://en.wikipedia.org/wiki/Behavioral_pattern)，它通过一系列处理器(处理者/接收者)对象处理请求。请求从一个处理程序对象发送到另一个处理程序对象，并由一个(纯实现)或所有处理程序处理。所有的处理程序都是链条的一部分。

包含链式逻辑的两个简单示例是:

*   使用自动柜员机提取现金的人，(输入 pin、金额、收据)；
*   帮助台呼叫(选项列表，按拨号键，按照步骤操作)。

## 参与者

短版本中的模式包括:

*   处理程序:定义处理请求的接口。它可以是一个抽象类，可选地实现默认方法和在链中设置后继者的方式。
*   许多具体的处理程序对象:处理请求并可选地提供对后继者的访问；

CoR 还可以包括:
–执行请求并建立链的客户端对象；
–一个请求对象；
——回应对象；
–其他设计模式。

CoR 设计模式并不常用，但是它的核心逻辑使它在一些情况下非常有用。

CoR 在以下情况下很有用:

*   必须自动确定处理程序(例如，日志系统)；
*   不能预先知道处理程序(例如，处理异常)；
*   请求必须通过特定链的优先级(例如，事件传播、命令传播)，或者链的顺序必须是动态的。

## 基本用法

CoR 通常与[复合模式](http://en.wikipedia.org/wiki/Composite_pattern)结合使用，因此很容易以相同的方式对待所有处理程序，并将请求分派给链的后继者。
下面是一个基本的 PHP 例子:

```
<?php
abstract class BasicHandler
{
    /**
     * @var Handler
     */
    private $successor = null;

    /**
     * Sets a successor handler.
     * 
     * @param Handler $handler
     */
    public function setSuccessor(Handler $handler)
    {
        $this->successor = $handler;
    }

    /**
     * Handles the request and/or redirect the request
     * to the successor.
     *
     * @param mixed $request
     *
     * @return mixed
     */
    abstract public function handle($request);

}
```

下面是一个示例(未完全实现)，继续上面的代码:

```
class FirstHandler extends BasicHandler
{
    public function handle($request)
    {
        //provide a response, call the next successor
    }
}

// .. code for SecondHandler and ThirdHandler classes ..

$firstHandler = new FirstHandler();
$secondHandler = new SecondHandler();
$thirdHandler = new ThirdHandler();

$firstHandler->setSuccessor($secondHandler);
$secondHandler->setSuccessor($thirdHandler);

$result = $firstHandler->handle($request);
```

## 高级用法

这种模式背后的力量是链式组织中的灵活性和开放逻辑。通过一些例子，这种灵活性变得更加清晰。

从`BasicHandler`代码中，可以自动化 handle 方法(带有一些约束),将其范围从子抽象类转移到父抽象类。

虽然下面的例子没有解决所有的 CoR 问题，但它展示了如何轻松地重构模式并适应不同的逻辑。

```
<?php
abstract class AdvancedHandler
{
    /**
     * @var Handler
     */
    private $successor = null;

    /**
     * Sets a successor handler,
     * in case the class is not able to satisfy the request.
     *
     * @param Handler $handler
     */
    final public function setSuccessor(Handler $handler)
    {
        if ($this->successor === null) {
            $this->successor = $handler;
        } else {
            $this->successor->setSuccessor($handler);
        }
    }

    /**
     * Handles the request or redirect the request
     * to the successor, if the process response is null.
     *
     * @param string|array $data
     *
     * @return string
     */
    final public function handle($request)
    {
        $response = $this->process($request);
        if (($response === null) && ($this->successor !== null)) {
            $response = $this->successor->handle($request);
        }

        return $response;
    }

    /**
     * Processes the request.
     * This is the only method a child can implements, 
     * with the constraint to return null to dispatch the request to next successor.
     * 
     * @param $request
     *
     * @return null|mixed
     */
    abstract protected function process($request);
}
```

```
class FirstHandler extends AdvancedHandler
{
    public function process($request)
    {
        //do something
    }
}

// .. code for SecondHandler and ThirdHandler classes ..
```

```
$firstHandler = new FirstHandler();
$secondHandler = new SecondHandler();
$thirdHandler = new ThirdHandler();

//the code below sets all successors through the first handler
$firstHandler->setSuccessor($secondHandler);
$firstHandler->setSuccessor($thirdHandler);

$result = $firstHandler->handle($request);
```

上面的例子最小化了具体处理程序中的方法(增加了[内聚力](http://en.wikipedia.org/wiki/Cohesion_%28computer_science%29)),尽管约束了非空响应。

如果每个请求都必须到达链的末尾，那么当后继者为空时，很容易重构返回响应的 handle 方法，因为最后一个链的处理程序没有后继者。也可以使用更结构化的响应对象。

对 CoR 有一些修改——例如，将这种模式与其他模式相结合，如 Factory 或 Decorator 模式，以获得响应的增量构造/更改。

现实世界的例子包括汽车的制造(底盘、内部、外部、油漆、车轮等)。)，或者网页的创建(页眉、正文、内容和页脚)。

我鼓励你探索其他的模式修改。

接下来，我们将看看一些关于常见 CoR 问题的建议，如配置优先级和加速链。

## 配置链

提取配置链的处理程序的逻辑有助于保持代码的整洁，并允许进行简单的更改。

使用带有[依赖注入](http://en.wikipedia.org/wiki/Dependency_injection)的配置对象，我们可以使配置全局化(例如，通过 yaml 文件)。

```
class Client
{
    private $firstHandler;

    public function setChainOrder($handlersOrder)	
    {
        //code for setup the chain
    }
    public function process($request)
    {
        $response = $this->firstHandler->handle($request);

        return $response; 
    }
}
```

```
$client = new Client();
$client->setChainOrder($myHanldersOrder);
$client->process($request);
```

依赖注入可以提供更大的灵活性。例如，链可以由每个请求对象配置，如下例所示，只需稍加修改代码。

```
$client = new Client();
$handlersOrder = $request->getHandlersOrder();
$client->setChainOrder($handlersOrder);
$client->process($request);
```

## 加速链条

如果 CoR 受到几个请求的压力，并且处理程序也很复杂——也许它们会创建其他类来处理请求——那么服务容器可能会很有用。

服务容器(或称[依赖注入容器](http://fabien.potencier.org/article/12/do-you-need-a-dependency-injection-container))管理对象的实例化，而不是每次都创建它们，这有助于提高内存和性能。

如果 CoR 经常收到相同类型的请求，缓存系统对于提高速度很有用。
一个可能的解决方案是使用一个 [Flyweight 模式](http://en.wikipedia.org/wiki/Flyweight_pattern)的实现来存储响应，并在相同的请求上提供存储的响应(如果存在的话),而不必再次处理这个链。

在这个例子中，我向`AdvancedHandler`类添加了一个简单的缓存。

```
abstract class AdvancedHandler
{
//code ...
    final public function handle($request)
    {
        $response = $this->cache->getResponse($request);
        if ($response === null) {
            $response = $this->handleRequestWithChain($request);    
        }

        return $response;
    }   

    final public function handleRequestWithChain($request)
    {
        $response = $this->process($request);
        if (($response === null) && ($this->successor !== null)) {
            $response = $this->successor->handleRequestWithChain($request);
        }
        $this->cache->setResponse($response);

        return $response;
    }
//code ... 
}
```

## 最后的想法

责任链可能是一个非常强大的模式。也可以实现一系列的链，形成一个多维结构。

CoR 提倡[松耦合](http://en.wikipedia.org/wiki/Loose_coupling):每个处理程序都可以被改变(或删除)而不会对整个结构产生大的影响，但是它的灵活性，如果设计错误，可能会对所有相关的链对象造成问题。

我建议你在实现 CoR 模式之前，对你试图解决的问题进行彻底的分析，并注意处理程序(抽象类或接口)的定义、请求、响应及其交互。

## 分享这篇文章