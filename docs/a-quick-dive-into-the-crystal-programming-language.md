# 快速了解 Crystal 编程语言

> 原文：<https://www.sitepoint.com/a-quick-dive-into-the-crystal-programming-language/>

![Screenshot 2016-07-20 07.57.18](img/5b76e6e8fe175335eb5a5e5b70682dce.png)

你可能听说过最近提到的[水晶](https://crystal-lang.org)编程语言。这种语言看起来*非常*类似于 Ruby。事实上，许多 Ruby 程序也是有效的 Crystal 程序。然而，必须强调的是，这仅仅是语言语法的副作用，并不是项目的目标。

关于 Crystal 最有趣的事情之一是，它是一种静态类型检查语言，然而它不需要程序员像 Java 一样到处散布类型。Crystal 编译成高效的代码，这意味着 Crystal 程序比 Ruby 程序快得多。

在本文中，我们将快速了解 Crystal。这绝不是对 Crystal 中所有特性的全面介绍。相反，我们将开发一个并发的查克·诺里斯笑话提取器，带有一个 Rubyist 的镜头。这涉及到 HTTP GET 请求和一些 JSON 解析。

我们将看到这将带我们走多远，以及研究 Crystal 提供的使事情更方便的设施。

## 得到水晶

我将温和地把您引向[安装指南](https://crystal-lang.org/docs/installation/index.html),因为它无疑在涵盖各种 Linux 发行版、Mac OSX 和其他 Unix 系统的安装方面做得更好。对于微软视窗系统的用户:对不起！Crystal 需要基于 Linux/Unix 的系统。

## 顺序版本

我们先从顺序版本开始。首先，我们需要一个 HTTP 客户端来获取笑话。我们的数据将来自互联网上的查克·诺里斯数据库。为了检索一个笑话，您只需调用 URL，例如:

```
http://api.icndb.com/jokes/123 
```

这将返回:

```
{
  "type":"success",
  "value":{
    "id":123,
    "joke":"Some people wear Superman pajamas. Superman wears Chuck Norris pajamas.",
    "categories":[
    ]
  }
} 
```

让我们创建一个新类，并将其命名为`chucky.cr`。此时，我们将实现`Chucky#get_joke(id)`:

```
require "http/client"
require "json"

class Chucky
  def get_joke(id)
    response = HTTP::Client.get "http://api.icndb.com/jokes/#{id}"
    JSON.parse(response.body)["value"]["joke"]
  end
end

c = Chucky.new
puts c.get_joke(20) 
```

Crystal 内置了 HTTP 客户端和 JSON 解析器。让我们试着在终端中运行它，这是通过将文件名传递给`crystal`命令来完成的:

```
$ crystal chucky.cr
The Chuck Norris military unit was not used in the game Civilization 4, because a single Chuck Norris could defeat the entire combined nations of the world in one turn. 
```

大获成功！到目前为止，一切顺利。假设我们想要检索一些笑话。看起来很简单:

```
class Chucky
  ...other methods...

  def get_jokes(ids : Array(Int32))
    ids.map do |id|
      get_joke(id)
    end
  end

end 
```

您将注意到的与 Ruby 不同的第一件事是，`ids`的类型被明确定义为`Array(Int32)`。这读作“一个`Int32` s 的`Array`”。说清楚一点，我们可以不考虑这个。然而，因为我很确定`ids`总是`Int32` s，所以我想说得非常清楚，避免错误，比如:

```
c = Chucky.new
puts c.get_jokes(["20"]) 
```

事实上，当您尝试运行这个程序时，您会得到一个编译时错误:

```
Error in ./chucky.cr:50: no overload matches 'Chucky#get_jokes' with type Array(String) Overloads are:
 - Chucky#get_jokes(ids : Array(Int32))
puts c.get_jokes(["20"])
   ^~~~~~~~~ 
```

敏锐的读者会指出，在`Chucky#get_joke`中指定参数的类型更有意义，她绝对是对的。事实上，您还可以指定方法的返回类型:

```
class Chucky

  def get_joke(id : Int32) : String
    # ...
  end

  def get_jokes(ids : Array(Int32)) : Array(String)
    # ...
  end

end

c = Chucky.new
puts c.get_jokes([20]) # <-- Change this back to an Array(Int32) 
```

让我们再试一次:

```
% crystal chucky.cr

Error in ./chucky.cr:53: instantiating 'Chucky#get_jokes(Array(Int32))'

puts c.get_jokes([20])
       ^~~~~~~~~

in ./chucky.cr:20: instantiating 'get_joke(Int32)'

      get_joke(id)
      ^~~~~~~~

in ./chucky.cr:24: type must be String, not JSON::Any

  def get_joke(id : Int32) : String
      ^~~~~~~~ 
```

哎呦！编译器捕捉到了一些东西！所以看起来好像`Chucky#get_joke(id)`没有返回一个`String`，而是返回了一个`JSON::Any`。这很好，因为编译器捕捉到了我们的一个错误假设。

现在我们只剩下两个选择。要么我们切换我们的`Chucky#get_joke[s]`返回`JSON::Any`，要么我们继续使用`String`。我投票支持`String`，因为任何客户端代码都不应该在乎这些笑话是关于`JSON::Any`的。我们来修改一下`Chucky#get_joke(id)`。为了更好地衡量，我们还处理了有一些解析错误的情况，并简单地返回一个空的`String`:

```
class Chucky

  # ...

  def get_joke(id : Int32) : String
    response = HTTP::Client.get "http://api.icndb.com/jokes/#{id}"
    JSON.parse(response.body)["value"]["joke"].to_s rescue ""
  end

end 
```

现在一切都应该运行良好。不过，我们有个小问题。试着想象当我们这样做时会发生什么:

```
c = Chucky.new
puts c.get_jokes[20, 30, 40] 
```

在我们当前的实现中，笑话将被顺序获取。这意味着`Chucky#get_jokes(ids)`完成的时间是*获取所有三个笑话的总时间*。

我们可以做得更好！

## 并发版本

既然我们已经成功了，那就让它变得更快吧。Crystal 附带了一个并发原语，叫做*纤程*，它基本上是线程的一个轻量级版本。其他并发原语是*通道*。如果你做过任何 Golang，这基本上是相同的想法。通道是纤程进行通信的一种方式，没有共享内存、锁和互斥等令人头疼的问题。

我们将同时使用光纤和信道来获取笑话。

这里是主要的想法。我们将在主光纤中创建一个通道。对`Chucky#get_joke(id)`的每个调用都将在一个纤程中完成。一旦笑话被获取，我们将把结果发送给频道。

```
class Chucky
  # ...

  def get_jokes(ids : Array(Int32)) : Array(String)

    # 1\. Create channel in the main fiber.
    chan = Channel(String).new

    # 2\. Execute get_joke in a fiber. Send the result to the channel.
    ids.each do |x|
      spawn do
        chan.send(get_joke(x))
      end
    end

    # 3\. Receive the results.
    (1..ids.size).map do |x|
      chan.receive
    end
  end
end 
```

首先，我们创建一个渠道。注意，我们需要指定通道的类型。

接下来，我们在纤程中执行`get_joke(id)`。这是在`spawn`模块中完成的。一旦我们从`get_joke(id)`得到结果，我们就把结果发送到之前创建的通道。

向通道发送值只是问题的一部分。为了从通道中获取一个值，我们需要调用`Channel#receive`。每次调用 receive 时，我们都会返回一个值。如果还没有值，它将阻塞。既然知道了`ids`的大小，我们只需要调用`Channel#receive`次`ids.size`次。

尝试再次运行该程序。这一次，总时间大约是最长请求的时间。

## 水晶耶的

水晶看起来真的令人印象深刻。我喜欢它是静态类型的，但是类型系统不会碍事，特别是因为你可以不指定类型。

作为一门编译语言(至少在 LLVM 中)，我看到的一些性能基准测试令人印象深刻并不奇怪。

我喜欢将纤程和通道作为并发原语。我希望有更多的人来。还有一些其他的语言特性，比如宏和结构，让它从 Ruby 中脱颖而出。

## 水晶 Meh 的

没有 REPL。对于一个 Ruby 爱好者来说，如果没有忠实的 IRB(或 Pry ),这几乎是不可想象的。

虽然有并发性，但还没有并行性。这意味着一个 Crystal 程序只使用一个内核。然而，它仍然是一种年轻的语言，所以这不是最终的故事。

最后，Crystal 社区面临着创建一个繁荣的生态系统的艰巨任务，比如 Rubygems。

## 感谢阅读！

我希望你喜欢这个快速潜入水晶。如果你有兴趣了解更多，我鼓励你通读一下[文档](https://crystal-lang.org/docs/)。

快乐结晶！

## 分享这篇文章