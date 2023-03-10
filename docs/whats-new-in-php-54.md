# PHP 5.4 发布了——有什么新消息？

> 原文：<https://www.sitepoint.com/whats-new-in-php-54/>

很难相信 PHP 5.3.0 已经过去将近三年了。下一个版本应该是 PHP 6.0，但是 unicode 问题推迟了开发。这个最新版本提供了许多为 6.0 版设计的特性。

[**PHP 5.4 可从 php.net 网站**](http://docs.php.net/downloads.php) 下载。如果你想保留你的旧设置，有一个 [PHP 5.3 迁移指南](http://uk.php.net/migration54)。虽然它很稳定，但建议您在将它安装到实时服务器上之前测试您的站点和应用程序。PHP 团队通常会在最初发布几周后发布一个 bug 修复版本。

因此，让我们来看看最好的新功能和改进…

## 短数组语法

现在可以使用节省手指的类似 JavaScript 的方括号，而不是使用旧的`array(…)`结构，例如

```
 $array1 = [1, 2, 3];

$array2 = [
	"one" => "first",
	"two" => "second",
	"three" => "third"
]; 
```

## 特征

性状减少了单一遗传的一些限制。本质上，特征类似于抽象类，可以包含任意数量的属性和方法。一个类可以有任意数量的特征，例如

```
 trait Hello
{
    function sayHello() {
        return "Hello";
    }
}

trait World
{
    function sayWorld() {
        return "World";
    }
}

class MyWorld
{
    use Hello, World;
}

$world = new MyWorld();
echo $world->sayHello() . ' ' . $world->sayWorld(); 
```

更多信息，请参考[在 PHPmaster.com](https://www.sitepoint.com/using-traits-in-php-5-4/)上使用 PHP 5.4 中的 Traits。

## 内置 Web 服务器

PHP 5.4 提供了一个内置的 web 服务器，可以从 Windows、Mac 或 Linux 命令行运行。虽然它不是 Apache 或 IIS，但对于简单的测试来说还是不错的。我怀疑许多更好的 PHP IDEs 将很快实现支持。

更多信息，请参考 [PHP 5.4 新的内置网络服务器](https://www.sitepoint.com/php-54-web-server/)。

## 新命令

已经实现了许多有用的命令:

1.  [hextobin(string $hex)](http://uk.php.net/manual/en/function.hex2bin.php) :将数据的十六进制表示转换为二进制表示。
2.  [http _ response _ code(int＄code)](http://uk.php.net/manual/en/function.http-response-code.php):允许您设置或获取 HTTP 响应代码，例如`http_response_code(404);`
3.  [header _ register _ callback(string $ function)](http://uk.php.net/manual/en/function.header-register-callback.php):允许你注册一个函数，这个函数在 PHP 开始发送输出时被调用。
4.  [trait_exists(string $name [，bool $autoload])](http://uk.php.net/manual/en/function.trait-exists.php) :确定一个特性是否存在，以及是否应该自动加载。

## 杂项更新

如果这还不够…

*   可以在实例化时访问类成员，例如`(new MyClass)->MyMethod()`
*   无论如何设置 short_open_tag ini 选项,`<?=$variable?>`始终可用。
*   可以声明二进制数，例如`0b11110000`
*   会话上传进度已经实现，所以 PHP 可以跟踪文件上传的状态。
*   一些用户报告速度提高了 25%,而内存减少了 35%。

## 兼容性问题

大多数旧的 PHP 代码*应该不用修改就能运行*,但是有一些问题需要注意:

*   [安全模式](http://uk.php.net/manual/en/features.safe-mode.php)、[注册 _ 全局](http://uk.php.net/manual/en/ini.core.php#ini.register-globals)和[魔法引号](http://uk.php.net/manual/en/security.magicquotes.php)被移除。 [get_magic_quotes_gpc()](http://uk.php.net/manual/en/function.get-magic-quotes-gpc.php) 将始终返回 FALSE。
*   [trait](http://uk.php.net/manual/en/language.oop5.traits.php) ，callable 和[代替](http://uk.php.net/manual/en/language.oop5.traits.php)成为保留字。
*   几个 mysqli 别名已被删除。

你还应该注意到 PHP 5.4.x 将是支持 Windows XP 和 Windows 2003 的最后一个版本。

PHP 5.4 没有 5.3 那么激进，但是有足够多的新特性让开发者高兴一段时间。如果您对最新版本有任何正面或负面的体验，请告诉我们。

## 分享这篇文章