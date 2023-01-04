# 面向 Java 人的 Python

> 原文：<https://www.sitepoint.com/python-for-java-people/>

*编者按*:在 Java 渠道中，我们大多数人都非常了解这种语言，并且已经在它的生态系统中至少呆了几年。这给了我们常规和专业知识，但也导致了一定程度的狭隘视野。在系列 [*外-内爪哇*](https://www.sitepoint.com/python-for-java-people/) 中，非爪哇人将向我们展示他们对我们生态系统的看法。

### 目录 

*   [语法](#syntax)
*   [动态打字](#dynamictyping)
*   [动态打字原理](#thedynamictypingphilosophy)
*   [一种混合范式](#ahybridparadigm)
*   [序列](#sequences)
*   [功能](#functions)
*   [对象和动态运行时](#objectsandthedynamicruntime)
*   [物体](#objects)
*   [类](#classes)
*   [结束](#wrappingup)
*   [评论](#comments)

从哲学上讲，Python 几乎是 Java 的对立面。它放弃了静态类型和僵化的结构，支持一个宽松的沙箱，在沙箱中你可以自由地做任何你想做的事情。或许 Python 是关于你能做什么，而 Java 是关于你能做什么。

然而，这两种语言仍然分享着大量可以追溯到 c 的灵感。它们都是命令式语言，具有块、循环、函数、赋值和中缀数学。两者都大量使用了类、对象、继承和多态。两者都相当显著地突出了例外。两者都自动处理内存管理。它们甚至都编译成在 VM 上运行的字节码，尽管 Python 为您透明地编译。Python 甚至从 Java 那里得到了一些启示——标准库的 [`logging`](https://docs.python.org/3/library/logging.html) 和 [`unittest`](https://docs.python.org/3/library/unittest.html) 模块分别受到 log4j 和 JUnit 的启发。

考虑到这种重叠，我认为 Java 开发人员应该对 Python 感到相当熟悉。所以我带着一些温和的 Python 宣传来找你。如果您给我一个机会，我可以向您展示 Python 与 Java 的不同之处，以及为什么我觉得这些差异如此吸引人。至少，您可能会发现一些有趣的想法可以带回 Java 生态系统。

(想要 Python *教程*，Python 文档有[好的一个](https://docs.python.org/3/tutorial/)。另外，这是从 Python 3 的角度来看的！Python 2 在野外仍然相当普遍，并且它有一些语法差异。)

## 句法

让我们先解决这个问题。这里是你好世界:

```
print("Hello, world!") 
```

嗯，好吧，这不是很有启发性。好的，这里有一个函数可以找到一个文件中最常见的十个单词。我是用标准库的 [`Counter`](https://docs.python.org/3/library/collections.html#collections.Counter) 类型作弊了一点，不过就是*这么好*。

```
from collections import Counter

def count_words(path):
    words = Counter()
    with open(path) as f:
        for line in f:
            for word in line.strip().split():
                words[word] += 1

    for word, count in words.most_common(10):
        print(f"{word} x{count}") 
```

Python 由空格分隔。人们对此经常有强烈的意见。*我*第一次看的时候甚至觉得是异端。十年后的今天，我似乎很自然地很难再回到牙套上。如果你对此感到不快，我怀疑我能否说服你，但我敦促你至少暂时忽略它；在实践中，它真的不会引起任何严重的问题，并且消除了一点噪音。另外，Python 开发人员永远不必争论`{`应该去哪里。

除了美学上的差异，大部分应该看起来很熟悉。我们有一些数字，一些赋值，和一些方法调用。`import`语句的工作方式稍有不同，但是它具有相同的一般含义“使这个东西可用”。Python 的`for`循环与 Java 的`for`非常相似——每一个循环，只是少了一点标点符号。函数本身是用`def`而不是类型来分隔的，但是它的工作方式与您预期的一样:可以用参数调用它，然后返回一个值(尽管这个没有)。

这里只有两件事是不寻常的。一个是`with`块，非常类似于 Java 7 的“`try`-with-resources”——它保证文件将在块的末尾关闭，即使其中出现异常。另一个是`f"..."`语法，这是一个相当新的特性，允许将表达式直接插入字符串。

就是这样！你已经读过一些 Python 了。至少，这不是一种来自完全不同星球的语言。

## 动态打字

从这个例子中可能很明显，但是 Python 代码中没有太多的类型。不在变量声明上，不在参数或返回类型上，不在对象的布局上。任何东西在任何时候都可以是任何类型。我还没有展示一个类定义，所以这里有一个琐碎的。

```
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def magnitude(self):
        return (self.x ** 2 + self.y ** 2) ** 0.5

point = Point(3, 4)
print(point.x)  # 3
print(point.magnitude())  # 5.0 
```

甚至`x`和`y`都没有被声明为属性；它们之所以存在，是因为构造函数创建了它们。没有任何东西强迫我传递整数。我本可以通过浮点数考试，或者也许是[小数](https://docs.python.org/3/library/decimal.html)或[分数](https://docs.python.org/3/library/fractions.html)。

如果你只使用静态语言，这听起来可能有点混乱。类型是温暖、舒适和令人欣慰的。他们保证…嗯，也许不是代码真的能工作(尽管[有些人不同意](https://www.sitepoint.com/types-are-mightier-than-tests/)，而是*什么的*。当你甚至不知道任何东西都是正确的类型时，你怎么能依赖代码呢？

但是等等——Java 也没有这样的保证！毕竟任何物体都可能是`null`对吧？这实际上从来不是正确类型的对象。

您可能会认为动态类型是对`null`问题的彻底投降。如果我们无论如何都要处理它，我们不妨拥抱它，让它为我们工作——通过推迟一切运行时间。类型错误变成了正常的逻辑错误，您可以用同样的方式处理它们。

(对于相反的方法，参见 [Rust](https://www.rust-lang.org/en-US/) ，它没有`null`值——或者异常。我还是宁愿写 Python，但是我很欣赏 Rust 的类型系统并不总是悄悄地对我撒谎。)

在我的`magnitude`方法中，`self.x`是 int 还是 float 或者任何类型的数字都没有关系。它只需要支持`**`操作符，并返回支持`+`操作符的东西。(Python 支持操作符重载，所以这可能是任何东西。)这同样适用于普通的方法调用:任何类型都是可以接受的，只要它在实践中有效。

这意味着 Python 不需要泛型；一切都已经正常工作了。不需要接口；一切都已经是多态的了。类型系统中没有向下强制转换，没有向上强制转换，没有转义。没有遇到需要一个`List`的 API，当它们可以和任何`Iterable`一起工作时。

一些常见的模式变得容易多了。您可以创建包装对象和代理，而无需更改消费代码。您可以使用组合而不是继承来扩展第三方类型，而不需要做任何特殊的事情来保留多态性。灵活的 API 不需要复制每个类作为接口；一切都已经充当了隐式接口。

### 动态类型哲学

使用静态类型，无论是谁*编写了*一些代码，都可以选择类型，编译器检查它们是否工作。有了动态类型，无论是谁*使用了*一些代码，都可以选择类型，运行时会尝试一下。相反的哲学在起作用:类型系统关注你*能*做什么，而不是你*能*做什么。

以这种方式使用动态类型有时被称为“鸭子类型”，意思是“如果它走路像鸭子，叫起来像鸭子，它就是鸭子。”这个想法是，如果你想要的只是嘎嘎叫的东西，那么你就不要静态地强制你的代码必须接收一只鸭子，而是接受你得到的任何东西，并让它嘎嘎叫。如果是的话，那也是你所关心的，所以它和鸭子一样好。(如果它不能，你会得到一个`AttributeError`，但那不是很有力度。)

还要注意，Python 仍然是强类型的。这个术语有点模糊，但它通常意味着值在运行时保留它们的类型。典型的例子是 Python 不允许向数字中添加字符串，而像 JavaScript 这样的弱类型语言会[悄悄地将一种类型转换成另一种类型](https://www.destroyallsoftware.com/talks/wat)，使用的优先规则可能不符合您的预期。

与许多动态语言不同，Python 在早期捕捉错误方面出错——至少在运行时。例如，*从一个尚不存在的变量中读取*会引发异常，从字典中读取一个不存在的键也会引发异常(比如`Map`)。在 JavaScript、Lua 和类似的语言中，在这两种情况下，您都会得到一个`null`值。(连 Java 都因为缺少`Map`键而返回`null`！)如果你想让回到默认值，字典有更明确的表达方法。

这里肯定有一个权衡，它是否值得将因项目和人而异。至少对我来说，在之后，我更容易为系统*确定一个确定的设计，但是静态类型语言期望预先设计。静态打字使得尝试许多不同的想法更加困难，让*玩*更加困难。*

你确实有更少的静态保证，但是根据我的经验，大多数类型错误都会被立即发现…因为我写完代码后做的第一件事就是试着运行它！任何其他的都应该被你的测试所捕获——你应该用任何一种语言来编写，并且 Python 使之变得相对容易。

## 混合范式

Python 和 Java 都是命令式和面向对象的:它们通过执行指令来工作，并且它们将一切都建模为对象。

在最近的版本中，Java 已经增加了一些功能特性，我猜是为了欢呼。Python 也有其合理的功能特性，但是……方法有些不同。它提供了一些像`map`和`reduce`这样的内建令牌，但它并不是真正围绕将许多小功能链接在一起的想法而设计的。

相反，Python 融入了…其他东西。我不知道 Python 采用的方法有什么通用名称。我想它把“链接函数”的想法一分为二:处理序列，让函数本身更强大。

### 顺序

序列和迭代在 Python 中扮演着重要的角色。序列可以说是最基本的数据结构，所以拥有处理它们的工具是非常好的。我将此解释为 Python 对函数式编程的替代:Python 并没有让将大量小函数组合起来然后应用于序列变得更容易，而是让用命令式代码操作序列变得更容易。

回到开始的时候，我不经意地说了这样一句话:

```
 for word, count in words.most_common(10): 
```

一个`for`循环已经足够熟悉了，但是这段代码一次迭代两个变量。实际上，列表`most_common`中的每个元素都返回一个*元组*，一组按顺序区分的值。通过将元组分配给一个变量名元组，可以对元组进行*解包*，这就是这里真正发生的事情。在 Python 中，元组通常用于返回多个值，但它们有时在特殊结构中也很有用。在 Java 中，你需要一个完整的类和几行赋值语句。

任何可以迭代的东西也可以解包。解包支持任意嵌套，所以`a, (b, c) = ...`做它看起来的样子。对于未知长度的序列，一个`*leftovers`元素可以出现在任何地方，并根据需要吸收尽可能多的元素。也许你真的喜欢 LISP？

```
values = [5, 7, 9]
head, *tail = values
print(head)  # 5
print(tail)  # (7, 9) 
```

Python 也有从简单表达式中创建列表的语法——所谓的“列表综合”——这比像`map`这样的函数式方法更常见。创建字典和集合也有类似的语法。整个循环可以简化为一个表达式，强调你真正感兴趣的内容。

```
values = [3, 4, 5]
values2 = [val * 2 for val in values if val != 4]
print(values2)  # [6, 10] 
```

标准库还在 [itertools](https://docs.python.org/3/library/itertools.html) 模块中包含了许多有趣的可迭代函数、组合子和配方。

最后，Python 有*生成器*，用于生成带有命令式代码的惰性序列。包含`yield`关键字的函数在被调用时不会立即执行；相反，它返回一个生成器对象。当生成器被迭代时，函数运行直到遇到一个`yield`，此时*暂停*；产生的值成为下一个迭代的值。

```
def odd_numbers():
    n = 1
    while True:
        yield n
        n += 2

for x in odd_numbers():
    print(x)
    if x > 4:
        break
# 1
# 3
# 5 
```

因为生成器运行迟缓，所以可以产生无限序列或者中途中断。通过让它们同时存在，它们可以产生大量的大对象，而不会消耗大量的内存。它们也是函数式编程的“链式”风格的一般替代方式。您可以编写熟悉的命令式代码，而不是组合地图和过滤器。

```
# This is the pathlib.Path API from the standard library
def iter_child_filenames(dirpath):
    for child in dirpath.iterdir():
        if child.is_file():
            yield child.name 
```

要在 Java 中表达一个完全任意的惰性迭代器，您需要编写一个`Iterator`来手动跟踪它的状态。除了最简单的情况之外，其他情况都可能变得非常棘手。Python 也有迭代接口，所以您仍然可以使用这种方法，但是生成器非常容易使用，大多数定制迭代都是用它们编写的。

因为生成器可以自己暂停，所以它们在其他一些环境中也很有用。通过手动推进生成器(而不仅仅是用一个`for`循环一次迭代)，有可能中途运行一个函数，让它在某个点停止，并在恢复函数之前运行其他代码。Python 利用这一点添加了对纯粹作为库的[异步 I/O](https://docs.python.org/3/library/asyncio.html) (无线程的非阻塞网络)的支持，尽管现在它有了专用的`async`和`await`语法。

### 功能

乍一看，Python 函数非常熟悉。你可以用参数调用它们。传递方式和 Java 中完全一样——Python 既没有引用，也没有隐式复制。Python 甚至有“docstrings”，类似于 Javadoc 注释，但是内置于语法中，在运行时可读。

```
def foo(a, b, c):
    """Print out the arguments.  Not a very useful function, really."""
    print("I got", a, b, c)

foo(1, 2, 3)  # I got 1 2 3 
```

Java 有带`args...`语法的可变函数；Python 使用`*args`也差不多。(`*leftovers`的解包语法受到了函数语法的启发。)但是 Python 还有一些锦囊妙计。任何参数都可以有默认值，因此是可选的。任何参数也可以用*名*给出——我之前用`Point(x=3, y=4)`做了这个。当*调用*任何函数时，可以使用`*args`语法来传递一个序列，就像它是单独的参数一样，并且有一个等价的`**kwargs`可以接受或传递命名的参数作为 dict。一个参数可以是“仅关键字的”，所以它必须通过名字传递，这对于可选的布尔值来说非常好。

当然，Python 的*没有*的函数重载，但是你用它来做的大部分事情都可以用 duck 类型和可选参数来代替。

现在已经为 Python 最强大的特性之一做好了准备。就像动态类型让你透明地用包装器或代理替换对象一样，`*args`和`**kwargs`允许任何*函数*被透明地包装。

```
def log_calls(old_function):
    def new_function(*args, **kwargs):
        print("i'm being called!", args, kwargs)
        return old_function(*args, **kwargs)

    return new_function

@log_calls
def foo(a, b, c=3):
    print(f"a = {a}, b = {b}, c = {c}")

foo(1, b=2)
# i'm being called! (1,) {'b': 2}
# a = 1, b = 2, c = 3 
```

不好意思，这有点复杂。不要太担心它到底是如何工作的；要点是,`foo`被一个`new_function`所取代，T1 将其所有参数转发给`foo`。无论是`foo`还是打电话的人都不需要知道有什么不同。

我不能轻描淡写这是多么强大。它可以用于日志记录、调试、管理资源、缓存、访问控制、验证等等。它与其他元编程特性配合得非常好，同样，它让您能够分析出*结构*而不仅仅是代码。

![Java or Ptyhon?](img/f83787dee873b2494f3cf75e8f929897.png)

## 对象和动态运行时

动态运行时是一个运行时——在幕后驱动语言核心部分的东西——可以在运行时用*播放。像 C 或 C++这样的语言没有动态运行时；源代码的结构被“烘焙”到编译后的输出中，没有合理的方法在以后改变它的行为。另一方面，Java 的*有动态运行时吗！它甚至附带了一整套用于反思的软件。**

 *当然，Python 也有反射。有许多内置的简单函数，用于实时检查或修改对象的属性，这对调试和偶尔的恶作剧非常有用。

但是 Python 对此做了进一步的探讨。因为一切都是在运行时完成的，所以 Python 公开了许多扩展点来定制它的语义。你不能改变语法，所以代码看起来仍然像 Python，但是你经常可以分解出*结构*——这在更严格的语言中是很难做到的。

举个极端的例子，看看 [pytest](https://docs.pytest.org/en/latest/) ，它用 Python 的`assert`语句做了非常聪明的事情。通常情况下，编写`assert x == 1`只会在错误时抛出一个`AssertionError`,让你不知道哪里出错了。这就是为什么 Python 的内置`unittest`模块——像 JUnit 和许多其他测试工具一样——提供了一堆特定的实用函数，比如`assertEquals`。不幸的是，这使得测试有些冗长，很难一目了然。但是用 pytest，`assert x == 1`就可以了。如果失败，pytest 会告诉你什么是`x`……或者两个列表在哪里有分歧，或者两个集合之间有什么元素不同，等等。所有这些都是自动发生的，基于正在进行的比较和操作数的类型。

pytest 是如何工作的？你*真的*不想知道。你也不需要*知道如何用 pytest 编写测试——并且乐在其中。*

这是动态运行时的真正优势。您个人可能不会使用这些功能。但是你可以从使用它们的图书馆中获得巨大的好处，而不用关心它们是如何工作的。甚至 Python 本身也使用自己的扩展点实现了许多额外的特性——不需要修改语法或解释器。

### 目标

我最喜欢的简单例子是属性访问。在 Java 中，`Point`类可能选择`getX()`和`setX()`方法，而不是普通的`x`属性。理由是，如果你需要改变阅读或书写`x`的方式，你可以在不破坏界面的情况下这样做。在 Python 中，您不需要预先担心这一点，因为如果需要，您可以拦截属性访问。

```
class Point:
    def __init__(self, x, y):
        self._x = x
        self._y = y

    @property
    def x(self):
        return self._x

    # ... same for y ...

point = Point(3, 4)
print(point.x)  # 3 
```

有趣的`@property`语法是一个装饰器，看起来像 Java 注释，但可以更直接地修改函数或类。

读取`point.x`现在调用一个函数并计算其返回值。这对于调用代码来说是完全透明的——与读取的任何其他属性没有区别——但是对象可以干预并以它喜欢的方式处理它。与 Java 不同，属性访问是类的 API 的一部分，可以自由定制。(注意这个例子也把`x`设为只读，因为我没有指定如何写入！可写的`property`的语法看起来有点滑稽，它如何工作在这里并不重要。但是你可以简单地，比如说，强制规定只有奇数可以分配给`point.x`。)

类似的特性也存在于其他静态语言中，比如 C#，所以这可能并不令人印象深刻。Python 真正有趣的部分是`property`一点也不特别。这是一种普通的内置类型，可以用不到一屏的纯 Python 编写。它之所以有效，是因为 Python 类可以自定义自己的属性访问，包括一般属性访问和按属性访问。包装器、代理和组合很容易实现:您可以将所有方法调用转发给底层对象，而不必知道它有什么方法。

同样的钩子`property`可以用于[延迟加载属性](http://classtools.readthedocs.io/en/latest/#classtools.reify)或[一个自动保存弱引用](http://classtools.readthedocs.io/en/latest/#classtools.weakattr)的属性——对调用代码完全透明，全部来自纯 Python。

您可能已经注意到，我的代码没有`public`或`private`修饰符，事实上，Python 没有这样的概念。按照惯例，单个前导下划线用来表示“私有的”——或者更准确地说，“不打算作为稳定的公共 API 的一部分”。但是这没有语义上的意义，Python 本身并没有阻止任何人检查或更改这样的属性(或者调用它，如果它是一个方法的话)。也没有`final`或者`static`或者`const`。

这是同样的哲学在起作用:核心 Python 通常不会阻止你做任何事情。而且当你需要它的时候，它非常有用。我通过在启动时调用或覆盖甚至直接重新定义私有方法来修补第三方库中的错误。它让我不必创建项目的整个本地分支，一旦错误被上游修复，我只需删除我的补丁代码。

同样，您可以很容易地为依赖于外部状态的代码编写测试——比如说，当前时间。如果重构不切实际，您可以在测试期间用一个虚拟函数替换`time.time()`。库函数只是模块的属性(像 Java 包一样)，而 Python 模块像其他任何东西一样是对象，所以可以用同样的方式检查和修改它们。

### 班级

Java 类是由一个对象支持的，但是这两者是不能互换的。对于一个类`Foo`，类对象是`Foo.class`。我不认为`Foo`可以单独使用，因为它命名了一个*类型*，Java 在类型和值之间做了一些微妙的区分。

在 Python 中，一个类*是一个对象*，一个`type`的实例(T0 本身是一个对象，因此是它自身的实例，想想很有趣。)因此，类可以像任何其他值一样被处理:作为参数传递、存储在更大的结构中、被检查或被操作。创建关键字是类的字典的能力有时特别有用。因为类只是通过调用它们来实例化——Python 没有`new`关键字——在许多情况下它们可以与简单的函数互换。一些常见的模式，比如工厂，简单到几乎消失。

```
# Wait, is Vehicle a class or a factory function?  Who cares!
# It could even be changed from one to the other without breaking this code.
car = Vehicle(wheels=4, doors=4) 
```

现在，我已经几次将函数甚至常规代码放在顶层，在任何类之外。这是允许的，但其中的含义有点微妙。在 Python 中，甚至`class`和`def`语句都是在运行时执行*的常规代码。Python 文件是自顶向下执行的，`class`和`def`在这方面并不特殊。它们只是创建某些类型的对象的特殊语法:类和函数。*

最酷的部分来了。因为类是对象，它们的类型是`type`，你可以*子类* `type`并改变它的工作方式。然后，您可以创建作为子类实例的类。

一开始把你的头包起来有点奇怪。但同样，你不需要知道它是如何工作的才能从中受益。例如，Python 没有`enum`块，但是它有一个[枚举模块](https://docs.python.org/3/library/enum.html):

```
class Animal(Enum):
    cat = 0
    dog = 1
    mouse = 2
    snake = 3

print(Animal.cat)           # <Animal.cat: 0>
print(Animal.cat.value)     # 0
print(Animal(2))            # <Animal.mouse: 2>
print(Animal['dog'])        # <Animal.dog: 1> 
```

`class`语句创建一个对象，这意味着它在某个地方调用一个构造函数，这个构造函数可以被覆盖以改变类的构建方式。这里，`Enum`创建一组固定的实例，而不是类属性。所有这些都是用普通的 Python 代码和普通的 Python 语法实现的。

整个图书馆都是建立在这些理念之上的。你讨厌为构造函数中的每个属性键入`self.foo = foo`的单调乏味吗？然后手工定义等式、散列、克隆和一个开发者可读的表示？Java 需要编译器的支持，这可能会伴随着[琥珀项目](https://www.sitepoint.com/project-amber-will-revolutionize-java/#dataclasses)而来。Python 足够灵活，社区用 [attrs](http://attrs.readthedocs.io/en/stable/) 库解决了这个问题。

```
import attr

@attr.s
class Point:
    x = attr.ib()
    y = attr.ib()

p = Point(3, 4)
q = Point(x=3, y=4)
p == q  # True, which it wouldn't have been before!
print(p)  # Point(x=3, y=4) 
```

或者以 [SQLAlchemy](http://www.sqlalchemy.org/) 为例，它是 Python 的一个特色数据库库。它包含了一个受 Java 的 [Hibernate](http://hibernate.org/orm/) 启发的 ORM，但是你不用在一个配置文件中或者通过一些冗长的注释来声明一个表的模式，你可以直接简洁地把它写成一个类:

```
class Order(Table):
    id = Column(Integer, primary_key=True)
    order_number = Column(Integer, index=True)
    status = Column(Enum('pending', 'complete'), default='pending')
    ... 
```

这是与`Enum`相同的基本思想，但是 SQLAlchemy 也使用与`property`相同的钩子，所以您可以自然地修改列值。

```
order.order_number = 5
session.commit() 
```

最后，类本身可以在运行时创建。这稍微有点小众，但是[thrifty](https://github.com/eleme/thriftpy)基于 [Thrift](http://thrift.apache.org/) 定义文件创建了一个充满类的完整模块。在 Java 中，您需要代码生成，这增加了一个全新的编译步骤，可能会失去同步。

所有这些例子都依赖于 Python 现有的语法，但却赋予了新的含义。它们中没有一个*做*任何你在 Java 或任何其他语言中做不到的事情，但是它们减少了结构重复——这使得代码更容易写，更容易读，更不容易出错。

## 包扎

Python 有许多与 Java 相同的基本概念，但是把它们引向了一个非常不同的方向，并加入了一些全新的想法。Java 侧重于稳定性和可靠性，而 Python 侧重于表达性和灵活性。这是一种完全不同的思考命令式编程的方式。

我怀疑 Python 会在 Java 擅长的领域取代 Java。例如，Python 可能不会赢得任何速度竞赛(但是请看 [PyPy](http://pypy.org/) ，一个 JITted Python)。Java 有对线程的本地支持，而 Python 社区很大程度上避开了它们。有许多灰尘角落的非常大的复杂软件可能更喜欢静态类型提供的健全性检查(但是请参见 [mypy](http://mypy-lang.org/) ，一个用于 Python 的静态类型检查器)。

但是或许 Python 会在 Java 没有的领域大放异彩。许多软件不需要特别快或并行，然后其他问题就浮出了水面。我发现用 Python 启动一个项目非常快速简单。由于没有单独的编译步骤，写/运行循环要快得多。代码更短，通常意味着更容易理解。尝试不同的架构方法感觉更便宜。有时候尝试愚蠢的想法很有趣，比如[用库](https://github.com/snoack/python-goto)实现 goto。

希望你试试 Python。我从中得到很多乐趣，我想你也会的。只是不要把它当成 Java，对你隐藏所有的类型。

最糟糕的情况是，总有 [Pyjnius](http://pyjnius.readthedocs.io/en/latest/) 让你这么做。

```
from jnius import autoclass

System = autoclass('java.lang.System')
System.out.println('Hello, world!') 
```* 

## *分享这篇文章*