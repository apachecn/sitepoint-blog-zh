# 理解 Ruby 中的作用域

> 原文：<https://www.sitepoint.com/understanding-scope-in-ruby/>

![SCOPE Overlapping Letters Vector Icon](img/c92df1e9430e4dbda58da5966e8c3e00.png)

范围是理解的一个基本要素，不仅仅是在 Ruby 中，在任何编程语言中都是如此。回到我开始写 Ruby 的时候，超过一半的错误都是因为没有很好地理解这个概念。像变量没有被定义，不正确的变量赋值等等。这都是因为对范围理解不够。你不必经历所有这些头痛的事情！这篇文章希望能帮助你避免我犯的许多错误。

## 范围到底是什么？

当有人提到范围时，想到两个词:**变量**和**可见性。**。这很简单:作用域是关于什么东西是可见的。它是关于在给定的时刻你能得到什么(变量，常数，方法)。如果你对作用域有足够的了解，你应该能够指向你的 Ruby 程序的任何一行，并指出在那个上下文中哪些变量是可用的，更重要的是，哪些是不可用的。

那么，首先限制变量的可见性有什么意义呢？为什么不在任何地方都有可用的东西呢？那样生活会容易得多，不是吗？嗯，不完全是…

程序员对很多事情都有不同意见(使用函数式还是面向对象的方法，使用不同的变量命名模板，等等。).范围不在其中。尤其是当人们对编程有了更多的经验，他们会更强烈地支持它。这是为什么呢？

因为你从事编程工作越多，你就有越多的机会体验到处都有可用的东西的恐怖。[全局状态](https://programmers.stackexchange.com/questions/148108/why-is-global-state-so-evil)让你的程序非常不可预测。当每个人都有能力这样做时，很难跟踪谁改变了什么。谁写了这个变量？谁读的？想象一下，有一万行代码，每一行都要问这些问题！

然后是命名问题。如果你有一个很大的程序，你必须给所有的变量起一个唯一的名字以避免冲突。想象一下记录数以千计的变量名。

作用域就像计算机安全中使用的另一个原则:最少访问的[原则。想象一下，如果银行中的每个人(出纳员、会计)都可以(读和写)访问客户、财务等方面的所有记录。突然，有人改变了客户的余额。是出纳员还是会计？也许他们两个都做了？你明白了。](https://etutorials.org/Server+Administration/securing+windows+server+2003/Chapter+2.+Basics+of+Computer+Security/2.3+POLA+The+Principle+of+Least+Access/)

## Ruby 变量作用域:快速参考

Ruby 在不同的范围内定义了变量，这你可能已经知道了。我发现大多数教程都简单地描述了它们(变量类型)，但是它们没有精确地提到它们的范围。以下是详细情况:

*   **类变量(@@a_variable):** 可从类定义和任何子类中获得。外面任何地方都没有。
*   **实例变量(@a_variable):** 仅在特定对象内可用，跨类实例中的所有方法。不能直接从类定义中获得。
*   **全局变量($a_variable):** 在 Ruby 脚本中随处可见。
*   **局部变量(a_variable):** 取决于作用域。你将会和它们打交道最多，因此遇到最多的问题，因为它们的范围取决于各种因素。

这里的[是一个很好的插图](https://natashatherobot.com/ruby-variable-scope/)来形象化所有 4 个的可见性。它应该让您对它们的范围和可用性有一个简单的概念:

![](img/6ccdae764933c289c46a453107f5b78e.png)

在接下来的几节中，我将集中讨论局部变量。通过我的经验和与人交谈，我发现大多数关于作用域的问题都来自于对局部变量及其作用方式了解不够。

## 什么时候局部变量在范围内？

在 Ruby 中不需要声明实例变量。你可以把类似于`@anything_goes_here`的东西放在一个方法定义中，结果你会得到`nil`。现在，试着去掉开头的@符号(从而将变量变成局部变量)，你将得到一个`NameError` ( `undefined local variable or method`)。

每当**看到**一个局部变量被赋值时，Ruby 解释器就会把它放入作用域。如果代码没有被执行也没关系，当解释器看到一个局部变量的赋值时，它就把它放在作用域中:

```
if false # the code below will not run
  a = 'hello' # the interpreter saw this, so the local var. is in scope from now on
end
p a # nil, since that code didn't execute, thus the variable wasn't initialized 
```

尝试按原样运行这段代码，然后删除`a = 'hello'`并再次运行，看看会发生什么。

## 局部变量命名冲突

假设您有以下代码:

```
def something
  'hello'
end

p something
==> hello
something= 'Ruby'
p something
==> Ruby #'hello' is not printed 
```

在 Ruby 中，可以在没有显式接收者和任何括号的情况下调用方法，就像局部变量一样。因此，您可能会遇到如上所述的潜在命名冲突。

如果有一个正在初始化的局部变量和一个同名的方法调用在同一个范围内，局部变量将“隐藏”该方法并优先使用。这并不意味着方法消失了，根本无法访问。您可以通过在末尾添加括号(用`something()`)或者在它前面添加一个显式的`self`接收器(用`self.something`)来轻松访问它。看一看:

```
def some_var; 'I am a method'; end
public :some_var # Because all methods defined at the top level are private by default
some_var = 'I am a variable'
p some_var # I am a variable
p some_var() # I am a method
p self.some_var # I am a method 
```

## 检测变量是否超出范围的有用练习

如果您想查看一个局部变量是否在作用域内，首先，将鼠标光标放在它上面。现在，按下左箭头键，返回到您的代码并停止，直到您:

1.  到达作用域的开头(一个定义/类/模块/do-end 块)
2.  到达为该局部变量赋值的代码。

如果你在 2)之前到达 1)，你可能会在你的代码中遇到一个`NameError`。如果你在 1)之前达到 2)，那么恭喜你。

## 局部变量与实例变量

实例变量与特定对象相关联。只要你在那个物体里，你就能接触到它们。与实例变量不同，局部变量与特定的范围相关联。只要你在这个范围内，你就能接触到它们。实例变量会随着每个新对象而变化并被替换。局部变量会随着每个新的作用域而变化并被替换。你如何知道一个作用域是否被改变了？两个字:范围门。

## 范围门:理解范围的一个基本概念

你认为当你:

1.  定义一个类(用`class SomeClass`)
2.  定义一个模块(用`module SomeModule`
3.  定义一个方法(用`def some_method`)？

每当你做这三件事情中的任何一件，你就进入了一个新的范围。这就像 Ruby 为你打开了一扇大门，带你进入了一个完全不同的环境，有着完全不同的变量。

每个方法/模块/类定义都被称为**范围门**，因为创建了一个新的范围。旧的作用域不再可用，其中所有可用的变量都被新的替换了。

如果这令人困惑，不要担心。这里有一个例子可以帮助你更好地理解这个概念:

```
v0 = 0
class SomeClass # Scope gate
  v1 = 1
  p local_variables # As the name says, it gives you all local variables in scope

  def some_method # Scope gate
    v2 = 2
    p local_variables
  end # end of def scope gate
end # end of class scope gate

some_class = SomeClass.new
some_class.some_method 
```

你会看到`[:v1]`和`[:v2]`被打印到控制台上。程序进入`def some_method`时`v1`变量发生了什么变化？类的作用域被实例方法的作用域所取代，引入了一组全新的变量(在本例中，只有一个变量)。

那`v0`呢？它没有出现在任何地方！嗯，当我们进入`class SomeClass`时，顶级作用域(`v0`在顶级中定义)也被替换了。我说的“替代”是指暂时的，不是永久的。试着在这个节目的末尾`some_class.some_method`后面加上`p local_variables`，你会在列表中看到`v0`。

## 打破范围门！

正如我们之前看到的，模块/方法/类定义限制了变量的可见性。如果你在一个类中有局部变量，当一个新的方法在这个类中被定义时，这个类中的局部变量就不再可用了，正如我们已经看到的。如果不管方法定义如何，您仍然想访问这些变量，那该怎么办呢？您如何“打破”这些范围门？

关键很简单:用方法调用替换作用域门。更具体地说，更换:

*   带`Class.new`的类定义
*   带`Module.new`的模块定义
*   使用`define_method`的方法定义

让我们看看上面的代码(变量名，一切都是一样的),只是用方法调用替换作用域门:

```
v0 = 0
SomeClass = Class.new do
  v1 = 1
  p local_variables

  define_method(:some_method) do
    v2 = 2
    p local_variables
  end
end

some_class = SomeClass.new
some_class.some_method 
```

运行这个程序后，您会看到打印了两行:`[:v1, :v0, :some_class]`和`[:v2, :v1, :v0, :some_class]`。我们成功地打破了每一个范围门，并使外部变量可用。通过块的力量，这个结果是可能的，我们将在下面探讨。

## 积木是范围门吗？

您可能认为块也是范围门。毕竟，它们引入了一个内部声明了变量的新作用域，您无法在外部访问这些变量，如下例所示:

```
sample_list = [1,2,3]
hi = '123'
sample_list.each do |item| # the block scope begins here
  puts hi # will this print 123 or produce an error?
  hello = 'hello' # declaring and initializing a variable
end

p hello # undefined local variable or method "hello" 
```

正如您在“hello”中看到的，特定块中的变量是该块的本地变量，在其他地方不可用。

如果块是范围门，那么`puts hi`会产生一个错误，因为`hi`变量在一个单独的范围内。然而，事实并非如此，您可以通过运行上面的代码看到这一点。

你不仅可以访问外部变量，还可以改变它们的内容！试着把`hi = '456'`放在`do/end`里面，它的内容会被改变。

如果不希望块修改外部变量怎么办？块局部变量会有所帮助。要定义块局部变量，在块参数的末尾加上分号(下面的块只有一个参数，`i`)，然后列出它们:

```
hi = 'hi'
hello ='hello'
3.times do |i; hi, hello|
  p i
  hi = 'hi again'
  hello = 'hello again'
end
p hi # "hi"
p hello # "hello" 
```

如果去掉`; hi, hello`部分，就会得到“hi again”和“hello again”作为这两个变量的新内容。

记住**当你用`do`开始一个程序块并以`end`** 结束它的时候，你就引入了一个新的作用域:

```
[1,2,3].select do |item| # do is here, new scope is being introduced
  # some code
end 
```

将`select`替换为`each`、`map`、`detect`或任何其他方法。一个块被使用的事实(一旦你看到`do/end`)意味着一个新的作用域被引入。

## 块和作用域的一些怪癖

试着猜猜这段 Ruby 代码会打印出什么:

```
2.times do
  i ||= 1
  print "#{i} "
  i += 1
  print "#{i} "
end 
```

你期待过`1 2 2 2`吗？答案是:`1 2 1 2`。使用`times`的每一次迭代都是一个新的块定义，重置其中的局部变量。在这种情况下，我们有 2 次迭代，所以当第二次迭代开始时，`i`再次被重置为 1。

您认为这段 Ruby 代码会打印出什么内容(最后一行):

```
def foo
  x = 1
  lambda { x }
end

x = 2

p foo.call 
```

答案是 1。原因是，块和块对象(procs，lambdas)在它们的**定义**中而不是在它们的**调用中看到作用域。**这与它们在 Ruby 中被视为闭包的事实有关。闭包只是包含行为的代码，这些行为可以:

*   像一个物体一样被传递(可以在以后调用)
*   还记得定义闭包(本例中为 lambda)时范围内的变量吗？

这在各种情况下都会派上用场，比如定义一个无穷数生成器:

```
def increase_by(i)
  start = 0
  lambda { start += i }
end

increase = increase_by(3)
start = 453534534 # won't affect anything
p increase.call # 3
p increase.call # 6 
```

您还可以使用 lambda 延迟变量的修改:

```
i = 0
a_lambda = lambda do
  i = 3
end

p i # 0
a_lambda.call
p i # 3 
```

你认为最后一行会打印出什么:

```
a = 1
ld = lambda { a }
a = 2
p ld.call 
```

如果你的答案是 1，那你就错了。它将打印 2。但是等等，lambda/proc 不是在它们的定义中看到了作用域吗？确实如此，而且仔细想想，`a = 2`也在定义范围内。直到 lambda 第一次被调用时，它才算出其定义中变量的值，正如你在这个例子中看到的。意识不到这一点可能会在代码中引入潜在的难以跟踪的错误。

## 两个方法如何共享同一个变量？

一旦我们知道如何打破范围门，我们就可以用这些知识做一些令人惊奇的事情。我从[元编程 Ruby](https://www.amazon.com/Metaprogramming-Ruby-Program-Like-Facets/dp/1941222129/ref) 这本书上了解到这个概念，这本书对我理解作用域在幕后是如何工作的帮助很大。总之，代码如下:

```
def let_us_define_methods
  shared_variable = 0

  Kernel.send(:define_method, :increase_var) do
    shared_variable += 1
  end

  Kernel.send(:define_method, :decrease_var) do
    shared_variable -= 1
  end
end

let_us_define_methods # methods defined now!
p increase_var # 1
p increase_var # 2
p decrease_var # 1 
```

很整洁，是吧？

## 顶级范围

在 Ruby 或任何其他编程语言中，在顶级作用域中意味着什么？你怎么知道你什么时候在里面？处于顶层仅仅意味着要么你还没有调用任何方法，要么你所有的方法调用都已经返回。

在 Ruby 中，一切都是对象。即使当你在顶层时，你也是在一个对象中(称为`main`，属于一个`Object`类)。尝试运行以下代码来亲自检查这一点:

```
p self # main
p self.class # Object 
```

## 我在哪里？

通常，在调试过程中，如果你知道`self`的当前值，你就可以[解决很多令人头疼的问题](https://yehudakatz.com/2009/11/15/metaprogramming-in-ruby-its-all-about-the-self/)。`self`的当前值影响实例变量以及没有显式接收器的方法。如果您得到一个关于`undefined method/instance variable`的错误，而您确定它已经被定义了(因为您可以在您的代码中看到)，那么您可能在跟踪`self`时遇到了问题。

## 小练习:我能得到什么？

做这个小练习来确保你理解了这篇文章中的基本原则。假设您是一个运行以下代码的小型 Ruby 调试器:

```
class SomeClass
  b = 'hello'
  @@m = 'hi'
  def initialize
    @some_var = 1
    c = 'hi'
  end

  def some_method
    sleep 1000
    a = 'hello'
  end
end

some_object = SomeClass.new
some_object.some_method 
```

尽量停在`sleep 1000`。你看到了什么？在那个特定的点上，哪些变量是可用的？在继续之前，试着想出一些答案。你的回答不仅要包含变量，还要包含变量存在的原因。

正如我们之前提到的，局部变量是有范围限制的。`some_method`的定义是一个范围门，替换所有以前的范围并开始一个新的范围。在新的作用域中，`a`变量是唯一可用的局部变量。

正如我们之前提到的，实例变量是被`self`绑定的，在这种情况下，`some_object`是当前的实例，`@some_var`在所有与之相关的方法中都是可用的，包括`some_method`。类变量是相似的，`@mm`也将在作用域中可用。由于范围门，局部变量`b`和`c`将无法达到。如果您想让它们随处可用，请参考打破范围门一节。

希望这篇文章对你有用。如果你有任何问题，请在下面的评论中告诉我！

## 分享这篇文章