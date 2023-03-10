# 没有 PHP 7 或 HHVM，我们能在 PHP 中拥有静态类型吗？

> 原文：<https://www.sitepoint.com/can-we-have-static-types-in-php-without-php-7-or-hhvm/>

现在 [PHP 7 已经发布了一段时间](https://www.sitepoint.com/learn-php-7-find-out-whats-new-and-more/)，它有一些有趣的特性，比如错误处理、零合并操作符、标量类型声明等等。，我们经常听到仍然坚持使用 PHP 5 的人说它的输入系统很弱，事情很快变得不可预测。

![Vector illustration of programmer's desktop](img/1bf0dd7674ca1b0c914cc769ddf8d097.png)

尽管这是部分正确的，但是当你知道你在做什么的时候，PHP 允许你保持对你的应用程序的控制。让我们看一些代码示例来说明这一点:

```
function plusone($a)
{
    return $a + 1;
}

var_dump(plusone(1));
var_dump(plusone("1"));
var_dump(plusone("1 apple")); 
```

```
// output

int(2)
int(2)
int(2) 
```

我们的函数会将作为参数传递的数字加 1。但是，第二次和第三次调用传递的是一个字符串，函数仍然返回整数值。这叫做[字符串转换](http://php.net/manual/en/language.types.string.php#language.types.string.conversion)。我们可以确保用户通过验证传递一个数值。

```
function plusone($a)
{
    if ( !is_numeric($a) )
    {
        throw new InvalidArgumentException("I can only increment numbers!", 1);
    }

    return $a + 1;
} 
```

这将像预期的那样在第三次调用时抛出一个`InvalidArgumentException`。如果我们在函数原型上指定期望的类型…

```
function plusone(int $a)
{
    return $a + 1;
}

var_dump(plusone(1));
var_dump(plusone("1"));
var_dump(plusone("1 apple")); 
```

```
// output

PHP Catchable fatal error:  Argument 1 passed to plusone() must be an instance of int, integer given, called in /vagrant/test_at/test.php on line 7 and defined in /vagrant/test_at/test.php on line 2 
```

这个错误乍一看似乎有点奇怪，因为第一次调用我们的函数**是使用整数的**！

如果我们仔细阅读消息，我们会看到错误消息说“必须是 int 的一个**实例**”——它假设**整数**是一个类，因为 7 版之前的 PHP 只支持类的类型提示！

在 PHP 5 中，函数返回参数变得更加棘手。简而言之，我们不能自动锁定它们的类型，我们应该在函数调用返回值后检查期望值。

## 扩充类型

在 PHP 7 发布之前， [Box](https://www.box.com) 的团队想出了一个[的好主意](https://www.box.com/blog/making-php-safer-introducing-augmented-types/)来解决他们 PHP 5 应用程序的打字安全问题。在使用断言、类型提示等之后。他们决定研究一种*清洁剂*来解决这个问题。

我们已经看到脸书如何通过推出 [HHVM](https://www.sitepoint.com/hhvm-and-wordpress/) 和[黑客](https://www.sitepoint.com/premium/books/hacking-the-hacker/read/1)来推动 PHP 向前发展，但是 Box 的团队不想派生 PHP 源代码或修改任何核心内容。他们的解决方案是创建一个名为 [augmented types](https://github.com/box/augmented_types) 的独立扩展来解析方法的 phpDoc 并在运行时断言类型。

### 装置

下面的扩展是用于 PHP 5 的——如果你在 PHP 7 上，就坐下来享受这段旅程吧！

安装过程并不复杂，不需要任何特定的配置。以下说明适用于 Ubuntu，但也很容易适用于其他基于*nix 的操作系统。

```
# update system
sudo apt-get update

# install required dependencies
sudo apt-get install php5-dev bison flex

# clone the repo
git clone git@github.com:box/augmented_types.git

# install extension

phpize
./configure --enable-augmented_types
make
make test
sudo make install 
```

我们必须告诉 PHP 通过编辑`php.ini`文件来加载我们的扩展。

```
# Get php.ini location from PHP info. This will print the cli configuration file, you may need to edit /etc/php5/fpm/php.ini )
php -i | grep 'Loaded Configuration File'

# output: /etc/php5/cli/php.ini
vim /etc/php5/cli/php.ini 
```

```
# Get `extension_dir` the PHP info
php -i | grep extension_dir
# output: extension_dir => /usr/lib/php5/20100525 => /usr/lib/php5/20100525

# Add this line at the end of the file
zend_extension=/usr/lib/php5/20100525/augmented_types.so 
```

可以使用`ini_set`功能逐个文件地启用扩展。

```
ini_set("augmented_types.enforce_by_default",1); 
```

或者直接放在`php.ini`配置文件上。请务必查看[文档](https://github.com/box/augmented_types/wiki/Installation)了解安装过程的更多细节。

### 使用

我们之前提到过，扩展使用 [phpDoc](http://phpdoc.org/) 作为函数/方法原型。它的大部分功能可以通过代码示例来解释。

```
/**
 * Add one
 *
 * @param   int $a
 * @return    int
 */
function plusone($a)
{
    return $a + 1;
}

var_dump(plusone(1));
var_dump(plusone("1"));
var_dump(plusone("1 apple")); 
```

您可能认为您能够猜出上面代码的输出，但是您可能错了！

```
int(2)
PHP Fatal error:  Wrong type encountered for argument 1 of function plusone, was expecting a integer but got a (string) '1' in /vagrant/test_at/test.php on line 15 
```

连第二个电话都通不过！发生这种情况是因为我们没有进行我们之前讨论过的 PHP 转换，函数要求我们严格地传递一个整数。现在，用浮点数来调用它怎么样？

```
var_dump(plusone(1.5)); 
```

```
PHP Fatal error:  Wrong type encountered for argument 1 of function plusone, was expecting a integer but got a (float) 1.500000 in /vagrant/test_at/test.php on line 14 
```

通过使用[复合类型](https://github.com/box/augmented_types/wiki/The-Type-System#composite-types)定义，我们让我们的函数接受两种类型的参数(int 和 float)。

```
/**
 * Add one
 *
 * @param   int|float $a
 * @return    int
 */
function plusone($a)
{
    return $a + 1;
}

var_dump(plusone(1));
var_dump(plusone(1.5)); 
```

现在我们的函数应该像预期的那样工作了。

```
int(2)
PHP Fatal error:  Wrong type returned by function plusone, was expecting a integer but got a (float) 2.500000 in /vagrant/test_at/test.php on line 0 
```

哎呀！我们还应该为函数定义返回类型，或者将返回值转换为与 phpDoc 匹配。

```
/**
 * Add one
 *
 * @param   int|float $a
 * @return    int|float
 */
function plusone($a)
{
    return $a + 1;
} 
```

我们也可以使用复合类型:下面的例子对一个数组的元素求和。

```
/**
 * Calculate sum
 *
 * @param   array $nums
 * @return    int
 */
function sum($nums)
{
    $sum = 0;
    foreach ($nums as $value) {
        $sum += $value;
    }

    return $sum;
}

var_dump(sum([10, 12, 76]));

// output
int(98) 
```

该函数返回预期值。但是，如果数组包含的不是整数，那该怎么办呢？

```
var_dump(sum([10, 12, "something"]));

// output
int(22) 
```

该扩展使我们能够使用[数组类型](https://github.com/box/augmented_types/wiki/The-Type-System#array-types)。前面的例子将会抛出一个致命的错误。

```
/**
 * Calculate sum
 *
 * @param   int[] $nums
 * @return    int
 */
function sum($nums)
{
    $sum = 0;
    foreach ($nums as $value) {
        $sum += $value;
    }

    return $sum;
}

var_dump(sum([10, 12, 76]));
var_dump(sum([10, 12, "something"])); 
```

```
int(98)
PHP Fatal error:  Wrong type encountered for argument 1 of function sum, was expecting a (integer)[] but got a array in /vagrant/test_at/test.php on line 20 
```

一个有趣的例子是当我们有一个函数接受任意数量的参数。我们可以让前面的函数返回所有传递参数的总和。

```
/**
 * Calculate sum
 *
 * @param   *int $nums
 * @return    int
 */
function sum($nums)
{
    $args = func_get_args();
    $sum = 0;
    foreach ($args as $value) {
        $sum += $value;
    }

    return $sum;
}

var_dump(sum(10, 12, 76)); 
```

类型定义让我们的函数接收任意数量的参数，只要它是一个整数。我们可以结合`*int`和`int[]`类型定义，使我们的函数接受前面两种情况。

```
/**
 * Calculate sum
 *
 * @param   *int|int[] $nums
 * @return    int
 */
function sum($nums)
{
    if ( !is_array($nums) )
    {
        $nums = func_get_args();
    }

    $sum = 0;
    foreach ($nums as $value) {
        $sum += $value;
    }

    return $sum;
}

var_dump(sum(10, 12, 76));
var_dump(sum([10, 12, 76])); 
```

现在两个函数调用将返回相同的值(`int(98)`)。

### 默认参数

大多数情况下，函数包含可选参数，这些参数可以使用默认值进行初始化。我们使用`void`类型定义来告诉扩展，我们的值是可选的，但是如果传入的话是可以的。

```
/**
 * SSH to server.
 *
 * @param   string      $url
 * @param   int|void    $port
 * @return  bool
 */
function ssh($url, $port = 2222)
{
    return true;
} 
```

**注意**:当一个参数有默认值时，它不会强制指定的类型。这意味着在端口参数上传递一个字符串不会抛出错误。

### 返回类型

我们前面提到的参数类型同样适用于返回类型定义。我们可以指定普通的标量类型、类、复合类型和数组类型。每个函数都应该有一个`@return <type>`定义，如果不返回任何东西，**必须**使用`@return void`。

```
class User
{
    protected $id;

    protected $name;

    /**
     * Constructor
     * @param int       $id
     * @param string    $name
     * @return void
     */
    public function __construct($id, $name)
    {
        $this->id = $id;
        $this->name = $name;
    }

    /**
     * Return the user info as a string.
     *
     * @return    string
     */
    public function __toString()
    {
        return $this->name;
    }
} 
```

## 忽略文件

大多数时候，我们的应用程序包含其他人的包，我们不希望 augmented types 扩展抛出错误，并因为缺少定义而暂停我们的应用程序。幸运的是，该扩展提供了一种干净的方式来将文件和文件夹*列入黑名单*和*列入白名单*。

```
augmented_types_blacklist([__DIR__."/vendor"]); 
```

扩展现在将忽略我们的供应商目录。如果我们在供应商目录中有一些我们自己的可检查目录，我们可以使用*白名单*函数将它们添加回来。你可以在[文档](https://github.com/box/augmented_types/wiki/Whitelisting-and-Blacklisting)中读到更多相关内容。

```
augmented_types_whitelist([__DIR__."/vendor/mylib"]); 
```

我们也可以使用`php.ini`文件达到同样的结果，根据扩展的开发者，这是推荐的方法。

```
# php.ini
augmented_types.whitelist=./vendor
augmented_types.blacklist=./vendor/mylib 
```

## PHP 7 的变化

PHP 7 为函数/方法引入了标量类型声明和返回类型，从而消除了进一步验证参数和返回类型的需要。查看 PHP 官方网站上的[变更列表](http://php.net/manual/en/migration70.new-features.php)。

如果 PHP 7 已经支持这个，安装一个新的扩展会有什么好处呢？

这个扩展提供了 PHP 7 中没有的各种选项，比如:

*   复合类型(`@param string|int $var`)
*   数组类型(`@param int[] $var`)
*   强制函数返回值(至少是`void`)

## 结论

升级到 PHP 7 并从所有令人兴奋的新特性中受益总是一个好主意。然而，我们并不总是在项目中使用尖端技术。

增强的类型扩展绝对会减慢应用程序的运行时间(正如所预料的那样)，这就是为什么它应该只在开发和测试期间使用，以保持产品服务器干净和快速。

你觉得延期怎么样？有用吗？你对如何做这件事有其他想法吗？我们很想知道您的想法，请告诉我们！

## 分享这篇文章