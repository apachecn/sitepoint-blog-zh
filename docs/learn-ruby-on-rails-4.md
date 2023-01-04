# 学习 Ruby on Rails:终极初学者教程

> 原文：<https://www.sitepoint.com/learn-ruby-on-rails-4/>

## Ruby 核心类

在前面的章节中，我们已经简要地讨论了 String 和 Fixnum 类，但是 Ruby 还有更多。让我们一起探索吧！

## 数组

我们使用 Ruby 的`Array`来存储对象集合。存储在`Array`中的每个对象都有一个唯一的数字键，我们可以用它来引用它。与许多语言一样，`Array`中的第一个元素存储在位置 0(零)。

要创建一个新的`Array`，只需实例化一个类`Array`的新对象(使用`Array.new`构造)。你也可以使用一种快捷的方法，那就是把你想放在`Array`里面的对象用方括号括起来。

例如，一个包含汽车定期保养里程的`Array`可能看起来像这样:

```
irb> service_mileage = [5000, 15000, 30000, 60000, 100000]

=> [5000, 15000, 30000, 60000, 100000]
```

为了从`Array`中检索单个元素，我们在方括号中指定数字键。

```
irb> service_mileage[0]
=> 5000
irb> service_mileage[2]
=> 30000
```

Ruby 有另一个快捷方式，它允许我们从一列`String`中创建一个`Array`:`%w( )`语法。使用这种快捷方式可以使我们不必键入大量双引号字符:

```
irb> available_colors = %w( red green blue black )
=> ["red", "green", "blue", "black"]
irb> available_colors[0]
=> "red"
irb> available_colors[3]
=> "black"
```

除了方便简单的元素检索之外，`Array` s 还提供了一组广泛的类方法和实例方法，极大地简化了数据管理任务。

如果接收的`Array`不包含任何元素，则`empty?`返回`true`:

```
 irb> available_colors.empty?
=> false
```

`size`返回一个`Array`中元素的个数:

```
irb> available_colors.size
=> 4
```

```
first and last return an Array's first and last elements, respectively:

```

```
irb> available_colors.first
=> "red"
irb> available_colors.last
=> "black"
```

`delete`从`Array`中移除命名元素并返回它:

```
irb> available_colors.delete "red"
=> "red"
irb> available_colors
=> ["green", "blue", "black"]
```

通过 Ruby 参考文档可以获得由`Array`类提供的类方法和实例方法的完整列表，您可以通过在终端窗口(针对您的操作系统，而不是 Ruby shell)中输入`ri`命令，后跟您想要查找的类名来访问该文档:

```
$ ri Array
```

哦，`ri`代表 ruby interactive，如果你想知道的话。不要和`irb`混淆。

## 混杂

一个`Hash`是另一种数据存储容器。从概念上讲，哈希表类似于字典:它们以一对一的关系将一个对象(例如，一个单词)映射到另一个对象(例如，一个单词的定义)。

新的`Hash` es 可以通过实例化类`Hash`的一个新对象(使用`Hash.new`构造)或者使用如下所示的花括号快捷方式来创建。当我们定义一个`Hash`时，我们必须使用`key => value`语法指定每个条目。

例如，下面的`Hash`将汽车名称映射到一种颜色:

```
irb> car_colors = {
irb>   'kitt' => 'black',
irb>   'herbie' => 'white',
irb>   'batmobile' => 'black',
irb>   'larry' => 'green'
irb> }
=> {"kitt"=>"black", "herbie"=>"white", "batmobile"=>"black",
"larry"=>"green"}
```

为了查询这个新建的`Hash`，我们将想要查找的条目的键放在方括号中，就像这样:

```
irb> car_colors['kitt']
=> "black"
```

各种有用的功能都内置在`Hash` es 中，包括以下方法:

如果接收的`Hash`不包含任何元素，则`empty?`返回`true`:

```
irb> car_colors.empty?
=> false
```

`size`返回一个`Hash`中元素的个数:

```
irb> car_colors.size
=> 4
```

`keys`返回一个`Hash`的所有键作为一个`Array`:

```
irb> car_colors.keys
=> ["kitt", "herbie", "larry", "batmobile"]
```

`values`将一个`Hash`的所有值作为一个`Array`返回，尽管应该注意元素的顺序(一个`Hash`中的键是为了最佳存储和检索而排序的；这个顺序不一定反映它们被输入的顺序):

```
irb> car_colors.values
=> ["black", "white", "green", "black"]
```

还有很多——关于由`Hash`类提供的类方法和实例方法的完整列表，请参考 Ruby 参考文档。

## 用线串

典型的 Ruby `String`对象——是的，就是我们在过去几节中使用的那个对象——保存并操作字符序列。大多数时候，新的`String`对象是用单引号或双引号括起来的字符串创建的。然后，可以将文字存储在变量中供以后使用:

```
irb> a_phrase = "The quick brown fox"
=> "The quick brown fox"
irb> a_phrase.class
=> String
```

如果字符串文字包含用于括住字符串本身的引号字符，则必须用反斜杠字符(`)对其进行转义:`

```
irb> 'I'm a quick brown fox'
=> "I'm a quick brown fox"
irb> "Arnie said, "I'm back!""
=> "Arnie said, "I'm back!""
```

指定包含引号的字符串文字的一种更简单的方法是使用`%Q`快捷键，如下所示:

```
irb> %Q(Arnie said, "I'm back!")
=> "Arnie said, "I'm back!""
```

`String`对象还支持通过 Ruby 表达式将 Ruby 代码替换成字符串文字`#{}`:

```
irb> "The current time is: #{Time.now}"
=> "The current time is: Wed Aug 02 21:15:19 CEST 2006"
```

`String`类还具有丰富的修改字符串对象的嵌入式功能。以下是一些最有用的方法:

`gsub`替换`String`内的给定模式:

```
irb> "The quick brown fox".gsub('fox', 'dog')
=> "The quick brown dog"
```

如果一个`String`包含另一个特定的`String`，则`include?`返回`true`:

```
irb> "The quick brown fox".include?('fox')
=> true
```

`length`返回一个`String`的字符长度:

```
irb> "The quick brown fox".length
=> 19
```

`slice`返回`String`的一部分:

```
irb> "The quick brown fox".slice(0, 3)
=> "The"
```

使用`ri`命令行工具可以获得完整的方法参考:

```
$ ri String
```

## 数字

因为有这么多不同类型的数字，Ruby 为每一种都有一个单独的类，其中有流行的`Float`、`Fixnum`和`Bignum`类。事实上，它们都是提供基本功能的`Numeric`的子类。

就像`String` s 一样，数字通常由文字创建:

```
irb> 123.class
=> Fixnum
irb> 12.5.class
=> Float
```

每一个特定的`Numeric`子类都有与它要处理的数字类型相关的特性。然而，以下功能是所有`Numeric`子类共享的:

如果对象是一个整数，则返回 true:

```
irb> 123.integer?
=> true
irb> 12.5.integer?
=> false
```

`round`将数字四舍五入为最接近的整数:

```
irb> 12.3.round
=> 12
irb> 38.8.round
=> 39
```

如果数字等于零，则返回 true:

```
irb> 0.zero?
=> true
irb> 8.zero?
=> false
```

此外，有一些方法可以在`Numeric`子类之间转换数字。`to_f`将值转换为`Float`,`to_i`将值转换为`Integer`:

```
irb> 12.to_f
=> 12.0
irb> 11.3.to_i
=> 11
```

## 标志

在 Ruby 中，`Symbol`是一个简单的文本标识符。像`String`一样，`Symbol`是使用文字创建的；不同之处在于`Symbol`的前缀是冒号:

```
irb> :fox
=> :fox
irb> :fox.class
=> Symbol
```

使用`Symbol`而不是`String`的主要好处是`Symbol`包含的功能更少。在某些情况下，这可能是一个优势。例如，我们之前看到的`car_colors` `Hash`可以改写如下:

```
car_colors =
:kitt => 'black',
:herbie => 'white',
:larry => 'green',
:batmobile => 'black'
}
```

类别`String`的对象可以转换为`Symbols`，反之亦然:

```
irb> "fox".to_sym
=> :fox
irb> :fox.to_s
=> "fox"
```

当我们在本书的后续章节中讨论 Rails 功能时，我们会经常用到`Symbols`。

## 无

我之前承诺过我会解释价值观——现在是时候了！

所有的编程语言都有一个价值，当它们实际上毫无意义时，它们可以使用。有的用`undef`；别人用`NULL`。红宝石使用`nil`。一个`nil`值，像 Ruby 中的所有东西一样，也是一个对象。因此它有自己的阶级:`NilClass`。

基本上，如果一个方法不返回任何东西，它实际上是在返回值`nil`。如果你将`nil`赋给一个变量，你实际上使它为空。`nil`出现在几个额外的地方，但我们会越过那些桥，当我们来到他们身边。

**Go to page:** [1](https://sitepoint.com/learn-ruby-on-rails-1/) | [2](https://sitepoint.com/learn-ruby-on-rails-2/) | [3](https://sitepoint.com/learn-ruby-on-rails-3/) | [4](https://sitepoint.com/learn-ruby-on-rails-4/) | [5](https://sitepoint.com/learn-ruby-on-rails-5/) | [6](https://sitepoint.com/learn-ruby-on-rails-6/) | [7](https://sitepoint.com/learn-ruby-on-rails-7/) | [8](https://sitepoint.com/learn-ruby-on-rails-8/) | [9](https://sitepoint.com/learn-ruby-on-rails-9/) | [10](https://sitepoint.com/learn-ruby-on-rails-10/)

## 分享这篇文章`