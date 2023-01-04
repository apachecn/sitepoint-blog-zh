# 依存倒置原则

> 原文：<https://www.sitepoint.com/dip-intro/>

如果我曾经不得不就每个[坚实原则](http://en.wikipedia.org/wiki/SOLID_(object-oriented_design))的相关性做出一个特别的“所罗门式”决定，我敢说[依赖倒置原则](http://en.wikipedia.org/wiki/Dependency_inversion_principle) (DIP)是所有原则中最被低估的。

虽然面向对象设计领域中的一些核心概念开始时通常很难理解，比如关注点分离和实现切换，但另一方面，更直观、更清晰的范例更简单，比如接口编程。不幸的是，DIP 的正式定义被一把双刃剑所包围，经常让程序员忽略它，因为在许多情况下，有一个隐含的假设，即该原则只不过是前面提到的“编程到接口”戒律的一个花哨表达:

1.  高层模块不应该依赖低层模块。两者都应该依赖于抽象。
2.  抽象不应该依赖于细节。细节应该依赖于抽象。

乍一看，上述说法似乎不言自明。考虑到在这一点上，没有人会不同意构建在对具体实现的强烈依赖之上的系统是糟糕设计的不祥之兆，切换一些抽象是完全有意义的。所以这将把我们带回到起点，认为 DIP 主要关心的是接口编程。

实际上，当涉及到满足原则的需求时，将接口从实现中解耦只是一种不成熟的方法。缺失的部分正在实现真正的反转过程。当然，出现的问题是:反转什么？

从传统意义上来说，系统总是被设计成与高层组件一起工作，无论是类还是过程例程，都依赖于低层组件(细节)。例如，一个日志模块可能对一组具体的日志记录器有很强的依赖性，这些记录器实际上将信息记录到系统中。毫不奇怪，每当记录器拥有的协议被修改时，即使该协议已经被抽象掉，这种方案也会对上层产生很大的副作用。

然而，DIP 的实现有助于在一定程度上减轻这些波动，方法是让日志记录模块拥有协议，从而反转整体依赖关系的流程。在反转时，记录器应该忠实地遵守协议，因此相应地改变，并且如果进一步发生波动，则适应其波动。

简而言之，这表明 DIP 在本质上比仅仅依靠标准接口实现解耦带来的好处要稍微复杂一些。是的，它谈到了使高级和低级模块都依赖于抽象，但同时高级模块必须拥有这些抽象的所有权——这是一个微妙但相关的细节，不能这么容易被忽略。

如您所料，有一种方法可以帮助您更容易地理解 DIP 保护伞下的实际情况，那就是通过一些实际操作的代码示例。因此，在这篇文章中，我将设置一些例子，以便您可以学习如何在开发 PHP 应用程序时最大限度地利用这一坚实的原则。

## 开发一个简单的存储模块(DIP 中缺少的“I ”)

有许多开发人员，尤其是那些厌恶面向对象 PHP 冰冷海水的开发人员，倾向于将 DIP 和其他坚实的原则视为严格的教条，与该语言固有的实用主义背道而驰。我可以理解这种想法，因为很难在野外找到实用的 PHP 示例来展示这一原则的真正好处。这并不是说我想把自己标榜成一个开明的程序员(那套衣服不太合适)，但是为一个好的目标奋斗并从实践的角度演示如何在一个现实的用例中实现 DIP 是有用的。

首先，考虑一个简单文件存储模块的实现。该模块负责从指定的目标文件读取数据和向指定的目标文件写入数据。在一个非常简单的层次上，我们讨论的模块可以写成这样:

```
<?php
namespace LibraryEncoderStrategy;

class Serializer implements Serializable
{
    protected $unserializeCallback;

    public function __construct($unserializeCallback = false)  {
        $this->unserializeCallback = (boolean) $unserializeCallback;
    }

    public function getUnserializeCallback() {
        return $this->unserializeCallback;
    }

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
        if (!is_string($data) || empty($data)) {
            throw new InvalidArgumentException(
                "The data to be decoded must be a non-empty string.");
        }

        if ($this->unserializeCallback) {
            $callback = ini_get("unserialize_callback_func");
            if (!function_exists($callback)) {
                throw new BadFunctionCallException(
                    "The php.ini unserialize callback function is invalid.");
            }
        }

        if (($data = @unserialize($data)) === false) {
            throw new RuntimeException(
                "Unable to unserialize the supplied data."); 
        }

        return $data;
    }
}
```

```
<?php
namespace LibraryFile;

class FileStorage
{
    const DEFAULT_STORAGE_FILE = "default.dat";

    protected $serializer; 
    protected $file;

    public function __construct(Serializable $serializer, $file = self::DEFAULT_STORAGE_FILE) {
        $this->serializer = $serializer;
        $this->setFile($file); 
    }

    public function getSerializer() {
        return $this->serializer;
    }

    public function setFile($file) {
        if (!is_file($file) || !is_readable($file)) {
            throw new InvalidArgumentException(
                "The supplied file is not readable or writable.");
        }

        $this->file = $file;
        return $this;
    }

    public function getFile() {
        return $this->file;
    }

    public function resetFile() {
        $this->file = self::DEFAULT_STORAGE_FILE;
        return $this; 
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

    public function read()
    {
        try {
            return $this->serializer->unserialize(
                @file_get_contents($this->file));

        }
        catch (Exception $e) {
            throw new Exception($e->getMessage());
        }
    }
}
```

该模块是一个非常简单的结构，仅由几个基本组件组成。第一个类向文件系统读写数据，第二个类是一个简单的 PHP 序列化程序，在内部用来生成数据的可存储表示。

这些示例组件整齐地独立工作，并且可以连接在一起同步工作，如下所示:

```
<?php
use LibraryLoaderAutoloader,
    LibraryEncoderStrategySerializer,
    LibraryFileFileStorage;    

require_once __DIR__ . "/Library/Loader/Autoloader.php";
$autoloader = new Autoloader;
$autoloader->register();

$fileStorage = new FileStorage(new Serializer); 
$fileStorage->write(new stdClass());
print_r($fileStorage->read());

$fileStorage->write(array("This", "is", "a", "sample", "array"));
print_r($fileStorage->read());

$fileStorage->write("This is a sample string.");
echo $fileStorage->read();
```

乍一看，该模块表现出相当不错的行为，因为它的功能允许毫不费力地从文件系统中保存和获取各种数据。此外，`FileStorage`类在构造函数中注入了一个`Serializable`接口，因此依赖于抽象提供的灵活性，而不是严格的具体实现。有了这些好处，模块会有什么问题呢？

和往常一样，肤浅的第一印象可能会很棘手，很模糊。仔细观察，不仅可以清楚地看到`FileStorage`实际上依赖于序列化程序，而且由于这种紧密的依赖性，从目标文件存储和提取数据被限制为只能使用 PHP 的本地序列化机制。如果数据必须以 XML 或 JSON 的形式传递给外部服务，会发生什么？精心制作的模块不再可重用。悲伤却真实！

这种情况引出了几个有趣的问题。首先也是最重要的一点是，`FileStorage`仍然表现出对底层`Serializer`的强烈依赖，即使使它们相互操作的协议已经从实现中分离出来。其次，所讨论的协议公开的泛型级别非常严格，仅限于用一个序列化程序替换另一个序列化程序。在这种情况下，不仅依赖抽象是一种错觉，而且 DIP 所鼓励的真正的反转过程永远不会实现。

可以重构文件模块的某些部分，使其忠实地遵守 DIP 的要求。在这种情况下，`FileStorage`类将获得用于存储和拉入文件数据的协议的所有权，从而摆脱对低级序列化程序的依赖，并为您提供在运行时在几种存储策略之间切换的能力。这样做，你实际上可以免费获得很多灵活性。因此，让我们继续下去，看看如何将文件存储模块转变成真正符合 DIP 的结构。

## 转换协议所有权并将接口从实现中分离出来(充分利用 DIP)

虽然没有太多的选项*本身*，但是有一些方法可以用来有效地转换`FileStorage`类和它的低级协作者之间的协议所有权，同时仍然保持协议的抽象性。不过，有一个特别之处，它非常直观，因为它依赖于 PHP 名称空间提供的开箱即用的自然封装。

要将这个有点难以理解的概念转化为具体的代码，应该对模块进行的第一个更改是定义一个更宽松的协议来保存和检索文件数据，这样就可以很容易地通过格式而不仅仅是 PHP 序列化来操作它。

如下图所示，一个细长的分离式界面优雅而简洁:

```
<?php
namespace LibraryFile;

interface EncoderInterface
{
    public function encode($data);
    public function decode($data);
}
```

`EncoderInterface`的存在似乎并没有对文件模块的整体设计产生深远的影响，但是它所做的比表面上所承诺的要多得多。第一个改进是为编码和解码数据定义了一个高度通用的协议。第二点与第一点同样重要，即现在协议的所有权属于`FileStorage`类，因为接口存在于类的名称空间中。简而言之，我们已经设法通过编写一个适当命名空间的接口，使仍然未定义的低级编码器/解码器依赖于高级`FileStorage`。一言以蔽之，这就是 DIP 在其学术面纱背后推动的实际反转过程。

自然地，如果没有修改`FileStorage`类来注入前一个接口的实现者，反转将是一个笨拙的中途尝试，所以这里是重构后的版本:

```
<?php
namespace LibraryFile;

class FileStorage
{
    const DEFAULT_STORAGE_FILE = "default.dat";

    protected $encoder; 
    protected $file;

    public function __construct(EncoderInterface $encoder, $file = self::DEFAULT_STORAGE_FILE) {
        $this->encoder = $encoder;
        $this->setFile($file); 
    }

    public function getEncoder() {
        return $this->encoder;
    }

    public function setFile($file) {
        if (!is_file($file) || !is_readable($file)) {
            throw new InvalidArgumentException(
                "The supplied file is not readable or writable.");
        }

        $this->file = $file;
        return $this;
    }

    public function getFile() {
        return $this->file;
    }

    public function resetFile() {
        $this->file = self::DEFAULT_STORAGE_FILE;
        return $this; 
    }

    public function write($data) {
        try {
           return file_put_contents($this->file,
               $this->encoder->encode($data));    

        }
        catch (Exception $e) {
            throw new Exception($e->getMessage());
        }
    }

    public function read() {
        try {
            return $this->encoder->decode(
                @file_get_contents($this->file));

        }
        catch (Exception $e) {
            throw new Exception($e->getMessage());
        }
    }
}
```

现在`FileStorage`在构造函数中明确声明了编码/解码协议的所有权，剩下唯一要做的就是创建一组具体的低级编码器/解码器，从而允许您处理多种格式的文件数据。

第一个组件只是之前编写的 PHP 序列化程序的重构实现:

```
<?php
namespace LibraryEncoderStrategy;

class Serializer implements LibraryFileEncoderInterface
{
    protected $unserializeCallback;

    public function __construct($unserializeCallback = false) {
        $this->unserializeCallback = (boolean) $unserializeCallback;
    }

    public function getUnserializeCallback() {
        return $this->unserializeCallback;
    }

    public function encode($data) {
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

    public function decode($data) {
        if (!is_string($data) || empty($data)) {
            throw new InvalidArgumentException(
                "The data to be decoded must be a non-empty string.");
        }

        if ($this->unserializeCallback) {
            $callback = ini_get("unserialize_callback_func");
            if (!function_exists($callback)) {
                throw new BadFunctionCallException(
                    "The php.ini unserialize callback function is invalid.");
            }
        }

        if (($data = @unserialize($data)) === false) {
            throw new RuntimeException("Unable to unserialize the supplied data."); 
        }

        return $data;
    }
}
```

剖析`Serializer`背后的逻辑肯定是多余的。尽管如此，值得指出的是，它现在不仅依赖于更宽松的编码/解码抽象，而且抽象的所有权在名称空间级别上是显式公开的。

同样，我们可以更进一步，开始编写更多的编码器，以便突出 DIP 带来的好处。也就是说，下面是如何编写另一个底层组件的方法:

```
<?php
namespace LibraryEncoderStrategy;

class JsonEncoder implements LibraryFileEncoderInterface
{
    public function encode($data) {
        if (is_resource($data)) {
            throw new InvalidArgumentException(
                "PHP resources cannot be JSON-encoded.");
        }

        if (($data = json_encode($data)) === false) {
            throw new RuntimeException(
                "Unable to JSON-encode the supplied data.");
        }

        return $data;
    }

    public function decode($data) {
        if (!is_string($data) || empty($data)) {
            throw new InvalidArgumentException(
                "The data to be decoded must be a non-empty string.");
        }

        if (($data = json_decode($data)) === false) {
            throw new RuntimeException(
                "Unable to JSON-decode the supplied data.");
        }

        return $data;
    }
}
```

正如所料，额外编码器背后的底层逻辑通常类似于第一个 PHP 序列化程序，除了任何明显的改进和变体。此外，组件符合 DIP 强加的要求，因此符合在`FileStorage`名称空间中定义的编码/解码协议。

由于文件模块中的高级和低级组件都依赖于一个抽象，并且编码器暴露了对文件存储类的明显依赖，我们可以有把握地宣称该模块表现为一个真正的符合 DIP 的结构。

此外，以下示例显示了如何将组件组合在一起:

```
<?php
use LibraryLoaderAutoloader,
    LibraryEncoderStrategyJsonEncoder,
    LibraryEncoderStrategySerializer,
    LibraryFileFileStorage;    

require_once __DIR__ . "/Library/Loader/Autoloader.php";
$autoloader = new Autoloader;
$autoloader->register();

$fileStorage = new FileStorage(new JsonEncoder); 
$fileStorage->write(new stdClass());
print_r($fileStorage->read());

$fileStorage = new FileStorage(new Serializer);
$fileStorage->write(array("This", "is", "a", "sample", "array"));
print_r($fileStorage->read());
```

除了模块暴露给客户端代码的一些天真的微妙之处，它对于阐明观点和以一种非常有启发性的方式演示为什么 DIP 的谓词实际上比旧的“接口编程”范式更广泛是有用的。它描述并明确规定了依赖关系的反转，因此，它应该通过不同的机制来实现。PHP 的名称空间是一种无需过多负担就能实现这一点的好方法，即使像定义结构良好、富有表现力的应用程序布局这样的传统方法也能产生相同的结果。

## 结束语

总的来说，基于主观专业知识的观点往往是有偏见的，当然，我在本文开头表达的观点也不例外。然而，有一种轻微的倾向忽略了依赖倒置原则，而倾向于它更复杂的坚实的对应物，因为它很容易被误解为抽象依赖的同义词。此外，一些程序员倾向于凭直觉做出反应，认为术语“反转”是对[控制反转](http://en.wikipedia.org/wiki/Inversion_of_control)的缩写表达，尽管它们彼此相关，但最终是一个错误的概念。

既然您已经知道 DIP 的真正本质，请确保利用它带来的所有好处，这些好处肯定会使您的应用程序不那么容易受到随着时间的推移最终可能出现的脆弱性和刚性问题的影响。

<small>图片通过[肯托](http://www.shutterstock.com/gallery-168430p1.html) / [快门](http://shutterstock.com)</small>

## 分享这篇文章