# 使用跟踪点挖掘

> 原文：<https://www.sitepoint.com/digging-tracepoint/>

![digging with spade](img/fedbaa24516ea3337f8c3ead78f5d7ec.png)

本文描述了一个罕见的用例，所以我觉得有必要给出一些背景来解释我用 TracePoint“挖掘”的动机。如果你不感兴趣，可以跳到下一部分。

> **注**。所有例子都不是生产代码！这些示例只是为了本文的目的。未经彻底审查，请不要将其复制粘贴到您的项目中。

我为什么开始玩 TracePoint？

在日常的 Ruby 编程中，在一个方法的开始有一些代码来验证它的参数是很常见的。尤其是如果你编写一个 API 方法，它将被外部代码调用，因为你不能相信任何来自外部的东西。

这导致我编写如下代码:

```
def my_method(arg)
  arg = arg.to_sym if arg.is_a?(String)
  raise unless arg.is_a?(Symbol)
end
```

我对自己说“停下来。”我正在用每种方法写类似于上面例子的东西。这段代码应该统一提取到我的日常使用库中。Google 没有给我任何可用的宝石，于是我开始了 [EnsureIt](https://github.com/cybernetlab/ensure_it) 项目。

提供这种功能的显而易见的方法是用一组像`ensure_symbol`或`ensure_string`这样的方法来修补(或细化，从`2.1.0`开始)一般的 ruby 对象，并在那里进行适当的验证。这个方法给了我们最快的(在某些情况下，根本不需要验证)代码。

当我开始编写 bang 方法(`ensure_symbol!`等等)时，我在错误报告方面遇到了麻烦。错误消息应该是信息性的，并且至少包含无效参数的名称。但是我需要有清晰可用的 API，所以将参数名作为另一个参数传递(类似于`arg.ensure_symbol!(:arg)`)是行不通的。我的决定是从`ensure_symbol!`方法中获取调用者上下文，检查它的参数和局部变量。

这就是本文的出发点。

### 目标

我的第一个目标是从被调用的方法中获取调用者上下文。这不是很复杂，但是我将在下面描述我的实现。真正有趣的是，也是我的第二个目标，用从调用者上下文构造的消息在被调用的方法中引发一个错误。

### 从被调用的方法获取调用方上下文

让我们从一些代码开始。考虑到我上面的背景表格，monkeypath `Object`与`ensure_symbol!`:

```
class Object
  def ensure_symbol!
    raise 'argument should be a Symbol or a String'
  end
end

class Symbol
  def ensure_symbol!
    self
  end
end

class String
  def ensure_symbol!
    to_sym
  end
end

def test_method(test_arg)
  test_arg = test_arg.ensure_symbol!
  puts "passed argument: #{test_arg.inspect}"
end

test_method(:symbol)
test_method('string')
test_method(0)
```

很简单。对于符号，返回自我。对于字符串，将值转换为符号。对任何其他对象引发错误。

以下是输出:

```
$ ruby example_01.rb
passed argument: :symbol
passed argument: :string
example_01.rb:3:in `ensure_symbol!': argument should be a Symbol or a String ( RuntimeError)
    from example_01.rb:20:in `test_method'
    from example_01.rb:26:in `<main>'
```

问题是错误消息没有提供信息。我希望消息中有参数名(`test_arg`)和方法名(`test_method`)。类似`argument 'test_arg' in method 'test_method' should be a Symbol or a String`的东西。

为此，我需要`ensure_symbol!`中的调用者上下文。网络上也有类似的实现。它们中的许多，像 [binding_of_caller](https://github.com/banister/binding_of_caller) 都是依赖于 ruby 平台的。我更喜欢在任何可能的地方使用 ruby 原生工具。这里是`TracePoint`发挥作用的地方。

从版本`2.0.0`开始，Ruby 引入了一种跟踪程序执行的新方法(旧方法是[set _ trace _ func](http://www.ruby-doc.org/core-2.1.1/Kernel.html#method-i-set_trace_func))—[trace point](http://www.ruby-doc.org/core-2.1.1/TracePoint.html)类。它提供了许多 ruby 内部事件，比如从一个方法返回或者引发一个异常，我们可以用自己的代码块捕获这些事件。请阅读官方文档以获得更好的理解。

作为第一次尝试，我绑定到`:return`事件并写出有用的消息来查看程序执行期间发生了什么:

```
def raise_error(method_name, message: nil)
  message = 'local variable have wrong type' unless message.is_a?(String)
  counter = 0
  TracePoint.trace(:return) do |tp|
    if (counter += 1) > 2
      tp.disable
      puts 'Caller context reached.'
      puts "Caller method name is: #{tp.method_id}"
      method = eval("method(:#{tp.method_id})", tp.binding)
      puts "Caller arguments: #{method.parameters}"
    end
  end
end # <= first call of trace point block

class Object
  def ensure_symbol!
    raise_error(
      :ensure_symbol!,
      message: 'argument should be a Symbol or a String'
    )
  end # <= second call of trace point block
end

def test_method(test_arg)
  test_arg = test_arg.ensure_symbol!
  puts "passed argument: #{test_arg.inspect}"
end # <= last call of trace point block

test_method(0)
```

这里我们创建一个名为`raise_error`的助手方法来完成这项工作。我们在它内部创建一个 TracePoint 实例，它将被每个方法在`return`上调用，直到它被禁用。对`raise_error`的初始调用只创建了这个跟踪点，所以我们继续增加我们的计数器。我们还没有到达我们想要的环境。从`ensure_symbol!`返回时调用下一个程序块。也跳过。最后，从我们期望的调用者上下文中调用最后一个块，我们可以获取方法名及其参数。让我们看看输出:

```
$ ruby example_02.rb
passed argument: #<TracePoint:enabled>
Caller context reached.
Caller method name is: test_method
Caller arguments: [[:req, :test_arg]]
```

没错。我们有了梦寐以求的呼叫者上下文。但是首先，我们仍然没有提出错误。同样，我们的`ensure_symbol!`块在之后执行了调用者方法(`puts "passed argument...`)中的剩余代码**。**

让我们修改`raise_error`以在从`ensure_symbol!`返回后立即执行我们的跟踪点块:

```
def raise_error(method_name, message: nil)
  message = 'local variable have wrong type' unless message.is_a?(String)
  counter = 0
  TracePoint.trace(:return) do |first_tp|
    if (counter += 1) > 1
      first_tp.disable
      TracePoint.trace(:line) do |second_tp|
        second_tp.disable
        puts 'Caller context reached.'
        puts "Caller method name is: #{second_tp.method_id}"
        method = eval("method(:#{second_tp.method_id})", second_tp.binding)
        puts "Caller arguments: #{method.parameters}"
      end
    end
  end
end # <= first call of first_tp block

class Object
  def ensure_symbol!
    raise_error(
      :ensure_symbol!,
      message: 'argument should be a Symbol or a String'
    )
  end # <= second and last call of first_tp block
end

def test_method(test_arg)
  test_arg = test_arg.ensure_symbol!
  puts "passed argument: #{test_arg.inspect}" # <= call of second_tp block
end

test_method(0)
```

现在，我们只跟踪前两个返回(而不是前一个例子中的三个)。在第二个调用中，我们处于`ensure_symbol!`方法的末尾。在这里，我们使用另一个跟踪事件类型–`:line`,所以我们的块将为每一行代码调用。由于`raise_error`只会在内部使用，我们可以放心地假设对`raise_error`的调用总是放在方法的最后一行。在这种情况下，我们可以跳过一行到达调用者上下文。让我们试着执行代码:

```
$ ruby example_03.rb
Caller context reached.
Caller method name is: test_method
Caller arguments: [[:req, :test_arg]]
passed argument: #<TracePoint:disabled>
```

瞧啊。我们已经达到了第一个目标！调用方上下文是在调用我们的`ensure_symbol!`方法之后执行任何代码之前获得的。然而，第二个问题仍然存在-我们没有提出一个错误。

### 引发“智能”错误

我们如何提出我们的信息性错误信息？我们的第一个想法是从跟踪点块产生错误:

```
def raise_error(method_name, message: nil)
  message = 'local variable have wrong type' unless message.is_a?(String)
  counter = 0
  TracePoint.trace(:return) do |first_tp|
    if (counter += 1) > 1
      first_tp.disable
      TracePoint.trace(:line) do |second_tp|
        second_tp.disable
        raise "raised from trace point in method #{second_tp.method_id}"
      end
    end
  end
end
```

执行此操作会给我们带来:

```
$ ruby example_04.rb
example_04.rb:9:in `block (2 levels) in raise_error': raised from trace point in method test_method (RuntimeError)
    from example_04.rb:38:in `test_method'
    from example_04.rb:41:in `<main>'
```

这看起来很不错，但是如果对`ensure_symbol!`的调用放在`begin-rescue`块中，它就不会工作。让我们修改一下我们的`test_method`:

```
def test_method(test_arg)
  begin
    test_arg = test_arg.ensure_symbol!
  rescue RuntimeError => err
    puts 'error occured while checking the argument'
    raise err
  end
  puts "passed argument: #{test_arg.inspect}"
end
```

这导致:

```
$ ruby example_05.rb
example_05.rb:9:in `block (2 levels) in raise_error': raised from trace point in method test_method (RuntimeError)
    from example_05.rb:43:in `test_method'
    from example_05.rb:46:in `<main>'
```

同样的结果。但是在检查参数'消息时发生的错误“*”在哪里呢？发生了什么事？让我们回到上一节的最后一个例子。*

我们的第二个跟踪点跳过了`ensure_symbol!`方法中的一行，然后在`ensure_symbol!`之后立即给我们调用者上下文。此时，还没有引发异常。这解释了为什么我们上面的代码不工作——从方法返回时没有异常，ruby 只是跳过了`rescue`块。现在，跟踪点块中的异常被引发并输出。

这只是一个结论——我们应该在从`ensure_symbol!`返回之前提出异常。好的，但是现在我们有一个不同的问题。我们想要生成的消息基于调用者上下文，但是我们需要在获得所述上下文之前引发异常。我想起了伟大的《回到未来》电影。不可能？

其实是有可能的！如果异常是在`raise_error`内部引发的，并且一旦我们有了上下文就被修改:

```
def raise_error(method_name, message: nil)
  message = 'local variable have wrong type' unless message.is_a?(String)
  counter = 0
  error = nil
  TracePoint.trace(:return, :raise) do |first_tp|
    if first_tp.event == :raise
      error = first_tp.raised_exception
      puts "error captured with message: #{error.message}"
    else
      if (counter += 1) > 1
        first_tp.disable
        TracePoint.trace(:line) do |second_tp|
          second_tp.disable
          unless error.nil?
            puts "now we have #{error.inspect} in #{second_tp.method_id} context "
          end
        end
      end
    end
  end
  raise %q{raised from '#raise_error' method}
end
```

这给了我们:

```
$ ruby example_06.rb
error captured with message: raised from '#raise_error' method
now we have #<RuntimeError: raised from '#raise_error' method> in test_method context
error occured while checking the argument
example_06.rb:21:in `raise_error': raised from '#raise_error' method (RuntimeError)
    from example_06.rb:26:in `ensure_symbol!'
    from example_06.rb:47:in `test_method'
    from example_06.rb:55:in `<main>'
```

太好了！我们非常接近成功。错误以正确的方式被提出，并被一个`begin-rescue`块捕获，但是我们仍然可以访问调用者上下文。

现在我们只需要修改错误消息。可惜，事情没这么简单。`Exception`类没有消息设置器。我们可以创建自己的`Exception`类，但是我想让用户选择他们想要的任何`Exception`类型。

等等，什么是例外？它只是一个红宝石物体。多亏了 Ruby 的单例方法，我们可以在单个对象中重新定义任何方法，而不需要改变任何东西！

下面是最终的`raise_error`版本:

```
def raise_error(method_name, message: nil)
  message = 'local variable have wrong type' unless message.is_a?(String)
  counter = 0
  error = nil
  TracePoint.trace(:return, :raise) do |first_tp|
    if first_tp.event == :raise
      error = first_tp.raised_exception
    else
      if (counter += 1) > 1
        first_tp.disable
        TracePoint.trace(:line) do |second_tp|
          second_tp.disable
          unless error.nil?
            new_message = "#{message} in '#{second_tp.method_id}' method"
            error.define_singleton_method :message do
              new_message
            end
          end
        end
      end
    end
  end
  raise %q{raised from '#raise_error' method}
end
```

结果是:

```
$ ruby example_07.rb
error occured while checking the argument
example_07.rb:29:in `raise_error': argument should be a Symbol or a String in 'test_method' method (RuntimeError)
    from example_07.rb:34:in `ensure_symbol!'
    from example_07.rb:55:in `test_method'
    from example_07.rb:63:in `<main>'
```

红宝石魔术在行动！

当然，创建正确的错误消息还需要一些技术工作。我们可以使用正则表达式来获取和分析调用。对于方法参数、局部变量或文字，可能会调用`ensure_symbol`方法。解释所有这些超出了本文的范围。你可以在 [github](https://github.com/cybernetlab/ensure_it/blob/master/lib/ensure_it/errors.rb) 上找到 EnsureIt 项目的工作源代码了解详情。

### 结论

有些人可能会觉得这个话题很奇怪。我能理解这个观点。然而，像这样的练习对于 Ruby 编程来说是很好的体验。此外，考虑到项目的条件，调用诸如`ensure_symbol!`这样的方法是非常频繁的。因此，我的目标是在这些方法中执行最少的代码，仅用于验证和转换，仅在发生错误时才执行任何其他工作。

当我的试卷变成绿色时，我透过窗户看到了美丽的日出。:)用 ruby 挖掘真的很迷人！

祝你好运！

*(感谢亚历克斯·斯卡兹卡和我姐姐审阅本文)*

## 分享这篇文章