# PHP 中的生成器

> 原文：<https://www.sitepoint.com/generators-in-php/>

如果您已经阅读了我以前关于迭代器的文章，那么您将会知道迭代是一个重要的编程概念，但是实现所需的接口来创建一个可迭代的对象可能会非常麻烦，因为需要大量的样板代码。随着 PHP 5.5 的发布，我们终于有了生成器！

在本文中，我们将看一下生成器，它提供了一种简单的方法来实现简单的迭代器，而没有`Iterator`接口的开销或复杂性。

## 发电机是如何工作的？

根据维基百科，生成器“非常类似于返回数组的函数，因为生成器有参数，可以被调用，并生成一系列值”。生成器基本上是一个普通的函数，但是它不是返回一个值，而是根据需要生成尽可能多的值。它看起来像一个函数，但行为像一个迭代器。

生成器使用关键字`yield`而不是`return`。它的行为类似于 return，向函数的调用者返回一个值，但是，`yield`不是从堆栈中删除函数，而是保存它的状态。这允许函数在被再次调用时从原来的地方继续运行。事实上，您不能从生成器返回值，尽管您可以使用不带值的`return`来终止它的执行。

PHP 手册声明:“当一个生成器函数被调用时，它返回一个可以被迭代的对象。”这是内部 [`Generator`类](http://www.php.net/manual/en/class.generator.php)的一个对象，以与只进迭代器对象相同的方式实现了 [`Iterator`接口](http://www.php.net/manual/en/class.iterator.php)。当您迭代该对象时，PHP 每次需要一个值时都会调用生成器。当生成器让步时，状态被保存，以便在需要下一个值时可以恢复。

```
<?php
function nums() {
    echo "The generator has startedn"; 
    for ($i = 0; $i < 5; ++$i) {
        yield $i;
        echo "Yielded $in";
    }
    echo "The generator has endedn"; 
}

foreach (nums() as $v);
```

上述代码的输出将是:

```
The generator has started
Yielded 0
Yielded 1
Yielded 2
Yielded 3
Yielded 4
The generator has ended
```

## 我们的第一台发电机

生成器并不是一个新概念，在 C#、Python、JavaScript 和 Ruby(枚举器)等语言中已经存在，通常通过使用`yield`关键字来标识。以下是 Python 中的一个示例:

```
def file_lines(filename):
    file = open(filename)
    for line in file:
        yield line
    file.close()

for line in file_lines('somefile'):
    #do some work here
```

让我们用 PHP 重写示例 Python 生成器。(请注意，这两个代码片段都不执行任何类型的错误检查。)

```
<?php
function file_lines($filename) {
    $file = fopen($filename, 'r'); 
    while (($line = fgets($file)) !== false) {
        yield $line; 
    } 
    fclose($file); 
}

foreach (file_lines('somefile') as $line) {
    // do some work here
}
```

生成器函数打开一个文件，然后根据需要生成文件的每一行。每次调用生成器时，它都从停止的地方继续。它不再从头开始，因为它的状态在 yield 语句执行时已经保存。一旦所有行都被读取，生成器简单地终止，循环结束。

## 归还钥匙

PHP 迭代器由键/值对组成。在我们的示例中，只返回了一个值，因此键是数字的(默认情况下键是数字的)。如果您希望返回一个关联对，只需使用数组语法修改 yield 语句以包含键。

```
<?php
function file_lines($filename) {
    ...
        yield $key => $line; 
    ...
}

foreach (file_lines('somefile') as $key => $line) {
    // do some work here
}
```

## 注入价值观

`yield`不仅返回值；它也可以从外部接收值。这是通过用您希望传递的值调用生成器对象的 [`send()`方法](http://www.php.net/manual/en/generator.send.php)来完成的。这个值可以用于计算或做其他事情。该方法将该值作为 yield 表达式的结果发送给生成器，并继续执行。

```
<?php
function nums() {
    for ($i = 0; $i < 5; ++$i) {
        // get a value from the caller
        $cmd = (yield $i);
        if ($cmd == 'stop') {
            return; // exit the generator
        }
    }
}

$gen = nums();

foreach ($gen as $v) {
    // we are satisfied
    if ($v == 3) {
        $gen->send('stop');
    }
    echo "{$v}n";
}
```

输出将是:

```
0
1
2
3
```

## 使用发生器节省内存

当您正在计算大型集合，并且您不想同时为所有结果分配内存，或者当您不知道是否需要所有结果时，生成器非常有用。由于结果的处理方式，通过只为当前结果分配内存，可以将内存占用降至最低。

想象一下`file()`函数，它以数组的形式返回文件中的所有行。对`file()`和我们的演示 file_lines()函数运行一个简单的基准测试，每个都使用由 [Lipsum](http://www.lipsum.com/) 生成的相同的随机 100 段文本文件，结果显示文件函数使用的内存是生成器的 110 倍。

```
<?php
// Test 1
$m = memory_get_peak_usage();
foreach (file_lines('lipsum.txt') as $l);
echo memory_get_peak_usage() - $m, "n"; //Outputs 7336

// Test 2
$m = memory_get_peak_usage();
foreach (file('lipsum.txt') as $l);
echo memory_get_peak_usage() - $m, "n"; // Outputs 148112
```

## 结论

随着生成器的引入，PHP 为开发人员提供了一个强大的工具。我们现在可以快速编写迭代器，同时在这个过程中节省大量内存。通过这篇教程，我希望你已经有了足够的收获，可以开始在自己的项目中使用它们。就我个人而言，我心中有相当多的对象要重写。如果你有任何想法或意见，请提出来。

## 分享这篇文章