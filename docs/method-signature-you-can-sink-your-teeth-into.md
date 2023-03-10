# 您可以深入研究的方法签名

> 原文：<https://www.sitepoint.com/method-signature-you-can-sink-your-teeth-into/>

![sinkteeth](img/86c8d70acdad09824f088017305dee93.png)大家好。我最近开始更深入地研究 Ruby 2.0。对我来说最有趣的特性是增加了真正的关键字参数。当我搜索关于其他语言如何实现和使用关键字参数的信息时，我意识到这个增加是多么强大。总之很厉害。

在这里，我想讨论一下 Ruby 是如何允许参数设置的，Ruby 爱好者在创建方法时遵循什么样的模式，最后讨论一下 Ruby 2.0 的关键字参数有多好玩，多灵活。

对于高级开发人员来说，其中一些可能是初级的，因为本文旨在帮助初露头角的 ruby 爱好者。然而，我发现给一些我们认为理所当然的概念命名，即使对最老练的老手来说也是有帮助的。

在 2009 年的一篇文章[1]中，Alan Skorkin 将争论分为:

*   需要
*   可选择的
*   具有默认值的参数

我认为这是一个思考 Ruby 如何处理参数的好方法。有趣的是，本文中的所有(或至少大部分)参数处理技术都属于这些宽泛的定义。也就是说，一些技术，如果不是全部的话，可以互相使用。

例如，我可以有一个带有序号参数的方法、带有默认值的参数和一个选项散列参数。Ruby 是一种强大的语言，它的优势之一就是灵活性。确保你最大限度地探索和利用这种灵活性。

这里有一个我们将要讨论的参数处理类型的列表，将作为这篇文章的目录:

*   没有参数
*   序数参数
*   具有默认值的参数
*   Hash Param
*   数组形式的参数
*   关键字参数(Ruby 2.0)

## 简介

在开始之前，我们需要建立方法创建的正确语法。在 Ruby 中，方法是由关键字`def`声明的。在关键字`def`之后是方法名，必须以小写字母开头。约定建立了对包含多个单词的方法名使用[蛇用例](http://en.wikipedia.org/wiki/Snake_case)。在 method_name 之后，我们传递括号内的参数。关键字`end`，像`class`和`module`一样，标志着方法的结束，必须以换行符或分号开头。

```
def example_method(args); end
#

#OR

#
def example_method(args)
end
```

( **图一.** )

现在，您已经快速完成了重新封顶，我们可以开始讨论参数处理了。

## 没有参数

这可能是最短的路段，因为它是最不有趣的路径。您定义了一个没有参数的方法，并且调用它时也没有参数。

```
def example_method; puts "Hello, World."; end
#
example_method

#=> Hello, World
```

( **图 b.** )

仅此而已。

## 序数参数

之所以称之为顺序参数，是因为提供给方法的参数必须按照方法签名中确定的顺序发送。从技术上讲，顺序对所有参数类型都很重要(除了 solo ||无参数)。然而，当我在这里使用单词 ordinal 时，我指的是按顺序传递的必需参数。例如:

```
def example_method(a,b)
  puts a,b
end
#

# We must pass the arguments in proper order, so that they
# reference what we expect.

#
example_method("This is A", "This is B")

#=> This is A

#=> This is B
```

( **图 c.** )

不按顺序传递参数不是语法错误，但在方法内部，它们会在发送时引用值。因为这两个参数都是必需的参数，所以传递两个参数失败将引发一个`ArgumentError`。

## 具有默认值的参数。

Ruby 允许我们将默认值作为参数传递给方法。这样做很简单:在方法声明中使用等号(`=`)来设置默认值。例如:

```
def example_method(a,b="default_b_value")
end
```

( **图 d.** )

既然`b`现在有了默认值，那么这个方法可以叫做`example_method("a_value")` **或者** `example_method("a_value", "b_value")`。这里`example_method`仍然是一个顺序方法，因为它依赖于参数的顺序。然而，这种技术让我们对这个方法的接口有了更多的控制。

## Hash Params

在某些时候，您可能希望能够处理给定方法的任意数量的参数。Ruby 提供了一些方法来做到这一点。指定任意数量的参数的最常见方法是使用哈希作为参数。这就产生了类似于关键字的东西(我们将在后面更详细地讨论)。说实话，我不确定我能说这是最常见的传递论点的方式，但它肯定是许多受人尊敬的 ruby 爱好者的最爱。

```
def example_method(options)
  puts options[:message]
end
example_method({message: "Hello, World!"})

#=> Hello, World!
```

( **图鄂** )

Ruby 在这里做了一些聪明的事情。如果您的 options hash 是最后一个或唯一的参数，您可以省去大括号。这就是为什么这种技术有时被称为“伪关键字”参数，因为它类似于关键字，但实际上只是隐藏了一个散列。

下面的例子说明了如何使用它来编写一些漂亮的方法。

```
# the above method could be called like so (in 1.9)

example_method(message: "Hello, World!")
```

( **图 e.2\.** )

或者在参数列表的末尾使用选项。

```
def example_method(ordinal_a, ordinal_b, options)
  p [ordinal_a, ordinal_b, options]
end
example_method("Yo", "Sup", message: "Hello, World!")

#=> ["Yo", "Sup", {:message=>"Hello, World!"}]
```

( **图 e.3\.** )

使用这种模式可以得到漂亮的界面。然而，它也不是没有缺点。下面是 abberant 行为的几个例子(其中一些通过使用 Ruby 2.0 的关键字 args 得到缓解)

如果选项是默认的，但是方法被调用来传递一个意外的值(例如，Nil)，那么这个值将被使用而没有报错。在下面的例子中，我们预期参数`options`是一个散列，不幸的是我们的用户传递了 nil。[3]这会导致错误。

```
def display_name(options={})
  p options[:first_name]
end
display_name(nil)

#=> NoMethodError: undefined method `[]' for nil:NilClass

#=> from (pry):2:in `display_name'
```

( **图 e.4\.** )

这里我们有一个名字的默认值散列。如果我们传递一个散列，默认的散列将被忽略。因为默认散列被忽略，所以从不使用`first_name`键。

```
def display_name(options={:first_name => "George"})
  p options[:first_name]
end
display_name(:last_name => "Jetson")

#=> nil
```

( **图 e.5\.** )

好吧，所以有问题。也就是说，Ruby 开发人员可以使用一些模式来克服这些缺点。让我们看另一个例子。

```
class HelloWorld
  def initialize(options=nil)
    options ||= {}
    @message = opts.fetch(:message,"Hello, World!")
  end

  def to_s
    puts @message
  end
end
```

( **图 e.6\.** )

上面的代码中发生了一些事情。initialize 方法用默认参数`{}`设置`HelloWorld#new`。这样做是为了在不传递任何参数的情况下实例化这个对象。下一件有趣的事情是当我们设置`@message` var 时。因为我们知道`#new`的参数默认为一个散列，所以我们可以对它调用`Hash`方法。既然我们希望`HelloWorld`在被询问时说些什么，我们可以用`Hash#fetch` [2]默认它。

*   使用`Hash#fetch`允许我们缺省值。求解**图 e.5**
*   将选项赋给 nil，然后显式地将其重新赋给我们的默认值(`options ||= {}`)确保了在内部，我们的选项散列将是一个散列。求解**图 e.4**

## 作为数组的参数

如果我想给我的方法传入任意数量的参数呢？嗯，这可以用`*` (splat)操作符来完成。splat 操作符将接受任意数量的参数，并将它们收集为一个数组。起初，这可能看起来有点吓人。让我们看一个例子来巩固我们头脑中的想法。

```
def example_method(*args)
  p args
end
-> example_method("argument_one", "argument_two")

#=> ["argument_one", "argument_two"]
```

( **图鄂** )

您可以使用任意数量的参数(包括零个)调用此方法。这允许我们在调用方法时做一些花哨的事情。一个重要的注意事项是，由于 splat 操作符将贪婪地获取`(position..position+n)`处的所有值，所以它应该只用作最后或唯一的参数。

## 关键字参数(Ruby 2.0)

这就是它变好的地方。您可以开始使用 Ruby 2.0 的一些新特性。要使用关键字参数，您必须使用新的(1.9)样式哈希语法在签名中指定它们，尽管您可以使用经典样式调用它。既然我们对关键字应该是什么样子有了一个很好的基础(在看了“伪关键字”样式之后)，我们将直接进入一个例子。

再从**图 e.5** 看问题。在其中，您将希望为`:first_name`设置一个默认值，并且仍然允许传递`:last_name`。使用关键字参数，您可以简单地像这样定义方法:

```
def display_name(first_name: "George", last_name: "Jetson")
  p "#{last_name}, #{first_name}"
end
display_name(last_name: "Jetson")

#=>
```

可以通过以下任何方式调用此方法:

```
display_name

#=> "Jetson, George"

#
display_name(first_name: "Judy")

#=> "Jetson, Judy"

#
display_name(last_name: "Jefferson")

#=> "Jefferson, George"

#
display_name(last_name: "Flinstone", first_name: "Fred")

#=> "Flinstone, Fred"
```

为了在 Ruby 2.0 之前复制这个方法，您必须:

*   默认选项参数
*   创建一个用默认值填充的散列
*   将选项参数合并到默认哈希中

关键字参数让你在声明方法时非常简洁。在 Ruby 2.0 中，方法的创建如上，而不是 1.9.x 中更冗长的等效方法:

```
def display_name(options=nil)
  options ||= {}
  {first_name: "George", last_name: "Jetson"}.merge(options)
  p "#{last_name}, #{first_name}"
end
```

如果这是使用关键字参数的唯一方式，我会被说服，但是还有更多！盖帽和过程！说你想让`display_name`成为一个 proc(不管什么原因)。这很简单，因为:

```
display_name = Proc.new {|first_name: "George", last_name: "Jetson"| p "#{last_name}, #{first_name}" }
display_name.call

#=> "Jetson, George"
display_name.call(first_name: "Judy")

#=> "Jetson, Judy"
```

不错吧。^_^

如果传递一个没有在方法签名中指定的关键字参数，会发生什么？让我们来看看。

```
def example_method(first_keyword: "example")
end
example_method(last_keyword: "Last")

#=> ArgumentError: unknown keyword: last_keyword

#...
```

我们得到一个`ArgumentError`。如果您想要访问那些未赋值的关键字参数，您可以使用`**` (splat splat)操作符，它将任何剩余的关键字参数分组到一个散列中。[4]

```
def example_method(first_keyword: "example", **other_keywords)
  p other_keywords
end
example_method(last_keyword: "test")

#=> {:last_keyword=>"test"}
```

## 结论

很高兴你能走到这一步。我希望你现在和我一样对 Ruby 2.0 感到兴奋。更重要的是，我希望这有助于你更好地理解 Ruby 是如何工作的。Ruby 的灵活性允许在最简单的任务上做一些变动。这是它最强大的资产之一，也是我最初爱上这门语言的原因之一。

感谢阅读。

#### 参考

1.  [2010 年，艾伦·斯科尔金，“Ruby 中的方法参数”](http://www.skorks.com/2009/08/method-arguments-in-ruby/)
2.  [Ruby-Doc，1.9.3，“散列# fetch”](http://www.ruby-doc.org/core-1.9.3/Hash.html#method-i-fetch)
3.  [2011 年，雅各布·斯万纳，“可选参数陷阱”](http://jacobswanner.com/2012/11/09/optional-parameter-gotchas.html)
4.  [2012，Chris Zetter，“Ruby 2.0 中的关键字参数”](http://chriszetter.com/blog/2012/11/02/keyword-arguments-in-ruby-2-dot-0/)

#### 附加参考

*   2009 年，格雷戈里·布朗，“Ruby 最佳实践”

## 分享这篇文章