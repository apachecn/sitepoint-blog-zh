# 通过在 Ruby 中实现未来来学习并发

> 原文：<https://www.sitepoint.com/learn-concurrency-by-implementing-futures-in-ruby/>

![Abstract wallpaper with business concept](img/289fcf7436209932d36fb6470cc8d8f2.png)

未来是一个并发抽象，它代表了一个*异步计算*的*结果*。这意味着，当你给未来一些计算处理，这是在一个单独的线程。因此，主线程可以自由地进行其他处理。当你需要未来的结果时，你可以要求它。如果它仍然在处理计算，那么主线程就会被阻塞。否则，返回结果。

在本文中，我们将用 Ruby 实现我们自己的期货库。在这个过程中，您将了解到更多关于 Ruby 提供的一些并发库和一些有趣的 Ruby 技巧！让我们开始吧。

## 期货有什么用？

在我们开始之前，看看期货如何对我们有用会有一点帮助。Futures 是并发 HTTP 请求的完美候选。让我们从一个简单的 Ruby 应用程序开始，它从互联网 Chuck Norris 数据库中随机获取 Chuck Norris 的笑话:

```
require 'open-uri'
require 'json'
require 'benchmark'

class Chucky
  URL = 'http://api.icndb.com/jokes/random'

  def sequential
    open(URL) do |f|
      f.each_line { |line| puts JSON.parse(line)['value']['joke'] }
    end
  end

end 
```

为了运行这个应用程序，将上面的文件保存为 **chucky.rb** 并像这样运行程序:

```
% irb
> require "./chucky"
 => true
> chucky = Chucky.new
 => #<Chucky:0x007fe02c046d98>
> chucky.sequential
Contrary to popular belief, the Titanic didn't hit an iceberg. The ship was off course and ran into Chuck Norris while he was doing the backstroke across the Atlantic. 
```

每次你执行`chucky.sequential`，程序都会随机获取一个查克·诺里斯的笑话。(警告:这是高度上瘾的！)如果我们想要获取超过，比如说，*十个*笑话，会发生什么？一个简单的解决方案看起来像这样:

```
10.times { chucky.sequential } 
```

不幸的是，这是对 CPU 资源和您的时间的极大浪费。当发出每个请求时，主线程被阻塞，必须等待请求完成后才能继续下一个请求。我们将通过实现我们自己的未来抽象来解决这个问题(正如所有优秀的开发人员所做的那样)。

## 实现你自己的未来:测试第一！

我们将使用测试驱动开发(TDD)来实现我们的期货 gem。我们开始吧！我们首先使用`bundle gem <gem name>`创建一个新的 Ruby gem:

```
% bundle gem futurama
Creating gem 'futurama'...
MIT License enabled in config
      create  futurama/Gemfile
      create  futurama/.gitignore
      create  futurama/lib/futurama.rb
      create  futurama/lib/futurama/version.rb
      create  futurama/futurama.gemspec
      create  futurama/Rakefile
      create  futurama/README.md
      create  futurama/bin/console
      create  futurama/bin/setup
      create  futurama/LICENSE.txt
      create  futurama/.travis.yml
      create  futurama/.rspec
      create  futurama/spec/spec_helper.rb
      create  futurama/spec/futurama_spec.rb
Initializing git repo in futurama 
```

接下来，运行`bin/setup`来引入依赖项:

```
% bin/setup
Resolving dependencies...
Using rake 10.4.2
Using bundler 1.10.6
Using diff-lcs 1.2.5
Using futurama 0.1.0 from source at .
Using rspec-support 3.3.0
Using rspec-core 3.3.2
Using rspec-expectations 3.3.1
Using rspec-mocks 3.3.2
Using rspec 3.3.0
Bundle complete! 4 Gemfile dependencies, 9 gems now installed.
Use `bundle show [gemname]` to see where a bundled gem is installed. 
```

> > Note: if you don't see the installation of RSpec gems, add `spec.add_development_dependency "rspec"` to **Futurama.gemspec** file.

### 第一步

我们的第一个测试非常简单:

```
require 'spec_helper'
require 'timeout'

module Futurama
  describe 'Future' do

    it 'returns a value' do
      future = Future.new { 1 + 2 }

      expect(future).to eq(3)
    end

  end

end 
```

这个测试描述了创建一个`Future`的界面。`Future`对象接受一个计算块。此外，当访问对象时，返回值是该块内的计算结果(第二个条件并不像听起来那么简单)。

接下来，在 **lib/futurama/future.rb** 中创建一个名为 **future.rb** 的文件。接下来，确保你需要 **lib/futurama.rb** 中的 **future.rb** ，就像这样:

```
require "futurama/version"
require "futurama/future"

module Futurama
end 
```

为了通过测试，`Future`对象必须:

*   接受封锁
*   调用时返回块的值

满足第一个条件很容易:

```
module Futurama
  class Future

    def initialize(&block)
      @block = block
    end

  end
end 
```

另一个条件稍微复杂一些:

```
require 'delegate'

module Futurama
  class Future < Delegator

    # initialize was here

    def __getobj__
      @block.call
    end

  end
end 
```

当我们子类化内置的`Delegator`类时，必须实现`__getobj__`方法，否则 Ruby 会抱怨。但是话说回来，这就是使用`Delegator`类的全部意义！这个方法的返回值基本上就是访问对象时返回的内容。换句话说，当一个对象被访问时，我们可以*覆盖*它*的含义*，这正是我们所需要的！我们从`__getobj__`方法中调用这个块，当我们运行测试时:

```
% rspec
Future
  returns a value

Finished in 0.00077 seconds (files took 0.06902 seconds to load)
1 example, 0 failures 
```

大获成功！

### 在后台执行计算

回到我们的测试。Future 接受一个计算并在一个线程中运行它。为了验证这一点，我们可以创建一个未来，让它在返回值之前休眠一秒钟。在主线程上，我们还模拟了一些耗时一秒的计算:

```
module Futurama
  describe 'Future' do

    it 'executes the computation in the background' do
      future = Future.new { sleep(1); 42 }

      sleep(1) # do some computation

      Timeout::timeout(0.9) do
        expect(future).to eq(42)
      end
    end

  end
end 
```

我们断言的很有趣。由于未来在后台运行，就像主线程休眠一秒钟一样，理论上，在从未来返回结果之前，它应该花费*少于*1 秒钟。我们利用内置的`Timeout`库(我们在 **spec/futurama/future.rb** 的顶部做了一个`require 'timeout'`)来确保未来在我们设定的时间界限内执行。

```
module Futurama
  class Future < Delegator

    def initialize(&block)
      @block  = block
      @thread = Thread.new { run_future }
    end

    def run_future
      @block.call
    end

    def __getobj__
      @thread.value
    end

  end
end 
```

我们将块的调用封装在`run_future`方法中，将`run_future`封装在一个线程中。这个线程在未来被创建时运行。一旦线程完成，使用`Thread#value`访问返回值，如`__getobj__`的修改实现所示。

运行测试，一切都应该是绿色的。

### 处理异常

接下来，我们将注意力转向处理异常。由于期货是异步的，如果期货突然失败，我们不希望出现任何令人不快的意外。相反，最好是将来存储这个异常，只在我们戳它的时候引发它。下面是表达意图的测试:

```
module Futurama
  describe 'Future' do

    it 'captures exceptions and re-raises them' do
      error_msg = 'Good news, everyone!'

      future = Future.new { raise error_msg }

      expect { future.inspect }.to raise_error RuntimeError, error_msg
    end

  end
end 
```

令人高兴的是，你不需要做任何事情就能通过测试！不过有一个警告。如果`Thread.abort_on_exception`设置为`true`，任何线程中未处理的异常都将导致解释器退出。不好玩。让我们在测试中暴露这个问题:

```
module Futurama
  describe 'Future' do

    it 'captures exceptions and re-raises them' do
      Thread.abort_on_exception = true

      error_msg = 'Good news, everyone!'

      future = Future.new { raise error_msg }

      expect { future.inspect }.to raise_error RuntimeError, error_msg
    end

  end
end 
```

有了这条新线，测试就不再通过了。怎么办？结果是，你需要做更多的工作。(抱歉！)这就是我们实现的实质。

### 排队！

从上一节中，我们现在必须找到一种方法来存储发生的异常，而不是依赖解释器。此外，当未来对象被调用时，我们需要*重新引发*异常。

在进入实施之前，我们再来思考一下未来。从某种意义上来说，它是一个*数据结构*，用于存储*、解析值*或异常值*。另一个非常重要的考虑是确保线程安全。我们该如何表达呢？？*

虽然 Ruby 没有很多线程安全的集合类， [`Queue`](http://ruby-doc.org/core-2.2.0/Queue.html) 是个例外。根据课程描述:

> 该类提供了一种同步线程间通信的方法。

对于我们的实现，回想一下，我们要么存储已解析的值，要么存储异常。因此，我们需要一个最大尺寸为*一个*的`Queue`。 [`SizedQueue`](http://ruby-doc.org/stdlib-2.0.0/libdoc/thread/rdoc/SizedQueue.html) 来救援了！

```
module Futurama
  class Future < Delegator

    def initialize(&block)
      @block  = block
      @queue  = SizedQueue.new(1)
      @thread = Thread.new { run_future }
    end

    def run_future
      @queue.push(value: @block.call)
    rescue Exception => ex
      @queue.push(exception: ex)
    end

    def __getobj__
      # this will change in the next section
    end

  end
end 
```

增加了一个新的实例 variabl，`@queue`，它是一个容量为 1 的`SizedQueue`。然后我们修改`run_future`,要么推送程序块的结果，要么在出现异常时推送异常。因为我们使用了一个`SizedQueue`，我们保证队列中不会有两个元素被推入。

### 从队列中获取结果或异常

接下来，我们需要解决从`SizedQueue`获得结果或异常的问题。另一件要记住的事情是，一旦期货决定了一个价值，它就完成了。下一次你从未来得到一个值，结果将会是直接的。换句话说，一旦解决了这个值/异常，未来就会记住它。

```
module Futurama
  class Future < Delegator

    def initialize(&block)
      @block  = block
      @queue  = SizedQueue.new(1)
      @thread = Thread.new { run_future }
      @mutex  = Mutex.new
    end

    def __getobj__
      resolved_future_or_raise[:value]
    end

    def resolved_future_or_raise
      @resolved_future || @mutex.synchronize do
        @resolved_future ||= @queue.pop
      end

      Kernel.raise @resolved_future[:exception] if @resolved_future[:exception]
      @resolved_future
    end

  end
end 
```

让我们集中讨论一下`resolved_future_or_raise`方法:

```
@resolved_future || @mutex.synchronize do
  @resolved_future ||= @queue.pop
end 
```

在这里，首先检查未来是否已经解决。这只是说未来已经完成计算一个值或异常的一种奇特方式。否则，从`@queue`检索该值/异常。我们需要确保弹出队列并将结果分配给`@resolved_future`的操作是由*自动执行的*。换句话说，我们必须保证不发生交错。因此，我们将操作包装在一个`@mutex`中:

```
Kernel.raise @resolved_future[:exception] if @resolved_future[:exception]
@resolved_future 
```

现在，检查`@resolved_future`是否有异常。如果有，那就养吧。注意，我们使用的是`Kernel#raise`。如果不指定`Kernel`，将使用`Thread#raise`。

最后，如果没有异常，将返回解析后的值。再次运行测试，一切都应该是甜蜜的绿色！

### 节省击键次数(或者:污染内核名称空间)

不得不打字一点也不好玩。如果我们可以用`future{ }`来代替。在 Ruby 中，这是微不足道的。让我们先写一个测试:

```
module Futurama
  describe 'Future' do
    it 'pollutes the Kernel namespace' do

      msg    = 'Do the Bender!'
      future = future { msg }

      expect(future).to eq(msg)
    end

  end
end 
```

运行测试，它将失败，并显示:

```
NoMethodError: undefined method `future' for #<RSpec::ExampleGroups::Future:0x007fd274988390>
./spec/futurama/future_spec.rb:67:in `block (2 levels) in <module:Futurama>'
-e:1:in `load'
-e:1:in `<main>' 
```

我们可以通过在 **lib/futurama** 中创建一个名为 **kernel.rb** 的文件来轻松解决这个问题:

```
require 'futurama'

module Kernel
  def future(&block)
    Futurama::Future.new(&block)
  end
end 
```

为这个文件添加一个`require`语句到 **lib/futurama.rb** ，然后再次运行测试。我们又变回绿色了！

### 从未来获取值或例外

目前，可以从`__getobj__`访问该值或异常。显然，我们不希望客户机代码知道`__getobj__`。我们可以将*别名*改为类似`value`的东西:

```
module Futurama
  describe 'Future' do

    it 'allows access to its value' do
      val    = 10
      future = Future.new { val }

      expect(future.value).to eq(val)
    end

  end
end 
```

不出所料，测试将会失败:

```
NoMethodError: undefined method `value' for 10:Futurama::Future
./spec/futurama/future_spec.rb:76:in `block (2 levels) in <module:Futurama>'
-e:1:in `load'
-e:1:in `<main>' 
```

通过测试的代码只有一行:

```
 require 'thread'

module Futurama
  class Future < Delegator

  def __getobj__
    resolved_future_or_raise[:value]
  end

  # place this *below* __getobj__
  alias_method :value, :__getobj__
  end
end 
```

现在一切都应该是绿色的！

## 让我们有一些并发的查克·诺里斯笑话！

我们再来看看 **chucky.rb** 。注意，我已经把这个文件放到了`futurama`的 **sample/chucky.rb** 中。

```
require '../lib/futurama'
require 'open-uri'
require 'json'
require 'benchmark'

class Chucky
  URL = 'http://api.icndb.com/jokes/random'

  def sequential
    open(URL) do |f|
      f.each_line { |line| puts JSON.parse(line)['value']['joke'] }
    end
  end

  def concurrent
    future { sequential }
  end

end 
```

执行并发 Chuck Norris 笑话查找只需要一个微小的改变:

```
def concurrent
  future { sequential }
end 
```

就是这样！这可能会让人觉得有点反气候，但我们已经差不多完成了。为了进行测试，我们可以对并发版本和顺序版本进行基准测试:

```
 chucky = Chucky.new

Benchmark.bm do |x|
  x.report('concurrent') { 10.times { chucky.concurrent } }
  x.report('sequential') { 10.times { chucky.sequential } }
end 
```

或者，如果你太懒，你可以在我的机器上看到结果:

[https://www.youtube.com/embed/gEfrjpMQOlw](https://www.youtube.com/embed/gEfrjpMQOlw)

## 限制、鸣谢以及从哪里了解更多信息

创建无限数量的未来将导致无限数量的线程的创建。这显然是一件坏事。像 Java 这样的语言有管理线程池的线程池执行器。用 Ruby 实现一个并不难。

这篇文章的灵感来自于研究[未来观测仪](https://github.com/codegram/futuroscope)宝石的源代码。事实上，它的实现有一个线程池，传递给未来的构造函数。

如果你有兴趣学习更多关于 Ruby 内置的并发抽象，那么只需看看 [Concurrent Ruby](https://github.com/ruby-concurrency/concurrent-ruby) gem。它由过多的并发工具组成，如代理、线程池、管理器，是的，还有未来！提供的文档可读性也很高。

## 感谢阅读！

我希望您在学习期货的过程中获得了乐趣，也希望更多地了解 Ruby。虽然 Ruby 在默认情况下没有一个成熟的并发库，但是像 Concurrency Ruby 这样的 gem 提供了一个很好的途径来学习你最喜欢的语言中的这些并发工具。

## 分享这篇文章