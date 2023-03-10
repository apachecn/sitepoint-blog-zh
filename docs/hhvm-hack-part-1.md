# HHVM 和哈克——我们能指望他们取代 PHP 吗？

> 原文：<https://www.sitepoint.com/hhvm-hack-part-1/>

脸书在 2008 年开始为 PHP 开发 HipHop。他们的目标是加快 PHP 的执行速度，项目的第一个版本由串联的 HPHPc/HPHPi 组成。HPHPc 是一个 PHP 到 C++的 transpiler，用于将代码部署到生产服务器，而 HPHPi 是一个在开发和调试阶段使用的解释器。

HPHPc 在提高性能方面做得很好，但也不是没有问题:保持 HPHPc 和 HPHPi 同步被证明是很麻烦的，而且在编译的代码和解释的代码之间仍然存在一些差异。这就是为什么早在 2010 年，脸书决定走另一条路，并创建了[HHVM](https://www.sitepoint.com/hhvm-revisited/)——一种新的虚拟机，旨在取代 PHP 使用的 Zend 引擎。到 2012 年底，HHVM 实现了与前 HPHPc 的性能对等，不久[超过了它](http://www.hhvm.com/blog/2027/faster-and-cheaper-the-evolution-of-the-hhvm-jit)。

HHVM 的目的是实现与 Zend 引擎的同等功能和最佳性能。脸书声称，通过从 PHP+APC 转换到 HHVM，速度提高了 3 到 10 倍，内存占用减少了 1/2。当然，这实际上取决于应用程序(10x 代表 FB 代码库)。本文将不会关注奇偶校验或性能，因为已经有大量的资源可用，检查 [HHVM 博客](http://www.hhvm.com/blog/)或[谷歌的“HHVM 基准”](https://www.google.com/search?q=hhvm+benchmark)。要了解更多关于 HipHop 和 HHVM 的信息，请阅读之前关于它的 [SitePoint 文章](https://www.sitepoint.com/hhvm-and-wordpress/)。

相反，本文将关注 HACK，它是 PHP 语言的一种发展，旨在更安全、实现更好的性能并提高开发人员的效率。注意，HHVM 同等支持 HACK 和 PHP。尽管 HACK 已经在脸书的所有生产服务器上使用，但目前只有很少的信息被泄露。简而言之，HACK 是脸书的 PHP 6——它旨在修复当今 PHP 的大部分问题，同时增加一些新功能，如静态类型。

### 你好黑客

到今天为止，并不是所有的工具和文档都已经发布了。然而最新的 HHVM 源代码已经支持 HACK。您可以安装一个[流浪者](http://www.vagrantup.com/)虚拟机，开始尝试 HACK 并运行本文中的代码片段:

```
# Vagrant should be installed on your machine
$ git clone https://github.com/vicb/hhvm-vagrant.git
$ cd hhvm-vagrant
$ vagrant up
```

现在，您已经准备好编写您的第一个黑客程序了:

```
<?hh
require "/vagrant/www/xhp/php-lib/init.php";

$hello = "Hello HACK!";

echo <html>
    <head>
        <title>{$hello}!</title>
    </head>
    <body>
        <h1>{$hello}</h1>
    </body>
</html>;
```

这个例子可以在你的流浪者虚拟机的克隆中找到(位于`www/hello/index.php`)。将浏览器指向`http://localhost:8080/hello/`就可以看到结果。

HACK 应用程序从位于源文件最开头的`<?hh`标记开始，注意 HACK 中没有结束标记。这个示例非常简单，唯一值得注意的一点是它使用了 [XHP](https://github.com/facebook/xhp) ，这是一种 XML 标记语言，也可以作为[普通 PHP 扩展](https://github.com/facebook/xhp)使用。本文稍后将详细介绍 XHP。

更多有趣的 HACK good 将在下面的章节中介绍。

### 构造函数参数提升

HACK 不仅在执行速度上比普通 PHP 更高效，而且它还致力于提高开发人员的效率。

“构造函数参数提升”是帮助减少样板代码的特性之一。事实上，在 PHP 中创建一个类的常见模式是首先声明属性，然后直接从构造函数参数中为所有属性赋值。使用 HACK，将可见性作为构造函数参数修饰符添加，对于声明属性和初始化属性都足够了。它使代码更加简洁:

```
<?hh
class PHPClass {
    public $pub;
    protected $pro;
    private $pri;

    public function __construct($pub, $pro, $pri) {
        $this->pub = $pub;
        $this->pro = $pro;
        $this->pri = $pri;
    }
}

// object(PHPClass)#6 (3) { ["pub"]=> string(3) "pub" ["pro":protected]=> string(3) "pro" ["pri":"PHPClass":private]=> string(3) "pri" }
var_dump(new PHPClass('pub', 'pro', 'pri'));

// The same class written in HACK is much more concise 
// thanks to constructor argument promotion.
class HHClass {
    public function __construct(public $pub, protected $pro, private $pri) {}
}

// object(HHClass)#6 (3) { ["pub"]=> string(3) "pub" ["pro":protected]=> string(3) "pro" ["pri":"HHClass":private]=> string(3) "pri" }
var_dump(new HHClass('pub', 'pro', 'pri'));
```

这一部分的示例代码位于`www/promotion/index.php`，您可以通过将浏览器指向`http://localhost:8080/promotion/`来查看它的输出。

注意，facebook 已经提议将这个特性包含在股票 PHP 中。

### 收集

HACK 增加了对集合的支持。一些可用的类对 PHP 开发人员来说看起来很熟悉，但也有一些期待已久的新类。

`Vector`和`Map`类型是 PHP 数组的增强版本，因为它们提供了相同的功能，但是公开了一个优秀的面向对象的接口，这是普通 PHP 所没有的:

```
<?hh
require "/vagrant/www/xhp/php-lib/init.php";

$v = Vector {"d", "c", "b"};
$v->add("a");
$v->reverse();
$v = $v->map(($_) ==> strtoupper($_))->filter(($_) ==> strcmp($_, "D") < 0);

$items = <ul />;
foreach ($v as $i) {
    $items->appendChild(<li>{$i}</li>);
}
echo <div>Vector:{$items}</div>; // A, B, C

$m = Map {"a" => 1, "b" => 2};
$m->add(Pair {"d", 4});

echo <div>Map:
    <ul>
        <li>contains "a": {$m->contains("a") ? "yes" : "no"}</li> // yes
        <li>contains "c": {$m->contains("c") ? "yes" : "no"}</li> // no
        <li>size: {$m->count()}</li> // 3
    </ul>
</div>;
```

在这个示例中，您还可以注意到“lambdas”的引入。它们是闭包的简化版本，`($args) ==> $result`相当于`function($args) { return $result; }`。

这一部分的示例代码位于`www/collections/index.php`，您可以通过将浏览器指向`http://localhost:8080/collections/`来查看它的输出。

HACK 还提供了一个`Set`类型——一个独特元素的集合，一个“冻结的”不可变变量，用于上面描述的每种类型(以允许进一步的速度优化),一堆迭代器和许多有用的方法。你可以通过检查[集合 IDL 文件](https://github.com/facebook/hhvm/blob/master/hphp/system/idl/collections.idl.json)来了解更多。

### 关于类型和泛型的更多信息

PHP 5 引入了类型提示，允许函数强制参数成为对象、接口、数组或可调用的。HACK 更进一步，它是静态类型的，支持标量类型(即`int`、`num`或`string`)。

```
<?hh
require "/vagrant/www/xhp/php-lib/init.php";

set_error_handler(function ($no, $str) {
    $func = debug_backtrace(DEBUG_BACKTRACE_IGNORE_ARGS, 2)[1]['function'];
    $line = debug_backtrace(DEBUG_BACKTRACE_IGNORE_ARGS, 2)[1]['line'];
    echo <p>ERROR(calling "{$func}()" on l.{$line}) : {$str}</p>;
});

function echo_upper(string $s) {
    echo (strtoupper($s));
}

echo_upper("lower"); // LOWER
echo_upper(5); // ERROR(calling "echo_upper()" on l.16) : Argument 1 passed to echo_upper() must be an instance of string, int given
echo_upper(null); // ERROR(calling "echo_upper()" on l.17) : Argument 1 passed to echo_upper() must be an instance of string, null given
```

在这个代码示例中，`echo_upper`函数希望第一个参数是`string`。传递一个`int`或`null`会触发一个错误。`null`值可以通过在类型前加一个`?`来允许，例如`?string`。

HACK 还通过类似于 Java 的泛型带来了对泛型编程的支持。例如，您可以通过指定元素的类型来专门化常规的`array`:

```
<?hh
function sum(array<int> $a) {
    return array_reduce($a, ($sum, $i) ==> $sum + $i);
}

echo <p>sum([1, 2, 3]) = {sum([1, 2, 3])}</p>; // sum([1, 2, 3]) = 6
```

使用带有内置类型的泛型很好，但是您也可以使用泛型实现自己的类型:

```
<?hh
class Generics<T as Countable> {
    private ?T $t;

    public function get() : ?T {
        return $this->t;
    }

    public function set(?T $t) {
        $this->t = $t;
    }

    public function __toString() {
        return var_export($this->t, true);
    }

    public function count() {
        return $this->t ? $this->t->count() : 0;
    }
}

$type = new Generics();
$type->set(Vector {1, 2, 3});

echo <div>$type
    <ul>
        <li>= {$type}</li> <!-- HH\Vector { 1, 2, 3, } -->
        <li>size = {$type->count()}</li> <!-- 3 -->
    </ul></div>;
```

这个代码示例本身并不真正有用，但是它引入了一些有趣的概念。HHVM 使得:
–约束可接受的类型:`<T as Countable>`意味着被包含的类型必须实现`\Countable`接口，这是由`count()`方法规定的需求，
–指定属性类型，`$t`属性必须是 T 类型。开头的`?`表示`$t`也可以为空，
指定方法(或函数)的返回类型，`public function get() : ?T`表示`get`将返回 T 类型，
约束参数，`set()`方法只允许可能为空的 T 类型。

这一部分的示例代码位于`www/types/index.php`，您可以通过将浏览器指向`http://localhost:8080/types/`来查看它的输出。

黑客类型的系统非常强大，有两个用途。第一个是尽可能早地捕捉编程错误，第二个是生成更快的代码(下一部分将详细介绍)。

## 下一步是什么

本文的下一部分将向您展示更多关于 HACK 的静态类型本质，HACK 如何使异步代码执行变得容易，XHP 标记语言和一些更令人兴奋的特性。

## 分享这篇文章