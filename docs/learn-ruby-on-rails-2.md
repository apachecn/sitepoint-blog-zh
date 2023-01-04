# 学习 Ruby on Rails:终极初学者教程

> 原文：<https://www.sitepoint.com/learn-ruby-on-rails-2/>

## 与 Ruby 对象交互

习惯从对象的角度思考需要一些时间。让我们看看几种不同类型的对象，看看我们如何与它们进行交互。

**文字对象**

文字对象是直接出现在代码中的字符串或数字，就像上一节中返回的数字 1 一样。我们已经看到数字在起作用；接下来，让我们看一个字符串文字。

字符串文字是包含一串字符的对象，如名称、地址或特别机智的短语。与我们在前一个例子中创建 1 文本对象的方式相同，我们可以很容易地创建一个新的字符串文本对象，然后向它发送一条消息。字符串文字是通过用单引号或双引号将组成字符串的字符括起来创建的，如下所示:

```
irb> "The quick brown fox"
=> "The quick brown fox"
```

首先，我们将确认我们的字符串文字确实属于类`String`:

```
irb> "The quick brown fox".class
=> String
```

这个 String 对象具有丰富的嵌入式功能。例如，我们可以通过发送长度消息来确定字符串文字包含的字符数:

```
irb> "The quick brown fox".length
=> 19
```

简单的事，嗯？

**变量和常数**

每个应用程序都需要一种存储信息的方式。输入:变量和常数。顾名思义，这两个数据容器有自己独特的角色。

常量是一个只被赋值一次的对象(通常在应用程序启动时)。因此，常量用于存储在运行的应用程序中不需要改变的信息。例如，可以使用一个常数来存储应用程序的版本号。Ruby 中的常量总是用大写字母书写，如下所示:

```
irb> CONSTANT = "The quick brown fox in a constant"
=> "The quick brown fox in a constant"
irb> APP_VERSION = 5.04
=> 5.04
```

相反，变量是能够随时改变的对象。它们甚至可以被重置为空，这释放了它们先前占用的内存空间。Ruby 中的变量总是以小写字符开头:

```
irb> variable = "The quick brown fox in a variable"
=> "The quick brown fox in a variable"
```

关于变量还有一个更特殊的(也可以说是邪恶的)东西——它的范围。变量的作用域是程序中变量可见的部分。如果你试图从一个变量的作用域之外访问它(例如，从一个变量不可见的应用程序的一部分)，你通常不能。

定义变量范围的规则的显著例外是全局变量。顾名思义，程序的任何部分都可以访问全局变量。虽然这听起来很方便，但是不鼓励使用全局变量——它们可以在程序的任何部分被写入和读取的事实会带来安全问题。

让我们回到前面看到的字符串文字的例子。将一个字符串赋给一个变量允许我们在这个变量上调用我们之前在字符串上调用的方法:

```
irb> fox = "The quick brown fox"
=> "The quick brown fox"
irb> fox.class
=> String
irb> fox.length
=> 19
```

## 拼音的标点符号

Ruby 代码中标点符号的使用与其他语言(如 Perl 和 PHP)有很大不同，因此如果您习惯于用这些语言编程，一开始可能会感到困惑。然而，一旦掌握了一些基础知识，Ruby 中的标点符号就变得非常直观，并且可以极大地增强代码的可读性。

**点符号**

Ruby 中最常见的标点符号之一是句点(。).正如我们所看到的，Ruby 使用句点来分隔接收者和发送给它的消息，形式为`Object.receiver`。

如果您需要注释一行，无论是出于文档目的还是为了临时从程序流中取出一行代码，请使用散列符号(#)。注释可能从一行的开头开始，也可能出现在更远的地方，在一些 Ruby 代码之后:

```
irb> # This is a comment. It doesn't actually do anything.
irb> 1 # So is this, but this one comes after a statement.
=> 1
irb> fox = "The quick brown fox"    # Assign to a variable
=> "The quick brown fox"
irb> fox.class                      # Display a variable's class
=> String
irb> fox.length                     # Display a variable's length
=> 19
```

## 将语句链接在一起

Ruby 不要求我们使用任何字符来分隔命令，除非我们想在一行中将多个语句链接在一起。在这种情况下，分号(`;`)用作分隔符。然而，如果你把每一个语句放在它自己的行上(就像我们一直做的那样)，分号是完全可选的。

如果在交互式 shell 中将多条语句链接在一起，屏幕上将只显示最后执行的命令的输出:

```
irb> fox.class; fox.length; fox.upcase
=> "THE QUICK BROWN FOX" 
```

**括号的使用**

如果你曾经钻研过众多 JavaScript 库的源代码，当你看到所有的圆括号都包含在[向方法](https://www.sitepoint.com/article/javascript-library/)传递参数中时，你可能会尖叫着从你的计算机上跑出来。

在 Ruby 中，在没有参数传递给方法的情况下，可以选择在方法调用中使用括号。因此，以下语句是等价的:

```
irb> fox.class()
=> String
irb> fox.class
=> String
```

常见的做法是为带有多个参数的方法调用加上括号，例如`String`类的`insert`方法:

```
irb> "jumps over the lazy dog".insert(0, 'The quick brown fox ')
=> "The quick brown fox jumps over the lazy dog"
```

这个调用在接收方`String`对象(`"jumps over the lazy dog"`)的位置 0 插入传递给`insert`对象(`"The quick brown fox "`)的第二个参数。位置 0 指的是字符串的最开始。

**方法符号**

到目前为止，我们已经看到了 Ruby 比其竞争对手使用更少标点符号的例子。事实上，在命名方法时，Ruby 大量使用了表达性标点符号。

正如我们所见，常规方法名是一个简单的字母数字字符串。如果一个方法具有潜在的破坏性(例如，它直接修改接收对象，而不是修改它的副本)，那么它通常以感叹号(！).

以下示例使用 upcase 方法来说明这一点:

```
irb> fox.upcase
=> "THE QUICK BROWN FOX"
irb> fox
=> "The quick brown fox"
irb> fox.upcase!
=> "THE QUICK BROWN FOX"
irb> fox
=> "THE QUICK BROWN FOX"
```

这里，fox 变量的内容已经被`upcase!`方法修改了。

标点符号也用在返回布尔值的方法的名称中。布尔值是一个或真或假的值；这些值通常用作询问是/否问题的方法的返回值。这种方法以一个问号结尾，这很好地反映了它们有是/否答案的事实:

```
irb> fox.empty?
=> false
irb> fox.is_a? String
=> true
```

这些命名约定使得识别破坏性方法和返回布尔值的方法变得容易，从而使 Ruby 代码更具可读性。

**Go to page:** [1](https://sitepoint.com/learn-ruby-on-rails-1/) | [2](https://sitepoint.com/learn-ruby-on-rails-2/) | [3](https://sitepoint.com/learn-ruby-on-rails-3/) | [4](https://sitepoint.com/learn-ruby-on-rails-4/) | [5](https://sitepoint.com/learn-ruby-on-rails-5/) | [6](https://sitepoint.com/learn-ruby-on-rails-6/) | [7](https://sitepoint.com/learn-ruby-on-rails-7/) | [8](https://sitepoint.com/learn-ruby-on-rails-8/) | [9](https://sitepoint.com/learn-ruby-on-rails-9/) | [10](https://sitepoint.com/learn-ruby-on-rails-10/)

## 分享这篇文章