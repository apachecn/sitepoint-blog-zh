# 子类型多态性——运行时的交换实现

> 原文：<https://www.sitepoint.com/subtype-polymophism/>

有多少人怀疑面向对象设计中继承和多态的相关性？可能很少，很可能是因为无知或者狭隘的心态。但是这里有一个不容易被忽视的小问题。虽然理解控制继承的逻辑很简单，但是当深入研究多态性的具体细节时，事情就变得更加困难了。

术语多态性本身就令人生畏，它的学术定义被几个完全不同的倾斜所困扰，这使得它更难理解其真正的含义。一些外围概念，如参数多态性和特别多态性，通常通过方法覆盖/重载来实现，在一些编程语言中确实有丰富的应用，但当涉及到设计能够消费特定契约(读取抽象)而不必检查实现者是否属于预期类型的可扩展系统时，应该放弃最后一个实例。

简而言之，在大多数情况下，对 OOP 中多态性的任何一般引用都被隐含地假定为系统所公开的能力，用于定义一组契约或接口，这些契约或接口又由不同的实现来实现。这种“规范的”多态性通常被称为子类型多态性，因为无论是否有实际的层次结构，接口的实现者都被认为是它们的子类型。

正如人们所料，理解多态性的本质只是学习过程的一半；当然，另一半是演示如何设计多态系统，使其能够在相当现实的情况下工作，而不会陷入展示“一些漂亮的教学代码”(在许多情况下是玩具代码的廉价委婉语)的陷阱。

在本文中，我将向您展示如何通过开发可插拔缓存组件来利用多态提供的优点。通过开发额外的高速缓存驱动程序，可以在以后扩展核心功能以满足您的需求。

## 定义组件的接口和实现

在构建可扩展的缓存组件时，可供选择的选项非常少(如果您持怀疑态度，只需看看一些流行框架的幕后)。不过，特别是在这种情况下，我在这里展示的组件具有在运行时交换不同缓存驱动程序的出色能力，而无需修改任何一段客户端代码。

那么，我们如何在开发过程中不流汗地完成这项工作呢？嗯，第一个合乎逻辑的步骤是…是的，定义一个分离的缓存契约，它将在以后由不同的实现来同意，因此利用了多态性的好处。

在最基本的层面上，上述契约如下所示:

```
<?php
namespace LibraryCache;

interface CacheInterface
{
    public function set($id, $data);
    public function get($id);
    public function delete($id);
    public function exists($id);
}
```

`CacheInterface`接口是一个框架契约，它抽象了通用缓存元素的行为。有了这个接口，就很容易创建一些符合 its 契约的具体缓存实现。

因为我想保持简洁和易于理解，所以我设置的缓存驱动程序只是一个简单的二重奏:第一个使用文件系统作为底层后端来缓存/获取数据，而第二个在幕后使用 APC 扩展。

以下是基于文件的缓存实现:

```
<?php
namespace LibraryCache;

class FileCache implements CacheInterface
{
    const DEFAULT_CACHE_DIRECTORY = 'Cache/';
    private $cacheDir;

    public function __construct($cacheDir = self::DEFAULT_CACHE_DIRECTORY) {
        $this->setCacheDir($cacheDir);
    }

    public function setCacheDir($cacheDir) {
        if (!is_dir($cacheDir)) {
            if (!mkdir($cacheDir, 0644)) {
                throw InvalidArgumentException('The cache directory is invalid.');
            }
        }
        $this->cacheDir = $cacheDir;
        return $this;
    }

    public function set($id, $data) {
        if (!file_put_contents($this->cacheDir . $id, serialize($data), LOCK_EX)) {
            throw new RuntimeException("Unable to cache the data with ID '$id'.");
        }
        return $this;
    }

    public function get($id) {
        if (!$data = unserialize(@file_get_contents($this->cacheDir . $id, false))) {
            throw new RuntimeException("Unable to get the data with ID '$id'.");
        }
        return $data;
    }

    public function delete($id) {
        if (!@unlink($this->cacheDir . $id)) {
            throw new RuntimeException("Unable to delete the data with ID '$id'.");
        }
        return $this;
    }

    public function exists($id) {
        return file_exists($this->cacheDir . $id);
    }
}
```

`FileCache`类的驱动逻辑应该很容易理解。到目前为止，这里最相关的事情是它公开了一个整洁的多态行为，因为它是前面的`CacheInterface`的忠实实现者。

虽然这种能力很可爱，但就其本身而言，考虑到这里的目标是创建一个能够在运行时切换后端的缓存组件，我不会对它大加赞赏。让我们为一项有益的事业付出额外的努力，为`CacheInterface`带来另一个苗条的实现者。

下面的实现遵循了接口的约定，但这次是通过使用与 APC 扩展捆绑在一起的方法:

```
<?php
namespace LibraryCache;

class ApcCache implements CacheInterface
{
    public function set($id, $data, $lifeTime = 0) {
        if (!apc_store($id, $data, (int) $lifeTime)) {
            throw new RuntimeException("Unable to cache the data with ID '$id'.");
        }
    }

    public function get($id) {
        if (!$data = apc_fetch($id)) {
            throw new RuntimeException("Unable to get the data with ID '$id'.");
        } 
        return $data;
    }

    public function delete($id) {
        if (!apc_delete($id)) {
            throw new RuntimeException("Unable to delete the data with ID '$id'.");
        }
    }

    public function exists($id) {
        return apc_exists($id);
    }
}
```

`ApcCache`类并不是你职业生涯中看到的最炫的 APC 包装器，它打包了从内存中保存、检索和删除数据所需的所有功能。

让我们拍拍自己的背，因为我们已经成功实现了一个轻量级缓存模块，由于其多态特性，它的具体后端不仅可以在运行时轻松交换，而且添加更多的后端也简单得可笑。只需编写另一个符合`CacheInterface`的实现。

然而，我应该强调，实际的子类型多态性是通过实现由接口构造定义的契约来实现的，这是一种非常普遍的方法。然而，没有什么可以阻止你不那么正统，通过切换一个被声明为一组抽象类中的抽象方法的接口来获得相同的结果。如果你喜欢冒险，想走那条小路，契约和相应的实现可以重构如下:

```
<?php
namespace LibraryCache;

abstract class AbstractCache
{
    abstract public function set($id, $data);
    abstract public function get($id);
    abstract public function delete($id);
    abstract public function exists($id);
}
```

```
<?php
namespace LibraryCache;

class FileCache extends AbstractCache
{
	// the same implementation goes here
}
```

```
<?php
namespace LibraryCache;

class ApcCache extends AbstractCache
{
	// the same implementation goes here 
}
```

从上到下，这是一种真正的多态方法，与前面讨论过的方法针锋相对。就我个人而言，我更喜欢使用接口构造来定义契约，只有在封装一些子类型共享的样板实现时，才求助于抽象类。选择最适合您需求的方法取决于您。

在这一点上，我可以放下帷幕，写一些花哨的结束语，喋喋不休地谈论我们令人印象深刻的编码技能，吹嘘 or cache 组件的灵活性，但这显然是在帮倒忙。当存在一些客户端代码能够消费几个实现而不检查它们是否是某种类型时，多态性暴露了它最诱人的一面，只要它们符合预期的契约。因此，让我们通过将缓存组件挂接到一个基本的客户端视图类来揭示这个方面，这将允许我们毫不费力地进行一些简单的 HTML 缓存。

## 让缓存驱动程序发挥作用

通过我们的示例缓存模块缓存 HTML 输出是如此的平庸，以至于我将把任何详细的解释留到以后再说。整个缓存过程可以简化为一个简单的视图类，如下所示:

```
<?php
namespace LibraryView;

interface ViewInterface
{
    public function setTemplate($template);
    public function __set($field, $value);
    public function __get($field);
    public function render();
}
```

```
<?php
namespace LibraryView;
use LibraryCacheCacheInterface;

class View implements ViewInterface
{
    const DEFAULT_TEMPLATE = 'default';    
    private $template;
    private $fields = array();
    private $cache;

    public function __construct(CacheInterface $cache, $template = self::DEFAULT_TEMPLATE) {
        $this->cache = $cache;
        $this->setTemplate($template);
    }

    public function setTemplate($template) {
        $template = $template . '.php';
        if (!is_file($template) || !is_readable($template)) {
            throw new InvalidArgumentException(
                "The template '$template' is invalid.");   
        }
        $this->template = $template;
        return $this;
    }

    public function __set($name, $value) {
        $this->fields[$name] = $value;
        return $this;
    }

    public function __get($name) {
        if (!isset($this->fields[$name])) {
            throw new InvalidArgumentException(
                "Unable to get the field '$field'.");
        }
        return $this->fields[$name];
    }

    public function render() {
        try {
            if (!$this->cache->exists($this->template)) {
                extract($this->fields);
                ob_start();
                include $this->template;
                $this->cache->set($this->template, ob_get_clean());
            }
            return $this->cache->get($this->template);
        }
        catch (RuntimeException $e) {
            throw new Exception($e->getMessage());
        } 
    }
}
```

这个模块中最引人注目的是类的构造函数，它使用了前面的`CacheInterface`和`render()`方法的一个实现。由于最后一个函数的职责是缓存视图的模板，一旦它被推过输出缓冲区，利用这个功能缓存整个 HTML 文档会很好。

假设视图的默认模板具有以下结构:

```
<!doctype html>
<html>
 <head>
  <meta charset="utf-8">
  <title>The Default Template</title>
 </head>
 <body>
  <header>
   <h1>You are viewing the default page!</h1>
<?php echo $this->header;?>
  </header>
  <section>
<?php echo $this->body;?>
  </section>
  <footer>
<?php echo $this->footer;?>
  </footer>
 </body>
</html>
```

现在，让我们找点乐子，通过向视图提供一个`ApcCache`类的实例来缓存文档:

```
<?php
use LibraryLoaderAutoloader,
    LibraryCacheFileCache,
    LibraryCacheApcCache,
    LibraryViewView;

require_once __DIR__ . '/Library/Loader/Autoloader.php';
$autoloader = new Autoloader;
$autoloader->register();

$view = new View(new ApcCache());

$view->header = 'This is my fancy header section';
$view->body = 'This is my fancy body section';
$view->footer = 'This is my fancy footer section';

echo $view->render();
```

很不错，对吧？但是请稍等片刻！我当时太投入了，以至于忘记提到上面的代码片段会在任何没有安装 APC 扩展的系统上爆炸(淘气的系统管理员！).这是否意味着制作精美的缓存模块不再可重用？

这正是基于文件的驱动程序的用武之地，它可以被放入客户端代码中，而不需要 it 部门的任何支持:

```
<?php
$view = new View(new FileCache());
```

上面的一行程序明确声明视图将使用文件系统而不是共享内存来缓存其输出。这种高速缓存后端的动态切换简单地说明了为什么多态性在设计高度解耦的模块时如此重要。它允许我们在运行时轻松地重新布线，而不会将脆弱性/刚性相关的工件波及到系统的其他部分。

## 结束语

被压倒性的形式定义压得喘不过气来，使得理解这个概念变得难以捉摸，多态性确实是生活中的美好事物之一，一旦你理解了它，你会想，如果没有它，你怎么能坚持这么久。

多态系统本质上更正交，更容易扩展，更不容易受到侵犯中心范式的攻击，如开放/封闭原则和明智的“编程到接口”原则。虽然相当原始，但是我们的缓存模块是这些优点的一个很好的例子。

如果您还没有重构您的应用程序，以便它们可以利用多态性带来的好处，那么您最好抓紧时间，因为您错过了大奖！

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章