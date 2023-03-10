# 用 PHP-CPP 开发 PHP 扩展:面向对象代码

> 原文：<https://www.sitepoint.com/php-extension-development-php-cpp-object-oriented-code/>

在我的关于用 PHP-CPP 构建 PHP 扩展的[第 1 部分中，我们已经看到了使用](https://www.sitepoint.com/getting-started-php-extension-development-via-php-cpp/) [PHP-CPP](http://www.php-cpp.com/) 创建我们的第一个框架 PHP 扩展的一些基础知识，并且涵盖了一些重要的术语。

在这一部分，我们将进一步阐述它的面向对象特性。我们将模仿一个复数(以`3+4i`的形式)类来展示 PHP-CPP 库的一些更有用和更强大的方面。

我们从修改 PHP-CPP 提供的空项目文件开始，并修改以下两个文件以适应我们的新库(本例中为`complex.so`)和我的系统:

*   将`yourtextension.ini`重命名为`complex.ini`，并将其内容改为`extension=complex.so`。
*   修改`Makefile`中的几行:

```
NAME = complex
INI_DIR =  /etc/php5/cli/conf.d
```

现在我们继续在`main.cpp`中创建我们的`Complex`类。

### C++构造函数和 PHP 构造函数

在 C++类中，有构造函数和析构函数。构造函数以熟悉的形式出现，没有返回类型，以类名作为其函数名，具有可选的不同参数集(在它们的重载版本中)，而析构函数没有返回类型，没有参数，共享以倾斜(`~`)为前缀的类名:

```
class Complex
{
    Complex();
    // We can have many other overloaded versions of the constructor 
    ~Complex();
} 
```

我们也知道在 PHP 中，我们有一些“神奇”的方法，特别是作为类构造函数和析构函数的`__construct()`和`__destruct()`。

PHP-CPP 为构造函数/析构函数支持这种类似 PHP 的神奇方法。这使得我们用 class 实现 PHP 扩展更加容易。

( [PHP-CPP 官方文档](http://www.php-cpp.com/documentation/constructors-and-destructors)详细解释了这两种“构造器/析构器”的区别。请阅读该文档和其他相关文档以了解更多详细信息。)

我个人的观点是，由于我们最终将在 PHP 脚本中使用这个类(用 C++编写),我们应该在我们的类中实现两个:C++构造函数/析构函数和类似 PHP 的神奇构造函数/析构函数。代码片段如下所示:

```
class Complex : public Php::Base {
private:
    double r = 0, i = 0;

public:
    /**
     *  C++ constructor and destructor
     */
    Complex() {
    }

    virtual ~Complex() {
    }

    Php::Value getReal() {
        return r;
    }

    Php::Value getImage() {
        return i;
    }

    void __construct(Php::Parameters &params) {
        if (params.size() == 2) {
            r = params[0];
            i = params[1];
        } else {
            r=0;
            i=0;
        }
    }
```

在这段代码中，有几件事需要进一步阐述:

1.  我们库中声明的任何类都必须从`Php::Base`继承。这是 PHP-CPP 的要求。`Php::Base`有一些面向对象操作的底层方法的基本实现。(`Php::Base`类的头文件是`base.h`。)
2.  我们声明了一个 C++构造函数/析构函数和一个类似 PHP 的神奇构造函数。后者也是参数化的，因此我们可以通过在对象实例化期间从 PHP 传入的参数来设置复数的实数/虚数部分。
3.  由于我们类中定义的复数的实部/虚部是`private`，我们还定义了两个 getter 函数来返回这两个部分:`getReal()`、`getImage()`。PHP-CPP 也支持类中的属性。更多详情请参见[本文件](http://www.php-cpp.com/documentation/properties)。
4.  最后，由于这是一个相当简单的类，我们实际上并没有在 C++构造函数/析构函数中添加任何代码。所有初始化工作都在类似 PHP 的构造函数中完成。

### 返回复数模的成员函数

复数的模被定义为笛卡尔坐标系中到原点的距离。它通过以下公式计算:

```
mod=sqrt(r*r+i*i)
```

首先讨论这个函数，因为它非常简单:它只使用类成员进行处理，并返回一个标量值。

因此，该功能的实现非常简单。

```
Php::Value mod()  const  {  return  (double)sqrt(r * r + i * i);  }
```

你可能需要`#include <cmath>`进行数学运算。

我们将讨论如何注册这个函数，以便以后 PHP 可以使用它。

像我们在第 1 部分中讨论的常规函数签名一样，PHP-CPP 只支持一个成员函数的 8 个签名:

```
// signatures of supported regular methods  void  YourClass::example1();  void  YourClass::example2(Php::Parameters  &params);  Php::Value  YourClass::example3();  Php::Value  YourClass::example4(Php::Parameters  &params);  void  YourClass::example5()  const;  void  YourClass::example6(Php::Parameters  &params)  const;  Php::Value  YourClass::example7()  const;  Php::Value  YourClass::example8(Php::Parameters  &params)  const;
```

任何与上述签名不同的函数仍然可以出现在类声明中，但是不能被注册，并且对 PHP 脚本不可见。

在这个`mod()`函数中，我们使用的是它的第 7 种形式。

### 做加法的成员函数

创建一个将两个复数相加的函数有点复杂。

在传统的 C++中，我们有 3 个选项:

1.  重写运算符`+`，这样两个数的加法运算就像:`c+d`一样简单优雅。不幸的是，这在 PHP 中是不可能的，因为 PHP 不支持操作符重写。
2.  这个类的一个成员函数叫做“`add`”，参数是另一个复数。所以加法会这样做:`c->add(d)`。
3.  一个带两个参数的正则函数，返回一个新的复数，像这样:`complex_add(c, d)`。

选项 3 通常被认为是最差的选项，我们将看到如何使用选项 2 创建这样的加法函数。

```
 Php::Value add(Php::Parameters  &params)  {  Php::Value t=params[0];  Complex  *a=(Complex  *)t.implementation(); r+=(double)a->getReal(); i+=(double)a->getImage();  return  this;  }``
```

`add`功能本身有两个至关重要的方面:

首先，请注意函数签名。它使用 PHP-CPP 支持的第四种形式。它返回一个`Php::Value`并接受一个类型为`Php::Parameters`的参数，其中所有参数——在本例中只有一个——都以数组形式传递。

然后第一行将传递给函数(`params[0]`)的第一个参数赋给一个`Php::Value`类型的变量`t`。

正如我们将在后面的函数注册中看到的，我们可以非常确定这个变量虽然被赋值为`Php::Value`类型，但实际上是一个`Complex`类型的变量。因此，我们可以安全地将该变量转换为指向复杂类实例的指针。这正是第二行所做的:

```
Complex  *a=(Complex  *)t.implementation();
```

在`value.h`的`Php::Value`类中实现了`implementation()`方法。根据对该功能的解释，我引用如下:

```
 /**
     *  Retrieve the original implementation
     * 
     *  This only works for classes that were implemented using PHP-CPP,
     *  it returns nullptr for all other classes
     * 
     *  @return Base*
     */ 
```

它的关键任务是将一个`Php::Value`转换成它的底层类实现。

这也解释了为什么我们必须从`Php::Base`派生我们自己的类，因为这个函数返回一个`Php::Base`指针。

具有讽刺意味的是，如此重要的功能在 PHP-CPP 目前的文档集中却没有得到解释。我从 PHP-CPP 的一个作者那里得到了这个技巧(Emiel，谢谢！)通过一些邮件交流。

当这个`implementation`到位时，`add`功能是可能的。

### 以更友好的方式显示复数的神奇功能

PHP-CPP 支持 PHP [【魔法方法】](http://www.php-cpp.com/documentation/magic-methods)。我们在前面的段落中已经看到了`__construct()`，让我们通过在我们的复杂类中创建一个`__toString()`方法来进一步演示这一点，以更直观的方式打印复数，就像`3+4i`。(您可能需要在下面的代码中使用`#include <sstream>`进行字符串输出操作。)

```
 Php::Value __toString()  { std::ostringstream os; os<<r;  if(i>=0) os<<'+'; os<<i<<'i';  return os.str();  } 
```

这个函数的实现很简单。

### 注册到目前为止创建的所有函数

对于这个版本的被模仿的复杂类，我只实现了上面的几个函数。鼓励感兴趣的用户扩展这个类以添加更多的功能(例如，基本的算术运算)。第 1 部分中复杂类和演示的代码可以从 Github 中克隆[。](https://github.com/phpmasterdotcom/complex-phpcpp)

为了让这些函数在 PHP 脚本中可见(可调用)，我们需要注册它们。注册一个类及其方法与注册一个常规函数有点不同:

```
extern  "C"  { PHPCPP_EXPORT void  *get_module()  {  // create static instance of the extension object  static  Php::Extension myExtension("complex",  "1.0");  // description of the class so that PHP knows which methods are accessible  Php::Class<Complex> complex("Complex");  // register the methods, and specify the parameters complex.method("mod",  &Complex::mod,  {}); complex.method("__construct",  &Complex::__construct); complex.method("add",  &Complex::add,  {  Php::ByVal("op",  "Complex",  false,  true)  });  // add the class to the extension myExtension.add(std::move(complex));  // return the extension  return myExtension;  }  } 
```

在这个`get_module()`函数中，我们创建了一个`Php::Class`变量(`complex`)来保存我们的类(`Complex`)信息。然后我们用`complex.method()`注册成员函数。

在注册`add`函数的最后一个`complex.method()`中，我们指定了它的参数数量和类型。要了解更多关于如何注册类和声明方法参数的信息，请参考[参数](http://www.php-cpp.com/documentation/parameters)和[类和对象](http://www.php-cpp.com/documentation/classes-and-objects)。

最后，我们将`complex`变量移到扩展中，并返回该扩展。

### 编译、安装和测试

现在，我们可以用下面的命令编译并安装这个`complex.so`扩展:

```
make && sudo make install
```

如果一切顺利，扩展将被安装并准备使用。

为了测试扩展，让我们编写几行 PHP 代码:

```
<?php

$c=new  Complex(-3,-4); echo $c->mod()."\n";  // First echo $d=new  Complex(4,3); echo $c->add($d)->mod()."\n";  // Second echo echo ((string)$d."\n");  // Third echo $f=new \DateTime(); $g=$d->add($f);  // Fourth echo but displays an error
```

第一个`echo`将打印`5`，这正是我们所期望的。

第二个`echo`会显示`1.41421...`。由于我们已经从`add`函数返回了一个`this`指针，我们可以将加法运算和`mod()`链接在一行中。

第三个`echo`显示`4+3i`。当我们试图使用我们的`__toString()`魔术方法来字符串化一个复数时，这是意料之中的。它使用我们定义的实现将一个复数转换成一个字符串。

在第四个`echo`中，我们将遇到一个错误，因为参数不是类型`Complex`(而是一个`DateTime`对象)。这显示了 PHP-CPP 在传递对象时严格的类型检查:对象类型必须与注册的类型匹配。

### 在名称空间中包装

让我们将刚刚创建的类包装到一个名称空间中，以获得更好的封装。

这个过程简单得惊人。我们根本不需要改变类的声明/实现。我们需要做的就是在`get_module()`函数中改变注册过程，现在看起来是这样的:

```
extern  "C"  { PHPCPP_EXPORT void  *get_module()  {  // create static instance of the extension object and the namespace instance  static  Php::Extension myExtension("complex",  "1.0");  Php::Namespace myNamespace("trComplex");  // description of the class so that PHP knows which methods are accessible  Php::Class<Complex> complex("Complex");  // register the methods, and specify the parameters complex.method("mod",  &Complex::mod,  {}); complex.method("__construct",  &Complex::__construct); complex.method("add",  &Complex::add,  {  Php::ByVal("op",  "trComplex\\Complex",  false,  true)  });  // add the class to namespace myNamespace.add(std::move(complex));  // add the namespace to the extension myExtension.add(std::move(myNamespace));  // return the extension  return myExtension;  }
```

我们没有将类直接添加到扩展中，而是多添加了一个名称空间层(在本例中命名为`trComplex`)。除了在`add`函数的参数声明中，我们使用了`trComplex\\Complex`而不是`Complex`之外，该类及其成员注册几乎保持一致。接下来，我们将类移动到名称空间中，并将名称空间移动到扩展中。

为了测试新的名称空间和类，我们可以修改测试片段:

```
<?php

$c=new trComplex\Complex(-3,-4); echo $c->mod()."\n"; $d=new trComplex\Complex(4,3); echo $c->add($d)->mod()."\n"; echo ((string)$d."\n"); $f=new \DateTime(); $g=$d->add($f);  //Error!
```

我们现在不仅仅使用`Complex`，而是使用`trComplex\Complex`(这个类的完全限定名)来声明变量。输出将与之前的演示相同。它表明 PHP-CPP 名称空间与自然的 PHP 名称空间是相同的。

### 最后的想法

PHP-CPP 非常强大，也很容易使用。我真的希望在这两部分讨论之后，用户会发现它是一个很有前途的库，可以帮助使用熟悉的 C++语法和语义开发 PHP 扩展。

不过，仍有一些地方需要改进。

首先，它的文档需要更有见地的 API 解释。例如，我们之前看到`implementation()`方法没有包含在它当前的在线文档中。不应该是这样的。用户需要一步一步的说明和演示(现在在它的官方文档中)，但也需要完整的 API 参考。

其次，我们希望它的 1.0 主版本能尽快发布。在我的测试中，弹出了一些有趣的分段错误，升级到最新版本后，这些错误消失了；但是我们确实需要一个稳定的 1.0 版本。

第三，即使在我们演示的最后一个`echo`中，PHP 提示:“PHP 可捕捉致命错误:传递给 Complex::add()的参数 1 必须是 Complex 的实例，给定 DateTime 的实例……”，这个错误在 PHP 的`try...catch`块中是不可捕捉的。这个有点烦。

在 PHP-CPP 的第 2 部分中，我们讨论了几个重要的方面:类和成员函数、魔术方法、名称空间封装等。

我会推荐 PHP-CPP 作为开发 PHP 扩展的开发工具。它的优势是显而易见的:快速的学习曲线，熟悉的 C++语法，PHP 魔术方法支持，OO 特性等。

欢迎评论，让我们知道你的想法！

## 分享这篇文章