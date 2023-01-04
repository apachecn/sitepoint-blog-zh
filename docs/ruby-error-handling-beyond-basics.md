# Ruby 错误处理，超越基础

> 原文：<https://www.sitepoint.com/ruby-error-handling-beyond-basics/>

通过我们的教程[在 SitePoint 上用 RSpec](https://www.sitepoint.com/premium/screencasts/setting-up-automated-testing-with-rspec) 设置自动化测试来学习更多关于 ruby 的知识。

![Alert - Finely crafted line icons](img/5ca56e143e64ae2a55686ad547b0680d.png)

想象你正在骑自行车。现在，想象一下那辆自行车的设计者建造了它，使它只能在没有颠簸的道路上平稳行驶，遇到颠簸会导致整辆自行车断裂！你不想那样，对吧？然而，这就是成千上万的软件开发人员每天如何设计他们的软件。他们把错误处理作为事后的想法，只在不可避免的时候才处理它。

事实是，这不是他们的错。关于这个主题的大部分材料都是非常基础的，涵盖了简单的事情，比如提出一个错误，挽救它，不同的错误类型等等。这篇文章将试图比这更深入。我假设您熟悉错误处理的基础知识(使用 raise、begin/rescue、什么是 StandardError、错误继承)。这是阅读这篇文章的唯一前提。我们开始吧。

## 在引发/处理异常之前，我们做了什么？

在异常被发明之前，程序中的某些东西失败的主要通信方法是通过错误返回代码。随着时间的推移，人们开始寻找方法来清楚地区分他们的程序做了什么和如果它没有做它应该做的事情会发生什么(返回代码对于这个目的来说远非理想)。因此，语言结构的发明就像:

*   `raise`
*   `rescue`
*   (许多其他语言使用不同的措辞，如`try/catch`或`throw`，但其背后的思想是相同的。)

有[种反对观点](http://www.lighterra.com/papers/exceptionsharmful/)首先反对使用异常和错误处理。其中一些观点很有道理，我们将在本文后面讨论它们。现在，让我们让您熟悉一些在 Ruby 中处理错误的方法，这些方法可以帮助您更好地管理它们。

## 崩溃前的清理

通常我们不知道我们的程序什么时候会崩溃。如果我们需要在程序结束前做一些清理操作(由于错误)会怎么样？这就是 [at_exit](http://ruby-doc.org/core-2.2.2/Kernel.html#method-i-at_exit) 前来救援的原因:

```
puts 'Hello'
at_exit do
  puts 'Exiting'
end
raise 'error'
```

这个程序将打印“你好”和“退出”。当程序退出时,`at_exit`中的代码将会执行(无论是正常退出还是异常退出)。如果想让它只在出现异常时运行，可以使用 Ruby 中的全局变量`$!`。`$!`的默认值为`nil`，设置为出错时引用异常对象:

```
puts 'Hello'
at_exit do
  if $! # If the program exits due to an exception
    puts 'Exiting'
  end
end
raise 'error' # Try running this code with this line and then remove it. See when "Exiting" will print.
```

人们使用`at_exit`做各种各样的事情，比如制作自己的错误崩溃日志(将`$!`中的消息打印到一个文件中)，通知另一个应用程序正在运行的应用程序不再运行等等。

您还可以将 Proc 对象传递给`at_exit`而不是 block，就像其他任何接受 block 的 Ruby 方法一样:

```
puts 'Hello'
to_execute = Proc.new { puts 'Exiting' }
at_exit(&to_execute) # & converts the Proc object to a block
```

## 砰的一声退出

让我们假设您已经在程序中实现了`at_exit`。但是，有些情况下，您不希望其中的块执行。在那种情况下你该怎么办？您应该删除/注释掉`at_exit`中的所有内容并再次运行您的程序吗？

有一种更好的方法可以随时添加和删除代码。在 Ruby 中，有一个叫做`exit!`的方法，它是`exit`的“危险版本”。主要的“危险”是，当你调用它时，有两件事情会发生，而在使用普通版本时通常不会发生:

*   程序将退出，不执行`at_exit`内的代码块。
*   退出代码被设置为 1，而不是常规(非 bang)版本的 0。

## 代码中任何地方的简单错误记录

我将分享我最喜欢的实现自己的异常记录器的方法(如果你对更多的方法感兴趣，我强烈推荐 Avdi Grimm 的[exceptual Ruby](http://devblog.avdi.org/exceptional-ruby/)，这是关于这个主题的最全面的资源。)一旦你掌握了主要思想，这种方法可以用于各种目的。

我们可以在 Ruby 中使用一个线程局部变量来存储我们的错误消息:

```
(Thread.current[:errors] ||= []) << 'Any error message goes here'
```

因为变量在你的代码中的任何地方都是可用的(我通常反对全局范围的变量，像这样的例外很少。可以把这个变量想象成一个非常简单的全局错误处理程序。)从技术上讲，线程局部变量有一个线程作用域，如果你没有编写多线程代码，这个作用域应该是没有意义的。如果你这样做了，要知道变量的名字说明了一切；它对于一个线程来说是本地的(如果你没有使用多线程，那么 thread =你的程序)。

您可以在代码中的任何地方，通过任何方法使用上面的表达式。

为了避免重复代码，我将把`Thread.current`表达式包装到一个单独的方法中(确保该方法在代码中的任何地方都可用):

```
def report_error(error_message)
  (Thread.current[:errors] ||= []) << "#{error_message}"
end
```

假设我们有`some_random_method`崩溃，我们想记录详细信息:

```
def some_random_method
  begin
    raise 'an error!'
  rescue => error
    report_error("#{error.class} and #{error.message}")
  end
end

some_random_method
p Thread.current[:errors] #=> ["RuntimeError and an error!"]
```

目前，错误被存储在线程局部变量中。我们希望将它们存储在一个文件中。让我们编写`log_errors`方法来实现这一点:

```
def log_errors
  File.open('errors.txt', 'a') do |file|
    (Thread.current[:errors] ||= []).each do |error|
      file.puts error
    end
  end
end
```

然而，这还不够。我们希望这个方法在程序退出时执行，不管它是否有错。正如我们前面看到的，这很容易做到:只需将方法调用放入`at_exit`:

```
at_exit { log_errors }
```

## 小心你的营救代码

我从惨痛的教训中学到的一件事是，你对放入`rescue`中的代码越小心越好。如果里面的代码失败并产生异常怎么办？当然，你可以嵌套`rescue` s，但是这违背了整个语句的目的！

想象一下，如果你的程序负责建造一栋大楼。然后发生了火灾。作为回应，一名消防员前来救援:

```
begin
  # building the building
rescue Fire
  Fireman.dispatch
end
```

我们想确保消防员完美地完成工作。我们不想让他因为设备故障而着火！另外，我们不希望消防员的车在去大楼的路上抛锚。我们希望一切都完美无缺，这包括零故障率。这应该是您的代码在`rescue`语句中的最终目标。正如我所提到的，你可以深入一些，找人来营救营救者，这可能会导致问题。

## 不救`Exception`，不救广

在阅读了 2 到 3 篇关于 Ruby 异常处理基础的文章后，你一定会看到关于永远不要拯救`Exception`的建议。`Exception`是异常类库的根，是“所有异常之母”。我想更进一步地提出这个建议，并建议你永远不要泛泛地救助。这包括忽略像`StandardError`这样的大类(`StandardError`本身有超过 [300 个子类从它往下](https://gist.github.com/danielfone/5654600))。所以基本上，通过拯救`StandardError`，你正在处理 300 个潜在的失败案例。现在告诉我，如果你有一个救援区块处理 300 个可能的故障案例，那么`rescue`区块自身发生故障的概率是多少？想象一下，给一个消防员同样的设备去处理单层房屋和 100 层的建筑！这不是个好主意。

那么，有什么解决办法呢？不要走得太远，试着挽救特定的错误(没有超过 100 个子异常)。这就引出了我的下一个观点…

## 例外的类型(按发生的概率)

许多人鼓吹“例外应该是例外”之类的东西，却没有意识到“例外”这个词经常被误解。我提出了一个更好的方法来对一个特殊的异常进行分类，通过它实际发生的概率:

*   可能发生的事情。这就是很多人说一个例外应该是“例外”的意思。一个在你无法预料的情况下会发生的例外。
*   **可能发生的**。您可以相当准确地预测，在某些情况下，这个异常最终会发生。
*   必须(被要求)发生例外情况会发生，你可以轻而易举地创造出这种情况。

让我们先处理第三种(最后一种)类型。如果您的异常是*要求发生的*或者发生的概率非常高，那么重新考虑一下您是否需要首先提出它。如果你加注了，你会用这个例外来控制流程吗？这是个坏主意。例如，如果用户输入了不正确的输入，而不是再次提示(使用某种类型的循环)，则引发异常。如果您正在挽救一个经常发生的异常，并且该异常是在某个库下引发的，那么看看您是否可以将整个逻辑封装到某种验证方法中，在该方法中您将获得一个返回值，而不是不断地崩溃。

如果异常是*可能发生*但不太可能，你真的需要处理它吗？你必须预见和区分每一种可能的情况吗？这是为什么这么多人救`StandardError`或`Exception`的一个主要原因。他们不希望他们的项目在任何情况下都失败。根据我的经验(以及我与之交谈过的许多其他人的经验)，这造成的问题比解决的问题还多。我在这里的建议是简单地足够频繁地运行你的程序，看看它在哪里失败了。查看引发的异常类型，当它们发生两次以上时，处理它。

*可能发生的*。这是营救发生时的最佳时机。你知道异常的类型，通过足够频繁地运行你的程序，你知道它发生的频率/时间。您知道它发生的频率和时间，因此您可以安全地处理它。

我的建议是不要拯救那些你不知道是否会发生的异常。如果它们发生过一次或两次，并且你已经运行你的程序几个月了，检查它的潜在原因。问“为什么”,直到你最终得到一个答案，这个答案可以让你修复你的程序/系统逻辑，防止错误再次发生。

不过，当你*提出*例外时，我不会给出同样的建议。在你这样做之前，看看几个备选方案。

## 提出例外的替代方法

写一个简单的加注很容易。然而，后果远非理想。你的程序将会停止。你确定要这么做吗？有几个方法可以让你的程序正常运行。

### 定制策略

您可以指示 Ruby 使用一个定制的策略来挽救一个异常(默认策略是 raise)。假设您有这样的代码(如果您不知道`begin`子句在哪里，每个方法定义都是一个隐式的`begin/end`语句，这意味着`def`本身就是`begin`):

```
def some_method(some_argument, error_strategy = method(:raise))
  raise "#{some_argument}"
rescue
  error_strategy.call
end

some_method(1) # '1' (RuntimeError)
```

这段代码将引发一个将消息作为参数的`RuntimeError`，即`1`。现在，保持方法定义不变，尝试以下操作:

```
error_handler = Proc.new { puts 'I rescued it!' }
some_method(1, error_handler)
```

现在，程序将结束，没有错误和“我救了它！”将打印到控制台。我们只是通过向对象传递一个过程来改变我们处理方法中错误的默认“策略”。如果`method(:raise)`部分你不熟悉，[看这篇文章](http://makandracards.com/makandra/1659-use-a-ruby-method-like-a-block-or-lambda)。

### 使用一个允许程序继续运行的值

假设你有一个返回数组的方法。在调用该方法时，会不时弹出一个异常。你想继续运行，而不是一直让你的程序崩溃。为什么不返回一个空数组？如果你的方法的结果被用来迭代一些东西，一个空的数组将会使后面的代码不迭代任何东西。示例代码将使这一点更容易理解:

```
def method_that_should_return_an_array
  this_method_call_will_raise_an_error('some argument')
rescue
  [] # your method will return this
end
```

### 升起`nil`

我认为返回`nil`而不是引发异常是 Ruby 社区中经常被滥用的策略。如果你得到了“不能在 NilClass 上调用 X 方法”的错误，并且沮丧地发现你的代码的哪一部分返回了一个意外的`nil`导致了所有的混乱，请举手。为了解决这个问题，许多人会这样做:

```
some_method_that_might_return_nil || raise 'nil returned'
```

如果同一行中的某个表达式返回`nil`，这个代码片段是引发异常的一种快捷方式。

当您返回`nil`时，最好确保稍后的代码会检查并处理它(否则您会得到一堆意外的“无法在 NilClass 上调用 X 方法”错误):

```
my_value = some_method_that_might_raise_nil

if my_value.nil?
  # do something
else
  # do something else
end
```

把这篇文章中的建议作为一套原则，而不是做什么的规则。尝试一下，看看它是否适合你的特殊情况。我在这里的目的是扩展您对异常处理的思考方式，并为您提供新的想法和工具，您现在就可以在您的程序中实现它们。我希望我的任务完成了。

通过我们的教程[在 SitePoint 上用 RSpec](https://www.sitepoint.com/premium/screencasts/setting-up-automated-testing-with-rspec) 设置自动化测试来学习更多关于 ruby 的知识。

## 分享这篇文章