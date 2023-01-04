# PHP 5.6 的新特性

> 原文：<https://www.sitepoint.com/new-features-php-5-6/>

说 PHP 的核心开发人员在一些特性上有一些小问题和一些非常荒谬的争论并不是亵渎——看看十年前关于为什么速记数组语法不好的愚蠢讨论。诸如此类的争论让人们认为一些核心开发人员在他们的日常生活中甚至不使用 PHP，这也是为什么 PHP 开发人员和使用 PHP 的人经常被认为是不专业的。

然而，未来并不黯淡。PHP 5.4 发布已经有一段时间了，新版本也越来越快。当 5.5 推出了一些意想不到的优秀特性时，PHP 社区松了一口气，并对更加专注、结构化和智能的核心开发重新燃起了希望。我们是否真的能做到这一点还有待观察，但未来确实看起来很有希望，尤其是如果你看看迄今为止 PHP 5.6 的变化。

虽然对所有即将到来的更新的完整解释在一篇文章中是太大了，我想把你的注意力引向一些我个人认为最重要的。

### CLI web 服务器中的 MIME 类型

PHP 中的 MIME 类型可用于将内容输出为 PHP 以外的类型，即 text/html 以外的类型。当您运行 PHP 页面时，默认输出是 text/html，但是您可以使用头文件将其设置为例如 PDF 并生成 PDF 文件。当服务器知道不同的 MIME 类型时，正如大多数服务器，如 HHVM、Apache 和 Nginx 通常所做的那样，它们知道如何默认地提供给定的文件，通过它的扩展名来判断，而不需要你在 PHP 本身中设置特定的指令。PHP 5.4 的命令行服务器到目前为止只有几种 MIME 类型，这个版本将引入更多的 MIME 类型。可以肯定地说，内置的 PHP 服务器将涵盖所有常见的 MIME 类型。

### 内部运算符重载

由于关键字“内部”，我们作为使用 PHP 的 web 开发人员可能不会接触到这个特性。内部意味着“非用户区域”,其中用户区域是我们 PHP 最终用户使用的 PHP 开发区域。它只适用于内部类，目的是使该领域的开发更简单，代码更易读。详细解释可以在[这里](https://wiki.php.net/rfc/operator_overloading_gmp)找到。

### 现在接受超过 2GB 的上传

在 5.6 之前，PHP 不支持 2GB 及以上的上传。正如 changelog 声明的那样，情况不再是这样了，现在支持任意大小的上传。

### POST 数据内存使用减少

在两次删除之后，POST 数据的内存使用量减少了 2 到 3 倍:来自`php.ini`的`always_populate_raw_post_data`设置和超全局变量`$HTTP_RAW_POST_DATA`。这意味着您不能再以这种方式访问原始帖子数据，而是需要依靠一种解决方案，例如:

```
$postdata = file_get_contents("php://input");
```

注意，当一个表单是多部分的(换句话说，当一个表单有一个文件上传元素)时，通过`://input`获取 POST 是不可用的。

### 改进了可变函数的语法

可变函数是可以接受任意数量参数的函数。当你给它提供一些参数的时候，通常要在调用`func_get_args`之后做一些拼接，有些不切实际。从示例[这里的](https://wiki.php.net/rfc/variadics)来看，5.5 和更早版本中的语法是:

```
class MySQL implements DB {
    protected $pdo;
    public function query($query) {
        $stmt = $this->pdo->prepare($query);
        $stmt->execute(array_slice(func_get_args(), 1));
        return $stmt;
    }
    // ...
}

$userData = $db->query('SELECT * FROM users WHERE id = ?', $userID)->fetch();
```

现在，语法将是:

```
class MySQL implements DB {
    public function query($query, ...$params) {
        $stmt = $this->pdo->prepare($query);
        $stmt->execute($params);
        return $stmt;
    }
    // ...
}
```

如您所见，`...$params`语法告诉函数原样接受第一个参数，并将所有其他参数放入$params 数组。这使我们摆脱了拼接和调用`func_get_params`，改进了函数签名，并使代码更具可读性。

新语法还允许通过引用传递额外的参数，在`...$params`前面加上一个&符号，比如:`&...$params`。这在`func_get_args`之前是不可能的。

### 参数解包

*注意:感谢[尼基克](http://www.reddit.com/user/nikic)指出这个特性——它是在本文最初撰写时实现的*

随着对可变函数支持的改进，[参数解包](https://wiki.php.net/rfc/argument_unpacking)也得到了批准。

到目前为止，调用带有任意数量参数的函数的唯一方法是使用 [`call_user_func_array`](http://www.php.net/call_user_func_array) ，字面意思是“调用带有参数数组的 userland 函数”。这很笨拙，在构造函数中不受支持，速度很慢，并且需要一个字符串形式的回调函数名，这意味着在大多数情况下没有 IDE 支持。

解包将消除上述函数的所有缺点，并自然地补充上面看到的变量支持。拆包工作是这样的:

```
$args = [1, 3, 5, 7, 9];
MyClass::someMethod(...$args);
```

这与呼叫是一样的

```
MyClass::someMethod(1, 3, 5, 7, 9);
```

即一个接一个地传入参数。这适用于任何可理解的场景，从类构造函数到在一次调用中被多次调用，什么都可以。参见上面链接的 RFC 以获得用法示例和进一步的解释。

### 常量标量表达式

这个 [RFC](https://wiki.php.net/rfc/const_scalar_exprs) 增加了在只期望静态值的地方拥有表达式的能力。这意味着你现在可以在常量声明、函数参数、类属性等中拥有基本的算术和逻辑结构。

例如，以前，像这样的代码会抛出一个错误:

```
const a = 1;
const b = a?2:100;
```

现在，情况不再是这样了。

如果一个常数依赖于另一个常数的值，人们可能会争论它是否真的是常数，但是这样的元讨论最好留给其他时候。

### PHPDBG 默认绑定

类似 gdb 的调试器 phpdbg 现在默认绑定为 [SAPI](http://stackoverflow.com/questions/9948008/what-is-sapi-and-when-would-you-use-it) 。它可以在命令行或简单的 Java UI 中使用，交互地指定断点，在运行时改变程序等等。它还可以检查操作码，并在您的 PHP 代码中使用。点击了解更多关于 phpdbg [的信息。](http://phpdbg.com/)

### Zip 改进

Zip 库得到了一些改进，特别是在新方法方面。一个特别突出的是`ZipArchive::setPassword($password)`，它最终允许你轻松地创建密码保护的 Zip 文件。

### 导入命名空间函数

根据这个 RFC 的[，新版本将允许导入命名空间函数和常量。现在，我们能够通过 use 语句导入名称空间和类型(类/接口/特征)，就像这样:](https://wiki.php.net/rfc/use_function)

```
namespace foo\bar {
    function baz() {
        return 'foo.bar.baz';
    }
}

namespace {
    use foo\bar as b;
    var_dump(b\baz());
}
```

从 5.6 开始，我们将能够使用`use function`和`use const`语句来导入一个单独的函数或常量(甚至是类常量)。

```
namespace {
    use function foo\bar as foo_bar;
    use const foo\BAZ as FOO_BAZ;
    var_dump(foo_bar());
    var_dump(FOO_BAZ);
}
```

### 结论

PHP 5.6，到目前为止还没有发布日期，看起来肯定很有前途，希望这个对变更日志的简短概述能帮助你理解一旦发布就升级的重要性。对于剩余的升级，请查看[新闻](https://github.com/php/php-src/blob/PHP-5.6/NEWS)文件，并继续检查更新。如果我错过了什么重要的东西，或者误解了什么，请在下面的评论中告诉我。

## 分享这篇文章