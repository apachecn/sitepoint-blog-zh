# Ruby 中的闭包

> 原文：<https://www.sitepoint.com/closures-ruby/>

不久前，作为一名探索 Ruby 神秘世界的彻头彻尾的 C++程序员，我被闭包的概念和语言处理它们的方式迷住了。从我的 [JavaScript](https://www.sitepoint.com/javascript-closures-demystified/) 经历中，我已经熟悉了闭包的概念，但是 Ruby 处理闭包的方式让我困惑和着迷。几年后，我决定写一篇关于他们的文章，就在这里。

## 什么是终结？

闭包基本上是一个函数，它:

1.  可以像变量一样处理，例如，赋给另一个变量，作为方法参数传递，等等。
2.  记住函数定义时作用域中所有变量的值，即使在不同的作用域中执行，也能够访问这些变量。

换句话说，闭包是一个具有词法作用域的一级函数。

## 代码块

实际上，块是一个匿名函数，它也提供类似闭包的功能。考虑以下情况，

```
outer = 1

def m
  inner = 99
  puts "inner var = #{inner}"
end
```

与其他一些语言不同，Ruby 不嵌套作用域，所以内部的*变量*和外部的*变量*是完全相互屏蔽的。如果我们想从*外部*(也称为*主*)范围访问*内部*变量，该怎么办？一种方法是使用块:

```
outer = 1

def m
  inner = 99
  yield inner
  puts "inner var = #{inner}"
end

m {|inner| outer += inner}
puts "outer var = #{outer}"
```

输出:

```
#=> inner var = 99
 #=> outer var = 100
```

在方法内部，我们将内部变量让给一个附加到方法调用的块。然后，我们使用内部变量值在主范围内进行一些运算。尽管代码块实际上是一个匿名函数，但它仍然可以访问其周围范围内的变量，如“outer ”,同时访问生成它的方法内的变量。换句话说，使用这样的块允许我们*跨越一个范围门*。

### "为什么我不能用方法的返回值来代替？"

在这个简单的例子中，您可以。但是，如果你想返回多个变量，你会失败，而你可以很容易地将多个变量`yield`到一个块中。更重要的是，该方法在定义时无法访问周围的变量，所以您将无法做我们将在本文的其余部分看到的很酷的事情:)

## Procs

您可能已经注意到，我们的代码块示例并没有真正满足我们为闭包定义的标准。虽然块记住了它周围作用域中的变量，并且可以访问从不同作用域中产生的变量，但是我们不能将它作为方法参数传递，也不能将它赋给另一个对象。换句话说，yield 语句使用的块*不是真正的闭包*。块*可以是真正的闭包，但是它们必须被当作*进程*。让我们看看如何做到这一点:*

```
outer = 1

def m &a_block
  inner = 99
  a_block.call(inner)
  puts "inner var = #{inner}"
  puts "argument is a #{a_block.class}"
end

m {|inner| outer += inner}
puts "outer var = #{outer}"
```

输出:

```
#=> inner var = 99
 #=> argument is a Proc
 #=> outer var = 100
```

这个例子和我们之前的例子的第一个区别是我们现在为我们的方法定义了一个参数:`&a_block`。这告诉该方法期待一个块作为参数，并把它当作一个`Proc`对象对待(&操作符隐式地将该块转换成一个`Proc`)。一个 [Proc](https://www.omniref.com/ruby/2.1.4/symbols/Proc) 是一个简单的命名代码块，意思是一个实际的对象。因为它是一个对象，所以可以传递这个块，并在其上调用方法。这里感兴趣的方法是`#call`，它调用 Proc。知道了所有这些，我们可以重写代码如下:

```
outer = 1

def m a_proc
  inner = 99
  a_proc.call(inner)
  puts "inner var = #{inner}"
  puts "argument is a #{a_proc.class}"
end

m proc {|inner| outer += inner}
# we can also use Proc.new instead of proc, with the same effect:
# m Proc.new {|inner| outer += inner}
puts "outer var = #{outer}"
```

输出:

```
#=> inner var = 99
 #=> argument is a Proc
 #=> outer var = 100
```

这里，我们正在动态创建一个`Proc`对象，并将其作为方法参数传递。为了充分利用闭包，我们需要能够将它赋给另一个变量，并在需要时调用它(延迟执行)。让我们修改我们的方法来实现这一点:

```
outer = 1

  def m a_var
    inner = 99
    puts "inner var = #{inner}"
    proc {inner + a_var}
  end

  p = m(outer)
  puts "p is a #{p.class}"

  outer = 0
  puts "changed outer to #{outer}"

  puts "result of proc call: #{p.call}"
```

输出:

```
#=> inner var = 99
 #=> p is a Proc
 #=> changed outer to 0
 #=> result of proc call: 100
```

我们的方法现在接收外部变量作为参数，并返回将`inner`和`outer`相加的`Proc`。然后我们将`Proc`赋给一个变量(`p`)，在我们空闲时在代码中调用它。请注意，即使我们在 proc 调用之前更改了`outer`的值并将其设置为 0，我们的结果也不会受到影响。`Proc`会记住定义时`outer`的值，调用时**不会记住**的值。我们现在有了一个真正的天生的终结！

## 希腊字母的第 11 个

是时候玩一个点差游戏了。看一下下面的代码，看看它与上一节中的代码有什么不同:

```
outer = 1

def m a_var
        inner = 99
        puts "inner var = #{inner}"
        lambda {inner + a_var}
end

p = m(outer)
puts "p is a #{p.class}"

outer = 0
puts "changed outer to #{outer}"

puts "result of proc call: #{p.call}"
```

输出:

```
#=> inner var = 99
 #=> p is a Proc
 #=> changed outer to 0
 #=> result of proc call: 100
```

是的，唯一的区别是我们的方法现在返回一个 *lambda* 而不是一个 *proc* 。我们代码的功能和输出保持不变。但是……等等，我们给`p`分配了一个*λ*，但是`p`告诉我们这是一个`Proc`！这怎么可能呢？答案是:lambda 不过是一只伪装的`Proc`。`#lambda`是一个`Kernel`方法，创建一个`Proc`对象，其行为与其他`Proc`对象略有不同。

### "我如何判断一个对象是 proc 还是 lambda？"

只需通过调用它的`#lambda?`方法来询问它。

```
obj = lambda {"hello"}.
puts obj.lambda?
#=> true
```

此外，在 lambda 上调用`#inspect`会给你一个作为`Proc(lambda)`的类。

## Procs vs Lambdas

我们已经看到 lambda 只是一个具有不同行为的`Proc`。一辆`Proc` ( *proc* )和一辆 *lambda* 的区别包括:

1.  返回
2.  参数检查

### 从过程中返回

```
def method_a
 lambda { return "return from lambda" }.call
 return "method a returns"
end

def method_b
 proc { return "return from proc" }.call
 return  "method b returns"
end

puts method_a
puts method_b
```

输出:

```
#=> method a returns
 #=> return from proc
```

虽然我们使用 lambda 的方法(`method_a`)的行为符合预期，但我们使用 proc 的方法(`method_b`)永远不会返回，而是返回 Proc 的返回值。对此有一个简单的解释:

1.  用 *lambda* 创建的块会像方法一样返回到它的父作用域，所以这并不奇怪。
2.  用`proc`(或`Proc.new`)创建的块认为它是其调用方法的一部分，所以返回到其调用方法的父作用域。当您意识到您的方法的一半代码没有被执行时，这可能有点令人震惊，因为您在中途执行的`Proc`有一个`return`语句。那么，我们能从`Proc`回到“正常”的道路上吗？是的，如果我们使用`next`关键字，而不是`return`。我们可以重写`method_b`，使其功能与`method_a`相同:

    ```
    def method_b
      proc { next "return from proc" }.call
      return  "method b returns"
    end

    puts method_b
    ```

输出:

```
#=> method b returns
```

### 参数检查

让我们创建并调用一个带有两个参数的 lambda:

```
l = lambda {|x, y| "#{x}#{y}"}

  puts l.call("foo","bar")
```

输出:

```
#=> foobar
```

如果我们忽略一个论点会发生什么？

```
puts l.call("foo")
```

输出:

```
#=> wrong number of arguments (1 for 2) (ArgumentError)
```

没错，lambda 对它的参数(arity)很严格，就像方法一样。procs 呢？

```
p = proc {|x, y| "#{x}#{y}"}

  puts p.call("foo","bar")
  puts p.call("foo")
```

输出:

```
#=> foobar
  #=> foo
```

我们可以看到 proc 对它的论点更加冷静。如果缺少一个参数，proc 将简单地假设它是`nil`并继续生活。

## 句法糖

我喜欢 Ruby 的一个原因是它允许我们用不同的方式做同样的事情。认为`#call`方法对你来说太像 Fortran 了？没问题，用点号或双冒号来调用你的 proc。也不喜欢那个？总是有方括号符号。

```
p = proc {|x, y| "#{x}#{y}"}

puts p.call("foo","bar")
puts p::("foo","bar")
puts p.("foo","bar")
puts p["foo","bar"]
```

输出:

```
#=> foobar
 #=> foobar
 #=> foobar
 #=> foobar
```

此外，还记得我们之前用来将块转换成过程的一元&运算符(`&`)吗？嗯，反过来也一样。[<sup>1</sup>](#unary_ampersand)

```
p = proc {|i| i * 2}
l = proc {|i| i * 3}

puts [1,2,3].map(&p)
puts [1,2,3].map(&l)
```

输出:

```
#=> 2
#=> 4
#=> 6
#=> 3
#=> 6
#=> 9
```

这里，`#map`方法需要一个块，但是我们可以很容易地传递给它一个 proc 或 lambda。这意味着我们可以利用闭包的力量将其他作用域中的变量转换成 Ruby 的块预期方法，这是非常强大的东西。

## 这有什么大不了的？

Ruby 在实现闭包方面提供了无与伦比的通用性。Blocks，procs 和 lambdas 可以互换使用，效果很好。我们最喜爱的宝石所创造的许多“魔力”都是由闭包促成的。闭包允许我们以更小、更紧凑、可重用和优雅的方式抽象代码 [<sup>2</sup>](#practical_closures) 。Ruby 为开发人员提供了这些美妙而灵活的构造，因此我们可以最大限度地利用闭包的能力。

:实际上，一元&运算符有其独特的细微差别

能感觉到有新文章上来了；)

## 分享这篇文章