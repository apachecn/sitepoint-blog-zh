# 管理类依赖:依赖注入、服务定位器和工厂介绍，第 2 部分

> 原文：<https://www.sitepoint.com/managing-class-dependencies-2/>

在这个由两部分组成的系列文章的前一期中，我介绍了几个简单例子的开发过程，以一种相当平易近人的方式探索了 PHP 在处理类依赖时的一些新方法。在这篇初级读本中，我介绍了在构造函数中包含罪恶的“新”操作符，这是一种应该毫不内疚地迅速扔进垃圾桶的方法，以及注入工厂的使用。

虽然公平地承认工厂在一些特殊用例中确实有一个整洁的位置，但我不会无情地谴责服务定位器和简单的依赖注入是一种不公平的放逐。在这最后一部分中，我们将仔细研究这些流行模式的实现，以便您可以选择最适合手边需要的模式。

## 中间人——通过服务定位器获得班级合作者

虽然在 PHP 世界中，服务定位器在很多情况下被认为是一种奇特的、令人兴奋的方法，但事实是，这种模式，当然也有一些创造性的倾向，已经在该语言的领域中享受了漫长而古老的生命。从根本上来说，服务定位器只不过是一个集中的注册中心，大部分时间是静态的(尽管一些流行的框架中出现了动态的)，充满了一堆对象。不多不少。

像往常一样，理解服务定位器下发生的事情的一种教学方法是通过例子。如果我们想利用模式的优点，给上次的`FileStorage`对象一个依赖，那么定位器可以这样实现:

```
<?php
namespace LibraryDependencyInjection;

interface ServiceLocatorInterface
{
    public function set($name, $service);
    public function get($name);
    public function has($name);
    public function remove($name);
    public function clear();
}
```

```
<?php
namespace LibraryDependencyInjection;

class ServiceLocator implements ServiceLocatorInterface
{
    protected $services = array();

    public function set($name, $service) {
        if (!is_object($service)) {
             throw new InvalidArgumentException(
                "Only objects can be registered with the locator.");
        }
        if (!in_array($service, $this->services, true)) {
            $this->services[$name] = $service;
        }
        return $this;
    }

    public function get($name) {
        if (!isset($this->services[$name])) {
            throw new RuntimeException(
                "The service $name has not been registered with the locator.");
        }
        return $this->services[$name];
    }

    public function has($name) {
        return isset($this->services[$name]);
    }

    public function remove($name) {
        if (isset($this->services[$name])) {
            unset($this->services[$name]);
        }
        return $this;
    }

    public function clear() {
        $this->services = array();
        return $this;
    }
}
```

如果你愿意，可以把我的意见当作一种宣泄，但是我必须承认，我不太愿意在简单的依赖注入中使用服务定位器，即使定位器是动态的，而不是受易变的全局访问问题困扰的静态注册表。不管是哪种情况，都值得研究一下，看看如何将它传递给`FileStorage`类。我们开始吧:

```
<?php
namespace LibraryFile;
use LibraryDependencyInjectionServiceLocatorInterface;

class FileStorage
{
    const DEFAULT_STORAGE_FILE = "data.dat";
    protected $serializer;
    protected $file;

    public function __construct(ServiceLocatorInterface $locator, $file = self::DEFAULT_STORAGE_FILE) {
        $this->setFile($file);
        $this->serializer = $locator->get("serializer");
    }

    public function setFile($file) {
        if (!is_file($file)) {
            throw new InvalidArgumentException(
                "The file $file does not exist.");
        }
        if (!is_readable($file) || !is_writable($file)) {
            if (!chmod($file, 0644)) {
                throw new InvalidArgumentException(
                    "The file $file is not readable or writable."); 
            }
        } 
        $this->file = $file;
        return $this;
    }

    public function read() {
        try {
            return $this->serializer->unserialize(
                @file_get_contents($this->file));
        }
        catch (Exception $e) {
            throw new Exception($e->getMessage());
        }
    }

    public function write($data) {
        try {
            return file_put_contents($this->file,
                $this->serializer->serialize($data));    
        }
        catch (Exception $e) {
            throw new Exception($e->getMessage());
        }
    }
}
```

为了说明问题，我又一次从上到下删除了`FileStorage`类，因为这可能会更容易理解它的驱动逻辑是如何保持与它的`read()` / `write()`方法无关的。到目前为止，构造函数是最相关的块，因为它使用定位器，然后定位器负责获取序列化程序对象。

虽然它的实现很简单，但这种方法远非无害。首先，`FileStorage`现在对定位器有很强的依赖性，即使有可能传递它的不同实现。第二，由于定位器本质上是类依赖的中间提供者，它在某些方面也违反了德米特的[法则。这是一个不可避免的与模式根源联系在一起的工件。我们要么学会接受这个问题，要么干脆忘记这个模式。这里没有中间立场可以考虑。](http://en.wikipedia.org/wiki/Law_of_Demeter)

下面的代码展示了如何最终使用定位器进行操作:

```
<?php
$locator = new ServiceLocator;
$locator->set("serializer", new Serializer());
$fileStorage = new FileStorage($locator);
$fileStorage->write("This is a sample string.");
echo $fileStorage->read();
```

虽然这个例子很原始，但是它显示了定位器在某些方面类似于基本依赖注入容器(DIC)的结构。主要的区别在于，定位器通常被注入或者静态地消耗在客户端类中，而 DIC 总是存在于它们之外。

到目前为止，我们已经介绍了大量用于管理类依赖关系的常用方法。然而，我们还没有游过最简单的领域……是的，原始依赖注入的甜蜜小溪！

## 最棒也是最简单的结局——使用简单的依赖注入

我知道这听起来可能很明显，但是为`FileStorage`提供序列化程序对象的最有效和最简单的方法是使用普通的 ol '依赖注入，这样就可以避免任何耦合问题或者打破 Demeter 法则强加的戒律。当然，我假设你足够聪明，并且从一开始就知道这一点。即便如此，展示一下使用这种方法时所讨论的类是什么样子也无妨:

```
<?php
namespace LibraryFile;

class FileStorage
{
    const DEFAULT_STORAGE_FILE = "data.dat";
    protected $serializer; 
    protected $file;

    public function __construct(Serializable $serializer, $file = self::DEFAULT_STORAGE_FILE) {
        $this->setFile($file);
        $this->serializer = $serializer;
    }

    // the remaining methods go here
}

$fileStorage = new FileStorage(new Serializer);
$fileStorage->write("This is a sample string.");
echo $fileStorage->read();
```

这太容易被同化了。在这种情况下，整个对象图是如此的缺乏活力，以至于求助于外部 DIC 的具体细节来创建它简直是矫枉过正。然而，为了有益的目的，我们可以构建一个原始的容器，类似于 swift，lightweight `Pimple`，然后马上看看如何使用它来连接组成文件存储模块的所有对象:

```
<?php
namespace LibraryDependencyInjection;

interface ContainerInterface
{
    public function set($name, $service);
    public function get($name, array $params = array());
    public function has($name);
    public function remove($name);
    public function clear();
}
```

```
<?php
namespace LibraryDependencyInjection;

class Container implements ContainerInterface
{
    protected $services = array();

    public function set($name, $service) {
        if (!is_object($service)) {
             throw new InvalidArgumentException(
                "Only objects can be registered with the container.");
        }
        if (!in_array($service, $this->services, true)) {
            $this->services[$name] = $service;
        }
        return $this;
    }

    public function get($name, array $params = array()) {
        if (!isset($this->services[$name])) {
            throw new RuntimeException(
                "The service $name has not been registered with the container.");
        }
        $service = $this->services[$name];
        return !$service instanceof Closure 
            ? $service : call_user_func_array($service, $params);
    }

    public function has($name) {
        return isset($this->services[$name]);
    }

    public function remove($name) {
        if (isset($this->services[$name])) {
            unset($this->services[$name]);
        }
        return $this;
    }

    public function clear() {
        $this->services = array();
    }
}
```

驾驶员信息中心和之前编码的维修定位器之间的相似之处绝非偶然。然而，前者实现了一些额外的功能，因为它能够根据请求存储和调用闭包，这非常类似于 Pimple 背后的力量。

有了这个简单的 DIC，就可以按需组装整个文件存储对象图:

```
<?php
$container = new Container();
$container->set("filestorage", function() {
    return new FileStorage(new Serializer());
});
$fileStorage = $container->get("filestorage");
$fileStorage->write("This is a sample string.");
echo $fileStorage->read();
```

很明显，DIC 是(或者理论上应该是)一个超越客户端类边界的元素，客户端类对其相当隐秘和无声的存在是完全不可知的。这种形式的无意识很可能是 DIC 和服务定位器之间存在的最大差异之一，尽管也可以通过某种“递归”注入将 DIC 注入到其他类中。

在我看来，这个过程不仅不必要地将 DIC 降低到普通服务定位器的水平，而且破坏了其自然的“局外人”条件。根据经验，无论您使用的是 DIC 还是服务定位器，都要确保这些元素能够发挥其应有的作用，而不会互相影响。

## 结束语

过去，管理类依赖关系只是将一些“新”操作符放入臃肿的构造函数中，现在看来，这种糟糕的日子终于要过去了。相比之下，以依赖注入为首的新兴模式组合正在强势渗透到 PHP 的每一个角落，这种范式转变已经对现有的几个代码库的质量产生了有益的影响。

尽管如此，大问题仍然在循环中浮动:DIC，服务定位器，注入工厂…哪一个最终最符合要求？正如我之前说过的，做出正确的决定很大程度上取决于你首先在处理什么。在所有情况下，它们只是控制反转的变体，用一些很好的改进和花哨的混合来装饰。你知道 IoC 是多态性的必由之路，因此也是可测试性的必由之路。让你的个人需求成为你最自信的顾问；他们不会让你失望的。

图片 via[SvetlanaR](http://www.shutterstock.com/gallery-109858p1.html)/[Shutterstock](http://shutterstock.com)

## 分享这篇文章