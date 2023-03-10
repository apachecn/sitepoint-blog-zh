# 。NET 到 Ruby:命名空间、命名约定、注释和文档

> 原文：<https://www.sitepoint.com/net-to-ruby-namespacing-naming-conventions-comments-documentation/>

Ruby 是一种固执己见的语言，它有一个固执己见的社区。这是一件伟大的事情。它保持语言的干净、易懂和有趣。

当涉及到像这样的框架时，Ruby 可能是对抗性的。NET，我会在这篇文章中谈到这一点。此外，我们将快速介绍命名空间，触及命名约定，然后进入注释和文档。

## 命名空间

我们编写的程序使用名称空间有两个目的:1 .将代码分组到相关的功能组中。防止方法、类、变量和常量相互冲突。英寸 NET 中，我们使用`namespace`关键字来命名我们的代码，这是一种很好的不言而喻的方式。命名空间语法包含在要包含在命名空间中的代码周围，它包含在左花括号和右花括号{ }中:

```
namespace FooBar {
    // ...
}
```

在 Ruby 中，关键字`module`用于命名代码:

```
module Fizz
  # ...
end
```

你应该还记得关于 [Ruby Classes](https://www.sitepoint.com/net-to-ruby-classes/) 的帖子，模块不仅仅用于命名空间，它们也用于创建 mixin——查看这篇帖子可以更详细地了解 mixin。

## 命名规格

Ruby 的命名约定相当容易理解。我们在以前的文章中已经介绍了大部分的命名约定，但是在一个地方还是值得一提的:

```
Type          .NET           Ruby
Namespaces    CamelCase      CamelCase
Classes       CamelCase      CamelCase
Methods       CamelCase      underscores_and_lowercase
Variables     camelBack      underscores_and_lowercase
Constants     CamelCase      ALL_CAPS
```

你可能通过它的别名 PascalCase 知道骆驼案例。

在之前的文章中，我们没有提到的一件事是用 Ruby 编写常量。Ruby 常量是用 ALL_CAPS 编写的，它们实际上也可以被重新赋值，但是如果发生这种情况，Ruby 会发出一个警告:

```
IMPORTANT_VALUE = 1
puts "Important Value: #{IMPORTANT_VALUE}"

IMPORTANT_VALUE = 2
# A warning is issued after the above line executes: 
#    warning: already initialized constant
puts "Important Value: #{IMPORTANT_VALUE}"
```

加上上面的 Ruby 的`@instance_variable`符号和`@@class_variable`符号，我们在关于 [Ruby 方法和变量](https://www.sitepoint.com/net-to-ruby-methods-and-variables/)的文章中提到过，你就有了 Ruby 的大部分命名约定。

## 评论和文档

在过去的几个帖子中，我一直在使用评论，但从未解释过我实际上是在使用评论。所以让我们来谈谈这个方面。对于 Ruby 中的单行注释，可以使用散列字符。

```
# This is a comment
```

如果我们想要创建一个多行注释，我们可以使用下面的代码:

```
=begin
  Comment goes here. =begin and =end must start
  at the left margin otherwise they are ignored.
=end
```

代码中不应该经常使用多行注释。相反，多行注释更适合在开发过程中注释掉大段代码。对于大块的注释(比如在文档中)，最好使用散列。

说到文档，我喜欢 Ruby 的一点是文档工具通过一个叫做 RDoc 的工具与核心语言捆绑在一起。不用再去寻找是否应该使用 NDoc、Sandcastle、GhostDoc、Doxygen 或本月最新的版本，以及该项目是否会像您的项目一样长久——RDoc 今天存在，明天也会存在。另一个很好的特性是 RDoc 支持标记，因此编写文档、从源代码中读取文档以及将文档处理成 web 文档都是很好很容易的事情。如果你渴望了解更多细节，RDoc 自述文件是个不错的去处。

其次，Ruby 核心文档可以在 http://ruby-doc.org/core 的[网站上找到。不要再搜寻 MSDN 了！Ruby 的文档非常有用，在大多数情况下包含了如何使用特定方法的简明示例。例如，让我们比较一下 String 的](http://ruby-doc.org/core) [C#文档。修剪()](http://msdn.microsoft.com/en-us/library/t97s7bs3.aspx)用最等效的方法[串#条](http://ruby-doc.org/core/classes/String.html#M001189)。我说基本相当是因为。NET 文档不清楚它是否删除了像`rn`这样的换行符…回到比较，这里是 trim 的文档:

```
using System;

public class Example
{
   public static void Main()
   {
      Console.Write("Enter your first name: ");
      string firstName = Console.ReadLine();

      Console.Write("Enter your middle name or initial: ");
      string middleName = Console.ReadLine();

      Console.Write("Enter your last name: ");
      string lastName = Console.ReadLine();

      Console.WriteLine();
      Console.WriteLine("You entered '{0}', '{1}', and '{2}'.",
                        firstName, middleName, lastName);

      string name = (firstName.Trim() + " " + middleName.Trim()).Trim() + " " +
                    lastName.Trim();
      Console.WriteLine("The result is " + name + ".");
   }
}
// The following is possible output from this example:
//       Enter your first name:    John
//       Enter your middle name or initial:
//       Enter your last name:    Doe
//
//       You entered '   John  ', '', and '   Doe'.
//       The result is John Doe
```

那是什么怪物！我对这样的例子感到沮丧，因为要弄清楚实际发生了什么需要做很多工作。与 Ruby 文档相比:

```
# str.strip -> new_str
# Returns a copy of str with leading and trailing whitespace removed.
"    hello    ".strip   #=> "hello"
"tgoodbyern".strip   #=> "goodbye"
```

是的，这就是`strip`的全部方法文档。你看，Ruby 的目标之一是让它成为一种有趣的编程语言，在这个目标中，所有的缺点都被去掉了。只剩下必要的了。然而，在 C#的例子中，你必须读到第 19 行才能到达`Trim()`函数。然后，您必须解析大括号、引号和连接。布雷克。

Ruby 的另一个优点是，如果您不喜欢某个特定方法的文档，并且认为它可以被清理，您可以提交该文档的补丁。你看, **Ruby 使编码人员能够完成工作，并在缺少某些东西时给予反馈。上一次 MS 允许你用 C#、和做任何事情是什么时候？除了通过牙齿支付他们的软件许可证！**

好了，听够了我的咆哮。现在我听到你问如果我没有连接到互联网会发生什么？我如何访问所有这些优秀的文档？这很简单——用一个叫 ri 的工具。ri 是 Ruby 内置的离线文档查看器。假设你想知道`String`有哪些方法，只要输入就可以了

ri 字符串

ri 将为您提供所有可以针对 String 运行的方法。如果您想更深入地了解方法的作用，请将方法名附加到它的类中:

ri 字符串#条

ri 是一个很好的工具，可以用来探索语言中的可用内容，当然比在 Visual Studio 中意外点击`F1`要好！需要注意的一点是，如果你运行的是 Ruby 1.8.7，你需要安装`rdoc-data` gem，并执行以下步骤:

```
$ gem install rdoc rdoc-data
$ rdoc-data --install
```

这个 gem [修复了 ri 文档，使它更加有用](http://unethicalblogger.com/2011/09/01/making-ri-useful.html)。这个问题在 1.9.2 中已经解决了，所以如果你是那个版本的话就不用担心了。

这篇文章到此结束。在这篇文章中，我们讨论了 Ruby 中的命名空间、命名约定、注释和文档。我们还了解了如何用 Ruby 编写常量。

在下一篇文章中，我们将深入探究 Ruby 的类型系统，看看 Ruby 的底层发生了什么。

## 分享这篇文章