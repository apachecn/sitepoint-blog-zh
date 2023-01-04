# 管理类依赖:依赖注入、服务定位器和工厂介绍，第 1 部分

> 原文：<https://www.sitepoint.com/managing-class-dependencies-1/>

让我们面对它:不管是好是坏，在过去的几年里，OOP 已经积极地在 PHP 的土壤上钻了很深的洞，因此它现在无处不在已经不是什么新闻了。此外，虽然这种语言领域中的稳步增长的“对象化”通常被认为是朝着新的和更有前途的前景的有益转变，但对于那些仍然植根于过程范式的人来说，这个过程远不是无害的。

有几个坚实的论据支持这种思维模式:首先，OOP 的本质是笨拙的，本质上是复杂的，充满了细微的差别，可能需要几年才能驯服。此外，定义面向对象组件用于相互交互的 API 有时会是一个很大的负担，尤其是当设计的系统的主干依赖于由庞大而复杂的[对象图](http://en.wikipedia.org/wiki/Object_graph)提供的设施时。

从程序员的角度来看，设计易于使用的 API，同时仍然保持所涉及的类之间良好的解耦水平，这始终是一个棘手的权衡，因为依赖关系越有表现力，就越难以在幕后完成按照正确的顺序连接协作者的工作。这个事实本身就是一个从很久以前就一直困扰着开发者头脑的困境，随着时间的推移当然更难消化。随着应用程序变得越来越臃肿，在它们的边界内包含越来越多的类，构建干净的声明性 API 不再只是一种松散的实践，最终可以不时地被推入语言的主流。简直是必须的。

这引发了一些有趣的问题:如何为一个类提供依赖关系，而不弄乱它的 API，或者更糟糕的是，不把它耦合到依赖关系本身？仅仅通过呼吁[依赖注入](http://en.wikipedia.org/wiki/Dependency_injection)的好处？通过注入[服务定位器](http://en.wikipedia.org/wiki/Service_locator_pattern)的统治？

由于显而易见的原因，没有一个放之四海而皆准的解决方案，即使前面提到的所有方法都有共同点。是的，在某种程度上，它们都利用了某种形式的控制反转，这不仅允许您从类中解耦依赖关系，还在依赖关系的接口和实现之间保持了一个整洁的隔离级别，使得模拟/测试变得轻而易举。

很明显，这个话题一点也不平庸，因此值得进行深入细致的分析。在这个由两部分组成的系列文章中，我将对现代应用程序开发中管理类依赖关系的一些最常用的方法进行快速总结，从使用服务定位器和可注入工厂，到深入研究普通的依赖注入，甚至实现一个简单的依赖注入容器。

## (有希望)消失的瘟疫——构造函数中的“新”操作符

这个咒语确实有些古老，但是它的声明仍然响亮而清晰:“在构造函数中放置‘新’操作符是非常邪恶的。”我们现在都知道了，甚至想当然地认为我们永远不会犯这样的大罪。但是在依赖注入进入 PHP 世界之前，这实际上是一个类多年来用来查找其依赖关系的默认方法。为了完整起见，让我们重现这个笨重的老式场景，并提醒自己为什么要远离这种有害的瘟疫。

假设我们需要构建一个简单的文件存储模块，该模块必须在内部使用一个序列化程序，以便对特定文件读写数据。序列化程序的实现如下所示:

```
<?php
namespace LibraryEncoder;

class Serializer implements Serializable
{
    public function serialize($data) {
        if (is_resource($data)) {
            throw new InvalidArgumentException(
                "PHP resources are not serializable.");
        }
        if (($data = serialize($data)) === false) {
            throw new RuntimeException(
                "Unable to serialize the supplied data.");
        }
        return $data;
    }

    public function unserialize($data) {
        if (!is_string($data)|| empty($data)) {
            throw new InvalidArgumentException(
                "The data to be unserialized must be a non-empty string.");
        }
        if (($data = @unserialize($data)) === false) {
            throw new RuntimeException("Unable to unserialize the supplied data."); 
        }
        return $data;
    }
}
```

`Serializer`类只是本机[可序列化接口](http://www.php.net/manual/en/class.serializable.php)的轻量级实现，并向外界公开典型的序列化/取消序列化二重奏。有了这个精心设计的策略类，前面提到的文件存储模块就可以像下面这样随意编码了:

```
<?php
namespace LibraryFile;
use LibraryEncoderSerializer;

class FileStorage
{
    const DEFAULT_STORAGE_FILE = "data.dat";
    protected $serializer;
    protected $file;

    public function __construct($file = self::DEFAULT_STORAGE_FILE) {
        $this->setFile($file);
        $this->serializer = new Serializer();
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

`FileStorage`的行为归结起来就是从预定义的目标文件中保存和提取数据。但是该类看似良性的本质只不过是一种假象，因为它公然在其构造函数中实例化了序列化程序！

这种形式的“独裁”依赖关系查找不仅在类和它的依赖关系之间引入了强耦合，而且使可测试性很快死亡。以下代码显示了使用这种方法时工件如何波及到客户端代码:

```
<?php
$fileStorage = new FileStorage();
$fileStorage->write("This is a sample string.");
echo $fileStorage->read();
```

放心运行代码，它会像一个魔咒，这是肯定的。但是我们知道这种耦合就在表面之下，更不用说没有任何线索表明`FileStorage`是否对另一个组件有内部依赖性，或者这个组件是如何获得的。毫无疑问，这很像是一个反模式，它违背了定义干净、有表现力的类 API 的初衷。

还有一些额外的方法值得研究，它们可以改进前面两个类的互操作方式。例如，我们可以更大胆一点，将工厂直接注入到`FileStorage`的内部，并在必要时让它创建一个序列化对象，从而使类的依赖性更加明确。

## 通过注入的工厂推迟依赖项的创建

任何形式的工厂都是漂亮的元素，允许我们从应用程序逻辑中很好地提取对象构造。虽然这种能力本身就很了不起，但如果与依赖注入的优点结合起来，它还可以更进一步。

如果你像我一样好奇，你会想知道如何利用工厂提供的好处来增强早期的`FileStorage`类查找其合作者的方式，在其构造函数中去掉臭名昭著的“new”操作符。在一个非常基本的层次上，查找过程可以通过下面的工厂类来重新表述:

```
<?php
namespace LibraryDependencyInjection;

interface SerializerFactoryInterface
{
    public function getSerializer();
}
```

```
<?php
namespace LibraryDependencyInjection;
use LibraryEncoderSerializer;

class SerializerFactory implements SerializerFactoryInterface
{   
    public function getSerializer() [
        static $serializer;
        if ($serializer === null) {
            $serializer = new Serializer;
        }
        return $serializer;
    }
}
```

有了负责按需创建序列化程序的动态工厂，现在可以重构`FileStorage`类来接受工厂实现者:

```
<?php
namespace LibraryFile;
use LibraryDependencyInjectionSerializerFactoryInterface;

class FileStorage
{
    const DEFAULT_STORAGE_FILE = "data.dat";
    protected $factory;
    protected $file;

    public function __construct(SerializerFactoryInterface $factory, $file = self::DEFAULT_STORAGE_FILE) {
        $this->setFile($file);
        $this->factory = $factory;
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
            return $this->factory->getSerializer()->unserialize(
                @file_get_contents($this->file));
        }
        catch (Exception $e) {
            throw new Exception($e->getMessage());
        }
    }

    public function write($data) {
        try {
            return file_put_contents($this->file,
                $this->factory->getSerializer()->serialize($data));
        }
        catch (Exception $e) {
            throw new Exception($e->getMessage());
        }
    }
}
```

`FileStorage`的功能基本保持不变，但是通过注入`SerializerFactoryInterface`的实现者打破了与序列化器的紧密耦合。这个简单的变化将这个类变成了一个灵活且可测试的生物，它公开了一个更具表现力的 API，因为现在从外部世界可以更容易地看到它需要什么依赖关系。下面的代码从客户端代码的角度展示了这些增强的结果:

```
<?php
$fileStorage = new FileStorage(new SerializerFactory());
$fileStorage->write("This is a sample string.");
echo $fileStorage->read();
```

不过，我们不要陷入盲目、毫无意义的乐观。公平地说，存储模块的重构实现看起来更有吸引力；这种方法使得在运行时切换不同的工厂实现变得很简单。但在我看来，至少在这种情况下，这是对 Demeter 的[定律的违背，因为注入的工厂对模块的真正合作者来说就像一个不必要的中介。](http://en.wikipedia.org/wiki/Law_of_Demeter)

这并不意味着注入的工厂是一件坏事。但是在大多数情况下，它们应该只用于按需创建依赖项，特别是当依赖项的生命周期比使用它们的客户端类的生命周期短的时候。

## 结束语

管理类依赖关系无疑是面向对象设计的一个中心点，它的底层逻辑肯定比在工厂中删除几个“新”操作符要深刻得多，或者更糟糕的是，在吝啬的构造函数中，这些构造函数对外界隐藏了这些依赖关系，因此它们不能被随意地正确解耦或孤立地测试。

没有必要觉得一切都失去了，但是，有一些额外的方法可以用来以有效的方式处理为类提供它们的合作者的方式，包括使用服务定位器模式、原始依赖注入，甚至利用依赖注入容器的好处。这些都将在后续中详细介绍[，敬请期待！](https://www.sitepoint.com/managing-class-dependencies-2)

图片 via[SvetlanaR](http://www.shutterstock.com/gallery-109858p1.html)/[Shutterstock](http://shutterstock.com)

## 分享这篇文章