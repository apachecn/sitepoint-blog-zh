# 函数式编程:纯函数

> 原文：<https://www.sitepoint.com/functional-programming-pure-functions/>

这是关于 Ruby 函数式编程的两部分系列的第二部分。在[之前，我们探索了不可变值](https://www.sitepoint.com/functional-programming-ruby-value-objects/)，现在我们来看看函数式编程的另一面:纯函数和可组合函数。

## 纯函数

纯函数是返回值仅由其输入值决定的函数，没有可观察到的副作用。数学中的函数是这样工作的:`Math.cos(x)`对于`x`的相同值，总是返回相同的结果。计算它不改变`x`。它不写日志文件，不做网络请求，不要求用户输入，也不改变程序状态。这是一个咖啡研磨机:豆子进去，粉末出来，故事结束。

当一个函数执行任何其他“动作”时，除了计算它的返回值，这个函数就是不纯的。由此可见，调用不纯函数的函数也是不纯的。不纯洁是会传染的。

一个纯函数的给定调用总是可以被它的结果替换。`Math.cos(Math::PI)`和`-1`没有区别；我们总是可以用第二个代替第一个。这个属性叫做*参照透明度*。

## 保持州本地

一个纯函数只能访问你传递给它的东西，所以很容易看到它的依赖关系。我们并不总是这样写函数。当一个函数访问其他程序状态时，比如一个实例或全局变量，它就不再是纯的了。

以全局变量为例。这些通常被认为是一个坏主意，而且理由很充分。当程序的各个部分开始通过全局变量进行交互时，它们之间的交流就变得不可见了。从表面上看，有些依赖性很难发现。它们会引起维护噩梦。程序员需要记住事物是如何相互关联的，并把每件事都安排得恰到好处。一个地方的小变化会导致看似无关的代码失败。

## 纯方法

在 Ruby 中，我们通常不谈论函数。相反，我们有带方法的对象，但差别很小。当你在一个对象上调用一个方法时，就好像这个对象是作为参数`self`传递给函数的。这是函数可以用来计算结果的另一个值。

采取`String`的`upcase`方法

```
str = "ukulele"
str.upcase # => "UKULELE"
str        # => "ukulele"
```

它将一个字符串转换成大写，但原始字符串保持不变。`upcase`没有做其他任何事情，比如写入日志文件或读取鼠标输入。`upcase`是纯粹的。同样不能说的是`upcase!`

```
str = "ukulele"
str.upcase! # => "UKULELE"
str         # => "UKULELE"
```

Ruby 添加了 bang 来表示这个函数*是破坏性的*。在你调用它之后，原来的字符串不见了，取而代之的是新版本。`upcase!`不纯洁。

## 利益

纯函数与不可变值齐头并进。它们一起产生了声明性程序，描述了输入与输出之间的关系，而没有详细说明从 A 到 b 的步骤。这可以简化系统，并且，面对并发性，引用透明性是天赐之物。

### 重复的结果

当函数是纯的，并且值易于检查和创建时，那么每个函数调用都可以被孤立地再现。这对测试和调试的影响很难夸大。

要编写测试，您需要声明将作为参数的值，将它们传递给函数，并验证输出。没有要设置的上下文，没有当前帐户、请求或用户。没有副作用可以模仿或模仿。实例化一组有代表性的输入，并验证输出。没有比这更简单的测试了。

### 并行化

纯函数总是可以并行化的。将输入值分布在多个线程上，并收集结果。下面是一个简单的平行贴图方法:

```
module Enumerable
  def pmap(cores = 4, &block)
    [].tap do |result|
      each_slice((count.to_f/cores).ceil).map do |slice|
        Thread.new(result) do |result|
          slice.each do |item|
            result << block.call(item)
          end
        end
      end.map(&:join)
    end
  end
end
```

现在让我们模拟一些昂贵的计算:

```
def report_time
  t = Time.now
  yield
  puts Time.now-t
end

report_time {
  100.times.map {|x| sleep(0.1); x*x }
}
# 10.014289725

report_time {
  100.times.pmap {|x| sleep(0.1); x*x }
}
# 2.504685127
```

带`#map`的版本用了 10 秒完成，平行版只用了 2.5 秒。但是如果我们知道被调用的函数是纯函数，我们只能用`#map`替换`#pmap`。

### 记忆化

因为纯函数在引用上是透明的，所以对于给定的输入，我们只需要计算一次它们的输出。缓存和重用计算结果被称为*记忆化*，并且只能用纯函数安全地完成。

### 怠惰

同一主题的变奏。我们只需要计算一次纯函数的结果，但是如果我们可以完全避免计算呢？调用一个纯函数意味着你指定了一个依赖关系:这个输出值依赖于这些输入值。但是如果你从来不使用输出值呢？因为函数不会产生副作用，所以调用与否并不重要。因此，一个智能系统可以*懒惰*并优化调用。

有些语言，比如 Haskell，完全建立在*惰性评估*之上。仅计算实现副作用所需的值，其余的被忽略。Ruby 的求值策略被称为*严格求值*，每个表达式在它的结果可以用于另一个表达式之前都被完全求值。这是不幸的，但通过一些想象，我们可以建立自己的选择懒惰。

```
class Lazy < BasicObject
  def initialize(&blk)
    @blk = blk
  end

  def method_missing(name, *args, &blk)
    _resolve.send(name, *args, &blk)
  end

  def respond_to?(name)
    _resolve.respond_to?(name)
  end

  def _resolve
    @resolved ||= @blk.call
  end
end

def lazy(&blk)
  Lazy.new(&blk)
end
```

现在我们可以将潜在的高成本计算包装在`lazy {}`中，

```
def mul(a, b, c)
  a * b
end

a = lazy { sleep(0.5) ; 5 }
b = lazy { sleep(0.5) ; 7 }
c = lazy { sleep(3)   ; 9 }

mul(a, b, c)
# => 35
```

对`sleep`的调用模拟了一些 CPU 密集型任务。大约一秒钟后弹出最终结果。尽管计算`c`需要 3 秒钟，但是因为这个值从来没有被使用过，所以我们不需要花费这个成本。

## 重构到功能

不过有一个问题。我们的程序所做的许多事情(与数据库交互、服务网络请求、写入日志文件)本质上都有副作用。我们的程序是处理输入并随时间产生输出的进程，它们不是数学函数。然而，有办法两全其美。

一种卓有成效的方法是将应用程序的纯粹的、功能性的、基于价值的核心与外部的、命令式的外壳分离开来。以一个需要解析命令行参数的命令行应用程序为例:

```
def parse_cli_options
  opts = OptionParser.new do |opts|
    opts.banner = 'cli_tool [options] infile outfile'
    opts.on('--version', 'Print version') do |name|
      $stderr.puts VERSION
      exit 0
    end.on('--help', 'Display help') do
      $stderr.puts opts
      exit 0
    end
  end

  opts.parse!(ARGV)
  if ARGV.length != 2
    $stderr.puts "Wrong number of arguments"
    $stderr.puts opts
    exit 1
  end

  opts
end
```

这是你能得到的最远离纯函数的距离。它执行以下所有操作。

*   直接写入`$stderr`
*   呼叫`Kernel.exit`
*   依靠全局`ARGV`
*   改变`ARGV`

您将如何着手为这样一个庞然大物编写测试呢？这是不可能的。要使它成为一个纯粹的功能，我们需要问自己什么需要进去，什么应该出来。作为输入，这个函数显然需要访问命令行参数。作为输出，它需要告诉我们:

*   解析成功了吗？
*   如果没有，错误信息是什么？
*   流程应该使用什么退出代码？

    ```
    def parse_cli_options(argv)
      opts = OptionParser.new do |opts|
        opts.banner = 'cli_tool [options] infile outfile'
        opts.on('--version', 'Print version') do |name|
          return { message: VERSION }
        end.on('--help', 'Display help') do
          return { message: opts.to_s }
        end
      end

      filenames = opts.parse(argv)
      if filename.length != 2
        return {
          message: ["Wrong number of arguments!", opts].join("\n"),
          exit_code: 1
        }
      end
      { filename: filenames }
    end
    ```

现在我们有了一个非常容易测试的纯函数，我们可以把它包装成一个“命令外壳”。

```
def run
  result = parse_cli_options(ARGV)
  perform(*result[:filenames])  if result.key?(:filenames)
  $stderr.puts result[:message] if result.key?(:message)
  Kernel.exit(result.fetch(:exit_code, 0))
end
```

保持核心的严格功能性是必要的，因为一个不纯的函数会污染任何调用它的函数。请注意我们是如何将一些副作用(如退出流程)转化为表示该副作用的中间值的。你可以用这种方式*评估*任何事情，甚至是错误条件或数据库操作，从而获得函数式编程的好处。

函数式编程是一个很大的课题，并不是所有的 ruby 爱好者都能理解。看完这两篇文章，你应该有了让自己的代码更有功能性的良好基础。尝试一下，看看旅程会把你带向何方。

## 分享这篇文章