# PHP 5.5 的新特性

> 原文：<https://www.sitepoint.com/whats-new-in-php-5-5/>

PHP 5.5 最近发布，为该语言引入了几个令人兴奋的新特性。在本文中，我们将浏览一些最有趣的附加功能，并讨论它们给开发人员带来的好处。

## 发电机

发电机是最受期待的新功能之一。它们提供了一种处理迭代的方法，无需编写实现 [`Iterator`接口](http://www.php.net/manual/en/class.iterator.php)的类。使一个类符合迭代器接口需要大量的样板代码，因此能够通过使用生成器来避免这一点可以大大减少开发人员必须编写的代码的大小和复杂性。

生成器从新的`yield`关键字中派生出它们的功能。生成器看起来与普通函数非常相似，但它不是返回单个值，而是可以生成任意数量的值。

为了恰当地说明发电机的功率，需要举一个例子。考虑 PHP 的`range()`函数，它返回一个介于`$start`和`$end`参数之间的值数组。我们可以如下使用`range()`:

```
<?php
foreach (range(0, 1000000) as $number) {
    echo $number;
}
```

这个例子的问题是`range()`返回的数组会占用大量内存(根据 PHP 手册超过 100mb)。虽然上面的代码只是一个简单的演示，但是在现实世界中有很多构建大型数据数组的情况，通常需要很长时间来构建并占用大量内存。

随着生成器的引入，现在很容易解决这个问题，而不需要编写一个`Iterator`类。生成器不构造大数组，而是在迭代时一次返回一个元素。考虑对上述代码的修改，现在使用一个生成器来产生值的范围:

```
<?php
// define a simple range generator
function generateRange($start, $end, $step = 1) {
    for ($i = $start; $i < $end; $i += $step) {
        // yield one result at a time
        yield $i;
    }
}

foreach (generateRange(0, 1000000) as $number) {
    echo $number;
}
```

这段代码产生的结果与第一个示例完全相同，但是没有产生一个大数组来存储所有的值。根据手册，这将内存占用减少到不到一千字节——与原始示例相比，这是一个巨大的节省。

## 密码哈希

新的[密码哈希 API](http://www.php.net/manual/en/book.password.php) 是 PHP 5.5 中添加的最重要和最有用的特性之一。在过去，开发人员不得不依赖于有些令人困惑的`crypt()`函数，PHP 手册中对此没有很好的记录。引入一组简化的函数来处理密码散列将使开发人员更容易理解并为他们的站点实现安全的密码散列。

新的 API 引入了两个新函数，`password_hash()`和`password_verify()`。调用`password_hash($password, PASSWORD_DEFAULT)`将使用 [bcrypt](https://www.sitepoint.com/why-you-should-use-bcrypt-to-hash-stored-passwords/ "Why You Should Use Bcrypt to Hash Stored Passwords") 返回一个强散列，自动处理加盐。稍后验证密码就像检查`password_verify($password, $hash)`的结果一样简单。

API 默认使用 bcrypt，但是将来可能会引入新的算法来提供更安全的散列方法。开发人员可以指定他们自己的 bcrypt 工作因子来调整产生的散列的强度，也可以使用他们自己的 salt 来代替自动 salt 生成(尽管手册不鼓励这样做)。

## 最后

PHP 5.5 增加了对`finally`关键字的支持，这是一个在许多其他带有异常处理的语言中被广泛使用的特性。`finally`允许开发人员在正常执行流程恢复之前，指定在`try`和`catch`块末尾运行的代码，而不管是否抛出异常。

如果没有`finally`关键字，开发人员有时会被迫在`try`和`catch`块中重复代码来处理清理任务。例如，在下面的例子中，对`releaseResource()`的调用必须在两个地方进行:

```
<?php
function doSomething() {
    $resource = createResource();
    try {
        $result = useResource($resource);
    }
    catch (Exception $e) {
        releaseResource($resource);
        log($e->getMessage());
        throw $e;
    }
    releaseResource($resource);
    return $result;
}
```

通过添加`finally`，我们可以消除重复代码:

```
<?php
function doSomething() {
    $resource = createResource();
    try {
        $result = useResource($resource);
        return $result;
    }
    catch (Exception $e) {
        log($e->getMessage());
        throw $e;
    }
    finally {
        releaseResource($resource);
    }
}
```

在修改后的版本中，我们在`finally`块中调用清理函数`releaseResource()`，因为我们知道它总是会被调用。注意，即使`try`块返回值，在执行`return`语句和继续正常执行之前，仍然会调用`finally`块。

## 数组和字符串文字解引用

现在可以使用数组访问语法取消引用数组和字符串文字:

```
<?php
// array dereferencing - returns 3
echo [1, 3, 5, 7][1];

// string dereferencing - returns "l"
echo "hello"[3];
```

添加这个特性主要是为了提高语言的一致性，可能不会彻底改变我们编写 PHP 的方式。但是，有一些有趣的应用需要考虑，例如:

```
<?php
$randomChar = "abcdefg0123456789"[mt_rand(0, 16)];
```

## 对函数调用和表达式使用 empty()

[`empty()`构造](http://php.net/manual/en/function.empty.php)现在可以用于函数调用和其他表达式。例如，`empty($object->getProperty())`是 PHP 5.5 中的有效代码。这使得在函数的返回值上使用`empty()`成为可能，而无需首先在变量中获取值。

## 类名解析

自从在 PHP 5.3 中引入了[名称空间](https://www.sitepoint.com/php-namespaces/ "PHP Namespaces")以来，在 PHP 项目中使用大量的名称空间来组织类已经成为一种常见的做法。然而，到目前为止，很难以字符串的形式检索完全限定的类名。考虑以下代码:

```
<?php
use NamespacedClassFoo;

$reflection = new ReflectionClass("Foo");
```

这将失败，因为 PHP 将尝试使用全局 Foo 类而不是命名空间类。在 PHP 5.5 中，现在可以使用关键字`class`以字符串形式检索完整的命名空间类名:

```
<?php
use NamespacedClassFoo;

$reflection = new ReflectionClass(Foo::class);
```

这将按预期工作，因为`Foo:class`将解析为`Namespaced\Class\Foo`。

## 对 foreach 的更改

PHP 中的 [`list()`构造](http://php.net/manual/en/function.list.php)允许数组的值很容易地赋给变量。例如:

```
<?php
$values = ["sea", "blue"];

list($object, $description) = $values;

// returns "The sea is blue"
echo "The $object is $description";
```

现在可以在`foreach`循环中使用多维数组`list()`。例如:

```
<?php
$data = [
    ["sea", "blue"],
    ["grass", "green"]
];

foreach ($data as list($object, $description)) {
    echo "The $object is $descriptionn";
}

/* Outputs:
The sea is blue
The grass is green
*/
```

这是一个强大的新特性，有可能使嵌套数组的迭代变得更加容易和简洁。

`foreach`循环现在也可以将非标量值作为迭代器键处理，这意味着元素键可以有非字符串或整数的值。

## 结论

PHP 5.5 提供了许多改进来促进 PHP 开发。除了新功能之外，在此版本中还解决了一长串错误(有关详细信息，请参见 changelog)，并进行了各种优化和增强，以提高性能和稳定性。

你会升级到 PHP 5.5 吗？请在评论中告诉我们。

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章