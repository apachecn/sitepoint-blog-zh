# 德米特里定律介绍

> 原文：<https://www.sitepoint.com/introduction-to-the-law-of-demeter/>

软件编程是艺术(有时是即兴创作的委婉说法)和一堆被证明有效的启发法的平衡结合，这些启发法用于处理某些问题并以体面的方式解决它们。随着时间的推移，艺术方面是最难打磨和提炼的，这一点很少有人会不同意。另一方面，驯服启发背后的力量是能够开发基于良好设计基础的软件的基础。

有这么多试探法说明软件系统应该如何以及为什么坚持一种特定的方法，但是在 PHP 世界中没有看到它们更广泛的实现是非常令人失望的。例如，德米特律法可能是语言领域中最被低估的一个。

实际上，法律的“与你最亲密的朋友交谈”的咒语在 PHP 中似乎仍然处于相当不成熟的状态，这导致了几个面向对象代码库的整体质量的恶化。一些流行的框架正在积极地推动它，试图更好地遵守法律的戒律。指责违反了 Demeter 定律是没有意义的，因为减轻这种破坏的最好方法是简单地务实，理解在定律的保护下实际上是什么，从而在编写面向对象的代码时有意识地应用它。

为了加入这一正义的事业，并从实用的角度更深入地研究这一法则，在接下来的几行中，我将通过一些实际操作的例子来演示，为什么在设计松散耦合的软件模块时，像遵守法则这样简单的事情可以成为真正的推动力。

## 知道得太多不是一件好事

通常被称为最少知识原则，德米特法则所提倡的规则很容易消化。简而言之，假设您有一个实现给定方法的制作精美的类，所讨论的方法应该被约束为调用属于以下对象的其他方法:

1.  方法的原始类的实例。
2.  作为目标方法参数的对象。
3.  由目标方法创建的对象。
4.  对象，这些对象是该方法的原始类的依赖项。
5.  全局对象(哎哟！)可以由目标方法中的原始类访问。

尽管这份清单与正式的清单相去甚远(如果需要更正式的清单，请查看[维基百科](http://en.wikipedia.org/wiki/Law_of_Demeter "Law of Demeter - Wikipedia, the free encyclopedia"))，但要点非常容易理解。

在传统的设计中，一个对象对另一个对象知道得太多(这隐含地包括知道如何访问第三个对象)的事实被认为是错误的，因为在有些情况下，对象必须不必要地从上到下遍历一个笨拙的中介来找到它需要按预期工作的实际依赖关系。很明显，这是一个严重的设计缺陷。调用者对中介的内部结构有相当广泛和详细的了解，即使这个结构是通过几个 getters 访问的。

此外，使用一个中间对象来访问调用者所需要的对象，这本身就是一个声明。毕竟，如果通过直接注入依赖项可以获得相同的结果，为什么要使用如此复杂的路径来获取依赖项或调用其方法之一呢？过程一点意义都没有。

比方说，我们需要建立一个文件存储模块，它在内部使用多态编码器将数据拉入并保存到给定的目标文件中。如果我们故意马虎，将模块连接到一个可注入的服务定位器，它的实现将如下所示:

```
<?php
namespace LibraryFile;
use LibraryDependencyInjectionServiceLocatorInterface;

class FileStorage
{
    const DEFAULT_STORAGE_FILE = "data.dat";
    private $locator;
    private $file;

    public function __construct(ServiceLocatorInterface $locator, $file = self::DEFAULT_STORAGE_FILE)  {
        $this->locator = $locator;
        $this->setFile($file);
    }

    public function setFile($file) {
        if (!is_readable($file) || !is_writable($file)) {
            throw new InvalidArgumentException(
                "The target file is invalid.");
        }
        $this->file = $file;
        return $this;
    }

    public function write($data) {
        try {
            return file_put_contents($this->file, 
                $this->locator->get("encoder")->encode($data),
                LOCK_EX);
        }
        catch (Exception $e) {
            throw new $e(
                "Error writing data to the target file: " . 
                $e->getMessage());
        }
    }

    public function read() {
        try {
            return $this->locator->get("encoder")->decode(
                @file_get_contents($this->file));
        }
        catch(Exception $e) {
            throw new $e(
                "Error reading data from the target file: " .
                $e->getMessage());
        }
    }
}
```

抛开一些不相关的实现细节，重点放在`FileStorage`类的构造函数及其`write()`和`read()`方法上。该类注入了一个仍未定义的服务定位器的实例，稍后将使用该实例来获取一个依赖项(前面提到的编码器),以便在目标文件中获取和存储数据。

考虑到类首先通过定位器，然后到达编码器，这是对 Demeter 定律的典型侵犯。调用者对定位器的内部结构知道得太多了，包括如何访问编码器，这绝对不是我会称赞的能力。这是一个从本质上根植于服务定位器(这就是为什么有些人把它们视为反模式)或任何其他类型的静态或动态注册中心的人工制品，这是我在之前[指出的。](https://www.sitepoint.com/managing-class-dependencies-2/ "Managing Class Dependencies: An Introduction to Dependency Injection, Service Locators, and Factories, Part 2")

为了更全面地了解这个问题，让我们检查一下定位器的实现:

```
<?php
namespace LibraryDependencyInjection;

interface ServiceLocatorInterface
{
    public function set($name, $service);
    public function get($name);
    public function exists($name);
    public function remove($name);
    public function clear();
}
```

```
<?php
namespace LibraryDependencyInjection;

class ServiceLocator implements ServiceLocatorInterface
{
    private $services = [];

    public function set($name, $service) {
        if (!is_object($service)) {
            throw new InvalidArgumentException(
                "Only objects can register with the locator.");
        }
        if (!in_array($service, $this->services, true)) {
            $this->services[$name] = $service;
        }
        return $this;
    }

    public function get($name) {
        if (!$this->exists($name)) {
            throw new InvalidArgumentException(
                "The requested service is not registered.");
        }
        return $this->services[$name];
    }

    public function exists($name) {
        return isset($this->services[$name]);
    }

    public function remove($name) {
        if (!$this->exists($name)) {
            throw new InvalidArgumentException(
                "The requested service is not registered.");
        }
        unset($this->services[$name]);
        return $this;
    }

    public function clear() {
        $this->services = [];
        return $this;
    }
}
```

在这种情况下，我将定位器实现为一个普通的动态注册表，没有额外的铃声或哨声，因此很容易理解。如果你有心情，你可以用一些额外的功能来装饰它。

我们必须做的最后一件事是创建至少一个相应编码器的具体实现，这样我们就可以让文件存储类工作了。这个类应该可以很好地完成这个任务:

```
<?php
namespace LibraryEncoder;

interface EncoderInterface
{
    public function encode($data);
    public function decode($data);
}
```

```
<?php
namespace LibraryEncoder;

class Serializer implements EncoderInterface
{
    public function encode($data) {
        if (is_resource($data)) {
            throw new InvalidArgumentException(
                "PHP resources are not serializable.");
        }
        if (($data = serialize($data)) === false) {
            throw new RuntimeException(
                "Unable to serialize the data.");
        }
        return $data;
    }

    public function decode($data) {
        if (!is_string($data)|| empty($data)) {
            throw new InvalidArgumentException(
                "The data to be unserialized must be a non-empty string.");
        }
        if (($data = @unserialize($data)) === false) {
            throw new RuntimeException(
                "Unable to unserialize the data."); 
        }
        return $data;
    }
}
```

有了编码器集，现在让我们一起使用所有的示例类:

```
<?php
use LibraryLoaderAutoloader,
    LibraryEncoderSerializer,
    LibraryDependencyInjectionServiceLocator,
    LibraryFileFileStorage;

require_once __DIR__ . "/Library/Loader/Autoloader.php";

$autoloader = new Autoloader();
$autoloader->register();

$locator = new ServiceLocator();
$locator->set("encoder", new Serializer());

$fileStorage = new FileStorage($locator);
$fileStorage->write(["This", "is", "my", "sample", "array"]);

print_r($fileStorage->read());
```

在这种情况下，违反法律是一个相当隐秘的问题，很难从表面上追踪到，除了定位器的 mutator 的使用，这表明在某个时候编码器将被`FileStorage`的实例以某种形式访问和消费。不管怎样，我们知道对外部世界来说，侵犯是隐藏的，这一事实不仅揭示了定位器结构的太多信息，而且不必要地将`FileStorage`类与定位器本身联系起来。

只要坚持法律的规则，摆脱定位器，我们就可以消除耦合，同时为`FileStorage`提供它开展业务所需的实际合作者。一路上不再有笨拙的，透露的调解人！

幸运的是，只需一点点努力，所有这些胡言乱语就可以很容易地转化为工作代码。只需点击这里查看增强版的符合 Demeter 定律的`FileStorage`类:

```
<?php
namespace LibraryFile;
use LibraryEncoderEncoderInterface;

class FileStorage
{
    const DEFAULT_STORAGE_FILE = "data.dat";
    private $encoder;
    private $file;

    public function __construct(EncoderInterface $encoder, $file = self::DEFAULT_STORAGE_FILE)  {
        $this->encoder = $encoder;
        $this->setFile($file);
    }

    public function setFile($file) {
        // the sample implementation
    }

    public function write($data) {
        try {
            return file_put_contents($this->file, 
                $this->encoder->encode($data), LOCK_EX);
        }
        catch (Exception $e) {
            throw new $e(
                "Error writing data to the target file: " .
                $e->getMessage());
        }
    }

    public function read() {
        try {
            return $this->encoder->decode(
                @file_get_contents($this->file));
        }
        catch(Exception $e) {
            throw new $e(
                "Error reading data from the target file: " .
                $e->getMessage());
        }
    }
}
```

事实上，这很容易重构。现在，该类直接使用`EncoderInterface`接口的任何实现者，避免了遍历不必要的中间体的内部。这个例子毫无疑问是琐碎的，但它确实提出了一个有效的观点，并展示了为什么遵守 Demeter 的戒律是改进类设计的最好方法之一。

尽管如此，仍有一个特殊的法律案例，在罗伯特·马丁的书《干净的代码:[敏捷软件工艺手册](http://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882 "Clean Code: A Handbook of Agile Software Craftmanship: Robert C. Martin: 9780123250885: Amazon.com: Books")中有深入的介绍，值得特别分析。请想一想:如果文件存储被定义为通过一个数据传输对象(d to)获取它的协作者，会发生什么情况，就像这样？

```
<?php
namespace LibraryFile;

interface FileStorageDefinitionInterface
{
    public function getEncoder();
    public function getFile();
}
```

```
<?php
namespace LibraryFile;
use LibraryEncoderEncoderInterface;

class FileStorageDefinition implements FileStorageDefinitionInterface
{
    const DEFAULT_STORAGE_FILE = "data.dat";
    private $encoder;
    private $file;

    public function __construct(EncoderInterface $encoder, $file = self::DEFAULT_STORAGE_FILE)  {
        if (!is_readable($file) || !is_writable($file)) {
            throw new InvalidArgumentException(
                "The target file is invalid.");
        }
        $this->encoder = $encoder;
        $this->file = $file;
    }

    public function getEncoder()  {
        return $this->encoder;
    }

    public function getFile()  {
        return $this->file;
    }
}
```

```
<?php
namespace LibraryFile;

class FileStorage
{
    private $storageDefinition;

    public function __construct(FileStorageDefinitionInterface $storageDefinition) {
        $this->storageDefinition = $storageDefinition;
    }

    public function write($data) {
        try {
            return file_put_contents(
                $this->storageDefinition->getFile(), 
                $this->storageDefinition->getEncoder()->encode($data),
                LOCK_EX
            );
        }
        catch (Exception $e) {
            throw new $e(
                "Error writing data to the target file: " . 
                $e->getMessage());
        }
    }

    public function read() {
        try {
            return $this->storageDefinition->getEncoder()->decode(
                @file_get_contents($this->storageDefinition->getFile())
            );
        }
        catch(Exception $e) {
            throw new $e(
                "Error reading data from the target file: " .
                $e->getMessage());
        }
    }
}
```

这绝对是实现文件存储类的一个有趣的倾向，因为它现在使用一个可注入的 DTO 在内部传输和使用编码器。需要回答的问题是，这种方法是否真的违反了法律。在纯粹的意义上，它确实如此，因为 DTO 毫无疑问是一个仲裁者，向调用者展示它的整个结构。然而，DTO 只是一个普通的数据结构，与早期的服务定位器不同，它没有任何行为。确切地说，数据结构的目的是…是的，公开它的数据。这意味着，只要中介不实现行为(这与常规类的行为正好相反，因为它在隐藏数据的同时公开了行为)，Demeter 法则将保持整洁。

下面的代码片段显示了如何将`FileStorage`与相关的 DTO 一起使用:

```
<?php
$fileStorage = new FileStorage(new FileStorageDefinition(new Serializer()));
$fileStorage->write(["This", "is", "my", "sample", "array"]);

print_r($fileStorage->read());
```

这种方法比直接将编码器传递给文件存储类要麻烦得多，但是这个例子表明，一些棘手的实现乍看起来似乎是公然违反法律的，但一般来说，只要它们使用的数据结构不附带任何行为，它们是无害的。

## 结束语

随着各种各样错综复杂的、有时是深奥的试探法在 OOP 中不断涌现，再增加一个试探法似乎毫无意义，这显然对层组件的设计没有任何可见的积极影响。然而，得墨忒耳定律只是一个在现实世界中很少或根本没有应用的原则。

尽管它的名字很华丽，但 Demeter 法则是一个强大的范例，它的主要目标是通过消除任何不必要的中介来促进高度解耦的应用程序组件的实现。只要遵循它的戒律，当然不要陷入盲目的教条主义，你会看到你的代码质量提高了。保证。

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章