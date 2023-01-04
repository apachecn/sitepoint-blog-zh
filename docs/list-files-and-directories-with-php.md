# 用 PHP 列出文件和目录

> 原文：<https://www.sitepoint.com/list-files-and-directories-with-php/>

在本文中，我将讨论一个你在开发 PHP 应用程序时可能经历过的常见任务:列出文件和目录。我将讨论几种基本的和高级的解决方案，每一种都有其优点和缺点。首先，我将介绍三种使用一些非常基本的 PHP 函数的方法，然后介绍使用 SPL 迭代器的更健壮的方法。

为了便于讨论，我们假设一个目录结构，如下所示:

```
---manager
|
---user
|   ---document.txt
|   ---data.dat
|   ---style.css
|---article.txt
|---master.dat
|---script.php
|---test.dat
|---text.txt
```

## 基本解决方案

第一组方法演示了函数`glob()`、函数`opendir()`、`readdir()`和`closedir()`的组合以及函数`scandir()`的使用。

### 使用 glob()

要讨论的第一个函数是`glob()`，它允许我们使用最常见的 shells 中的通配符来搜索路径名。该函数有两个参数:

*   `$pattern`(必填):搜索模式
*   `$flags`(可选):在[官方文件](http://php.net/glob "PHP: glob - Manual")中列出的一面或多面旗帜

来看几个例子吧！在目录中搜索以*结尾的所有文件和目录。txt* ，你会写:

```
<?php
$filelist = glob("*.txt");
```

如果显示`$filelist`，输出将是:

```
array (
  0 => 'article.txt',
  1 => 'text.txt'
)
```

如果您想要一个以“te”开头的文件和目录列表，应该编写的代码是:

```
<?php
$filelist = glob("te*");
```

输出是:

```
array (
  0 => 'test.dat',
  1 => 'text.txt'
)
```

要获得仅包含“ma”的目录列表，代码为:

```
<?php
$filelist = glob("*ma*", GLOB_ONLYDIR);
```

在最后一个示例中，输出是:

```
array (
  0 => 'manager'
)
```

注意，最后一个例子使用了常量`GLOB_ONLYDIR`作为可选的第二个参数。如你所见，名为`master.dat`的文件因此被排除在外。虽然`glob()`功能很容易使用，但有时并不灵活。例如，它没有一个只检索与给定模式匹配的文件(而不是目录)的标志。

### 使用 opendir()和 readdir()

我想讨论的第二种读取文件和目录的方法涉及到函数`opendir()`、`readdir()`和`closedir()`。

打开目录并返回一个连接句柄。一旦取回句柄，就可以使用`readdir()`。每次调用时，该函数将给出打开的目录中的下一个文件或目录的名称。当所有的名字都被检索到时，函数返回 false。你可以使用`closedir()`来关闭手柄。

与`glob()`不同，这种方法有点复杂，因为您没有参数来帮助您过滤返回的文件和目录。你必须自己进行后置过滤才能得到你想要的。

为了与`glob()`函数并行，以下示例检索以“te”开头的所有文件和目录的列表:

```
<?php
$filelist = array();
if ($handle = opendir(".")) {
    while ($entry = readdir($handle)) {
        if (strpos($entry, "te") === 0) {
            $filelist[] = $entry;
        }
    }
    closedir($handle);
}
```

输出与前面的示例相同。

但是如果您执行上面的代码并在运行时输出`$entry`的值，您会发现它有时包含一些看起来很奇怪的条目:“”还有“..”。这是两个虚拟目录，您可以在文件系统的每个目录中找到。它们分别代表当前目录和父目录(上级文件夹)。

第二个示例显示了如何只检索给定路径中包含的文件。

```
<?php
$filelist = array();
if ($handle = opendir(".")) {
    while ($entry = readdir($handle)) {
        if (is_file($entry)) {
            $filelist[] = $entry;
        }
    }
    closedir($handle);
}
```

正如您可能猜到的，使用上面的代码会产生以下输出:

```
array (
  0 => 'article.txt',
  1 => 'master.dat',
  2 => 'script.php',
  3 => 'test.dat',
  4 => 'text.txt'
)
```

### 使用 scandir()

最后，我想介绍一下`scandir()`函数。它只有一个强制参数:要读取的路径。返回值是路径中包含的文件和目录的数组。就像最后一个解决方案一样，要检索文件和目录的子集，您必须自己进行后过滤。另一方面，正如您在下面的代码中看到的，这个解决方案更加简洁，并且不需要管理文件句柄。

此示例显示了如何检索以字符串“te”开头的文件和目录:

```
<?php
$entries = scandir(".");
$filelist = array();
foreach($entries as $entry) {
    if (strpos($entry, "te") === 0) {
        $filelist[] = $entry;
    }
}
```

## 让我们使用 SPL 迭代器

现在我们来谈谈一些 [SPL 迭代器](http://php.net/manual/en/spl.iterators.php "PHP: Iterators - Manual")。但是在深入讨论它们的用途之前，让我先介绍一下它们和 SPL 图书馆。SPL 为面向对象的数据结构、迭代器、文件处理程序和其他特性提供了一系列的类。

优点之一是迭代器是类，所以你可以扩展它们来更好地满足你的需要。另一个优点是，它们有本地方法，这些方法对于完成您可能面临的许多常见任务非常有帮助，而且您只需在一个地方就可以使用它们。以在`readdir()`中使用`FilesystemIterator`为例，它们都将在一个循环中使用，但是在使用`readdir()`时，你的条目将只是一个字符串，使用`FilesystemIterator`你有一个对象可以为你提供关于该文件或目录的大量信息(大小、所有者、权限等等)。

当然，PHP 可以使用像`filesize()`和`fileowner()`这样的函数为你提供相同的信息，但是 PHP5 已经转向了 OOP。所以，最后，我的建议是遵循这种语言的新的最佳实践。如果你需要更多关于 SPL 迭代器的信息，看看使用 SPL 迭代器的。

如简介中所说，我将展示`FilesystemIterator`、`RecursiveDirectoryIterator`和`GlobIterator`的用法。它们中的第一个继承自`DirectoryIterator`，而其他的继承自`FilesystemIterator`。它们都有相同的构造函数，只有两个参数:

*   `$path`(强制):要迭代的文件系统项目的路径
*   `$flags`(可选):在[官方文件](http://www.php.net/manual/en/class.filesystemiterator.php#filesystemiterator.constants)中列出的一面或多面旗帜

这些迭代器的实际区别在于它们用来导航给定路径的方法。

### 文件系统启动器

使用`FilesystemIterator`非常简单。为了看到它的作用，我将展示两个例子。在第一个例子中，我将搜索所有以字符串“te”开头的文件和目录，而第二个例子将使用另一个迭代器`RegexIterator`来搜索所有**包含以“t.dat”或“t.php”结尾的**的文件和目录。`RegexIterator`用于根据正则表达式过滤另一个迭代器。

```
<?php
$iterator = new FilesystemIterator(".");
$filelist = array();
foreach($iterator as $entry) {
    if (strpos($entry->getFilename(), "te") === 0) {
        $filelist[] = $entry->getFilename();
    }
}
```

使用上面的代码，结果与前面的示例相同。

使用`RegexIterator`的第二个例子是:

```
<?php
$iterator = new FilesystemIterator(".");
$filter = new RegexIterator($iterator, '/t.(php|dat)$/');
$filelist = array();
foreach($filter as $entry) {
    $filelist[] = $entry->getFilename();
}
```

在这种情况下，输出是:

```
array (
  0 => 'script.php',
  1 => 'test.dat'
)
```

### 递归方向运算符

`RecursiveDirectoryIterator`提供了递归遍历文件系统目录的接口。由于其目的，它有一些有用的方法，如`getChildren()`和`hasChildren()`，分别返回当前条目是否是目录的迭代器。为了查看`RecursiveDirectoryIterator`和`getChildren()`的运行情况，我将重写上一个例子以获得相同的结果。

```
<?php
$iterator = new RecursiveDirectoryIterator('.');
$filter = new RegexIterator($iterator->getChildren(), '/t.(php|dat)$/');
$filelist = array();
foreach($filter as $entry) {
    $filelist[] = $entry->getFilename();
}
```

### 全局迭代器

`GlobIterator`以类似于`glob()`函数的方式遍历文件系统。所以第一个参数可以包含通配符。下面的代码展示了使用`GlobIterator`的常见例子。

```
<?php
$iterator = new GlobIterator("te*");
$filelist = array();
foreach($iterator as $entry) {
    $filelist[] = $entry->getFilename();
}
```

## 结论

在本文中，我展示了实现相同目标的不同方法:如何检索和过滤给定路径中的文件和目录。以下是一些需要记住的要点:

*   函数`glob()`是一个单行解决方案，允许过滤，但是它不是很灵活。
*   使用`opendir()`、`readdir()`和`closedir()`的解决方案有点冗长，需要后置滤波，但更灵活。
*   函数`scandir()`也需要后置过滤，但不需要管理句柄。
*   如果你想使用面向对象的方法，你应该使用 SPL 图书馆。此外，您可以扩展这些类来满足您的需求。
*   虽然`GlobIterator`能够进行预过滤，但其他人也可以使用`RegexIterator`以舒适的方式进行预过滤。

你知道实现目标的其他方法吗？如果是这样，你想与我们分享，请吧。知识共享总是受欢迎的。

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章