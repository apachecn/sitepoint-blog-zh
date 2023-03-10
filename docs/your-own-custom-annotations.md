# 您自己的自定义注释–不仅仅是注释！

> 原文：<https://www.sitepoint.com/your-own-custom-annotations/>

在本文中，我们将探讨如何在 Symfony 3 应用程序中创建和使用我们自己的自定义注释。你知道注释吧？它们是我们在类、方法和属性上面看到的文档块元数据/配置。你很可能见过它们被用来声明控制器路径(`@Route()`)和教条 ORM 映射(`@ORM()`)，或者甚至被用来[控制对像 Rauth](https://www.sitepoint.com/control-user-access-to-classes-and-methods-with-rauth/) 这样的包中的各种类和方法的访问。但是你有没有想过如何自己使用它们呢？如何定义自己的注释，然后用它来读取关于类或方法的信息，而不用实际加载它？

![Snippet of code with custom annotations](img/5e40f9de0682208ff805ceabaf0c3a6b.png)

在我们这样做之前，有一个小小的免责声明。注释与 Symfony 无关。它们是作为[教条项目](http://www.doctrine-project.org/)的一部分开发的概念，用于解决 ORM 信息到类方法的映射问题。

在本文中，我们将构建一个名为`WorkerBundle`的小型可重用包。是的，可重用，但仍然只是为了演示的目的，所以不太适合打包。我们将开发一个小概念，允许定义以不同速度“操作”的各种工作类型，然后可以由应用程序中的任何人使用。实际的工人操作超出了本文的范围，因为我们关注的是设置系统来管理它们(并通过注释发现它们)。

要了解我们要去哪里，您可以查看这个库并按照其中的说明在您的本地 Symfony 应用程序中设置软件包。

# 工人们

工人将实现一个需要一个方法的接口:`::work()`。在我们的新`WorkerBundle`中，让我们创建一个`Workers/`目录来保持整洁，并在那里添加接口:

```
<?php

namespace WorkerBundle\Workers;

interface WorkerInterface
{
    /**
     * Does the work
     *
     * @return NULL
     */
    public function work();
} 
```

## 注解

每个工人都必须实现上述接口。那很清楚。但是除此之外，我们还需要它们在类上有一个注释，以便找到它们并读取关于它们的一些元数据。

Doctrine 将 docblock 注释映射到一个类，该类的属性表示注释本身内部的键。让我们创建自己的，并在实践中看到这一点。

每个`WorkerInterface`实例在其文档块中都有以下注释:

```
/**
 * @Worker(
 *     name = "The unique Worker name",
 *     speed = 10
 * )
 */ 
```

我们将保持简单，只有两个属性:唯一名称(字符串)和工作速度(整数)。为了让这个注释被 Doctrine 的注释库识别，我们必须创建一个匹配的类，这个类也有自己的注释。

我们将把这个类放在我们的 bundle 名称空间的`Annotation`文件夹中，并简单地称它为`Worker`:

```
<?php

namespace WorkerBundle\Annotation;

use Doctrine\Common\Annotations\Annotation;

/**
 * @Annotation
 * @Target("CLASS")
 */
class Worker
{
    /**
     * @Required
     *
     * @var string
     */
    public $name;

    /**
     * @Required
     *
     * @var int
     */
    public $speed;

    /**
     * @return string
     */
    public function getName()
    {
        return $this->name;
    }

    /**
     * @return int
     */
    public function getSpeed()
    {
        return $this->speed;
    }
} 
```

如您所见，我们有两个属性和一些简单的 getters。然而，更重要的是，我们在顶部有两个注释:`@Annotation`(它告诉教义这个类代表一个注释)和`@Target("CLASS")`，它告诉教义它应该在整个类之上使用，而不是在方法或属性之上使用。就这样，`WorkerInterface`类现在可以使用这个注释了，方法是确保相应的类也被导入，并且在文件的顶部有一个`use`语句，如下所示:

```
use WorkerBundle\Annotation\Worker; 
```

## 经理

接下来，我们需要一个管理器，我们的应用程序可以使用它来获取所有可用工作人员的列表并创建它们。在与`WorkerInterface`相同的名称空间中，我们可以有这个简单的管理器类:

```
<?php

namespace WorkerBundle\Workers;

class WorkerManager
{
    /**
     * @var WorkerDiscovery
     */
    private $discovery;

    public function __construct(WorkerDiscovery $discovery)
    {
        $this->discovery = $discovery;
    }

    /**
     * Returns a list of available workers.
     *
     * @return array
     */
    public function getWorkers() {
        return $this->discovery->getWorkers();
    }

    /**
     * Returns one worker by name
     *
     * @param $name
     * @return array
     *
     * @throws \Exception
     */
    public function getWorker($name) {
        $workers = $this->discovery->getWorkers();
        if (isset($workers[$name])) {
            return $workers[$name];
        }

        throw new \Exception('Worker not found.');
    }

    /**
     * Creates a worker
     *
     * @param $name
     * @return WorkerInterface
     *
     * @throws \Exception
     */
    public function create($name) {
        $workers = $this->discovery->getWorkers();
        if (array_key_exists($name, $workers)) {
            $class = $workers[$name]['class'];
            if (!class_exists($class)) {
                throw new \Exception('Worker class does not exist.');
            }
            return new $class();
        }

        throw new \Exception('Worker does not exist.');
    }
} 
```

`WorkerManager`类做两件事:检索工人定义(`::getWorker()`和`::getWorkers()`)并实例化它们(`::create()`)。作为一个构造函数参数，它检索一个`WorkerDiscovery`对象，我们马上就要写这个对象了。其余的很容易理解。`::create()`方法期望每个工人定义是一个数组，该数组有一个用于实例化的`class`键。我们在这里保持事情简单，但是当然在真实世界的场景中，下一步将是使用单独的类来表示这个定义，并且将实际的实例化委托给工厂。

## 这个发现

我们注释演示的关键部分实际上是发现过程的一部分。为什么？因为我们使用了`Worker`注释来确定相应的类是否应该被视为工作者。这样做，我们在实际实例化对象之前使用元数据。让我们来看看我们的`WorkerDiscovery`类:

```
<?php

namespace WorkerBundle\Workers;

use WorkerBundle\Annotation\Worker;
use Doctrine\Common\Annotations\Reader;
use Symfony\Component\Finder\Finder;
use Symfony\Component\Finder\SplFileInfo;
use Symfony\Component\HttpKernel\Config\FileLocator;

class WorkerDiscovery
{
    /**
     * @var string
     */
    private $namespace;

    /**
     * @var string
     */
    private $directory;

    /**
     * @var Reader
     */
    private $annotationReader;

    /**
     * The Kernel root directory
     * @var string
     */
    private $rootDir;

    /**
     * @var array
     */
    private $workers = [];

    /**
     * WorkerDiscovery constructor.
     *
     * @param $namespace
     *   The namespace of the workers
     * @param $directory
     *   The directory of the workers
     * @param $rootDir
     * @param Reader $annotationReader
     */
    public function __construct($namespace, $directory, $rootDir, Reader $annotationReader)
    {
        $this->namespace = $namespace;
        $this->annotationReader = $annotationReader;
        $this->directory = $directory;
        $this->rootDir = $rootDir;
    }

    /**
     * Returns all the workers
     */
    public function getWorkers() {
        if (!$this->workers) {
            $this->discoverWorkers();
        }

        return $this->workers;
    }

    /**
     * Discovers workers
     */
    private function discoverWorkers() {
        $path = $this->rootDir . '/../src/' . $this->directory;
        $finder = new Finder();
        $finder->files()->in($path);

        /** @var SplFileInfo $file */
        foreach ($finder as $file) {
            $class = $this->namespace . '\\' . $file->getBasename('.php');
            $annotation = $this->annotationReader->getClassAnnotation(new \ReflectionClass($class), 'WorkerBundle\Annotation\Worker');
            if (!$annotation) {
                continue;
            }

            /** @var Worker $annotation */
            $this->workers[$annotation->getName()] = [
                'class' => $class,
                'annotation' => $annotation,
            ];
        }
    }
} 
```

前两个构造函数参数是配置我们的包的一部分。它们都是一个字符串，告诉我们的发现应该查看哪个文件夹，以及使用哪个名称空间来加载找到的类。这两者来自我们将在最后看到的服务容器定义。`rootDir`参数是内核目录的简单路径，`Reader`实例是我们用来读取注释的教条类。所有的魔法都发生在`::discoverWorkers()`内部。

首先，我们确定寻找工人的路径。然后，我们使用 [Symfony Finder 组件](http://symfony.com/doc/current/components/finder.html)来查找该文件夹中的所有文件。遍历所有找到的文件，我们根据文件名建立所有找到的类的类名，并创建`ReflectionClass`实例，然后传递给注释阅读器的`::getClassAnnotation()`。此方法的第二个参数表示要使用的注释类的名称空间。对于我们找到的所有注释，我们构建了一个包含类名的定义数组，如果有人需要，它可以用于实例化和整个注释对象。就是这样！我们的发现服务现在可以在不实例化任何工人的情况下查找工人。

有关更多信息，请查看`AnnotationReader`类，了解提取注释数据的可能性。还有读取方法和属性注释的方法，您可以通过传递相关的反射对象来使用这些方法。

## 连接起来

既然我们已经有了主要组件，是时候把所有的东西都连接起来了。首先，我们需要我们的服务定义，所以在我们的包的`Resource/config`文件夹中，我们可以有这个`services.yml`文件:

```
services:
    worker_manager:
        class: WorkerBundle\Workers\WorkerManager
        arguments: ["@worker_discovery"]
    worker_discovery:
        class: WorkerBundle\Workers\WorkerDiscovery
        arguments: ["%worker_namespace%", "%worker_directory%", "%kernel.root_dir%", "@annotation_reader"] 
```

这里没发生什么大事。`WorkerManager`获得`WorkerDiscovery`作为依赖项，而后者获得一些参数和教条注释阅读器服务。

但是为了让我们的服务定义能够被容器集中获取，我们需要编写一个小的扩展类。因此，在我们的包的`DependencyInjection`文件夹中，创建一个名为`WorkerExtension`的类。位置和名称对 Symfony 自动提取都很重要。

```
<?php

namespace WorkerBundle\DependencyInjection;

use Symfony\Component\DependencyInjection\Loader\YamlFileLoader;
use Symfony\Component\Config\FileLocator;
use Symfony\Component\HttpKernel\DependencyInjection\Extension;
use Symfony\Component\DependencyInjection\ContainerBuilder;

class WorkerExtension extends Extension
{
    public function load(array $configs, ContainerBuilder $container)
    {
        $loader = new YamlFileLoader($container, new FileLocator(__DIR__.'/../Resources/config'));
        $loader->load('services.yml');
    }
} 
```

在这里，我们只是使用`::load()`方法将我们的服务包含到与主容器合并的容器副本中。

我们需要做的最后一件事是注册我们的包。在我们的`AppKernel`里面:

```
public function registerBundles()
{
    return array(
        // ...
        new WorkerBundle\WorkerBundle(),
        // ...
    );
} 
```

仅此而已。

## 让我们工作吧！

现在我们准备工作。让我们配置一下在中央`parameters.yml`文件中我们的工作人员在哪里:

```
worker_namespace: AppBundle\Workers
worker_directory: AppBundle/Workers 
```

正如我们在上面看到的，这些参数从容器传递到`WorkerDiscovery`类。

位置由您决定，但是现在让我们将我们的工人放在应用程序的主`AppBundle`包中。我们的第一个工人可能看起来像这样:

```
<?php

namespace AppBundle\Workers;

use WorkerBundle\Annotation\Worker;
use WorkerBundle\Workers\WorkerInterface;

/**
 * Class SlowWorker
 *
 * @Worker(
 *     name = "Slow Worker",
 *     speed = 5
 * )
 */
class SlowWorker implements WorkerInterface
{

    /**
     * {@inheritdoc}
     */
    public function work()
    {
        return 'I work really slowly';
    }
} 
```

我们的注释在那里，`use`语句也在那里，所以没有什么可以阻止一些业务逻辑找到它并实例化它。让我们假设在控制器方法内部:

```
$manager = $this->get('worker_manager');
$worker = $manager->create('Slow Worker');
$worker->work(); 
```

或者

```
$workers = $manager->getWorkers();
$workers = array_filter($workers, function($definition) {
    return $definition['annotation']->getSpeed() >= 5;
});
foreach($workers as $definition) {
    /** @var WorkerInterface $worker */
    $worker = $manager->create($definition['annotation']->getName());
    $worker->work();
} 
```

呸…我们的`SlowWorker`今天差点没去上班！

## 结论

我们现在有能力使用注释来表达关于类(或者方法和属性)的元数据。在本教程中，我们已经构建了一个小的包，它公开了应用程序(或其他外部包)通过定义一些关于工人的元数据来声明工人能够执行*一些工作*的可能性。这些元数据不仅使它们容易被发现，还提供了关于它们是否应该被实际使用的信息。

你在自己的项目中使用自定义注释吗？如果是这样，你如何以劳思的方式实现它们，或者像我们在这里做的那样？或许是第三种方法？让我们知道！

* * *

有兴趣了解更多关于 Symfony、教义、注释和各种企业 PHP 的东西吗？加入我们在[网络夏令营](http://2016.websummercamp.com)为期三天的充实实践研讨会——这是唯一一个完全以实践为目的的会议，而且[会照顾你想带去的任何人](http://2016.websummercamp.com/Companions)！

## 分享这篇文章