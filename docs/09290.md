# 。NET 到 Ruby:方法和变量

> 原文：<https://www.sitepoint.com/net-to-ruby-methods-and-variables/>

在我们关于从。NET 到 Ruby，我们看了看[类](https://www.sitepoint.com/net-to-ruby-classes/)。阶级是一个真正深入的话题，因为在。NET 和 Ruby。这篇文章将看看方法和变量。首先，我们将讨论如何创建和调用方法，然后我们将研究块和块的一些应用，最后我们将讨论 Ruby 中存在的变量类型。

和往常一样，我们实际上会比较 Ruby 和 C#，因为[。NET 是一个框架而不是一门语言。](https://www.sitepoint.com/putting-ruby-rails-c-and-asp-net-in-context/)

## 方法

Ruby 方法非常简单——特别是因为没有类型系统要担心，这允许我们跳过定义参数类型和方法返回类型。ruby 中的方法是使用`def`关键字开始方法定义的，它们是使用`end`关键字结束的，就像这样:

```
def a_method()
  # this is a Ruby method
end
```

这与. NET 方法非常不同，后者看起来像这样:

```
public void AMethod() {
    // this is a C# method
}
```

使用方法参数时，将它们写在括号内并用逗号分隔:

```
def a_method(a, b, c)
  # this method has three parameters a, b, and c
end
```

正如上一篇关于 [Ruby 类](https://www.sitepoint.com/net-to-ruby-classes/)的文章所述，因为 Ruby 是鸭类型的，所以你不必在方法定义中声明参数的数据类型。比较一下。网络:

```
private void AMethod(int a, int b, int c) {
  // this method has three parameters: 
  // an integer a, an integer b, and an integer c
}
```

这两段代码的区别在于需要编写的字符数量。Ruby 被认为是一种简洁的语言，小方法定义显然是让 Ruby 如此简洁的原因之一。

接下来，让我们看看 Ruby 的一个更晦涩的语法。你可能会在代码中看到没有括号的方法——哇！

```
def a_method a, b, c
  # this method also has three parameters a, b, and c
end
```

这是非常有效的语法，您可以在自己的代码中使用它。让我们使用以下语法回溯并重写我们的第一个示例:

```
def a_method
  # No brackets here
end
```

所以你可以看到 Ruby 的方法与。NET 的。但是在我向世界发布一些牛仔编码员之前，有一点很重要**我要告诉你关于公认的社区实践，那就是:*当定义方法时，省略没有参数的方法的括号，当你的方法有参数时使用括号*。**

让我们继续讨论方法调用。它们看起来和。NET 方法调用，它们也可以在没有括号的情况下完成:

```
AMethod(1, 2, 3);
```

```
a_method(1, 2, 3)
a_method 1, 2, 3
```

以上所有代码行都使用参数 1、2、3 调用`a_method`。

现在我们已经完成了方法的创建和调用，让我们进入一些 C#所缺乏的方法的有趣方面，这也是 Ruby 的闪光点。

## 阻碍

我们从积木开始。使用花括号或在关键字`do`和`end`之间定义块:

```
{ puts "A Block" }
```

```
do
  puts "also a block"
end
```

从概念上讲，块就像 C#中的 lambda，它们可以被传递到一个方法中，然后在该方法的上下文中执行。这是使用 yield 完成的:

```
def a_method
  puts "begin"
  yield
  puts "end"
end

a_method { puts "block code" }
```

哪些输出:

```
begin
block code
end
```

就像方法一样，块可以接受变量。这是通过使用管道字符来分隔变量名实现的，如下所示:

```
def a_method
  yield("George")
end

a_method { |name| puts name }  #=> George
```

C#中的等效代码如下所示:

```
public static void a_method(Action<string> block) {
    block("George");
}

a_method((name) => System.Console.WriteLine(name));
```

Ruby 中大量使用了块，它们允许我们实现一些简洁的特性，比如 C#的 using 语句。例如，下面是一些用 C#编写的要写入文件的代码:

```
using (StreamWriter file = new StreamWriter(@"Output.txt")) {
    file.WriteLine("Line 1");
}
```

这段代码打开文件`Output.txt`，然后将“Line 1n”写入文件，最后关闭文件句柄。Ruby 也能做同样的事情，但是它使用块来完成:

```
File.open("Output.txt", "w") do |file|
  file.write "Line 1"
end
```

很整洁不是吗！？在上面的代码中，`file`是对由`File.open`创建的`IO`对象的变量引用。我承认，Ruby 的块语法比 C#的语法更隐晦，因为使用 C#你需要明确你的用法。这将是你学习 Ruby 时需要克服的一个小障碍。

块还有许多更强大的应用。以辛纳特拉为例。Sinatra 使用块为其 URL 路由创建 DSL(域特定语言)。辛纳特拉的 Hello World！示例如下所示:

```
get '/hello' do
  'hello world'
end
```

在上面的例子中，`get`仅仅是一个方法调用，在它的末尾附加了一个块。这使得在 Sinatra 中编写 routes 变得非常容易，并且使得代码非常容易阅读，同时仍然保持其功能的表现力。RubySource 在 Sinatra 上有一个四集系列，也值得一看！

最终，块实际上只是内置于 Ruby 语言中的策略模式的一种实现。因为块很容易编写，所以当 ruby 爱好者编写代码时，它们被广泛使用。

## 变量

离开程序块，让我们把这篇文章的剩余部分集中在 Ruby 程序的最基本的项目上，变量。局部变量是你在 Ruby 方法中写的变量，是这样写的:

```
a_variable = 2
```

C#中的等效代码:

```
var a_variable = 2;
```

同样，没有必要告诉 Ruby 你的变量类型是什么。Ruby 自己解决了这个问题，这使得变量语法和方法语法一样简洁。Ruby 变量使用一种叫做 snake case 的命名约定。所以不用大写字母比如。NET 不区分变量中的单词，您应该使用下划线。以下变量是有效的变量名:

```
variable
a_variable
a_really_big_variable_name
```

下面的变量仍然是有效的 Ruby 变量，但是它们打破了 Ruby 的命名惯例，所以不应该使用它们。

```
Variable
A_Variable
areallybigvariablename
```

在写类的时候，Ruby 有一些特殊的符号来区分局部变量和实例变量。实例变量以 at 符号为前缀:`@`。这与……所以有必要比较一下这两者:

```
public class Example {
    private int iv;

    public Example() {
        iv = 12;
    }
}
```

```
class Example
  def initialize
    @iv = 12
  end
end
```

在上面的两个示例类中，在`Example`的每个实例中都创建了一个实例变量`iv`。在 Ruby 中，没有对变量`@iv`的显式声明，相反，通过将`12`赋值给`@iv`，Ruby 足够聪明地确定`@iv`是你的实例变量。

除了实例变量，Ruby 还有另一种类型的变量，称为类变量。对于那些来自。NET 世界，这和静态变量是一样的。要创建一个类变量，你需要使用一个双 at 符号:`@@`。下面的代码示例是等效的:

```
public class Example {
    private static int id = 42;

    public static int GetId() {
        return id;
    }
}
```

```
class Example
  @@id = 12

  def self.get_id
    @@id
  end
end
```

同样，值得一提的是 Ruby 也有全局变量。它们的前缀是一个美元符号:`$`。当你开始学习 Ruby 时，你可能不会遇到全局变量，所以我不会列举存在的变量，但是 Ruby 用户指南有一个全局变量的[列表。](http://www.rubyist.net/~slagell/ruby/globalvars.html)

这里是我们结束这篇文章的地方。在这篇文章结束时，你应该能够创建方法，调用方法，使用块，并且知道 Ruby 中的 3 种类型的变量。至此，如果您已经按照本系列进行了学习，那么您已经拥有了开始编写自己的 Ruby 程序的基本构件。您已经有了在中工作的[环境，要使用的](https://www.sitepoint.com/net-to-ruby-the-ruby-environment/)[类](https://www.sitepoint.com/net-to-ruby-classes/)，现在还有构建您的例程的方法和变量。

在下一篇文章中，我们将做一些 Ruby 内务处理，并介绍一些工具，你可以用这些工具来保持你的 Ruby 程序对其他 Ruby 爱好者的可读性和可维护性。我们将触及命名约定、注释、文档和命名空间等主题，所以一定要回来查看这篇文章！

## 分享这篇文章