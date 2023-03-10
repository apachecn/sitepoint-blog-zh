# 测试过程中的无障碍文件系统操作？是的，请！

> 原文：<https://www.sitepoint.com/hassle-free-filesystem-operations-during-testing/>

当在我们的测试套件中使用文件系统时，一个大问题是在每次测试运行后清理临时文件。然而，如果由于某种原因，测试的执行在清理阶段之前被中断，那么进一步的测试可能会失败，因为环境还没有被清理。

![](img/210130e38d9b5d9dc60c6d9395449531.png)

在本文中，我们将使用名为 [vfsStream](http://vfs.bovigo.org/) 的库来创建文件系统模拟。它只不过是一个**虚拟文件系统**的包装器，也可以很好地与 [PHPUnit](http://phpunit.de) 配合使用。

> **注意**这篇文章要求对[单元测试和 PHPUnit](https://www.sitepoint.com/php/) 有一个基本的了解。

为了进行测试，我们将编写一个简单的类来创建文件:

```
<?php

namespace App\Tests;

class FileCreator extends PHPUnit_Framework_TestCase
{

    public static function create($path, $name, $content)
    {
        $file_name = rtrim($path, '/') . '/' . $name;

        if (file_put_contents($filename, $content)) {
            return true;
        }
        return false;
    }
} 
```

在类内部，我们有一个`create()`方法，它接受文件名、内容和保存路径。

为了测试这个类，我们首先采用传统的方法，在不使用虚拟文件系统的情况下创建一个测试类。然后，我们将把 vfsStream 引入游戏，看看它如何为我们简化测试过程。

## 传统方法

```
<?php

namespace App\Tests;

class FileCreatorTest extends PHPUnit_Framework_TestCase
{
    protected $path;

    public function setUp()
    {
        $this->path = sys_get_temp_dir();
    }

    public function tearDown()
    {
        if (file_exists($this->path . '/test.txt')) {
            unlink($this->path . '/test.txt');
        }
    }

    public function testCreate()
    {
        $this->assertTrue(FileCreator::create($this->path, 'test.txt', 'Lorem ipsum dolor sit amet'));
        $this->assertFileExists($this->path . '/test.txt');
    }
} 
```

在上面，我们通过使用 PHP 的 [`sys_get_temp_dir()`](http://php.net/manual/en/function.sys-get-temp-dir.php) 定义了临时目录(在`setUp()`中),我们将把它用于我们的临时文件。该函数返回临时文件的系统目录路径。

然后，我们通过做出两个断言来测试我们的类的功能，最后，我们为下一组测试清理环境(在`tearDown()`)。

这段代码在当前状态下没有任何问题。但是如果我们必须在每次测试运行中处理许多文件呢？如果在我们有机会删除临时文件之前代码就被破解了怎么办？vfsStream 有这些问题的答案。

## 使用 vfsStream

在这一节，我们将在 vfsStream 的帮助下重写`FileCreatorTest`。

要安装它，我们使用 Composer:

```
composer require mikey179/vfsStream 
```

下面是 vfsStream 的测试类:

```
<?php

use org\bovigo\vfs\vfsStream;

class FileCreatorTest extends PHPUnit_Framework_TestCase
{
    /**
     * @var org\bovigo\vfs\vfsStreamDirectory
     */
    protected $vfs;

    public function setUp()
    {
        // Setting up vfsStream in our test class
        $this->vfs = vfsStream::setup('testDirectory');
    }

    public function testCreate()
    {
        $this->assertTrue(FileCreator::create(vfsStream::url('testDirectory'), 'test.txt', 'Lorem ipsum dolor sit amet'));

        $this->assertFileExists($this->vfs->getChild('test.txt')->url());
    }
} 
```

在`setUp()`中，我们通过调用`vfsStream::setup()`来设置 vfsStream。这个方法设置 vfsStream，并返回一个`vfsStreamDirectory`的实例。这是我们的**目录容器**。

目录容器实际上是一个文件系统模拟，有一个名为`testDirectory`的主目录。

在第一个断言中，我们将主虚拟路径(通过使用`vfsStream::url()`)连同文件名及其内容一起传递给`FileCreator::create()`:

```
<?php

// ...

$this->assertTrue(FileCreator::create(vfsStream::url('testDirectory'), 'test.txt', 'Lorem ipsum dolor sit amet')); 
```

在下一个断言中，我们检查`test.txt`是否存在。为此，我们从目录容器中检索文件对象(`test.txt`)并获取其完整路径:

```
<?php

// ...

 $this->assertFileExists($this->vfs->getChild('test.txt')->url()); 
```

正如您所看到的，在我们的测试类中没有任何清理的迹象，因为所有的文件系统操作(创建和清理)都是由 vfsStream 处理的。如果由于任何原因代码中断，仍然没有必要做任何清理，因为一切都发生在内存中。

## 结论

除了我们在这里看到的，vfsStream 还提供了很多，包括但不限于处理权限、所有权、处理大文件和复杂的目录结构等。要了解更多信息，请查看他们的官方文档或等待 SitePoint 上即将发布的高级教程！

你认为这种测试文件系统操作的方法怎么样？你使用替代解决方案吗？预测使用这种方法时可能遇到的任何限制？让我们知道！

## 分享这篇文章