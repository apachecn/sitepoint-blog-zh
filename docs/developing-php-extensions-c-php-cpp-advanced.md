# 用 C++和 PHP-CPP 开发 PHP 扩展:高级

> 原文：<https://www.sitepoint.com/developing-php-extensions-c-php-cpp-advanced/>

在我之前的文章中，我已经介绍了 [PHP-CPP lib](http://www.php-cpp.com/) 来创建一个使用 C++的 PHP 扩展([第一篇](https://www.sitepoint.com/getting-started-php-extension-development-via-php-cpp/)和[第二篇](https://www.sitepoint.com/php-extension-development-php-cpp-object-oriented-code/))。在后一篇文章中，我演示了用一个`Complex`类进行复数操作来编写 PHP 扩展的面向对象方面。

![](img/843f52bc31f17a363606c2787dd6cf75.png)

该介绍并不完整，因为那篇文章的主要焦点更多的是关于 PHP-CPP 的面向对象能力的演示，而不是关于面向对象实现的细节。

在本文中，我们将进一步深入开发`Complex` lib，添加更多的成员函数，并解决使用 PHP-CPP 编写具有面向对象特性的 PHP 扩展的一些高级主题:

*   返回`this`指针；
*   返回一个`Complex`对象指针，即一个`Complex *`；
*   揭露`__toString`魔法；
*   链接成员函数调用；
*   抛出异常并在 PHP 中处理它

完成的`Complex` lib 源代码，以及一个测试 PHP 脚本位于这个 [Github repo](https://github.com/sitepoint-examples/complex) 中。

让我们开始吧。

## 准备

在第一篇文章的[中解释了准备环境的整个过程。](https://www.sitepoint.com/getting-started-php-extension-development-via-php-cpp/)

## 在 C++中返回`this`指针

如第二篇文章中的[所述，我们正在使用成员函数对复数进行各种数学运算。在这个演示中，我们将实现其中的四个功能:`add`、`sub`、`mul`和`div`。我先解释前三个。`div`函数涉及异常处理，将在后面讨论。](https://www.sitepoint.com/php-extension-development-php-cpp-object-oriented-code/)

让我们来看看`mul`函数(用于乘法运算)。`add`和`sub`功能大致相同。

```
Php::Value add(Php::Parameters &params) {
        Php::Value t = params[0];
        Complex *a = (Complex *) t.implementation();

        r += (double) a->getReal();
        i += (double) a->getImage();

        return this;
    }
```

**注意:**在这篇文章中，我不会涉及一些之前讨论过的基本话题，比如修改`Makefile`和 ini 文件，注册成员函数、类和名称空间等等。这些位请参考前面的部分。

将一个`this`指针从 C++返回 PHP 非常简单。在这个 C++函数中，`this`指针(作为一个`Complex *`类型)可以作为 Php::Value 类型返回给 PHP。转换不会丢失任何对象信息。它也不需要显式的类型转换。

## 返回一个`Complex`对象指针

返回`this`意味着，大多数时候，对象本身已经被改变。但在某些情况下，我们可能希望返回一个新对象，并保持“当前”对象(调用对象)不变。

在我们的`Complex`类中，我们有一个这样的函数，它返回给定复数的共轭数(`a+bi`变成了`a-bi`)。

```
Php::Value conjugate() {
        Complex *t = new Complex();

        t->r = r;
        t->i = -i;

        return Php::Object("tr\\Complex", t);
    }
```

这里的关键点是，我们必须使用`Php::Object`来显式地将我们的`Complex *`对象转换成`Php::Object`，这样当 PHP 脚本稍后解析该对象时，类信息可以被正确地保留并保持可访问。

这个函数的第一个参数是类类型，在这个例子中是`tr\\Complex`。我使用这个名字是因为我已经将这个类(“`Complex`”)包装到一个单独的名称空间(“`tr`”)。

第二个参数是要传回的对象。

返回一个新的类实例比仅仅返回一个`this`指针要稍微复杂一些，但是仍然是可以管理的，只要你通读了文档并且找到了正确的部分。更多的使用例子，你可能想阅读 PHP-CPP 官方文档中的[这部分](http://www.php-cpp.com/documentation/variables#objects)。

## 曝光`__toString`神奇的方法

在我们的类中，有一个`__toString`函数，它以更易读的方式打印一个复数，比如:`1+2i`。在我以前的文章中，这个函数没有公开(或者用 PHP-CPP 术语“注册”)，但是仍然可以从 PHP 内部调用。但是为了使这个函数在我们应用了一些像“`echo $a->add($b)->sub($c)`”这样的数学运算之后可以在`Complex`对象上调用，我们需要在我们编译的扩展中显式地注册它:

```
complex.method("__toString", &Complex::__toString);
```

我们必须这样做的原因在提交给 PHP-CPP repository 的第 150 期中有详细讨论。

## 链接成员函数调用

这个类中必须实现的一件事是链接成员函数的能力，这样我们就可以进行类似于`$a->add($b)->sub($c)`的计算。结果应该还能调用它的成员函数。

这是通过上述方法完成的，即返回一个指向 PHP 的`this`指针。然而，旧的 PHP-CPP lib 在解引用对象方面有一个缺陷，如果方法调用被链接，将会产生一个“分段错误”。

已经提交了一个问题( [#151](https://github.com/CopernicaMarketingSoftware/PHP-CPP/issues/151) )并提交了一个[提交](https://github.com/CopernicaMarketingSoftware/PHP-CPP/commit/ff9a22782a7d28b11af0ff2d3948a196ab12d003)和 PHP-CPP 源代码上的补丁。如果您正在使用旧版本的 PHP-CPP lib 来编译 PHP-CPP lib 和您自己的 lib，请更新 PHP 源代码并重新编译和安装 PHP-CPP lib 和您的 lib。

正如提交摘要所解释的:

```
fix issue #151, chaining method calls was not working as it should be…
…cause the per-object refcount was not updated correctly, which caused an object to be destructed even when it already was assigned to a different variable.
```

我很高兴我在自己项目中的工作可以帮助我使用的库变得更好。做得好！]

## PHP 中的异常抛出和处理

在我们的`Complex`类中还有两个函数可能会抛出一个异常给 PHP 来处理:`div`和`phi`。前者进行除法运算，后者返回复数的角度，作为其替代表示，极化符号`(r, θ)`。

如果将一个复数作为参数(或调用者)传递，但它的实部和图像部分都是 0，那么这两个操作都可能失败。对于这两个操作，我们需要异常处理。请记住，我们将在 C++代码中抛出一个异常，由 PHP 脚本捕捉异常并进行必要的处理:

```
Php::Value div(Php::Parameters &params) {
        Php::Value t = params[0];
        Complex *b = (Complex*) t.implementation();

        double t1 = b->mod() * b->mod();

        if (t1 < EPS) //EPS is a predefined double value which is very small, say 1E-12
            throw Php::Exception("Division by zero");

        double tr = r * (double) (b->getReal()) + i * (double) (b->getImage());
        double ti = i * (double) (b->getReal()) - r * (double) (b->getImage());

        r = tr / t1;
        i = ti / t1;

        return this;
    }
```

在 PHP 脚本中，我们像这样捕获这个异常:

```
$a=new tr\Complex(1,2);
$c=new tr\Complex(); //$c is actuall 0+0i

try
{
    $res=$a->div($c);
}
catch(Exception $e)
{
    echo "Caught exception: ".$e->getMessage()."\n";
}
}
```

上面的代码段将显示如下一行文本:

```
Caught exception:  Division  by zero
```

很简单，对吧？在我们的扩展中构造的 C++异常被传递回 PHP 并被正确捕获。此外，我们可以操纵异常，就好像它是由其他 PHP 代码抛出的本地 PHP 异常一样！

## 测试所有功能

最后，我们可以通过`make && sudo make install`为我们的 PHP 安装编译并安装`complex.so`扩展。如果一切顺利，我们可以通过在终端发出以下命令来验证扩展的安装:

```
php -i | grep complex
```

终端应该显示一行“`/etc/php5/cli/conf.d/complex.ini`”，我们可以确定我们的扩展已经安装好，可以被任何 PHP 脚本调用。

**注意:**如果我们检查这个扩展的`Makefile`，我们会看到我们正在将这个 PHP 扩展安装到它的 CLI 环境中。如果我们想安装这个扩展，以便 Apache 可以加载它，我们需要修改下面的代码行:

```
# Below is for installation to CLI
#INI_DIR = /etc/php5/cli/conf.d
# Below is for installation to Apache
INI_DIR  = /etc/php5/apache2/conf.d
```

这个扩展的测试 PHP 脚本摘录如下，并附有一些注释:

```
//Testing the mod function
//Displays 5
$c=new tr\Complex(-3,-4);
echo "Mod of $c is: ".$c->mod()."\n";
...
//Testing a string representation of a complex number
//Displays: -4-3i
$e=new tr\Complex(-4,-3);
echo ((string)$e."\n");
...
//Chain the member function calls
$a=new tr\Complex(1,1);
$b=new tr\Complex(1,2);
...
echo ($a->add($b)->sub($c)->add($d))."\n";
...
//Exception handling
$a=new tr\Complex(1,2);
$c=new tr\Complex();

try
{
    $res=$a->div($c);
}
catch(Exception $e)
{
    echo "Caught exception: ".$e->getMessage()."\n";
}
```

所有的测试脚本都应该能够正确运行，并且能够正确捕捉异常。

## 结论

这就结束了我关于这个强大的库的 3 篇文章系列，用 C++构建 PHP 扩展。我们讨论了基础知识、面向对象方面以及面向对象编程中的一些高级主题。我们也帮助 PHP-CPP 改进了一些。

PHP-CPP 还能做什么？我将引用我从 Emiel Bruijntjes(PHP-CPP 的合著者)那里收到的电子邮件中的几句话:

> 如果您正在从事一个项目，并且您有一个或多个以下需求，PHP-CPP 库是理想的选择:
> –您正在从事一个软件/数据结构/算法，并且您想要确保将来您的软件也可以在非 PHP 项目中使用。
> –您想要使用尚未作为 PHP 扩展提供的工具或库。
> –您希望 C/C++代码具有更好的性能(与 PHP 相比)，但是您也希望构建结构化的、面向对象的代码，以便于其他开发人员/同事理解和维护。

可能性是巨大的:一个框架(像 Phalcon)，一个模板语言(像 Smarty 或 Twig)等等。

请留下你的评论和观点，让我们知道你对这个库做了什么！

## 分享这篇文章