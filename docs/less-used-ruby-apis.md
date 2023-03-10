# 较少使用的 Ruby APIs

> 原文：<https://www.sitepoint.com/less-used-ruby-apis/>

如果你曾经使用 Ruby 开发过什么东西，那么你就会知道这是一个很好的选择。它流畅、易读，并且有很多很酷的方式来完成工作。除了您可能已经习惯的日常 API 之外，还有一大堆经常被忽略或者直到现在才被发现的好东西！在这篇文章中，我将探索这些宝石中的一些，没有双关语的意思，它们可以让你直接进入 Ruby zen。

## 核心

[`Kernel`](http://ruby-doc.org/core/classes/Kernel.html) 模块提供了多种方法，这些方法适用于所有继承自`Object`的对象，因为`Object`混合了`Kernel`。我为什么要做这种区分？从 Ruby 1.9 开始，已经添加了`BasicObject`类，现在它是所有类的父类。`BasicObject`没有`Kernel`模块提供的方法。

### __ 被呼叫者 _ _

这个奇怪命名的函数返回当前方法的名称作为一个`Symbol`。

```
def add(a, b)
    log("#{a} + #{b}", __callee__)
    a + b
end

def log(msg, caller)
    puts "##{caller}: #{msg}"
end
```

在上面的例子中，`log`方法有两个参数:消息和调用者。`add`方法利用`__callee__`函数将它的方法名传递给 log 函数。如果您启动 irb 控制台，键入这两个方法，然后输入`add 3,5`，您将得到:

```
#add: 3 + 5
=> 8
```

好的，这是非常基本的，但是有点漂亮，对吗？当然，你可以将`:add`传递给`log`方法，而不是使用`__callee__`，毕竟你知道你在`add`方法中，对吗？上面提供的用例中的一个小优点是，如果您将`add`方法重命名为`my_super_add_method`，您就不必更新对`log`方法的调用。随意想出一些其他方便的用法，并把它们贴在本文的评论部分。

### 在出口处

如果你需要在程序退出前运行一些代码，那么你应该检查一下`at_exit`。它将一个块注册为一个处理程序，当 Ruby 进程将要退出时，这个处理程序将被调用。事实上，您可以注册多个处理程序，如下例所示。

```
at_exit {
    puts "the program is exiting"
}

at_exit {
    puts "the program is really exiting"
}

puts "doing some stuff"
raise "Uh oh, something bad happened!"
```

将按照后进先出(LIFO)的顺序调用处理程序。如果您将上面的代码放入一个文件，可能命名为 at_exit_example.rb，并运行它`ruby at_exit_example.rb`，您将得到:

```
doing some stuff
the program is really exiting
the program is exiting
at_exit_example.rb:10:in `<main>': Uh oh, something bad happened! (RuntimeError)
```

正如你所看到的，当 Ruby 进程将要退出时，会调用`at_exit`处理程序，即使是因为一个未处理的异常。这对于清理一些可能由您的程序创建的文件、关闭与服务器的连接或记录一些可能有助于调试程序问题的上下文(如果它是一个通常不应该自己退出的程序)可能很有用。

### block_given？

这可能是你已经遇到过的一个函数，但是如果没有，现在是了解它的好时机。如果你曾经将一个块传递给一个函数，那么被调用的函数很有可能正在使用`block_given?`。下面是一个如何使用`block_given?`的例子。

```
def sum(values)
    error = result = nil
    begin
        result = values.inject do |total, value|
            total += value
        end
    rescue Exception => ex
        error = ex
    end

    if block_given?
        yield result, error
    else
        return result unless error
        raise error
    end
end

sum((1..5)) do |result, error|
    puts "Error: #{error}"       # => nil
    puts "Result: #{result}"        # => 15
end
```

`sum`函数获取一组值，计算它们的总和并返回结果。该函数检查是否给定了一个块，因此调用`block_given?`，如果给定了，它将`yield`结果和错误。如果在执行 sum 时出现异常，那么它将被捕获并返回给程序块。

然而，如果没有给定一个块，`sum`函数将计算结果并返回它。如果在这种情况下出现异常，那么`sum`函数将会抛出这个异常，而不是返回`nil`或其他值来指出问题。如果你想允许一个块传递给你的函数，试着给`block_given?`一个旋转！

## 目标

[`Object`](http://beta.ruby-doc.org/core/Object.html) 类被奇怪地描述为:

> 对象是 Ruby 的类层次结构的根。除非显式重写，否则它的方法对所有类都可用。

我说奇怪是因为，正如我前面提到的，`BasicObject`是新的根，是 Ruby 类层次结构中的顶级类。我认为文档需要更新，这是你或我可以自愿做的事情；).

`Object`类通过混合在`Kernel`模块中获得了很多它的行为，但是也有一些它自己的优点，比如下面描述的`method`。

### 方法

想过如何获得对方法的引用吗？许多语言都提供了这种能力，但是看看 Ruby 有多简洁:

```
m = Calculator.method(:sum)  # => assume "sum" is a class method
m.call 3, 4  # => 7
```

尝试在 Java 中这样做，您将很容易地添加更多的代码，包括多个`catch`子句来处理无数可能抛出的潜在异常。

## 对象空间

[`ObjectSpace`](http://www.ruby-doc.org/core/classes/ObjectSpace.html) 模块描述如下:

> 包含许多与垃圾收集工具交互的例程，允许您用迭代器遍历所有活动对象。

很酷吧。我想是的，这就是为什么我要给你看`each_object`和`_id2ref`。

### 每个对象

`each_object`函数提供了所有活动对象的迭代器(对于**这个** Ruby 进程)。如果您只对特定类型的对象感兴趣，可以按类型过滤枚举器。您可能会使用`each_object`和一般的`ObjectSpace`模块来帮助调试或分析。

```
class Foo; end

ObjectSpace.each_object(Foo) do |foo_obj|
    puts foo_obj
end    # => 0  ...there are no instances yet.

# create a new instance and print the object_id
puts Foo.new.object_id  # => 2160309640

ObjectSpace.each_object(Foo) do |foo_obj|
    puts foo_obj.object_id   # => 2160309640
end
```

当然，您可以迭代任何类型的对象，而不仅仅是您自己的类型。去吧，看看所有的`String`物体！

### _id2ref

如果出于某种原因，您碰巧知道在 Ruby 进程中运行的对象的 object_id，那么您很幸运。您可以使用`ObjectSpace._id2ref`获取对该对象的引用。

```
f = Foo.new.object_id   # => 2160309640
ObjectSpace._id2ref(2160309640) == f  # => true
```

## 还有很多

希望在读完这篇文章后，你已经获得了一些新的很酷的东西，但是我打赌你想要更多，对吗？如果是这样，你可能想看看这些:

*   [T2`Kernel.lambda`](http://www.ruby-doc.org/core/classes/Kernel.html#M001447)
*   [T2`GC`](http://www.ruby-doc.org/core/classes/GC.html)
*   [T2`GC::Profiler`](http://www.ruby-doc.org/core/classes/GC/Profiler.html)
*   [T2`Method#parameters`](http://www.ruby-doc.org/core/classes/Method.html#M001075)

关键是，Ruby 有许多有趣的代码可供您探索。你有使用一些不常用的 Ruby 类、模块或函数的经验吗？如果有，贴个评论，分享给大家！

## 分享这篇文章