# Ruby 2.2 中的符号 GC

> 原文：<https://www.sitepoint.com/symbol-gc-ruby-2-2/>

> 这个帖子[这里有日文翻译](http://fiveteesixone.lackland.io/2015/01/21/symbol-gc-ruby-2-2/)！

![Fotolia_62448068_Subscription_Monthly_M](img/2916035f69d5e8980a42392277ea6eb9.png)

什么是符号 GC，为什么要关注它？Ruby 2.2 刚刚发布，除了增量 GC 之外，另一个重要特性是符号 GC。如果你到过红宝石景观，你一定听说过“符号 DoS”这个术语。当系统创建了太多的符号以至于耗尽了内存时，就会发生符号拒绝服务攻击。这是因为在 Ruby 2.2 之前，符号是永久存在的。例如在 Ruby 2.1 中:

```
# Ruby 2.1

before = Symbol.all_symbols.size
100_000.times do |i|
  "sym#{i}".to_sym
end
GC.start
after = Symbol.all_symbols.size
puts after - before
# => 100001
```

这里我们创建了 100，000 个符号，它们仍然存在，即使我们已经运行了 GC 并且没有变量引用这些对象。如果您编写了一些接受用户参数并对其调用`to_sym`的代码，这很容易成为一个问题:

```
def show
  step = params[:step].to_sym
end
```

在这种情况下，有人可能向`example.com/step=<random></random>`发出许多请求，由于你的应用程序从不清除符号，你的程序最终会耗尽内存并崩溃。这听起来像是一个虚构的例子，但它类似于我在我邪恶的 gem 中实际提交的代码[(别担心，现在已经修复了)。这也不是一个孤立的案例:](https://github.com/schneems/wicked/blob/master/CHANGELOG.md#040)

*   [https://www . ruby-lang . org/en/news/2013/02/22/JSON-dos-CVE-2013-0269/](https://www.ruby-lang.org/en/news/2013/02/22/json-dos-cve-2013-0269/)
*   [https://groups.google.com/forum/#!topic/ruby-security-ann/o 0 dsdk 2 wrq 0](https://groups.google.com/forum/#!topic/ruby-security-ann/o0Dsdk2WrQ0)
*   [http://ronin-ruby.github.io/blog/2013/01/09/rails-pocs.html](http://ronin-ruby.github.io/blog/2013/01/09/rails-pocs.html)

这个清单还在继续，但是你明白了。从用户输入创建符号是危险的；除非符号没有被垃圾收集，这是在 Ruby 2.2 之前发生的。

## Ruby 2.2 中的符号 GC

从 Ruby 2.2 开始，符号现在被垃圾收集。

```
# Ruby 2.2
before = Symbol.all_symbols.size
100_000.times do |i|
  "sym#{i}".to_sym
end
GC.start
after = Symbol.all_symbols.size
puts after - before
# => 1
```

因为我们创建的符号没有被任何其他对象或变量引用，所以可以安全地收集它们。这有助于防止我们意外地创建这样一个场景:一个程序创建并保留了太多的对象，以至于崩溃。然而，Ruby 并不垃圾收集所有的符号。

瓦特？

# #非所有符号

在 Ruby 2.2 之前，我们不能收集符号，因为它们是由 Ruby 解释器内部使用的。基本上，每个符号都有一个唯一的对象 ID。例如`:foo.object_id`在程序执行期间总是需要相同的值。这是由于`rb_intern`的工作方式。

在 C-Ruby 中，当你创建一个方法时，它会将一个惟一的 ID 存储到一个方法表中。

[![](img/8ec1a4ab220bc69e7bf3844040f83705.png)](http://www.slideshare.net/authorNari/symbol-gc)

来自 Nari 关于符号 GC 的演讲的幻灯片

稍后，当您调用该方法时，Ruby 将查找方法名称的符号，然后获取该符号的 ID。符号的 ID 用来指向 C 中函数的静态内存，然后调用 C 中的函数，这就是 Ruby 执行方法的方式。

如果我们垃圾收集了一个符号，而这个符号被用来引用一个方法，那么这个方法就不再是可调用的了。那就糟了。

为了解决这个问题，Narihiro Nakamura 在 C 世界引入了“不朽符号”的概念，在 Ruby 世界引入了“凡人符号”的概念。

基本上，所有的符号都是在 Ruby 运行时动态创建的(通过`to_sym`等)。)可以被垃圾收集，因为它们不在 Ruby 解释器的后台使用。但是，由于创建新方法而创建的符号或静态位于代码内部的符号将不会被垃圾回收。例如`:foo`和`def foo; end`都不会被垃圾收集，但是`"foo".to_sym`有资格被垃圾收集。

这种方法存在一些问题，如果您不小心创建了基于用户输入的方法，仍然有可能出现 DoS。

```
define_method(params[:step].to_sym) do
  # ...
end
```

因为`define_method`在后台调用`rb_intern`，即使我们传入一个动态定义的(即`to_sym`)符号，它也会被转换成一个不朽的符号，这样它就可以用于方法查找。希望你不会那样做，但是指出 Ruby 中的危险部分还是有好处的。

变量也在幕后使用符号。

```
before = Symbol.all_symbols.size
eval %Q{
  BAR = nil
  FOO = nil
}
GC.start
after = Symbol.all_symbols.size
puts after - before
# => 2
```

尽管变量是`nil`，但它在幕后使用了一个永远不会被垃圾回收的符号。除了避免根据用户输入随意定义方法之外，还要注意根据用户输入创建变量:

```
self.instance_variable_set( "@step_#{ params[:step] }".to_sym, nil )
```

为了真正安全，您应该在运行`GC.start`之后定期检查`Symbol.all_symbols.size`,以确保符号表没有增长。展望未来，希望一些关于符号安全与否的好标准成为更普遍的知识。如果你发现另一个真正常见的问题，请在 twitter 上联系[我](https://twitter.com/schneems)，我会尽力更新这一部分。

感谢 [@nari3](https://twitter.com/nari3) 查看此部分并提供反馈。欲了解更多关于内部和实现的信息，请阅读 [Nari 的幻灯片](http://www.slideshare.net/authorNari/symbol-gc)或[聆听 Ruby Kaigi 的演讲](http://rubykaigi.org/2014/presentation/S-NarihiroNakamura)。

## 我觉得需要速度

除了安全性，你应该关心这个特性的最大原因是速度。有大量的代码围绕将符号转换成字符串来编写，以避免意外分配用户输入的符号。一般来说，当你把“吨”和“代码”这两个词放在一起，结果不会很快。

避免符号分配最常见的例子是 Rail 的(ActiveSupport 的)`HashWithIndifferentAccess`。因为我写过 Hashie 之类的 Hash 的子类速度很慢(T2)，所以你可能不会惊讶地发现 Rails 中的这种行为会带来巨大的性能损失。

```
require 'benchmark/ips'

require 'active_support'
require 'active_support/hash_with_indifferent_access'

hash = { foo: "bar" }
INDIFFERENT = HashWithIndifferentAccess.new(hash)
REGULAR     = hash

Benchmark.ips do |x|
  x.report("indifferent-string") { INDIFFERENT["foo"] }
  x.report("indifferent-symbol") { INDIFFERENT[:foo] }
  x.report("regular-symbol")     { REGULAR[:foo] }
end
```

当我们运行这个时:

```
Calculating -------------------------------------
  indifferent-string   115.962k i/100ms
  indifferent-symbol    82.702k i/100ms
      regular-symbol   150.856k i/100ms
-------------------------------------------------
  indifferent-string      4.144M (± 4.4%) i/s -     20.757M
  indifferent-symbol      1.578M (± 3.7%) i/s -      7.939M
      regular-symbol      8.685M (± 2.4%) i/s -     43.447M
```

我们看到，使用字符串的无差别访问哈希的速度大约是使用符号键的常规哈希的一半。我们还看到，使用一个符号来访问一个无关紧要的访问散列中的值要比使用一个带有符号键的常规散列慢 5 倍。我[写过 Ruby 2.2 中的字符串键性能是如何得到巨大改善的](http://www.schneems.com/2014/11/26/unraveling-string-key-performance.html)，然而，用符号访问散列仍然是最快的，有些人可能会说，是最美观的访问散列的方式。现在有了 Ruby 2.2，我们可以在 Rails 的参数中使用符号键。如果我们实现了这一转变，我们就不必担心安全问题，也不必承担税收的开销。

> 注意:在进行任何大的性能更改之前，您应该在应用程序级别进行基准测试，尤其是当它需要 API 弃用时。不要以“一些博客说它更快”为由提交性能补丁，即使那个博客是我的。始终用个案基准来验证索赔。

## 概述

Symbol GC 使您免受 DoS 攻击，并允许您在任何需要的地方灵活地使用符号。再加上 Ruby 2.2 的其他性能特性，包括增量 GC 和使用散列键的字符串重复数据删除，没有理由不马上升级。本地安装:

```
$ ruby-install ruby 2.2.0
```

在生产中运行(如果您正在使用 Heroku ):

```
$ echo "ruby '2.2.0'" >> Gemfile
```

不要等待，Ruby 的未来就在现在！

—
[@schneems](https://twitter.com/schneems) 撰写关于红宝石、表演和符号的文章，请追随他。

## 分享这篇文章