# 。NET 到 Ruby:Ruby 环境

> 原文：<https://www.sitepoint.com/net-to-ruby-the-ruby-environment/>

从过渡。NET 到 Ruby:

你好。我听说你想从。NET 到 Ruby。这不是一项艰巨的任务，但从指导开始总是很好的。我也曾和你一样。的硕士。NET 是 LINQ 的超级明星、CLR 大师、通才、WCP、WPF 和 WF 的征服者，并且能够与其中的佼佼者进行较量。也许你也像我一样；越过栅栏凝视着红宝石牧场，想知道草是否真的更绿了？为了与牧场的比喻保持一致，这个系列将引导你从。NET 转 Ruby[【1】](#footnote_1)。在这篇文章中，我们将深入探讨这两种语言是如何相互融合的。我们将触及一些更高层次的项目，比如 MVC 框架、测试框架和 ORM，但是对于这篇文章，我们将从基础开始，弄清楚 Ruby 是什么，以及你需要熟悉哪些工具。够了，让我们开始吧。

## 什么是 Ruby？

首要任务是定义什么是 Ruby。Ruby 是一种动态的、强类型的解释型语言。C#是一种静态的强类型语言。Ruby 是由[松本幸宏](http://en.wikipedia.org/wiki/Yukihiro_Matsumoto)，简称 Matz 在 20 世纪 90 年代中期创造的。首先，Ruby 是面向对象的，事实上它是如此的面向对象，以至于它的类都是对象，正如我们将在本系列中看到的，这是 Ruby 如此强大的原因之一。Ruby 的当前版本是 1.9.2，如果你还没有安装，你应该安装！要安装 Ruby，并正确设置您的环境，请使用 Glenn Goodrich 的文章 [Rails 开发 101: RVM](https://www.sitepoint.com/rails-development-101-rvm/) 。这篇文章假设你已经安装了 Ruby。

## 命令行

Ruby 主要从命令行运行。不像。NET，rubyists 住在命令行，随着时间的推移，你会爱上它！有很多 IDE，但是你会发现大多数 Ruby 开发人员使用 emacs、vim 或 textmate。

我强烈推荐设置一个* nix[【2】](#footnote_2)的环境。Ruby 可以在 windows 上运行，但速度明显较慢。绝大多数开发人员都在*nix 环境中工作，所以你在网上找到的大多数资源都有这种倾向，这让你从. NET 过渡到 Ruby 更加容易。

Ruby 程序使用命令`ruby`运行。使用它，我们可以直接从命令行运行 Ruby 程序:

```
ruby -e 'puts "Hello World!"'
```

这个命令会输出 **Hello World！**到你的控制台。`-e`开关告诉 Ruby 它正在执行一行脚本，代码周围的单引号限定了程序。

我们还可以通过将代码放入扩展名为`.rb`的文件中，然后将文件名传递给`ruby`命令来运行程序。让我们试一试。一份`hello_world.rb`文件里面写着如下内容:

```
# hello_world.rb
puts "Hello World!"
```

然后在命令行上运行命令:

```
ruby hello_world.rb
```

因为我们如此频繁地使用命令行，空格可能是 Ruby 的一个大麻烦，这就是为什么我们将文件命名为`hello_world.rb`而不是`hello world.rb`。同样，Ruby 的命名约定让大多数代码保持小写，而不是 CamelCase like。当然也有例外，但是我们会在另一篇文章中给出命名规则。

## 工业基准黑(industrial reference black)

Ruby 还有另一个运行代码的好工具，叫做**I**interactive**R**u**B**y Shell(IRB ),你可以像这样从命令行启动它:

```
irb
```

在 IRB 中，你可以输入 Ruby 代码，看看代码执行时会发生什么。IRB 的好处是你拥有 Ruby 的全部功能，所以你可以像普通的 Ruby 程序一样定义模块、类、方法和 lamda 函数。但是要小心，一旦你关闭了一个 IRB 会话，你执行的所有代码都会丢失，这就是为什么它是一个比永久开发环境更好的发现工具。

这里有一个练习可以让你熟悉 IRB。运行以下命令，并报告它们的输出:

```
irb #=> To start irb 

arr = [1, 2, 3, 4] 
arr.first 
arr.include?(7) 
arr.include?(2) 
arr.join("-") 

exit #=> To exit irb
```

## 你好世界

好了，我们兜了一圈，又回到了 Hello World 编程的例子。分解这里发生的事情很重要，因为它不同于。网络:

```
# hello_world.rb
puts "Hello World!" #=> Hello World!
```

首先要注意的是 Ruby 没有定义像。网办[【3】](#footnote_3)。相反，Ruby 从文件的顶部开始，一直读到底部。这个解析是通过一个叫做 Matz 的 Ruby 解释器的工具来完成的——更广为人知的名字是 MRI——它是一个单程解析器。这与不同。NET，它使用多遍解析器。这意味着，如果我们试图调用解释器还没有读取的类或方法，我们会得到一个错误。当我从。这是我经常遇到的问题之一。当这种情况发生时，您将看到一个如下所示的错误:

```
# This is a class error
uninitialized constant Object::Foo (NameError)

# This is a method error
undefined method `bar' for main:Object (NoMethodError)
```

接下来，在 hello world 程序中`puts`是一个用来写控制台的方法。至少最终结果是这样的。在幕后，我们真正做的是在`stdout`上调用`puts`。代码如下所示:

```
$stdout.puts("Hello World")
```

那和我们写的差太多了，我们来分解一下。首先，Ruby 中带有美元符号的变量引用了一个全局变量。在这种情况下，`$stdout`指的是标准输出流。

可能是你在这个世界上从未遇到过的事情。NET world，那么我们来解释一下它是什么。在它的核心，stdout 是正在运行的程序和执行它的上下文之间的连接——在我们的例子中，上下文是一个终端，我们的程序是`hello_world.rb`。*当程序运行时，nix 系统会打开其中的 3 个流: *stdin* 、 *stdout* 和 *stderr* 。在 Ruby 中，可以使用`gets`从 *stdin* 中提取输入，我们已经看到`puts`写入 *stdout* ，如果我们想要写入 *stderr* 中，我们可以使用它的全局引用来访问流:

```
$stderr.puts("Error Message")
```

`hello_world.rb`中的另一个奇怪之处是，我们没有将传递给`puts`方法的参数括起来。这是因为括号在 Ruby 中是可选的。这意味着您可以编写下面的代码，并从两行代码中获得相同的结果:

```
puts "Hello World!"
puts("Hello World!")
```

不管这看起来有多奇怪，你必须相信我，这是 Ruby 的一个奇妙特性。我现在不打算告诉你为什么，我们首先要讨论更多的内容，先把这个放在心里，以后再说。

这就结束了你对 Ruby 的第一次尝试。现在事情可能看起来有点吓人——我们没有经历太多代码。但是我们正在为这个系列(以及 RubySource 上的任何帖子)打下基础。我希望你喜欢它，并希望你能在下一篇文章中回来，在那里我们将讨论[类](https://www.sitepoint.com/net-to-ruby-classes/)、方法、变量和块！

最后，如果有任何你想在这里看到的话题，请在评论中留下你的想法，我会看看我能做些什么。

### 脚注

T2 我不希望我们一开始就不顺利。`.NET != Ruby`。。NET 是框架，而 Ruby 不是，Ruby 是语言。在这一系列的文章中，我们将比较 C#和 Ruby。 <sub>[回](#ref_1)</sub>

对于那些来自 Windows 世界的人来说，你可能已经见过*nix 这个术语，但可能不知道它是什么意思。*nix 代表任何表现类似 Unix 的操作系统。 <sub>[回](#ref_2)</sub>

总管实际上确实存在。因为 Ruby 把所有东西都当作一个对象，所以 Main 实际上是在运行脚本的顶层对象中定义的。如果你想测试这个，把下面的代码写到一个名为`test.rb`的文件中，注意打印出来的结果是多么的“主要”:

```
# test.rb
puts self.to_s
```

<sub>[回](#ref_3)</sub>

## 分享这篇文章