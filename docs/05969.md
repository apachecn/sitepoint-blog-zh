# 了解 Symfony 捆绑包配置和服务容器

> 原文：<https://www.sitepoint.com/understanding-symfony-bundle-configuration-service-container/>

在这篇文章中，我们将介绍如何在 Symfony2 中配置包的不同方法，以及依赖注入容器如何与配置一起工作。当第一次开始使用 Symfony2 进行开发时，捆绑包配置和 Symfony 依赖注入容器(也称为服务容器)可能是很难理解的概念，特别是如果依赖注入不是一个熟悉的概念。包配置也可能有点混乱，因为有多种方法可以做到这一点，而最佳方法取决于具体情况。

这篇文章中所有的配置例子都是在 YAML。Symfony 还支持其他配置格式(XML 和 PHP 数组),它们是有效的选项。我习惯于使用 YAML，因为我认为它比 XML 可读性更好，但是在使用 XML 时，您确实可以获得模式验证的好处。配置格式的选择取决于您(或您的项目团队)，这里没有正确或错误的选项。就用你觉得最舒服的那个。

## 捆绑包创建

> Bundle 是包含一组文件(PHP 文件、样式表、JavaScripts、图像等等)的目录，这些文件实现了一个特性(博客、论坛等等)。在 Symfony2 中，(几乎)所有东西都生活在一个 bundle 中。

或者，换句话说，从[到](http://symfony.com/doc/2.0/book/page_creation.html#page-creation-bundles)

> 捆绑包类似于其他软件中的插件，但更好。关键的区别在于，Symfony2 中的一切都是捆绑在一起的，包括核心框架功能和为您的应用程序编写的代码。Bundles 是 Symfony2 中的一等公民。这使您可以灵活地使用第三方捆绑包中的预构建特性，或者发布您自己的捆绑包。它可以让您轻松挑选应用程序中要启用的功能，并按照您想要的方式优化它们。

当您创建一个新的包时，无论是通过自动生成(`php app/console generate:bundle --namespace=Acme/TestBundle`)还是手动生成，您都需要包根目录下的`BundleNameBundle.php`文件。这个文件中的类虽然大部分是空的，但它扩展了 Symfony 核心的`Symfony\Component\HttpKernel\Bundle\Bundle`类，这就是你需要在`AppKernel` `registerBundles`方法中注册的。当内核启动时，它创建每个包的实例并加载每个包的*容器扩展*(使用父`Bundle`类中的方法)。容器扩展是包的`DependencyInjection`文件夹中的`BundleNameExtension.php`文件中的类。容器扩展类加载并管理包配置。整个扩展类都是可选的，没有它 bundle 也能工作，但是了解事情是如何工作的对于更好地理解配置系统是有好处的。

## 加载包配置，简单的方法

如上所述，扩展类是可选的，还有其他方式来配置一个包。包配置的最简单形式是在应用程序的主配置文件(`app/config/config.yml`)中配置参数和服务。这是一个完全有效的选择，尽管您需要理解它的含义，以便更好地理解什么时候这才是真正合适的。在主配置文件中包含包配置会使您的包与当前应用程序紧密耦合，因此不太具有可移植性。如果捆绑包没有很多服务或参数，并且您知道它将只在当前应用程序中使用，这可能没问题。这就是说，我真的不建议使用这种方法，因为事情往往会发生变化，以后可能需要更多的配置，其他开发人员可能会在捆绑包中寻找配置。

另一个简单的方法是在包中有一个单独的配置文件(例如`Resources/config/services.yml`),并将其导入到主配置中。在`config.yml`的开始，通常已经有一些导入了，所以您需要做的就是在指向您的包配置文件的列表中再添加一个导入。注意，该路径是相对于主配置文件的。这里有一个小例子:

```
imports:
    - { resource: parameters.yml }
    - { resource: security.yml }
    - { resource: ../../src/Cvuorinen/ExampleBundle/Resources/config/services.yml }
```

## 加载捆绑配置，语义方式

如上所述，扩展类处理包配置的加载。它从同样位于`DependencyInjection`文件夹中的`Configuration.php`文件中创建了一个`Configuration`类的实例。`Configuration`类用于验证和处理与包相关的主配置(来自`app/config/`中的配置文件)。如果在主配置中没有与您的包相关的配置，那么`Configuration`类可以保持原样(在下一段之后会有更多的介绍)。

在处理完主配置后，扩展类使用适合于配置文件类型的加载器(使用 YAML 时为 T1)从包的`Resources/config`文件夹中加载*包特定配置*。bundles 配置文件中定义的任何服务都可以使用应用程序主配置文件中的参数(和[全局参数](http://symfony.com/doc/current/cookbook/bundles/extension.html#global-parameters))，并将被添加到*服务容器*。此后，这些服务就可以用于您的控制器、CLI 命令或应用程序的任何其他“容器感知”部分。您只需要用服务配置中指定的服务名调用容器的`get`方法。

语义配置的真正强大之处在于它可以用来处理、合并和验证来自应用程序主配置的配置选项，并将这些选项传递给 bundles 服务配置。这在创建面向更广泛受众的包时特别有用，比如开源包，但也可能适合公司内部的分发。通过这种方式，您可以提供一些简单且记录良好的配置选项(在适当的地方提供默认值)，包的用户可以在主配置文件中设置这些选项，然后包扩展类可以处理和验证它们，这样包的用户就不需要接触包内部的任何配置。

## 加载捆绑包配置，方法正确吗？

当然，没有一种正确的方法。但是大多数时候，简单的方法并不可行，因为捆绑包的整体思想是让它们可以在不同的应用程序中重用。使用 Symfony CLI 命令自动生成语义配置所需的所有文件也很容易:

```
$ app/console generate:bundle
```

当第一次开始用 Symfony 开发时，我会说用简单的方法或者(自动生成的)语义方法，不需要任何配置处理。当您更加熟悉 bundle 系统并开始创建您计划分发给其他人的 bundle 时，然后学习更多关于主配置的[验证和合并。](http://symfony.com/doc/current/cookbook/bundles/extension.html#validation-and-merging-with-a-configuration-class)

## 配置文件结构

好了，现在我们知道了如何加载一个配置文件，让我们来看看在那里放什么。配置主要包括两种类型的信息，**参数**和**服务**。

*参数*是您的包需要的任何静态值。像登录凭证、API 密钥、主机名或外部服务的 URL 之类的东西，你大概明白了。参数可以是任何标量值(如字符串、数字、布尔值)和数组，它们在配置中的`parameters`键下指定。将服务类名指定为参数也是一种很好的做法，因为这允许扩展包，例如，覆盖另一个包中的某些服务。您可以通过使用参数名作为参数调用`getParameter`方法来从容器中检索参数，但是大多数时候您会将参数作为参数传递给配置中的服务。

*服务*是保存您的包的业务逻辑的类。通过在配置文件中定义它们，您可以利用依赖注入容器的功能，即自动将参数和其他服务注入其中，然后通过一行代码从完全可操作的容器中检索它们。如果你对依赖注入不是很熟悉，我建议你先读一点，其他地方有关于依赖注入的[多](http://www.javaworld.com/article/2071914/excellent-explanation-of-dependency-injection--inversion-of-control-.html) [好](http://martinfowler.com/articles/injection.html) [解释](http://en.wikipedia.org/wiki/Dependency_injection)，这里就不赘述了。

按照惯例，*参数*和*服务*是用包名命名的(在[蛇案](http://en.wikipedia.org/wiki/Snake_case)中)，但是这并不是强制的，所以你可以随意命名它们。尽管我建议使用包名命名空间来避免与其他包的冲突。

下面是一个来自示例包的`services.yml`文件的小例子:

```
parameters:
    cvuorinen_example.greeter.class: Cvuorinen\ExampleBundle\Service\Greeter
    cvuorinen_example.greeter.greeting: "Hello"

services:
    cvuorinen_example.greeter:
        class: %cvuorinen_example.greeter.class%
        arguments: [%cvuorinen_example.greeter.greeting%]
```

这里我们定义一个服务(称为`cvuorinen_example.greeter`)。我们有两个参数，第一个保存欢迎服务的类名，并被设置为服务的`class`参数，另一个是一个字符串值，它将作为构造函数参数使用服务的`arguments`参数数组传递给服务。

如您所见，定义的参数可以在配置中引用，方法是用%字符将它们括起来。您还可以引用主配置中的参数，也可以引用其他包中的参数(但是请注意，这又会使您的包与其他包耦合)。你可以在 Symfony 文档中阅读更多关于参数的信息。

您可以使用 CLI 命令测试和调试您的配置:

```
$ app/console container:debug
```

这将构建并合并包配置，并打印出所有已注册的服务，或者在配置有问题的情况下打印出一条错误消息。还有一个`--parameters`选项可以打印出所有的参数。

## 把所有的放在一起

现在我们有了一个工作服务配置，让我们看看如何在控制器中使用它。

首先，这是我们非常简单的迎宾服务:

```
namespace Cvuorinen\ExampleBundle\Service;

class Greeter
{
    public function __construct($greeting)
    {
        $this->greeting = $greeting;
    }

    public function greet($name)
    {
        return $this->greeting . ' ' . $name;
    }
}
```

在这篇文章中，我将关注服务配置，所以我不会详细讨论路由器配置。如果你不熟悉它，你可以在[官方文件](http://symfony.com/doc/current/book/routing.html)中了解更多。但是假设我们有一个模式为`/hello/{name}`的路由，它指向示例包中的一个`DefaultController`。那么我们的控制器可能看起来像这样:

```
namespace Cvuorinen\ExampleBundle\Controller;

use Symfony\Bundle\FrameworkBundle\Controller\Controller;
use Symfony\Component\HttpFoundation\Response;

class DefaultController extends Controller
{
    public function indexAction($name)
    {
        $greeter = $this->get('cvuorinen_example.greeter');

        return new Response(
            $greeter->greet($name)
        );
    }
}
```

正如您所看到的，控制器中没有逻辑，它只从容器中检索服务，并调用该服务上的方法，然后传递请求中的参数。控制器不需要知道如何构造欢迎服务，依赖注入容器会为它做所有的工作。这允许我们保持控制器的“精简性”以及其他地方的所有业务逻辑和依赖性管理。

现在，假设我们想要更改问候语，我们需要做的就是更改配置文件中的参数。对于这个例子来说，这似乎没什么大不了的，但是在现实世界的应用程序中，您可能会在应用程序的许多地方重用相同的服务。从这个角度看，好处是显而易见的。

## 依赖关系呢！？

好吧，如果你一直在关注，你可能已经注意到我们的例子并没有真正注入任何依赖，只有一个简单的参数。这是真的，我想让第一个例子尽可能简单。现在，如果我们需要制作一个多语言应用程序呢？我们将不得不使用某种翻译系统，`Greeter`服务需要它来翻译问候。

首先，让我们像这样更改服务类:

```
namespace Cvuorinen\ExampleBundle\Service;

use Symfony\Component\Translation\TranslatorInterface;

class Greeter
{
    public function __construct($greeting, TranslatorInterface $translator)
    {
        $this->greeting = $greeting;
        $this->translator = $translator;
    }

    public function greet($name)
    {
        return $this->translator->trans($this->greeting) . ' ' . $name;
    }
}
```

然后，让我们更改配置，注入一个转换器服务，如下所示:

```
parameters:
    cvuorinen_example.greeter.class: Cvuorinen\ExampleBundle\Service\Greeter
    cvuorinen_example.greeter.greeting: "Hello"

services:
    cvuorinen_example.greeter:
        class: %cvuorinen_example.greeter.class%
        arguments:
            - %cvuorinen_example.greeter.greeting%
            - @translator
```

除此之外，这与前面的例子完全相同，唯一的区别是在欢迎服务配置的`arguments`部分。这里我们添加了另一个构造函数参数，这次是另一个服务。通过在服务前面加上@-字符，可以在配置中引用服务。我们使用默认的 [Symfony 翻译服务](http://symfony.com/doc/current/book/translation.html)，它的名字是“Translator”。一些核心服务的名称中没有名称空间，但是您也可以使用`@cvuorinen_example.translator`注入一个定制的翻译服务。

## 注射所有的东西

到目前为止，我只介绍了“基本”注入方法，*构造函数注入*。Symfony 服务容器也可以与 *setter 注入*和*属性注入*一起使用。

Setter 注入意味着服务类对于依赖项有一个单独的 setter 方法(例如，因为它可能是可选的)。这可以通过向服务配置添加一个`calls`参数来配置，该参数是一个方法调用数组，将在服务构建完成后执行。属性注入意味着该类具有公共属性，并且可以从外部对其设置依赖关系。这可以通过服务配置中的`properties`参数来实现。关于不同注射类型的更多信息可以在[文档](http://symfony.com/doc/current/components/dependency_injection/types.html)中找到。

服务配置还可以使用另一个服务作为工厂来创建服务。工厂可以是您自己的包中的另一个服务，但是您也可以将这个特性用于一些现有的服务。一个例子是，如果您的服务需要某个教条存储库，您可以使用*实体管理器服务*作为工厂，将存储库类定义为服务，而不是注入实体管理器服务。这样，您的服务类只需要向存储库类添加一个依赖项，而不是整个实体管理器。这使得服务更具可移植性，更易于测试，因为您只需要模仿存储库类。

服务也可以声明为私有。这意味着它们不能从使用 get 方法的容器中获得，它们只能用作注入到其他服务的参数。这可以通过在服务配置中设置`public: false`来完成。

这里是最后一个例子，展示了我们如何使用教条实体管理器作为工厂将*实体库*定义为服务，并使用 *setter 注入*将其注入到另一个服务中。实体存储库服务也被声明为私有的，因此它不能通过容器使用。

```
parameters:
    cvuorinen_example.item_repository.class: Doctrine\ORM\EntityRepository
    cvuorinen_example.item_repository.entity: "CvuorinenExampleBundle:Item"
    cvuorinen_example.items.class: Cvuorinen\ExampleBundle\Service\Items

services:
    cvuorinen_example.item_repository:
        class: %cvuorinen_example.item_repository.class%
        public: false
        factory_service: doctrine.orm.entity_manager
        factory_method: getRepository
        arguments: [%cvuorinen_example.item_repository.entity%]
    cvuorinen_example.items:
        class: %cvuorinen_example.items.class%
        calls:
            - [setRepository, [@cvuorinen_example.item_repository]]
```

## 结论

这是一篇相当长的文章，还有很多关于 Symfony bundle 配置和依赖注入容器的内容我还没有介绍。希望这能帮助你更好地理解 Symfony bundle 系统，并鼓励你更多地使用依赖注入。我希望在未来的帖子中涵盖更多高级主题，例如覆盖捆绑包配置(捆绑包继承)、在捆绑包之间共享参数以及将控制器定义为服务。

同时，您可以从 Symfony 文档中了解更多关于[服务容器](http://symfony.com/doc/current/book/service_container.html)和[依赖注入](http://symfony.com/doc/current/components/dependency_injection/index.html)的信息。

最后一件事。即使服务容器本身是一个可以在配置中引用并注入到其他服务中的服务，也不要这样做。这实际上是依赖注入的反模式，因为服务将再次对它们自己的依赖负责，并且服务也将变得与容器本身紧密耦合。

## 分享这篇文章