# 。NET 到 Ruby:类型

> 原文：<https://www.sitepoint.com/net-to-ruby-types/>

到目前为止，在[上的这些帖子。我们已经看了类、命名空间、文档、方法、变量和 Ruby 环境。我们已经走过了很多地方。今天，我们将看看构成 Ruby 语言基础的数据类型。](https://www.sitepoint.com/switching-to-ruby-from-net/)

即使我们不必在 Ruby 中显式声明变量数据类型，它们仍然存在。来自 C#背景，你会发现许多你熟悉的朋友:字符串、整数、数组和散列。此外，Ruby 引入了一些新的类型:范围和符号。在这篇文章中，我们将会看到这些主要的项目。

## 用线串

字符串是任何编程语言的主要组成部分。在 C#中，字符串的写法如下:

```
var value = "string"
```

和 Ruby 实际上是一样的:

```
value = "string"
```

Ruby 标准库为操作字符串提供了很多很好的功能。我最喜欢的特性之一是 Ruby 如何格式化字符串。Ruby 通过使用语法`#{}`为您提供了内联字符串的能力。在 C#中，格式字符串看起来像这样:

```
var place = "Rockey Mountains"
var feeling = "happy!"
var result = string.Format("Driving to the {0} makes me {1}", place, feeling);
```

在 Ruby 中，相同的功能如下所示:

```
place = "Rockey Mountains"
feeling = "happy!"
result = "Driving to the #{place} makes me #{feeling}"
```

这还不是最酷的部分。您可以在花括号之间放置一个可求值的表达式，计算结果将内联到您的字符串中。让我们看看实际情况:

```
puts "I am #{32 - (10 / 2)} years old" 
  #=> I am 27 years old
```

Ruby 还以单引号的形式提供了另一个字符串分隔符。这个分隔符不提供与双引号相同的替换量，所以当您需要输出换行符之类的控制字符时，它很有用:

```
puts 'Reduced substitutionn' #=> Reduced substitutionn
puts "More substitutionn"    #=> More substition
```

上面的第二行将在末尾添加一个换行符，而第一行只是打印出`n`字符。

在我们继续之前需要注意的一个重要事实是，Ruby 字符串不是不可变的。从. NET 背景来看，这看起来很奇怪，因为对于 C#程序员来说，字符串是不可变的。让我们来看看这个概念的应用:

```
s = "string"
puts s.object_id        #=> 2156184600

s.gsub!("ing", "ong")
puts s.object_id        #=> 2156184600
```

在这里，我们可以看到方法`gsub!`实际上修改了字符串实例，请特别注意值发生了变化，但是`object_id`保持不变。方法签名中的`!`是一个指示器，表明调用方法将修改它被调用的对象，这是 Ruby 的惯例。这与有很大的不同。所以确保它不会绊倒你。

我可以写一整篇关于弦乐的文章，但这应该足够让你放松了；通读[字符串文档](http://www.ruby-doc.org/core-1.9.3/String.html)以更好地了解存在哪些函数。

## 整数

Ruby 的 integer 类与 C#的 int 有很大不同。虽然它们都表示相同的原始数据类型，但是这两种语言实现这种表示的方式是不同的。对于 C#，int 被称为值类型，它是一个固定大小的 32 位整数。在 Ruby 中，integer 是一个包装或管理另外两个与数字相关的类的类:Fixnum 和 Bignum。

更详细地说，当整数值可以用本机字减 1 位(这是 CPU 特定的)来表示时，使用 Fixnum，而 Bignum 是 Fixnum 范围之外的任何值。现在这可能看起来有点混乱，但是幸运的是 Ruby 处理了所有这些信息，包括以完全透明的方式从 Fixnum 到 Bignum 的转换。

Ruby integers 的有用之处在于它们被表示为对象，这意味着所有的整数都有一组可以在其上调用的方法。让我用一个例子来演示其中的两种方法。

让我们采用[斑马条纹](http://en.wikipedia.org/wiki/Zebra_striping)的概念，并使用 C#和 Ruby 创建一个代码片段，为数字 1-10 生成字符串“偶数”或“奇数”。

```
for (int i = 0; i < 10; i++) {
    var i_is = (i % 2 == 0) ? "even" : "odd"
    Console.Write(i_is);
}
```

```
10.times do |i| 
  i_is = i.even? ? "even" : "odd"
  puts i_is
end
```

在上面的例子中，代码完成了相同的任务，但是 Ruby 代码更简洁，没有失去任何意义。同样，ruby 代码展示了我提到的两个方法，`even?`和`times`。如果方法名没有意义，检查他们各自的[文档](http://www.ruby-doc.org/core-1.9.3/Integer.html)。

## 数组

让我们看看 C#中的一个数组:

```
var items = new string[] { "item1", "item2", "item3" }
```

在 Ruby 中，你可以像这样构造一个数组:

```
items = [ "item1", "item2", "item3" ]
```

Ruby 数组的一个不同之处是它们不是强类型的。也就是说，在一个数组中可以有一个整数、字符串和对象，而 Ruby 不会抱怨。虽然您通常不会将整数、字符串和对象放在同一个数组中，但当您有实现相同方法的不同对象时，这很有用，duck typing 允许您这样做:

```
class ObjectA
  def hello
    "I'm object A"
  end
end
class ObjectB
  def hello
    "I'm object B"
  end
end
class ObjectC
  def hello
    "I'm object C"
  end
end

[ObjectA.new, ObjectB.new, ObjectC.new].each {|obj| puts obj.hello }
#=> "I'm object A"
#=> "I'm object B"
#=> "I'm object C"
```

想一想，你就会明白鸭子打字在 Ruby 中是如何大放异彩的。

Array 类定义了大量的[方法](http://www.ruby-doc.org/core-1.9.3/Array.html)，这些方法允许你不用写`for`或`while`循环就可以轻松地迭代数组。

Array 类的另一个很酷的特性是，它有一些方法允许将数组视为一个堆栈或队列。对于堆栈，它有方法`push`和`pop`。对于队列，它有方法`push`用于将一个项目入队，还有方法`shift`用于出列。

## 混杂

Ruby 的一大优点是散列是一等公民。它们的语法简洁，并且享受标准库中提供的大量功能。

在 C#中，您可以创建这样的散列:

```
Hashtable ht = new Hashtable{ {"key", "value"} };
```

在 Ruby 中，散列就像这样写一样简单:

```
ht = { :key => "value" }
```

散列在 Ruby 中被广泛使用，因为它们非常容易访问。添加新的键/值非常简单:

```
ht[:new_key] = "value"
```

或者假设您想要迭代所有的键/值对，代码将如下所示:

```
social_media = { :twitter => "cool", :facebook => "scary", :google_plus => "meh" }
social_media.each_pair do |key, value|
  puts "#{key} is #{value}"
end
```

这将输出:

```
twitter is cool
facebook is scary
google_plus is meh
```

如果在这一点上哈希没有显得过于有用，就等着吧。当我们谈到符号时，我将向您展示频繁使用散列的原因之一。

## 范围

范围是一种有趣的数据类型，它采用起始值和结束值，组成这些值之间的间隔。可以用定义比较运算符 `and the method `succ`的任何对象来构造范围，以便可以返回序列中的下一个元素。例如，我们可以创建一系列数字、字符或日期:`

```
(1..10)                         #=> 1..10
('a'..'z')                      #=> a..z
(Date.today - 5...Date.today)   #=> #<Date: 2011-11-11>...#<Date: 2011-11-16>
```

您会注意到，在上面的日期示例中，使用了三个点(`...`)，而不是两个点(`..`)。对于范围，两个点表示“使用整个范围”，三个点表示“排除最终值”范围不会出现得太频繁，但是当你需要它们的时候，它们真的很有用。

## 标志

符号是 C#中不存在的另一种数据类型，但是你会发现它们在 Ruby 中被广泛使用。C#和 Ruby 之间最接近的比较是把符号称为枚举器和常数的混合体。

符号通过在单词或字符串前加冒号来声明:

```
:name           #=> :name
:"a symbol"     #=> :"a symbol"
```

第二个例子很少使用，如果有的话。符号最常见的用法之一是选项散列，这在 Rails 等框架中很常见。这里有一个例子:

```
def make_link(href, opts = {})
  title = opts[:title] || href

  "<a href="#{href}">#{title}</a> 
end

puts make_link("http://www.sitepoint.com", {:title => "How To Be An Awesome Rubyist"})
  #=> <a href="http://www.sitepoint.com">How To Be An Awesome Rubyist</a>

puts make_link("http://www.sitepoint.com")
  #=> <a href="http://www.sitepoint.com">http://www.sitepoint.com</a>
```

你可以在上面的代码中看到，我们使用符号`:title`作为方法`make_link`的可选值。Ruby 中的主要框架倾向于使用这种约定，因为它清理了方法接口，并使添加/删除可选参数变得更容易。通过这种方式，您可以更改方法签名(添加或移除参数),而不会导致依赖于该方法的每段代码中断。

需要强调的一点是，在 Ruby 中，为什么要在字符串上使用符号。一个主要原因是一个符号总是引用同一个对象，而每个字符串实例是一个不同的对象(记住 Ruby 字符串是可变的。)让我们来看看实际情况:

```
puts :symbol.object_id   #=> 442248
puts :symbol.object_id   #=> 442248
puts "string".object_id  #=> 2156234340
puts "string".object_id  #=> 2156197521
```

注意两个字符串实例的`object_id`是如何变化的，但是对于`:symbol`是相同的。这支持字符串部分中提到的可变字符串的概念。

至此，我们已经看了很多比较 C#和 Ruby 的代码。今天的帖子讨论了 Ruby 和 C#数据类型之间的区别。我们探索了字符串、整数、数组、散列、范围和符号。我想你已经准备好把一个 C#程序转换成 Ruby 程序了。我建议从类似 [FizzBuzz](http://www.codinghorror.com/blog/2007/02/why-cant-programmers-program.html) 这样的东西开始，然后从那里开始。学习一门新语言的最好方法是用那种语言编写程序。

## 分享这篇文章`