# PHP 的好处和坏处

> 原文：<https://www.sitepoint.com/the-delicious-evils-of-php/>

*这篇文章由[沃恩·安切塔](https://www.sitepoint.com/author/wancheta)和[德姬·阿卡拉](https://www.sitepoint.com/author/dakala/)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

* * *

我想看看两个 PHP 函数:`eval`和`exec`。它们经常被扔在明智的开发人员从不使用的公共汽车下，我有时想知道我们错过了多少令人敬畏的应用程序。

像标准库中的其他函数一样，这些函数也有它们的用途。它们会被滥用。它们的危险在于它们提供给哪怕是最新手的开发人员的灵活性和能力。

让我给你看一些我见过的使用这些的方法，然后我们可以谈谈安全预防措施和适度。

![Evil elephpant](img/4a2635960fd3011e552104711be745eb.png)

## 动态类创建

我第一次看到动态类创建是在 [CodeIgniter](https://www.codeigniter.com/) 的肚子里。当时，CodeIgniter 正在用它创建 ORM 类。`eval`仍然用于[为没有启用该功能的系统重写短开放标签](https://github.com/bcit-ci/CodeIgniter/blob/4b94152a48d8053ab72669278abfec18e1793310/system/core/Loader.php#L969)

不过，最近，我的朋友亚当·瓦森在推特上发布了关于用它来动态创建 T2 的拉勒维尔外观的消息。看看这些类通常是什么样子的:

```
namespace Illuminate\Support\Facades;

class Artisan extends Facade
{
    protected static function getFacadeAccessor()
    {
        return "Illuminate\Contracts\Console\Kernel";
    }
} 
```

*这是来自[github . com/laravel/framework/blob/5.3/src/Illuminate/Support/Facades/artisan . PHP](https://github.com/laravel/framework/blob/5.3/src/Illuminate/Support/Facades/Artisan.php)*

这些 facade 类不是传统意义上的 facade，但是它们确实充当存储在 Laravel 的[服务定位器类中的对象的静态引用。它们提供了一种简单的方法来引用在别处定义和配置的对象，并且比传统的静态(或单例)类有优势。这些好处之一是在测试中:](https://laravel.com/docs/5.3/container)

```
public function testNotificationWasQueued()
{
    Artisan::shouldReceive("queue")
        ->once()
        ->with(
            "user:notify",
            Mockery::subset(["user" => 1])
        );

    $service = new App\Service\UserService();
    $service->notifyUser(1);
} 
```

…虽然这些立面创建起来很简单，但数量却很多。我觉得写这种代码没什么意思。当我们写下推文时，亚当似乎也有同样的感受。

那么，我们如何动态地创建这些外观类呢？我没有见过 Adam 的实现代码，但我猜它看起来像这样:

```
function facade($name, $className) {
    if (class_exists($name)) {
        return;
    }

    eval("
        class $name extends Facade
        {
            protected static function getFacadeAccessor()
            {
                return $className::class;
            }
        }
    ");
} 
```

这是一个巧妙的诡计。无论您是否使用(甚至喜欢)Laravel facades，我猜您都能看到编写更少代码的好处。当然，这可能会增加每个请求的执行时间，但是我们必须对性能进行分析，以确定这是否很重要。

我以前在处理函数式编程库时也用过类似的技巧。我想创建支持代码，使我能够使用更少的(如果有的话)类编写应用程序。这是我用`eval`创建的:

```
functional\struct\create("person", [
    "first_name" => "string",
    "last_name" => "string",
]);

$me = person([
    "first_name" => "christopher",
    "last_name" => "pitt",
]); 
```

我希望这些结构可以从任何地方访问，并自我验证。我希望能够自由地传入一组属性，并且控件能够拒绝无效的类型。

> 下面的代码使用了许多`ƒ`的实例。这是一个 unicode 字符，我把它作为一种伪名称空间，用于我不能私有的属性和方法，但又不想让其他人直接访问。有趣的是，大多数 unicode 字符都是方法和属性名的有效字符。

为此，我创建了以下代码:

```
abstract class ƒstruct
{
    /**
     * @var array
     */
    protected $ƒdef = [];

    /**
     * @var array
     */
    protected $ƒdata = [];

    /**
     * @var array
     */
    protected $ƒname = "structure";

    public function __construct(array $data)
    {
        foreach ($data as $prop => $val) {
            $this->$prop = $val;
        }

        assert($this->ƒthrow_not_all_set());
    }

    private function ƒthrow_not_all_set()
    {
        foreach ($this->ƒdef as $prop => $type) {
            $typeIsNotMixed = $type !== "mixed";
            $propIsNotSet = !isset($this->ƒdata[$prop]);

            if ($typeIsNotMixed and $propIsNotSet) {
                // throw exception
            }
        }

        return true;
    }

    public function __set($prop, $value)
    {
        assert($this->ƒthrow_not_defined($prop, $value));
        assert($this->ƒthrow_wrong_type($prop, $value));

        $this->ƒdata[$prop] = $value;
    }

    private function ƒthrow_not_defined(string $prop)
    {
        if (!isset($this->ƒdef[$prop])) {
            // throw exception
        }

        return true;
    }

    private function ƒthrow_wrong_type(string $prop, $val)
    {
        $type = $this->ƒdef[$prop];

        $typeIsNotMixed = $type !== "mixed";
        $typeIsNotSame = $type !== type($val);

        if ($typeIsNotMixed and $typeIsNotSame) {
            // throw exception
        }

        return true;
    }

    public function __get($prop)
    {
        if ($property === "class") {
            return $this->ƒname;
        }

        assert($this->ƒthrow_not_defined($prop));

        if (isset($this->ƒdata[$prop])) {
            return $this->ƒdata[$prop];
        }

        return null;
    }
}

function type($var) {
    $checks = [
        "is_callable" => "callable",
        "is_string" => "string",
        "is_integer" => "int",
        "is_float" => "float",
        "is_null" => "null",
        "is_bool" => "bool",
        "is_array" => "array",
    ];

    foreach ($checks as $func => $val) {
        if ($func($var)) {
            return $val;
        }
    }

    if ($var instanceof ƒstruct) {
        return $var->class;
    }

    return "unknown";
}

function create(string $name, array $definition) {
    if (class_exists("\\ƒ" . $name)) {
        // throw exception
    }

    $def = var_export($definition, true);

    $code = "
        final class ƒ$name extends ƒstruct {
            protected \$ƒdef = $def;
            protected \$ƒname = '$name';
        }

        function $name(array \$data = []) {
            return new ƒ$name(\$data);
        }
    ";

    eval($code);
} 
```

*这类似于在[github.com/assertchris/functional-core](https://github.com/assertchris/functional-core)T3 发现的代码*

这里发生了很多事情，所以让我们来分解一下:

1.  `ƒstruct`类是这些自验证结构的抽象基础。它定义了`__get`和`__set`行为，包括检查用于初始化每个结构的数据的存在和有效性。
2.  当一个结构被创建时，`ƒstruct`检查是否已经提供了所有需要的属性。也就是说，除非任何属性是混合的，否则必须对它们进行定义。
3.  设置每个属性时，会根据该属性的预期类型检查提供的值。
4.  所有这些检查都是为了处理(并包装)对`assert`的调用而设计的。这意味着检查只在开发环境中执行。
5.  `type`函数用于返回最常见变量类型的可预测类型字符串。此外，如果变量是`ƒstruct`的子类，那么`ƒname`属性值将作为类型字符串返回。这意味着我们可以像`create("account", ["holder" => "person"])`一样容易地定义嵌套结构。需要注意的是，预定义类型(如`"int"`和`"string"`)总是在同名结构之前被解析。
6.  `create`函数使用`eval`来创建`ƒstruct`的新子类，包含适当的类名、`ƒname`和`ƒdef`。`var_export`获取变量的值并返回其语法字符串形式。

*[`assert`](http://php.net/function.assert)功能通常在生产环境中通过将`php.ini`中的`zend.assertions`设为 0 来禁用。如果您没有在预期的地方看到断言错误，请检查该设置的设置。*

## 领域特定语言

领域特定语言(通常称为 DSL)是替代的编程语言，可以很好地表达一个想法或问题领域。Markdown 就是一个很好的例子。

我用 Markdown 写这篇文章，因为它让我可以定义每一段文字的意义和重要性，而不会陷入文章的视觉外观。

CSS 是另一种优秀的 DSL。它提供了许多不同的方法来处理一个或多个 HTML 元素(通过一个选择器)，以便可以对它们应用视觉样式。

DSL 可以是内部的或外部的。内部 DSL 使用现有的编程语言作为它们的语法，但是它们在该语法中具有独特的结构。流畅的界面就是一个很好的例子:

```
Post::where("is_published", true)
    ->orderBy("published_at", "desc")
    ->take(6)
    ->skip(12)
    ->get(); 
```

这是您可能在 Laravel 应用程序中看到的一些代码的示例。它使用一个名为[雄辩](https://www.sitepoint.com/what-are-polymorphic-relations-and-how-do-we-use-them-with-eloquent/)的 [ORM](https://en.wikipedia.org/wiki/Object-relational_mapping) ，为一个 [SQL](https://en.wikipedia.org/wiki/SQL) 数据库建立一个查询。

外部 DSL 使用它们自己的语法，需要某种解析器或编译器将这种语法转换成机器代码。SQL 语法就是一个很好的例子:

```
SELECT * FROM posts
    WHERE is_published = 1
    ORDER BY published_at DESC
    LIMIT 12, 6; 
```

上面的 PHP 代码应该大致呈现为这个 SQL 代码。它通过网络被发送到一个 MySQL 服务器，该服务器将它转换成服务器可以理解的代码。

如果我们想制作自己的外部 DSL，我们需要将自定义语法转换成机器可以理解的代码。除了学习汇编器如何工作，我们可以将自定义语法翻译成低级语言。比如 PHP。

想象一下，我们想创造一种超级语言。这意味着该语言将支持 PHP 所做的一切，但也支持一些额外的语法。一个小例子是:

```
$numbers = [1, 2, 3, 4, 5];
print_r($numbers[2..4]); 
```

我们如何将它转换成有效的 PHP 代码呢？我在[的上一篇文章](https://www.sitepoint.com/php-macros-for-fun-and-profit/)中回答了这个问题，但它的要点是通过使用类似于以下的代码:

```
function replace($matches) {
    return '
        call_user_func(function($list) {
            $lower = '.explode('..', $matches[2])[0].';
            $upper = '.explode('..', $matches[2])[1].';
            return array_slice(
                $list, $lower, $upper - $lower
            );
        }, $'.$matches[1].')
    ';
}

function parse($code) {
    $replaced = preg_replace_callback(
        '/\$(\S+)\[(\S+)\]/', 'replace', $code
    );

    eval($replaced);
}

parse('
    $numbers = [1, 2, 3, 4, 5];
    print_r($numbers[2..4]);
'); 
```

这段代码获取一个类似 PHP 的语法字符串，并通过用标准 PHP 语法替换新语法来解析它。一旦语法成为标准 PHP，就可以评估代码了。它本质上是进行内联代码替换，这只有在代码可以动态执行时才有可能。

要做到这一点，如果没有`eval`函数，我们需要构建一个编译器。获取高级代码并返回低级代码的东西。在这种情况下，它需要获取我们的 PHP 超集语言代码，并返回有效的 PHP 代码。

## 平行

再来看看另一个让人厌倦的核心函数:`exec`。除了更有冒险精神的开发者之外，`exec`可能比`eval`受到更多的谴责。我想知道为什么。

如果你不熟悉，`exec`是这样工作的:

```
exec("ls -la | wc -l", $output);
print $output[0]; // number of files in the current dir 
```

`exec`是 PHP 开发人员在当前脚本的一个新的子进程中运行操作系统命令的一种方式。通过一点点刺激，我们实际上可以让这个子流程完全在后台运行:

```
exec("sleep 30 > /dev/null 2> /dev/null &"); 
```

为此:我们将`stdout`和`stderr`重定向到`/dev/null`，并在我们希望在后台运行的命令的末尾添加一个`&`。有很多原因可以让你这样做，但我最喜欢的是能够远离主 PHP 进程执行缓慢和/或阻塞的任务。

想象一下你有一个这样的脚本:

```
foreach ($images as $image) {
    $source = imagecreatefromjpeg($image["src_path"]);

    $icon = imagecreatetruecolor(64, 64);

    imagecopyresampled(
        $source, $icon, 0, 0, 0, 0,
        64, 64, $image["width"], $image["height"]
    );

    imagejpeg($icon, $image["ico_path"]);

    imagedestroy($icon);
    imagedestroy($source);
} 
```

对于一些图像来说，这很好。但是想象一下成百上千的图片，或者每秒几十个请求。这样的流量很容易影响服务器性能。在这种情况下，我们可以隔离缓慢的代码，并将其与面向用户的代码并行(甚至远程)运行。

下面是我们运行慢速代码的方法:

```
exec("php slow.php > /dev/null 2> /dev/null &"); 
```

我们甚至可以更进一步，为 PHP 命令行界面生成一个动态脚本来运行。首先，我们可以安装[超级封闭](https://github.com/jeremeamia/super_closure):

```
require __DIR__ . '/vendor/autoload.php';

use SuperClosure\Serializer;

function defer(Closure $closure) {
    $serializer = new Serializer();
    $serialized = $serializer->serialize($closure);

    $autoload = __DIR__ . '/vendor/autoload.php';

    $raw = '
        require \'' . $autoload . '\';

        use SuperClosure\Serializer;

        $serializer = new Serializer();
        $serialized = \'' . $serialized . '\';

        call_user_func(
            $serializer->unserialize($serialized)
        );
    ';

    $encoded = base64_encode($raw);

    $script = 'eval(base64_decode(\'' . $encoded . '\'));';

    exec('php -r "' . $script . '"', $output);

    return $output;
}

$output = defer(function() {
    print "hi";
}); 
```

当我们可以动态地生成我们想要运行的代码，并通过管道将它直接传输到 PHP 二进制文件中时，为什么我们需要硬编码一个脚本(以并行运行)？

我们甚至可以将这个`exec`技巧与`eval`结合起来，对我们想要运行的源代码进行编码，并在执行时对其进行解码。这使得启动子流程的命令总体上更加简洁。

我们甚至可以添加一个唯一的标识符，这样子过程就更容易被跟踪和杀死:

```
function defer(Closure $closure, $id = null) {
    // create $script

    if (is_string($id)) {
        $script = '/* id:' . $id . ' */' . $script;
    }

    $shh = '> /dev/null 2> /dev/null &';

    exec(
        'php -r "' . $script . '" ' . $shh,
        $output
    );

    return $output;
} 
```

## 保持安全

这么多开发人员不喜欢和/或反对`eval`和`exec`的主要原因是因为它们的误用会导致比`count`更灾难性的后果。

我建议，不要听信这些人的话，立即忽略`eval`和`exec`，而是要学习如何安全地使用它们。您要避免的主要事情是将它们与未经过滤的用户提供的输入一起使用。

不惜一切代价避免:

```
exec($_GET["op"] . " " . $_GET["path"]); 
```

请尝试:

```
$op = $_GET["op"];
$path = $_GET["path"];

if (allowed_op($op) and allowed_path($path)) {
    $clean = escapeshellarg($path);

    if ($op === "touch") {
        exec("touch {$clean}");
    }

    if ($op === "remove") {
        exec("rm {$clean}");
    }
} 
```

…或者更好:避免将任何用户提供的数据直接放入`exec`命令中！你也可以尝试其他的转义功能，比如 [escapeshellcmd](http://php.net/manual/en/function.escapeshellcmd.php) 。记住这是进入你系统的入口。运行 PHP 进程的用户被允许做的任何事情，`exec`都被允许做。这就是为什么它在共享主机上被故意禁用的原因。

和所有 PHP 核心函数一样，适度使用它们。请特别注意您允许输入的数据，并避免在`exec`中输入未经过滤的用户提供的数据。但是，不要在不明白为什么要回避的情况下回避这些功能。这对你或那些将向你学习的人没有帮助。

## 分享这篇文章