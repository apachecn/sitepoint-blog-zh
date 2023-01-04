# 使用 SPL 迭代器，第 1 部分

> 原文：<https://www.sitepoint.com/using-spl-iterators-1/>

当我第一次遇到术语迭代，并在 SPL 中看到与它相关的铺天盖地的类列表时，我大吃一惊。似乎迭代对我来说太复杂了，难以理解。我很快意识到这只是对我们程序员一直在做的事情的一个花哨的描述。

如果你使用 PHP，你很可能使用过数组。如果你使用过数组，那么你肯定循环过它的元素。浏览任何代码，几乎可以肯定你会发现一个`foreach`循环。是的，迭代就是遍历一个值列表的过程。迭代器是一个遍历列表的对象，可以是数组、目录列表，甚至是数据库结果集。

在这个两部分系列的第一部分中，我将向您介绍迭代，以及如何利用标准 PHP 库(SPL)的一些内置类。SPL 附带了大量的迭代器，在您的代码中使用它们可以使您的代码更加高效，并且在大多数情况下，可读性更好。

## 为什么以及何时使用 SPL 迭代器

正如你将会看到的，迭代迭代器对象基本上和迭代数组是一样的，所以很多人想知道首先坚持使用数组不是更容易吗？然而，迭代器的真正好处在遍历大量数据或任何比简单数组更复杂的东西时表现出来。

循环[复制传递给它的任何数组](http://sldn.softlayer.com/blog/dmcaloon/PHP-Memory-Management-Foreach)。如果您正在处理大量数据，那么每次在一个`foreach`循环中使用大型数组时复制它们可能是不可取的。SPL 迭代器封装了列表，一次暴露一个元素的可见性，这使得它们更加高效。

当创建数据提供者时，迭代器是一个很好的构造，因为它们允许您延迟加载数据。这里的延迟加载只是在需要的时候检索所需的数据。您还可以在将数据交给用户之前对其进行处理(过滤、转换等)。

然而，使用迭代器的决定总是由你来决定。迭代器有很多好处，但在某些情况下(如较小的数组集)会导致不必要的开销。何时使用它们取决于你自己；你对风格的选择，以及它们在特定情况下的适用性，都是你应该考虑的因素。

## 迭代数组

我要介绍的第一个迭代器是`ArrayIterator`。构造函数接受参数数组，并提供可用于循环访问该数组的方法。这里有一个例子:

```
<?php
// an array (using PHP 5.4's new shorthand notation)
$arr = ["sitepoint", "phpmaster", "buildmobile", "rubysource",
    "designfestival", "cloudspring"];

// create a new ArrayIterator and pass in the array
$iter = new ArrayIterator($arr);

// loop through the object
foreach ($iter as $key => $value) {
    echo $key . ":  " . $value . "<br>";
}
```

上述代码的输出是:

```
0: sitepoint
1: phpmaster
2: buildmobile
3: rubysource
4: designfestival
5: cloudspring
```

然而，通常情况下，你会使用`ArrayObject`，这个类允许你在特定的上下文中像处理数组一样处理对象，而不是直接使用`ArrayIterator`。当你使用一个`foreach`循环或者直接调用`ArrayIterator::getIterator()`时，这会自动为你创建一个`ArrayIterator`。

请注意，虽然`ArrayObject`和`ArrayIterator`在这个上下文中表现得像数组，但它们仍然是对象；试图在它们上面使用内置的数组函数，比如`sort()`和`array_keys()`，将会失败。

`ArrayIterator`的使用非常简单，但是仅限于一维数组。有时你会有一个多维数组，你想递归地遍历嵌套数组。这种情况下可以使用`RecursiveArrayIterator`。

一个常见的场景是嵌套`foreach`循环或者创建一个递归函数来检查多维数组的所有项。例如:

```
<?php
// a multidimensional array
$arr = [
    ["sitepoint", "phpmaster"],
    ["buildmobile", "rubysource"],
    ["designfestival", "cloudspring"],
    "not an array"
];

// loop through the object
foreach ($arr as $key => $value) {
    // check for arrays
    if (is_array($value)) {
        foreach ($value as $k => $v) {
            echo $k . ": " . $v . "<br>";
        }
    }
    else {
        echo $key . ": " . $value . "<br>";
    }
}
```

上述代码的输出是:

```
0: sitepoint
1: phpmaster
0: buildmobile
1: rubysource
0: designfestival
1: cloudspring
3: not an array
```

一种更优雅的方法是利用`RecursiveArrayIterator`。

```
<?php
...
$iter = new RecursiveArrayIterator($arr);

// loop through the object
// we need to create a RecursiveIteratorIterator instance
foreach(new RecursiveIteratorIterator($iter) as $key => $value) {
    echo $key . ": " . $value . "<br>";
}
```

输出与前面的示例相同。

请注意，您需要创建一个`RecursiveIteratorIterator`的实例，并在这里将`RecursiveArrayIterator`对象传递给它，否则您得到的将是根数组中的值(以及大量通知，具体取决于您的设置)。

当处理多维数组时，您应该使用`RecursiveArrayIterator`,因为它也允许您迭代当前条目，但是这由您来做。`RecursiveIteratorIterator`是一个[装饰器](http://en.wikipedia.org/wiki/Decorator_pattern)，它为你做这件事。它获取`RecursiveArrayIterator`，遍历它，并遍历它找到的任何`Iterable`条目(以此类推)。本质上，它“拉平”了`RecursiveArrayIterator`。您可以通过调用`RecursiveIteratorIterator::getDepth()`跟踪来获得当前的迭代深度。如果你想返回对象，小心使用`RecursiveArrayIterator`和`RecursiveIteratorIterator`；对象被视为`Iterable`，因此将被迭代。

## 迭代目录列表

毫无疑问，您需要在某个时间点遍历一个目录及其文件，PHP 已经提供了多种内置函数来实现这一点，比如使用`scandir()`或`glob()`。但是你也可以使用`DirectoryIterator`。最简单的形式，`DirectoryIterator`相当强大，但也可以子类化和增强。

下面是一个用`DirectoryIterator`迭代目录的例子:

```
<?php
// create new DirectoryIterator object
$dir = new DirectoryIterator("/my/directory/path");

// loop through the directory listing
foreach ($dir as $item) {
    echo $item . "<br>";
}
```

输出显然取决于您指定的路径和目录的内容。例如:

```
.
..
api
index.php
lib
workspace
```

不要忘记，使用`DirectoryIterator`以及许多其他 SPL 迭代器，您有使用异常处理任何错误的额外好处。

```
<?php
try {
    $dir = new DirectoryIterator("/non/existent/path");
    foreach ($dir as $item) {
        echo $item . "<br>";
    }
}
catch (Exception $e) {
    echo get_class($e) . ": " . $e->getMessage();
}
```

```
UnexpectedValueException: DirectoryIterator::__construct(/non/existent/path,/non/existent/path): The system cannot find the file specified. (code: 2)
```

有了许多其他方法，如`DirectoryIterator::isDot()`、`DirectoryIterator::getType()`和`DirectoryIterator::getSize()`，几乎可以满足您所有的基本目录信息需求。您甚至可以将`DirectoryIterator`与`FilterIterator`或`RegexIterator`结合起来，返回符合特定标准的文件。例如:

```
<?php
class FileExtensionFilter extends FilterIterator
{
    // whitelist of file extensions
    protected $ext = ["php", "txt"];

    // an abstract method which must be implemented in subclass
    public function accept() {
        return in_array($this->getExtension(), $this->ext);
    }
}

//create a new iterator
$dir = new FileExtensionFilter(new DirectoryIterator("./"));
...
```

SPL 也提供了`RecursiveDirectoryIterator`，其使用方法与`RecursiveArrayIterator`相同。递归遍历目录的函数通常会对有效的目录和文件进行条件检查，而`RecursiveDirectoryIterator`可以帮你完成大部分工作，产生更干净的代码。然而，有一点需要注意。`RecursiveDirectoryIterator`不返回空目录；如果一个目录包含许多子目录但没有文件，它将返回一个空结果(很像 Git 的行为)。

```
<?php
// create new RecursiveDirectoryIterator object
$iter = new RecursiveDirectoryIterator("/my/directory/path");

// loop through the directory listing
// we need to create a RecursiveIteratorIterator instance
foreach (new RecursiveIteratorIterator($iter) as $item) {
    echo $item . "<br>";
}
```

我的输出类似于:

```
./api/.htaccess
./api/index.php
./index.php
...
```

## 摘要

希望你现在意识到迭代并不像我最初想的那样复杂，它是我们作为程序员每天都要做的事情。在这篇文章中，我介绍了迭代和 SPL 提供的一些类，它们使迭代变得更加容易和健壮。当然，我只处理了一小部分可用类；SPL 还有很多很多，我强烈建议你去看一看。

SPL 是一个“标准”图书馆。有时你可能会发现这些课程太笼统，它们可能并不总是能满足你的需求。在这种情况下，您可以轻松地扩展这些类来添加您自己的功能或根据需要调整现有的功能。在本系列的下一部分，我将向您展示如何使用 SPL 接口来创建您自己的定制类，这些类可以像数组一样被遍历和访问。

<small>图片 via[musha kesa](http://www.shutterstock.com/gallery-96945p1.html)/[Shutterstock](http://www.shutterstock.com)</small>

## 分享这篇文章