# PHP 中的转换器变得简单

> 原文：<https://www.sitepoint.com/transducers-in-php-explained-and-demonstrated/>

听说过[函数式编程](https://en.wikipedia.org/wiki/Functional_programming)，高阶函数等吗？之前？大概是吧？但是，当你听到“换能器”的时候，你知道那些是什么吗？

![Input/output vector illustration](img/77d8d814f5f932b2fe6b3d672d835758.png)

## 传感器的定义

我们不能在不首先谈论减速器的情况下定义换能器。引用 [Rich Hickey](https://twitter.com/richhickey) :

> reducing 函数就是你要传递的那种函数——它接受目前为止的一个结果和一个新的输入，并返回下一个结果。

> 转换器是一个函数，它接受一个缩减函数并返回另一个缩减函数。

Rich Hickey 首先将传感器引入到 Clojure 中，而 T4 将 T2 移植到 PHP 中。转换器是构建算法转换的强大方法，您可以在许多上下文中重用它。在本文中，我们将通过一组实际例子来看看它们是如何有用的。

## 例子

在进一步操作之前，我们需要通过 Composer 安装传感器包。

```
composer require mtdowling/transducers 
```

在下面的例子中，我们将使用一个简单的`User`类。

```
class User
{
    public $id;
    public $name;
    public $age;

    public function __construct($id, $name, $age)
    {
        $this->id = $id;
        $this->name = $name;
        $this->age = $age;
    }

    public function __toString()
    {
        return sprintf("\n%d - %s - %d", $this->id, $this->name, $this->age);
    }
}

// demo data
$data = [
    new User(1, "younes", 24),
    new User(2, "youssef", 26),
    new User(3, "hamza", 25),
    new User(4, "ismail", 17),
]; 
```

```
use Transducers as t;

$uppercase = t\map(function($user) { 
    return new User($user->id, ucfirst($user->name), $user->age); 
});

$result = t\xform($data, $uppercase);

var_dump($result); 
```

`map`函数类似于 [array_map](http://php.net/manual/en/function.array-map.php) PHP 函数:我们传递一个[可调用的](http://php.net/manual/en/language.types.callable.php)，在本例中，它将大写用户名的第一个字母。

我们使用`xform`函数来应用我们的`uppercase`传感器。它将我们的数据作为第一个参数，将传感器作为第二个参数。

```
// output
array(4) {
  [0]=>
  object(User)#14 (3) {
    ["id"]=>
    int(1)
    ["name"]=>
    string(6) "Younes"
    ["age"]=>
    int(24)
  }
  [1]=>
  object(User)#15 (3) {
    ["id"]=>
    int(2)
    ["name"]=>
    string(7) "Youssef"
    ["age"]=>
    int(26)
  }
  [2]=>
  object(User)#16 (3) {
    ["id"]=>
    int(3)
    ["name"]=>
    string(5) "Hamza"
    ["age"]=>
    int(25)
  }
  [3]=>
  object(User)#17 (3) {
    ["id"]=>
    int(4)
    ["name"]=>
    string(6) "Ismail"
    ["age"]=>
    int(17)
  }
} 
```

`xform`返回与`data`参数相同的类型(本例中为数组)。如果你真的想输出一个数组，我们也可以使用`to_array`。

```
// ...
$result = t\to_array($data, $uppercase);
// ... 
```

我们也可以使用`to_string`将输出转换成字符串，或者使用`into($target, $coll, callable $xf)`将输出转换成特定的类型。查看[文档](https://github.com/mtdowling/transducers.php)了解更多详情。

```
use Transducers as t;

$uppercase = t\map(function($user) { 
    return new User($user->id, ucfirst($user->name), $user->age); 
});

$result = t\to_string($data, $uppercase);

var_dump($result); 
```

```
// output
string(64) "
1 - Younes - 24
2 - Youssef - 26
3 - Hamza - 25
4 - Ismail - 17" 
```

转换器最大的优点是我们可以将多个转换组合成一个转换器。例如，让我们大写用户名的第一个字母，并删除未成年人。

```
$uppercase = t\map(function($user) { 
    return new User($user->id, ucfirst($user->name), $user->age); 
});
$removeMinors = t\filter(function($user) { 
    return $user->age >= 18;
});

$comp = t\comp(
    $uppercase,
    $removeMinors
);

$result = t\to_string($data, $comp);

var_dump($result); 
```

`filter`函数类似于 [array_filter](http://php.net/manual/en/function.array-filter.php) PHP 函数。`comp`功能从传感器列表中创建一个传感器，在本例中为`uppercase`(使用`map`)和`removeMinors`(使用`filter`)。

```
// output
string(48) "
1 - Younes - 24
2 - Youssef - 26
3 - Hamza - 25" 
```

现在我们有了一个可重复使用的传感器组合，我们可以在任何时候使用这个标准来减少我们的数据。查看[文档](https://github.com/mtdowling/transducers.php#using-transducers)中可用的缩减功能列表。

## 创建传感器

减函数将一个值作为参数，并返回一个减函数数组，该数组必须包含三个元素:

*   `init`:返回传感器初始值的功能。只有在没有提供初始值的情况下，才会在第一次调用它。
*   `result`:调用`result`函数从调用堆栈中构建最终结果。
*   这是你写归约逻辑的地方——根据你的归约逻辑，你可以调用它零次或多次。

如果不展示一些实际的代码，这将变得非常混乱，所以让我们使用`take` transducer 函数作为例子。它从数据数组的顶部取出`n`个项目。

```
// ....
$comp = t\comp(
    $uppercase,
    $removeMinors,
    t\take(2)
);

$result = t\to_string($data, $comp);

var_dump($result); 
```

```
// output
string(33) "
1 - Younes - 24
2 - Youssef - 26" 
```

这是`take`减速器功能的源代码。

```
function take($n)
{
    return function (array $xf) use ($n) {
        $remaining = $n;
        return [
            'init'   => $xf['init'],
            'result' => $xf['result'],
            'step'   => function ($r, $input) use (&$remaining, $xf) {
                $r = $xf['step']($r, $input);
                return --$remaining > 0 ? $r : ensure_reduced($r);
            }
        ];
    };
} 
```

用`result`和`input`参数多次调用`take`函数。在每次调用时，它递减变量`remaining`并测试它是否小于零。在这种情况下，我们返回一个`Reduced`对象实例，它表示一个停止点。

我们的传感器函数示例将从数据中删除空元素。使用前面对转换器如何工作的解释，我们可以访问`$input`变量，并决定是调用下一个`step`回调还是简单地返回值。

```
function dropNull()
{
    return function (array $xf) {
        return [
            'init'   => $xf['init'],
            'result' => $xf['result'],
            'step'   => function ($result, $input) use ($xf) {
                return $input === null
                    ? $result
                    : $xf['step']($result, $input);
            }
        ];
    };
} 
```

我们可以通过向我们的`$data`变量添加一些`null`项来测试这一点。

```
$data = [
    null,
    new User(1, "younes", 24),
    new User(2, "youssef", 26),
    new User(3, "hamza", 25),
    new User(4, "ismail", 17),
    null
];
$result = t\to_string($data, t\dropNull());

var_dump($result); 
```

```
// output
string(64) "
1 - younes - 24
2 - youssef - 26
3 - hamza - 25
4 - ismail - 17" 
```

## 结论

在本文中，我们了解了函数式编程世界的一个新的方面，称为转换器。我们已经讨论了传感器的目的，即使数据转换更容易。我们还看了一些例子，以更好地展示传感器的价值。现在，您的开发人员带中有了一个新工具，或者至少对传感器概念有了更好的理解。

如果你对换能器有任何疑问，可以在下面贴出！

## 分享这篇文章