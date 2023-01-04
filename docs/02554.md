# 通过在 Ruby 中制作一个倒计时闩锁来学习并发性

> 原文：<https://www.sitepoint.com/learn-concurrency-by-making-a-countdown-latch-in-ruby/>

![](img/ee68b3de391edfa8528919aadb2bed78.png)

倒计时闩锁是一种并发抽象，它允许一个或多个线程等待，直到所有其他线程都完成了它们正在做的事情。因此，倒计时锁存器通常被称为*线程同步原语*。

## 倒计时锁有什么用？

比方说，你有一堆线程正在获取，说，查克诺里斯笑话。您创建了十个线程，并希望在继续下一步之前确保它们都已完成。

在这种情况下，您可以创建一个计数器为 10 的倒计时闩锁。然后，在代码的下一步之前，您可以等待线程完成。在此之前，您的代码不能继续，直到所有线程都完成。

当每个线程获取一个笑话时，它将递减计数器。最终，当所有十个线程都完成获取笑话时，倒计时锁存器中的计数器将最终达到零。这时代码才被允许继续运行。

## 实现你自己的倒计时闩锁:首先测试！

在上一篇文章中，我们讨论了如何在 Ruby 中[实现期货。在本文中，我们将测试一个倒计时锁的实现。我们开始吧！](https://www.sitepoint.com/learn-concurrency-by-implementing-futures-in-ruby/)

### 安装

让我们启动一个新的 Ruby 项目。我最喜欢的方法是打造一颗红宝石:

```
% bundle gem countdown_latch -t
Creating gem 'countdown_latch'...
MIT License enabled in config
      create  countdown_latch/Gemfile
      create  countdown_latch/.gitignore
      create  countdown_latch/lib/countdown_latch.rb
      create  countdown_latch/lib/countdown_latch/version.rb
      create  countdown_latch/countdown_latch.gemspec
      create  countdown_latch/Rakefile
      create  countdown_latch/README.md
      create  countdown_latch/bin/console
      create  countdown_latch/bin/setup
      create  countdown_latch/LICENSE.txt
      create  countdown_latch/.travis.yml
      create  countdown_latch/.rspec
      create  countdown_latch/spec/spec_helper.rb
      create  countdown_latch/spec/countdown_latch_spec.rb
Initializing git repo in /Users/benjamintan/workspace/countdown_latch 
```

> 你注意到追加的`-t`单位了吗？此标志将 RSpec 添加为开发依赖项。

接下来，进入项目目录:

```
% cd countdown_latch 
```

运行`bin/setup`来安装依赖项:

```
% bin/setup
Resolving dependencies...
Using rake 10.4.2
Using bundler 1.10.6
Using countdown_latch 0.1.0 from source at .
Using diff-lcs 1.2.5
Using rspec-support 3.3.0
Using rspec-core 3.3.2
Using rspec-expectations 3.3.1
Using rspec-mocks 3.3.2
Using rspec 3.3.0
Bundle complete! 4 Gemfile dependencies, 9 gems now installed.
Use `bundle show [gemname]` to see where a bundled gem is installed. 
```

### 创建倒计时闩锁

现在，开始有趣的事情吧！因为我们正在做这个*测试-首先*。测试将帮助我们朝着实现前进，为了通过测试，我们将充实实现。

作为健全性检查，我们可以快速确保一切都如预期的那样连接起来:

```
% rspec                                                                        
CountdownLatch
  has a version number
  does something useful (FAILED - 1)

Failures:

  1) CountdownLatch does something useful
     Failure/Error: expect(false).to eq(true)

       expected: true
            got: false

       (compared using ==)
     # ./spec/countdown_latch_spec.rb:9:in `block (2 levels) in <top (required)>'

Finished in 0.01957 seconds (files took 0.07757 seconds to load)
2 examples, 1 failure

Failed examples:

rspec ./spec/countdown_latch_spec.rb:8 # CountdownLatch does something useful 
```

太棒了。RSpec 工作正常！现在，打开位于**规格/倒计时*锁存器*规格 rb** 的测试文件。我们将编写第一个测试。

#### 倒计时闩锁需要一个非负整数作为参数

你应该删除**规格/倒计时*闩锁*规格 rb** 中的所有内容，并用这个骨架替换它:

```
module CountdownLatch

  describe CountdownLatch do

  end
end 
```

第一个测试是确保传递给倒计时锁存器的构造函数的参数是一个非负整数:

```
module CountdownLatch
  describe CountdownLatch do
    it "requires a non-negative integer as an argument" do
      latch = CountdownLatch.new(3)
      expect(latch.count).to eq(3)
    end
  end
end 
```

为了让它通过，我们需要一个接受非负整数作为参数的构造函数。通过这个测试很简单:

```
module CountdownLatch
  class CountdownLatch

    def initialize(count)
      if count.is_a?(Fixnum) && count > 0
        @count = count
      end
    end

  end
end 
```

此时，测试将通过。我们还要确保 0 被接受:

```
module CountdownLatch
  describe CountdownLatch do

    it "zero is a valid argument" do
      latch = CountdownLatch.new(0)
      expect(latch.count).to eq(0)
    end
  end
end 
```

哎呦！我们的测试发现了一些东西:

```
Failures:

  1) CountdownLatch::CountdownLatch zero is a valid argument
     Failure/Error: expect(latch.count).to eq(0)

       expected: 0
            got: nil

       (compared using ==) 
```

结果是，当比较`count`和 0 时，我们有一个误差:

```
module CountdownLatch
  class CountdownLatch

    def initialize(count)
      if count.is_a?(Fixnum) && count >= 0
        @count = count
      end
    end

  end
end 
```

例如，倒计时锁存器不能用负数或`Float`初始化，因为这两种情况没有意义。让我们添加测试:

```
module CountdownLatch
  describe CountdownLatch do

    it "throws ArgumentError for negative numbers" do
      expect { CountdownLatch.new(-1) }.to raise_error(ArgumentError)
    end

    it "throws ArgumentError for non-integers" do
      expect { CountdownLatch.new(1.0) }.to raise_error(ArgumentError)
    end
  end
end 
```

现在测试将会失败:

```
Failures:

  1) CountdownLatch::CountdownLatch throws ArgumentError for negative numbers
     Failure/Error: expect { CountdownLatch.new(-1) }.to raise_error(ArgumentError)
       expected ArgumentError but nothing was raised
     # ./spec/countdown_latch_spec.rb:17:in `block (2 levels) in <module:CountdownLatch>'

  2) CountdownLatch::CountdownLatch throws ArgumentError for non-integers
     Failure/Error: expect { CountdownLatch.new(1.0) }.to raise_error(ArgumentError)
       expected ArgumentError but nothing was raised
     # ./spec/countdown_latch_spec.rb:21:in `block (2 levels) in <module:CountdownLatch>' 
```

谢天谢地，这也很容易解决。我们只需要让初始化器，为论点中的错误，抛出一个`ArgumentError`。在**lib/count down*latch/count down*latch . Rb**:

```
module CountdownLatch
  class CountdownLatch

    def initialize(count)
      if count.is_a?(Fixnum) && count >= 0
        @count = count
        @mutex = Mutex.new
        @condition = ConditionVariable.new
      else
        raise ArgumentError
      end
    end

  end
end 
```

现在测试应该都通过了。

#### 倒计时

倒计时锁存器必须知道如何倒计时它自己的内部计数器。

```
module CountdownLatch
  describe CountdownLatch do

    it "#count decreases when #count_down is called" do
      latch = CountdownLatch.new(3)
      latch.count_down
      expect(latch.count).to eq(2)
    end

  end
end 
```

现在我们需要访问`@count`字段。这很简单:

```
module CountdownLatch
  class CountdownLatch

    def initialize(count)
      # ...
    end

    def count
      @count
    end

  end
end 
```

`count_down`方法看起来非常简单:

```
module CountdownLatch
  class CountdownLatch

    def count_down
      @count -= 1
    end

  end
end 
```

抵制添加更多行为和“更聪明”逻辑的诱惑，直到测试迫使你做些什么。在这种情况下，测试现在应该都是绿色的。

然而，我们的测试并不完整。倒计时锁应该只倒数到*零*，不能更少。让我们为此添加一个测试:

```
module CountdownLatch
  describe CountdownLatch do

    it "#count never reaches below zero" do
      latch = CountdownLatch.new(0)
      latch.count_down
      expect(latch.count).to eq(0)
    end

  end
end 
```

上述测试失败:

```
 1) CountdownLatch::CountdownLatch #count never reaches below zero
     Failure/Error: expect(latch.count).to eq(0)

       expected: 0
            got: -1

       (compared using ==) 
```

我们只需要检查`@count`是否为零:

```
module CountdownLatch
  class CountdownLatch

    def count_down
      unless @count.zero?
        @count -= 1
      end
    end

  end
end 
```

一切都应该是绿色的！

#### 等待线程

到目前为止，我们只是有一个美化的倒计时物体。它甚至不是线程安全的！这是因为`count_down`可以被多个线程调用，而且由于`@count`无论如何都不会同步，所以会出现竞争情况。

这是推动我们在倒计时闩锁中实现并发功能的第一个测试:

```
module CountdownLatch
  describe CountdownLatch do

    it "#await will wait for a thread to finish its work" do
      latch = CountdownLatch.new(1)

      Thread.new do
        latch.count_down
      end

      latch.await
      expect(latch.count).to eq(0)
    end

  end
end 
```

该测试创建一个初始化为`1`的倒计时锁存器。然后我们创建一个线程来减少闩锁。线程之外，我们称之为`latch.await`。

这基本上是说程序将等待线程完成它的工作。同样，一旦线程完成了工作，它将调用`latch.count_down`。因此，我们期望锁存器的`count`为零。

为了正确地看到测试失败*，我们需要一个空的`await`实现:*

```
module CountdownLatch
  class CountdownLatch

    def await
    end

  end
end 
```

运行测试时，以下操作将失败:

```
Failures:

  1) CountdownLatch::CountdownLatch #await will wait for a thread to finish its work
     Failure/Error: expect(latch.count).to eq(0)

       expected: 0
            got: 1

       (compared using ==) 
```

由于`await`什么都不做，程序不会等待线程告诉倒计时锁可以继续了。相反，程序只是直接运行。为了通过这个测试，你需要了解*条件变量*。

#### 条件变量

条件变量本质上是一个同步原语(回想一下，倒计时锁存器也是一个同步原语)，它允许线程等待，直到某个条件发生。在倒计时锁存器的情况下，该条件是当`@count`达到零时。

当这种情况发生时，必须在条件变量上调用`broadcast`方法来告诉所有等待的线程，它们可以停止等待并继续进行。

首先，我们将条件变量添加到实现中，并告诉它当条件满足时，*将*广播给所有等待的线程。不要忘记也包括`require "thread"`:

```
require "thread"  # <----

module CountdownLatch
  class CountdownLatch

    def initialize(count)
      if count.is_a?(Fixnum) && count >= 0
        @count = count
        @condition = ConditionVariable.new # <----
      else
        raise ArgumentError
      end
    end

    def count_down
      unless @count.zero?
        @count -= 1
      else
        @condition.broadcast # <----
      end
    end

  end
end 
```

现在，来处理等式的另一面:让线程暂停执行，直到满足条件。这是第一次尝试。`ConditionVariable`有一个方法叫做`wait`:

```
module CountdownLatch
  class CountdownLatch

    def await
      @condition.wait
    end

  end
end 
```

然而，这不起作用，因为`ConditionVariable#wait`需要一个接受`Mutex`对象的参数。互斥本质上是一个锁，保护一段代码不被多个线程进入。回想一下，我提到过的`@count`到目前为止还不是线程安全的。我们会解决这个问题的。

在这种情况下，我们需要为条件变量提供一个互斥体，这样在任一时刻只有一个线程可以读/写条件变量。

首先，我们将为倒计时闩锁创建一个互斥体:

```
module CountdownLatch
  class CountdownLatch

    def initialize(count)
      if count.is_a?(Fixnum) && count >= 0
        @count = count
        @condition = ConditionVariable.new
        @mutex = Mutex.new <----
      else
        raise ArgumentError
      end
    end

  end
end 
```

接下来，我们将使用`@mutex.synchronize`来标定一个*临界段*。临界区是只有一个线程可以进入的代码区域。首先，让我们处理一下`count_down`方法:

```
module CountdownLatch
  class CountdownLatch

    def count_down
      @mutex.synchronize {
        unless @count.zero?
          @count -= 1
        else
          @condition.broadcast
        end
      }
    end

  end
end 
```

我们终于有一个互斥体要传递给`@condition.wait`:

```
module CountdownLatch
  class CountdownLatch

    def await
      @condition.wait(@mutex)
    end

  end
end 
```

然而，就像`count_down`方法一样，你需要一个临界区。这是:

```
module CountdownLatch
  class CountdownLatch

    def await
      @mutex.synchronize {
        @condition.wait(@mutex)
      }
    end

  end
end 
```

当我们添加它时，让我们也用互斥体将`count`方法中的`@count`包装起来:

```
module CountdownLatch
  class CountdownLatch

    def count
      @mutex.synchronize { 
        @count
      }
    end

  end
end 
```

再次运行测试，我们应该是绿色的！呜！

### 试运行

在 **lib** 下创建一个名为 **sample** 的文件夹。在那个文件夹中，我创建了一个名为**的文件，内容如下:**

```
require 'open-uri'
require 'json'

module CountdownLatch

  class Chucky
    URL = 'http://api.icndb.com/jokes/random'

    def get_fact
      open(URL) do |f|
        f.each_line { |line| puts JSON.parse(line)['value']['joke'] }
      end
    end

    def get_facts(num)
      latch = CountdownLatch.new(num) # <---- Latch

      facts = []
      (1..num).each do |x|
        Thread.new do
          facts << get_fact
          latch.count_down
        end
      end

      latch.await

      facts
    end

  end
end 
```

这个类从第三方 API 获取笑话，解析 JSON 响应，并将笑话作为`String`返回。我们可以通过测试来驾驶`Chucky`并获得一些事实:

```
require 'spec_helper'
require 'sample/chucky' # <---- Remember to add this!

module CountdownLatch
  describe CountdownLatch do

    it "sample run", :speed => 'slow' do
      chucky = Chucky.new
      facts = chucky.get_facts(5)

      expect(facts.size).to eq(5)
    end

  end
end 
```

正如所料，测试将通过。试着取下插销，看看会发生什么。

## 限制、鸣谢以及从哪里了解更多信息

这个实现不处理*虚假唤醒*，这是一个有趣的现象，即使条件变量还没有发出信号/广播，线程也可以唤醒。

如果你想要一个更可靠、更精细的倒计时闩锁实现，看看神奇的 [Ruby Concurrency GitHub 库](https://github.com/ruby-concurrency/concurrent-ruby)。

## 感谢阅读！

我希望你已经学到了一些关于条件变量、互斥体的新知识，当然，还有创建你自己的倒计时闩锁！更重要的是，我希望你在跟随的过程中有很多乐趣。你可以在这里获取完整的源代码[。感谢阅读！](https://github.com/benjamintanweihao/countdown_latch)

## 分享这篇文章*