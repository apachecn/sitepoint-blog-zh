# PHP 中函数式编程的现状

> 原文：<https://www.sitepoint.com/the-state-of-functional-programming-in-php/>

随着 Javascript 以及 Python 和 Ruby 等语言的兴起，[函数式编程](http://lambda-the-ultimate.org/node/2539)正变得越来越主流。甚至 Java 似乎也在下一个版本中[获得了闭包，那么这是不是让 PHP 落后了，或者其中隐藏着未实现的潜力？](http://rickyclarkson.blogspot.com/2007/11/java-7-example-writing-your-own-foreach.html)

## 动态调度

究竟什么定义了函数式编程语言，也许是一个悬而未决的问题，但是一个关键的元素是作为数据的函数。碰巧的是，PHP 在某种程度上支持这个概念；语法允许您使用变量作为函数名，这使得以下情况成为可能:

```
 function add($a, $b) {
  return $a + $b;
}
$add = "add";
$add(2, 8); // return 10 
```

与支持第一类函数的语言不同，变量`$add`不是一种特殊类型——它只是一个字符串，在上下文中计算。它可能只是一个包装好的 eval，但是一旦函数被声明，表面上它的工作方式是相似的。

也可以用 [call_user_func](http://docs.php.net/manual/en/function.call-user-func.php) 显式调用函数引用。这很有趣，因为它接受不同类型的参数，这使得在对象上调用方法成为可能。稍后会有更多的介绍。

## 绑定状态

函数式编程的另一个先决条件是将变量绑定到函数的能力。这个绑定函数本质上与面向对象编程中的对象服务于相同的目的，但是通常更加细粒度和更加灵活。

在传统上与函数式编程联系在一起的语言中，函数通常通过一种叫做闭包的东西与变量绑定在一起。这是那些语言的[范围规则](http://en.wikipedia.org/wiki/Scope_(programming)#Static_scoping)的副作用。

由于 PHP 没有词法范围，所以我们不能使用闭包，但是我们可以使用[curry](http://en.wikipedia.org/wiki/Currying)来达到同样的目的。用维基百科的话说，currying 是*将一个带多个参数的函数转换成一个带单个参数的函数的技术*。如果这听起来很抽象，假设如下:

```
 $add2 = curry($add, 2); // returns a new function reference, to add(), bound with 2 as the first argument
$add2(8); // return 10 
```

在你尝试用*那个*之前，不要着急——它不会起作用，因为`curry`不是一个 PHP 函数。事实证明，创造它是可能的。算是吧。

## 实施 curry

不用深入细节，有两种方法可以在 PHP 中实现 currying。

*   第一种选择是某种运行时评估代码。这可能是指向函数名的[变量-变量](http://docs.php.net/manual/en/language.variables.variable.php)，或者是由 [call_user_func](http://docs.php.net/manual/en/function.call-user-func.php) 调用的[回调伪类型](http://docs.php.net/manual/en/language.pseudo-types.php#language.types.callback)。最终，它们只是一种[评估](http://docs.php.net/manual/en/function.eval.php)。
*   另一种选择是使用一个[命令对象](http://www.vincehuston.org/dp/command.html)，封装回调及其状态。

更详细的解释，请看 PHP 中的[部分函数应用。](http://metapundit.net/sections/blog/166)

## 权衡选择

从语法上看，与命令对象相比，变量对它们来说有更多的功能“感觉”。[回调伪类型](http://docs.php.net/manual/en/language.pseudo-types.php#language.types.callback)*确实允许引用一个对象+方法名，但是即使 PHP 的内部函数支持这一点，它在用户代码中还是很庞大。如果一个*命令对象*感觉像使用 OOP 语法的函数式编程，那么`call_user_func`感觉像使用过程语法的函数式编程。*

 *就性能而言，运行时评估方法有一些严重的缺点。Currying 必须通过每次创建新的静态代码来完成，而这在过程结束之前是无法回收的。此外，使用这种模式绑定状态的唯一方法是依赖某种全局容器，每次生成一个新的、唯一的符号。因为不可能知道，当回调不再被引用时，也就不可能安全地从全局容器中移除变量。因此，任何以这种方式绑定的状态都不可能被 PHP 的内存管理系统回收，这进一步增加了脚本耗尽内存的风险。

## 最后

因此，如果这篇文章的主旨是断言 PHP 中函数式编程的当前选项，那么唯一可行的解决方案就是使用命令对象。有了一个实用类库，这种尴尬就可以稍微消除了。phu functional 是这方面的一个尝试，我们也可能会看到一些想法出现在更通用的框架中。

另一个想法，可能是 PHP 的一个语言层面的附加。我们需要的是一种方法，使所有的回调都可以用变量-变量语法调用。我想这可以用一种神奇的方法来支持。如果一个对象被用作一个函数，并且它实现了这个神奇的方法，那么这个方法就会被调用。从设计的角度来看，它很适合其他的魔法方法，比如`__call`和 friends。

## 声明函数

另一个可以在语言层面解决的限制是首先声明函数。目前，函数必须在全局范围内声明，或者通过使用丑陋的 [create_function](http://docs.php.net/manual/en/function.create-function.php) 来声明。在 php-internals 团队中,[已经有一些改进这一点的尝试，所以也许我们应该幸运地在 PHP 6 中看到这一点？](http://devzone.zend.com/node/view/id/2013#Heading1)

## 分享这篇文章*