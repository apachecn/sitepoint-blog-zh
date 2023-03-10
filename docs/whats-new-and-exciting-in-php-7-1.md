# PHP 7.1 有什么新的令人兴奋的地方？

> 原文：<https://www.sitepoint.com/whats-new-and-exciting-in-php-7-1/>

这篇文章由 Thomas Punt 进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！

* * *

PHP 社区已经随着最新的 PHP 7 公告和它给这门语言带来的所有好处而疯狂传播。PHP 7.1 版本提供了更多好消息和特性。本文强调了最重要的几个，您应该查看 [PHP RFC](https://wiki.php.net/rfc#php_71) 以获得完整的列表。

![Flying baloon-tied elephpant](img/7860d7e2550b0ea0cae856c78d9f7346.png)

## ArgumentCountError 异常

PHP 的早期版本允许函数调用的参数比指定的少，当缺少参数时会发出警告。

```
// PHP 5.6

function sum($a, $b)
{
    return $a + $b;
}

sum(); 
// Warning: Missing argument 1 for sum()
// Warning: Missing argument 2 for sum()

sum(3);
// Warning: Missing argument 2 for sum()

sum(3, 4); 
```

警告在这种情况下没有用，开发人员应该检查是否所有的参数都设置正确。在 PHP 7.1 中，这些警告被转换成了一个`ArgumentCountError`异常。

```
// PHP 7.1

function sum($a, $b)
{
    return $a + $b;
}

sum(); 
// Fatal error: Uncaught ArgumentCountError: Too few arguments to function sum(), 0 passed in /vagrant/index.php on line 18 and exactly 2 expected in /vagrant/index.php:13

sum(3); // skipped

sum(3, 4); // skipped 
```

## 可空类型

PHP 7 增加了参数和返回类型的类型声明，但似乎缺少了什么！可空类型使指定类型可以接受该类型或空值。这里有一个例子:

```
function sum(int $a, int $b): ?int
{
    return $a + $b;
} 
```

上面的函数可能会返回一个整数或空值，所以如果函数的逻辑发生了错误，您不会将空值作为错误的类型返回。你也可以对论点做同样的事情！

```
function sum(?int $a, ?int $b): ?int
{
    if ($a == null || $b == null) {
        return null;
    }

    return $a + $b;
} 
```

意想不到的是，不带参数调用函数会抛出异常！

```
var_dump(sum(null, null)); // NULL
var_dump(sum()); // throw ArgumentCountError exception 
```

这意味着当参数没有默认值时，应该显式指定参数。

你应该记住的另一件事是，当重写或实现方法时，你不能将可空类型添加到返回类型中，但是你可以移除它。反之亦然，你不能从参数中删除可空类型，但是你可以添加它！

```
interface Fooable {
    function foo(): ?Fooable;
}
interface StrictFooable extends Fooable {
    function foo(): Fooable; // valid
}

interface Fooable {
    function foo(): Fooable;
}
interface LooseFooable extends Fooable {
    function foo(): ?Fooable; // invalid
} 
```

## 解构数组

当析构数组时，我们通常做以下事情。

```
list($a, $b, $c) = [1, 2, 3];
var_dump($a, $b, $c); // int(1) int(2) int(3) 
```

但是尝试这样做将会失败，因为我们无法指定要提取的键，并且该函数试图使用索引键。

```
list($a, $b, $c) = ["a" => 1, "b" => 2, "c" => 3];
var_dump($a, $b, $c); // NULL NULL NULL 
```

这个 [RFC](https://wiki.php.net/rfc/short_list_syntax) 提供了对数组析构的更多控制。上面的代码可以改成这样。

```
list("a" => $a, "b" => $b, "c" => $c) = ["a" => 1, "b" => 2, "c" => 3];
var_dump($a, $b, $c); // int(1) int(2) int(3) 
```

`list`函数现在接受用于数组析构的键，因为这被大量使用，所以引入了一个新的紧凑语法。以此为例。

```
["a" => $a, "b" => $b, "c" => $c] = ["a" => 1, "b" => 2, "c" => 3];
var_dump($a, $b, $c); // int(1) int(2) int(3) 
```

很酷，不是吗？它也适用于多维数组。

```
[[$a, $b], [$c, $d]] = [[1, 2], [3, 4]];
var_dump($a, $b, $c, $d); // int(1) int(2) int(3) int(4)

[["b" => $b], ["c" => $c]] = [["a" => 1, "b" => 2], ["c" => 3, "d" => 4]];
var_dump($b, $c); // int(2) int(3) 
```

## 可迭代类型

添加了`iterable`伪类型来连接数组原始类型和用于使值可迭代的`Traversable`接口。以下面的代码为例。

```
// PHP 5.6

function dump(array $items)
{
    var_dump($items);
}

dump([2, 3, 4]);
dump(new Collection()); 
```

```
array(3) {
  [0]=>
  int(2)
  [1]=>
  int(3)
  [2]=>
  int(4)
}

Catchable fatal error: Argument 1 passed to dump() must be of the type array, object given... 
```

但是在这种情况下，函数不接受可迭代的值，并将抛出一个错误。这个新的变化让您可以使用`iterable`来描述一个可迭代的值，而不是手动断言它。

```
// PHP 7.1

function dump(iterable $items)
{
    var_dump($items);
}

dump([2, 3, 4]);
dump(new Collection()); 
```

```
array(3) {
  [0]=>
  int(2)
  [1]=>
  int(3)
  [2]=>
  int(4)
}
object(Collection)#2 (0) {
} 
```

## 可调用函数的闭包

新的`fromCallable`方法提供了一种从可调用中创建闭包对象的高效且简洁的方法。这里有一个例子:

```
$callback = Closure::fromCallable([$this, 'fn']); 
```

## 无效返回类型

这是我最喜欢的新功能之一。它完成了 PHP 7 中引入的返回类型特性，其中函数可能不返回任何内容，因此将被迫返回 null！

```
function dump($object): void
{
    var_dump($object);
} 
```

void 函数可以省略 return 语句或者添加一个空语句(`return;`)。

## 类常量可见性

这是一个小变化，但却是 OOP 中强制封装的重要部分。它将可见性修饰符添加到类常量中。

```
class Post
{
    protected const PUBLISHED = 1;
    protected const DRAFT = 2;
    protected const TRASHED = 3;

    // ...
} 
```

## 捕捉多种异常类型

像 Java 这样的其他语言提供了在同一个 catch 块中捕获多种异常类型的能力，从而消除了复制代码的需要。以下面的代码为例:

```
// ...

try {
    $user->payMonth($month);
} catch (UserSuspendedException $ex) {
    DB::rollBack();

    return redirect()
            ->back()
            ->withInput()
            ->withErrors([$ex->getMessage()]);
} catch (PaidMonthException $e) {
    DB::rollBack();

    return redirect()
            ->back()
            ->withInput()
            ->withErrors([$ex->getMessage()]);
}

// ... 
```

您可以使用新的 catch 块语法来删除重复项:

```
// ...

try {
    $user->payMonth($month);
} catch (PaidMonthException | UserSuspendedException $ex) {
    DB::rollBack();

    return redirect()
            ->back()
            ->withInput()
            ->withErrors([$ex->getMessage()]);
}

// ... 
```

## 无效的字符串算法

当你开始学习 PHP 的时候，你会在做算术的时候有一个*啊哈*的瞬间，你会学到字符串是允许的！这是因为 PHP 通常处理 web，值是以字符串的形式传入的。

```
// PHP 5.6

var_dump("1" + "2");
var_dump("1" + "a string");
var_dump("1" + "2  with a string"); 
```

```
int(3)
int(1)
int(3) 
```

```
// PHP 7.1

var_dump("1" + "2");
var_dump("1" + "a string");
var_dump("1" + "2  with a string"); 
```

```
int(3)

Warning: A non-numeric value encountered in /vagrant/index.php on line 17
int(1)

Notice: A non well formed numeric value encountered in /vagrant/index.php on line 18
int(3) 
```

现在，如果发现非数字值，我们会得到一个警告，如果值的格式不正确，我们会得到一个通知(`2 a string` = `2`)。

## 结论

此列表并未涵盖所有新的变更，因此请务必查看 [RFC 列表](https://wiki.php.net/rfc#php_71)以获取完整列表。如果你对 PHP 7.1 的新特性或 PHP 有任何问题或评论，请在下面发表！

## 分享这篇文章