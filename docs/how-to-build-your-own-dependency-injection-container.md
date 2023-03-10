# 如何构建自己的依赖注入容器

> 原文：<https://www.sitepoint.com/how-to-build-your-own-dependency-injection-container/>

在 packagist 上搜索[“依赖注入容器”目前可以提供超过 95 页的结果。可以肯定地说，这种特殊的“轮子”已经被发明出来了。](https://packagist.org/search/?q=dependency%20injection%20container)

![Square wheel?](img/83b6a8a1412ac49b845e1c283633130f.png)

然而，没有一个厨师学会只用现成的食物来烹饪。同样，**没有一个开发人员仅仅使用“现成代码”**来学习编程。

在本文中，我们将学习如何制作一个简单的依赖注入容器包。本文中编写的所有代码，加上 PHPDoc 注释和 100%覆盖率的单元测试都可以在这个 GitHub 库获得[。它也在](https://github.com/sitepoint/Container)[包装商](https://packagist.org/packages/sitepoint/container)上。

## 规划我们的依赖注入容器

让我们从计划我们希望我们的容器做什么开始。将“依赖注入容器”拆分为“依赖注入”和“容器”两个角色是一个很好的开始。

完成依赖注入的两种最常见的方法是通过**构造器注入**或**设置器注入**。也就是说，通过构造函数参数或方法调用传递类依赖关系。如果我们的容器将能够实例化和包含服务，它需要能够做到这两者。

作为一个容器，它必须能够存储和检索服务的实例。与创建服务相比，这是一个非常琐碎的任务，但是仍然值得考虑。[容器互操作](https://github.com/container-interop/container-interop)包提供了一组容器可以实现的接口。主接口是定义了两个方法的`ContainerInterface`，一个用于检索服务，另一个用于测试服务是否已经定义。

```
interface ContainerInterface
{
    public function get($id);
    public function has($id);
} 
```

## 从其他依赖注入容器中学习

Symfony 依赖注入容器允许我们以各种不同的方式定义服务。在 YAML，容器的配置可能如下所示:

```
parameters:
    # ...
    mailer.transport: sendmail

services:
    mailer:
        class:     Mailer
        arguments: ["%mailer.transport%"]
    newsletter_manager:
        class:     NewsletterManager
        calls:
            - [setMailer, ["@mailer"]] 
```

Symfony 将容器配置分成参数和服务配置的方式非常有用。这允许将 API 密钥、加密密钥和 auth 令牌等应用程序机密存储在从源代码库中排除的参数文件中。

在 PHP 中，Symfony 依赖注入组件的相同配置如下所示:

```
use Symfony\Component\DependencyInjection\Reference;

// ...
$container->setParameter('mailer.transport', 'sendmail');

$container
    ->register('mailer', 'Mailer')
    ->addArgument('%mailer.transport%');

$container
    ->register('newsletter_manager', 'NewsletterManager')
    ->addMethodCall('setMailer', array(new Reference('mailer'))); 
```

通过在对`setMailer`的方法调用中使用一个`Reference`对象，依赖注入逻辑可以检测到这个值不应该被直接传递，而是被它在容器中引用的服务所替换。这使得 PHP 值和其他服务可以很容易地注入到一个服务中而不会混淆。

## 入门指南

首先要做的是创建一个新的项目目录并创建一个`composer.json`文件，这个文件可以被 [Composer](https://www.sitepoint.com/php-dependency-management-with-composer/) 用来自动加载我们的类。这个文件目前所做的就是将名称空间`SitePoint\Container`映射到`src`目录。

```
{
    "autoload": {
        "psr-4": {
            "SitePoint\\Container\\": "src/"
        }
    },
} 
```

接下来，因为我们要让我们的容器实现`container-interop`接口，我们需要让 composer 下载它们并把它们添加到我们的`composer.json`文件中:

```
composer require container-interop/container-interop 
```

除了主`ContainerInterface`，`container-interop`包还定义了两个异常接口。第一个用于创建服务时遇到的一般异常，另一个用于无法找到所请求的服务时。我们还将在此列表中添加另一个例外，用于无法找到所请求的参数的情况。

由于除了核心 PHP `Exception`类提供的功能之外，我们不需要添加任何功能，这些类非常简单。虽然它们可能看起来毫无意义，但像这样把它们分开可以让我们很容易地抓住并独立处理它们。

创建`src`目录，分别在`src/Exception/ContainerException.php`、`src/Exception/ServiceNotFoundException.php`和`src/Exception/ParameterNotFoundException.php`创建这三个文件:

```
<?php

namespace SitePoint\Container\Exception;

use Interop\Container\Exception\ContainerException as InteropContainerException;

class ContainerException extends \Exception implements InteropContainerException {} 
```

```
<?php

namespace SitePoint\Container\Exception;

use Interop\Container\Exception\NotFoundException as InteropNotFoundException;

class ServiceNotFoundException extends \Exception implements InteropNotFoundException {} 
```

```
<?php

namespace SitePoint\Container\Exception;

class ParameterNotFoundException extends \Exception {} 
```

## 容器参考

前面讨论的 Symfony `Reference`类允许库区分直接使用的 PHP 值和需要由容器中其他服务替换的参数。

让我们借用这个想法，为参数和服务的引用创建两个类。因为这两个类都是值对象，只存储它们所引用的资源的名称，所以使用抽象类作为基础是有意义的。这样我们就不必重复编写相同的代码。

分别在`src/Reference/AbstractReference.php`、`src/Reference/ServiceReference.php`和`src/Reference/ParameterReference.php`创建以下文件:

```
<?php

namespace SitePoint\Container\Reference;

abstract class AbstractReference
{
    private $name;

    public function __construct($name)
    {
        $this->name = $name;
    }

    public function getName()
    {
        return $this->name;
    }
} 
```

```
<?php

namespace SitePoint\Container\Reference;

class ServiceReference extends AbstractReference {} 
```

```
<?php

namespace SitePoint\Container\Reference;

class ParameterReference extends AbstractReference {} 
```

### 容器类

现在是时候创建我们的容器了。我们将从我们的容器类的基本示意图开始，并且我们将在进行过程中添加方法。

总的想法是在容器的构造函数中接受两个数组。第一个数组包含服务定义，第二个包含参数定义。

在`src/Container.php`处，放置以下代码:

```
<?php

namespace SitePoint\Container;

use Interop\Container\ContainerInterface as InteropContainerInterface;

class Container implements InteropContainerInterface
{
    private $services;
    private $parameters;
    private $serviceStore;

    public function __construct(array $services = [], array $parameters = [])
    {
        $this->services     = $services;
        $this->parameters   = $parameters;
        $this->serviceStore = [];
    }
} 
```

我们在这里所做的就是实现来自`container-interop`的`ContainerInterface`,并将定义加载到稍后可以访问的属性中。我们还创建了一个`serviceStore`属性，并将其初始化为一个空数组。当容器被要求创建服务时，我们将把它们保存在这个数组中，以便以后可以检索它们，而不必重新创建它们。

现在让我们开始编写由`container-interop`定义的方法。从`get($name)`开始，将下面的方法添加到类中:

```
use SitePoint\Container\Exception\ServiceNotFoundException;

// ...
    public function get($name)
    {
        if (!$this->has($name)) {
            throw new ServiceNotFoundException('Service not found: '.$name);
        }

        if (!isset($this->serviceStore[$name])) {
            $this->serviceStore[$name] = $this->createService($name);
        }

        return $this->serviceStore[$name];
    }
// ... 
```

确保将`use`语句添加到文件的顶部。我们的`get($name)`方法只是检查容器是否有服务的定义。如果没有，我们之前创建的`ServiceNotFoundException`就会被抛出。如果是，它将返回服务，创建服务并保存到存储区(如果还没有这样做的话)。

当我们这样做的时候，我们应该创建一个从容器中获取参数的方法。假设传递给构造函数的参数形成了一个 N 维关联数组，我们需要某种方法来使用单个字符串干净地访问数组中的任何元素。一种简单的方法是使用`.`作为分隔符，这样字符串`foo.bar`就可以引用根参数数组的`foo`键中的`bar`键。

```
use SitePoint\Container\Exception\ParameterNotFoundException;

// ...
    public function getParameter($name)
    {
        $tokens  = explode('.', $name);
        $context = $this->parameters;

        while (null !== ($token = array_shift($tokens))) {
            if (!isset($context[$token])) {
                throw new ParameterNotFoundException('Parameter not found: '.$name);
            }

            $context = $context[$token];
        }

        return $context;
    }
// ... 
```

现在，我们使用了一些我们还没有写的方法。第一个是`has($name)`方法，也是由`container-interop`定义的。这是一个非常简单的方法，它只需要检查提供给构造函数的定义数组是否包含一个用于`$name`服务的条目。

```
// ...
    public function has($name)
    {
        return isset($this->services[$name]);
    }
// ... 
```

我们调用的另一个尚未编写的方法是`createService($name)`方法。该方法将使用提供的定义来创建服务。因为我们不希望从容器外部调用这个方法，所以我们将它设为私有。

在这个方法中要做的第一件事是一些健全性检查。对于每个服务定义，我们需要一个包含一个`class`键和可选的`arguments`和`calls`键的数组。这些将分别用于构造函数注入和设置器注入。我们还可以通过检查我们是否已经尝试过创建服务来增加对循环引用的保护。

如果`arguments`键存在，我们希望将参数定义的数组转换成可以传递给构造函数的 PHP 值数组。为此，我们需要将之前定义的引用对象转换为它们从容器中引用的值。现在，我们将把这个逻辑引入到`resolveArguments($name, array $argumentDefinitons)`方法中。我们使用 [`ReflectionClass::newInstanceArgs()`](http://php.net/manual/en/reflectionclass.newinstanceargs.php) 方法来创建使用`arguments`数组的服务。这是**构造函数注入**。

如果`calls`键存在，我们希望使用`call definitions`的数组，并将它们应用到我们刚刚创建的服务中。同样，我们将把这个逻辑放入定义为`initializeService($service, $name, array $callDefinitions)`的独立方法中。这是**二传手注射的**。

```
use SitePoint\Container\Exception\ContainerException;

// ...
    private function createService($name)
    {
        $entry = &$this->services[$name];

        if (!is_array($entry) || !isset($entry['class'])) {
            throw new ContainerException($name.' service entry must be an array containing a \'class\' key');
        } elseif (!class_exists($entry['class'])) {
            throw new ContainerException($name.' service class does not exist: '.$entry['class']);
        } elseif (isset($entry['lock'])) {
            throw new ContainerException($name.' service contains a circular reference');
        }

        $entry['lock'] = true;

        $arguments = isset($entry['arguments']) ? $this->resolveArguments($name, $entry['arguments']) : [];

        $reflector = new \ReflectionClass($entry['class']);
        $service = $reflector->newInstanceArgs($arguments);

        if (isset($entry['calls'])) {
            $this->initializeService($service, $name, $entry['calls']);
        }

        return $service;
    }
// ... 
```

这给我们留下了两个最终的方法来创建。第一个应该将一个参数定义数组转换成一个 PHP 值数组。为此，需要用容器中的适当参数和服务替换`ParameterReference`和`ServiceReference`对象。

```
use SitePoint\Container\Reference\ParameterReference;
use SitePoint\Container\Reference\ServiceReference;

// ...
    private function resolveArguments($name, array $argumentDefinitions)
    {
        $arguments = [];

        foreach ($argumentDefinitions as $argumentDefinition) {
            if ($argumentDefinition instanceof ServiceReference) {
                $argumentServiceName = $argumentDefinition->getName();

                $arguments[] = $this->get($argumentServiceName);
            } elseif ($argumentDefinition instanceof ParameterReference) {
                $argumentParameterName = $argumentDefinition->getName();

                $arguments[] = $this->getParameter($argumentParameterName);
            } else {
                $arguments[] = $argumentDefinition;
            }
        }

        return $arguments;
    } 
```

最后一个方法在实例化的服务对象上执行 setter 注入。为此，它需要遍历一组方法调用定义。`method`键用于指定方法，可选的`arguments`键可用于为方法调用提供参数。我们可以重用刚刚编写的方法，将这些参数转换成 PHP 值。

```
 private function initializeService($service, $name, array $callDefinitions)
    {
        foreach ($callDefinitions as $callDefinition) {
            if (!is_array($callDefinition) || !isset($callDefinition['method'])) {
                throw new ContainerException($name.' service calls must be arrays containing a \'method\' key');
            } elseif (!is_callable([$service, $callDefinition['method']])) {
                throw new ContainerException($name.' service asks for call to uncallable method: '.$callDefinition['method']);
            }

            $arguments = isset($callDefinition['arguments']) ? $this->resolveArguments($name, $callDefinition['arguments']) : [];

            call_user_func_array([$service, $callDefinition['method']], $arguments);
        }
    }
} 
```

现在我们有了一个可用的依赖注入容器！要查看使用示例，请查看 GitHub 上的[库。](https://github.com/sitepoint/Container)

### 整理想法

我们已经学会了如何制作一个简单的依赖注入容器，但是还有很多容器具有我们还没有的很酷的特性！

一些依赖注入容器，比如 [PHP-DI](http://php-di.org/doc/autowiring.html) 和 [Aura。Di](https://github.com/auraphp/Aura.Di#auto-resolution-of-parameter-values) 提供了一个称为自动布线的功能。这是容器猜测应该将容器中的哪些服务注入其他服务的地方。为此，他们使用[反射 API](http://php.net/manual/en/reflectionclass.getconstructor.php) 来找出关于构造函数参数的信息。

随意派生[库](https://github.com/sitepoint/Container)并添加诸如自动布线或任何你能想到的功能，这是很好的实践！此外，我们保留了一个公开列表，列出了该容器的所有已知分支，以便其他人可以看到您所做的工作。只需使用下面的评论与我们分享您的工作，我们将确保它得到添加。

你也可以用下面的评论来联系。让我们知道你想要澄清或解释的任何事情，或者你发现的任何错误。

请留意 SitePoint PHP 上更多类似的文章。我们将很快解释如何用一系列常见的 PHP 组件重新发明轮子！

## 分享这篇文章