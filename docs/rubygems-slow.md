# Rubygems 为什么慢？

> 原文：<https://www.sitepoint.com/rubygems-slow/>

![rubygemsslow](img/dd26638bd6281db148d36bebf6d1919b.png)

“Rubygems 到底为什么慢？”不止一个开发人员问过这个问题，但很少有人费心去做些什么。最近， [@mfazekas](https://github.com/mfazekas) 接手了[使用 dtrace](https://github.com/rubygems/rubygems/issues/1167#issuecomment-82437003) 剖析一个特别慢的案件的任务。这导致了几个高调的拉取请求，以提高性能和减少内存分配。这些反过来让我不禁要问，Rubygems 到底在做什么，花了这么长时间？简而言之，比你想象的要多得多；长答案，继续看。

## Rubygems 基础

Rubygems gem 提供了现代版本的 Ruby。它的目标是使安装和使用外部代码(或库)更加容易。为了做到这一点，它接管了`Kernel#require`,这样在你跑完之后:

```
$ gem install wicked
```

你可以稍后加载正确的代码。

## 加载路径和分辨率

当你没有指定要加载的版本时，那么 Rubygems 如何知道使用哪个版本呢？它可以使用最新的版本，但是当你为一个不同的项目使用一个更新的版本时，你的项目可能会中断。Rubygems 试图根据其他依赖关系对您需要的依赖关系做出明智的决定。当一个 gem 被发布到 rubygems.org(服务器)时，它们包含一个`.gemspec`,声明关于当前 gem 的信息以及 gem 拥有的依赖项。例如在`wicked.gemspec`中

```
gem.add_dependency             "railties", [">= 3.0.7"]
gem.add_development_dependency "rails",    [">= 3.0.7"]
gem.add_development_dependency "capybara", [">= 0"]
```

如果你只需要这个版本的`wicked`，除非你也在使用比`3.0.7`更高的`railties`版本，否则它是不需要的。如果它找不到，就会引发一个异常。如果它找到一个有效的版本，它会将其添加到`$LOADED_FEATURES`。

使用另一个库`threaded`，我们可以看到`$LOADED_FEATURES`，这是加载前的:

```
puts $LOADED_FEATURES.inspect
=> ["enumerator.so", "rational.so", "complex.so", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/x86_64-darwin14/enc/encdb.bundle", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/x86_64-darwin14/enc/trans/transdb.bundle", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/unicode_normalize.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/x86_64-darwin14/rbconfig.rb", "thread.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/x86_64-darwin14/thread.bundle", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/rubygems/compatibility.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/rubygems/defaults.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/rubygems/deprecate.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/rubygems/errors.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/rubygems/version.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/rubygems/requirement.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/rubygems/platform.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/rubygems/basic_specification.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/rubygems/stub_specification.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/rubygems/util/stringio.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/rubygems/specification.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/rubygems/exceptions.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/rubygems/core_ext/kernel_gem.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/monitor.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/rubygems/core_ext/kernel_require.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/rubygems.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/e2mmap.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/irb/init.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/irb/workspace.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/irb/inspector.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/irb/context.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/irb/extend-command.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/irb/output-method.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/irb/notifier.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/irb/slex.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/irb/ruby-token.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/irb/ruby-lex.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/irb/src_encoding.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/irb/magic-file.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/x86_64-darwin14/readline.bundle", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/irb/input-method.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/irb/locale.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/irb.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/rubygems/path_support.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/rubygems/dependency.rb"]
```

之后:

```
["enumerator.so", "rational.so", "complex.so", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/x86_64-darwin14/enc/encdb.bundle", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/x86_64-darwin14/enc/trans/transdb.bundle", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/unicode_normalize.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/x86_64-darwin14/rbconfig.rb", "thread.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/x86_64-darwin14/thread.bundle", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/rubygems/compatibility.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/rubygems/defaults.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/rubygems/deprecate.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/rubygems/errors.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/rubygems/version.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/rubygems/requirement.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/rubygems/platform.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/rubygems/basic_specification.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/rubygems/stub_specification.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/rubygems/util/stringio.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/rubygems/specification.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/rubygems/exceptions.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/rubygems/core_ext/kernel_gem.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/monitor.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/rubygems/core_ext/kernel_require.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/rubygems.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/e2mmap.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/irb/init.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/irb/workspace.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/irb/inspector.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/irb/context.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/irb/extend-command.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/irb/output-method.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/irb/notifier.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/irb/slex.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/irb/ruby-token.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/irb/ruby-lex.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/irb/src_encoding.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/irb/magic-file.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/x86_64-darwin14/readline.bundle", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/irb/input-method.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/irb/locale.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/irb.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/rubygems/path_support.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/rubygems/dependency.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/timeout.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/logger.rb", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/x86_64-darwin14/stringio.bundle", "/Users/richardschneeman/.gem/ruby/2.2.1/gems/threaded-0.0.4/lib/threaded/version.rb", "/Users/richardschneeman/.gem/ruby/2.2.1/gems/threaded-0.0.4/lib/threaded/errors.rb", "/Users/richardschneeman/.gem/ruby/2.2.1/gems/threaded-0.0.4/lib/threaded/ext/stdout.rb", "/Users/richardschneeman/.gem/ruby/2.2.1/gems/threaded-0.0.4/lib/threaded/worker.rb", "/Users/richardschneeman/.gem/ruby/2.2.1/gems/threaded-0.0.4/lib/threaded/master.rb", "/Users/richardschneeman/.gem/ruby/2.2.1/gems/threaded-0.0.4/lib/threaded/promise.rb", "/Users/richardschneeman/.gem/ruby/2.2.1/gems/threaded-0.0.4/lib/threaded.rb"]
```

如果你担心 Rubygems 可能会选择错误的 gem 版本，你将需要使用`bundler`，大多数 Ruby 开发者已经这样做了。Bundler 会在您第一次运行`bundle install`时进行 gem 解析。Bundler 使用来自`Gemfile`的明确规格以及在单个宝石的`gemspec`中发现的信息。一旦解决，bundler 生成一个`Gemfile.lock`。开发人员将该文件签入他们的源代码控制中，以确保他们安装的每个版本都与其同事的版本相同。否则，你最终会得到“它在我的机器上工作”的微妙错误，这些错误存在于软件的微小版本差异之间。Bundler 通过将`Gemfile.lock`中的所有宝石显式添加到`$LOAD_PATH`来处理 Rubygems

```
$ echo Gemfile
source 'https://rubygems.org'

gem 'threaded'

$ bundle exec irb
> puts  $LOAD_PATH
# => ["/Users/richardschneeman/.gem/ruby/2.2.1/gems/threaded-0.0.4/lib", "/Users/richardschneeman/.gem/ruby/2.2.1/gems/bundler-1.8.3/lib", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/site_ruby/2.2.0", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/site_ruby/2.2.0/x86_64-darwin14", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/site_ruby", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/vendor_ruby/2.2.0", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/vendor_ruby/2.2.0/x86_64-darwin14", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/vendor_ruby", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0", "/Users/richardschneeman/.rubies/ruby-2.2.1/lib/ruby/2.2.0/x86_64-darwin14"]
```

这里你可以看到线程版本`0.0.4`已经在加载路径上。现在，当您尝试`require threaded`时，会找到那个显式版本，Rubygems 不必尝试解析。

看起来像是在 bundler 中使用`Gemfile.lock`消除了 Rubygems 解析依赖关系的需要。这几乎是真的，但是仍然有很多时候你需要`require`在没有`Gemfile.lock`的情况下正常工作。运行`rails new foo`就是一个很好的例子。这个命令实际上会为您生成一个`Gemfile`和一个`Gemfile.lock`。这正是@mfazekas 在发现[问题](https://github.com/rubygems/rubygems/issues/1167)时所做的事情。

## Rubygems 内部

当你在 Ruby 代码中需要一个 gem 时，会发生什么呢？当你调用`require "wicked"`时，代码会首先尝试在正常的标准库中寻找`wicked`。如果不能，那么 Rubygems 将会查看它之前是否被加载过。如果`wicked`以前没有被要求，Rubygems 必须找到它并调用:

```
found_specs = Gem::Specification.find_in_unresolved path
```

在 Rubygems 中，一旦我们确切地知道系统上需要什么版本，我们就“激活”一个 gem。一旦激活，我们不能激活宝石的不同版本。Rubygems 在可能的情况下解决依赖冲突，如果不能，就会出错。

在 Rubygems 内部,“未解决的”gem 规范是一个已经被另一个 gem 引用但是还没有被加载的 gem。例如，如果我们有一个宝石`a`需要一个名为`b`的宝石，当我们需要`a`时，我们会希望两个宝石都被装载。为了让这个工作`Gem::Specification#activate`被称为`a`宝石。如果只需要这两个依赖项，那么我们可以“激活”一个`a`需要的`b`版本。然而，如果系统知道还有其他 gem 没有被激活，引用了`b`，它还不能被解析和加载。当这种情况发生时，依赖性要求被存储在存储器中:

```
> puts Gem::Specification.unresolved_deps.inspect
[<Gem::Dependency type=:runtime name="b" requirements=">= 0">]
```

稍后，当一个 gem 被显式地`require` -d 时，我们检查它是否先前存储在我们的`Gem::Specification#find_in_unresolved`列表中。

如果我们试图在`a`被激活的地方`require 'c'`并引用`b`(这是“未解决的”)，那么 Rubygems 还不知道在哪里找到`c`。要做到这一点，它需要遍历所有未解决的依赖项，并找到哪些引用了`c`。为此，我们称之为:

```
found_specs = Gem::Specification.find_in_unresolved_tree path
```

它必须穿越所有可能的宝石。

## 宝石导线

Rubygems 内部的遍历方法是一个昂贵的方法，与 Chevy 无关。

![chevy traverse](img/2f642acd260a34e0647d373824160392.png)

(呻吟)

当试图在未解析的树中找到一个规范时，必须查找未解析的依赖项，并且需要检查它们的每个依赖项，以查看它们是否包含我们正在寻找的 gem。

你可以在`Gem::Specification.find_in_unresolved_tree`里看到这个逻辑:

```
def self.find_in_unresolved_tree path
  specs = unresolved_deps.values.map { |dep| dep.to_specs }.flatten

  specs.reverse_each do |spec|
    trails = []
    spec.traverse do |from_spec, dep, to_spec, trail|
      next unless to_spec.conflicts.empty?
      trails << trail if to_spec.contains_requirable_file? path
    end

    next if trails.empty?

    return trails.map(&:reverse).sort.first.reverse
  end

  []
end
```

这段代码对每个“未解决”的规范调用`traverse`方法:

```
def traverse trail = [], &block
  trail = trail + [self]
  runtime_dependencies.each do |dep|
    dep.to_specs.each do |dep_spec|
      block[self, dep, dep_spec, trail + [dep_spec]]
      dep_spec.traverse(trail, &block) unless
        trail.map(&:name).include? dep_spec.name
      end
  end
end
```

这个方法获取所有在运行时被目标 gem 自动激活的`Gem::Specification`；这些被称为“运行时依赖”。对于其中的每一个，它获取它们的依赖项并在每个依赖项上调用`traverse`。每当这种情况发生时，变量`trail`就会与当前的`Gem::Specification`相加。

这是一种开销很大的方法，因为每次调用它时都会分配 2 个数组:

```
trail = trail + [self]
```

除了`[self]`数组，将数组添加到现有数组的过程实际上分配了一个新数组，即使您使用了相同的变量:

```
irb(main):005:0> array = [1,2,3]
=> [1, 2, 3]
irb(main):006:0> puts array.object_id
70265837266320
=> nil
irb(main):007:0> array = array + [4]
=> [1, 2, 3, 4]
irb(main):008:0> puts array.object_id
70265837200880
```

一旦我们做了赋值，`array = array + [4]`，`array`变量的`object_id`就变了。已经分配了一个新数组。Ruby 解释器可以优化为不分配这个数组，但是这非常困难，因为在其他地方可以保存对这个数组的引用，包括用`eval`编码的代码:

```
array = [1, 2, 3] 
eval(
  <<-CODE
    def foo(ary); $array = ary; end
    foo(array)
  CODE
)
> array = array + [4]
```

每次运行到`traverse`时重复复制该数组，当稍后再次复制时，情况会变得更糟:

```
block[self, dep, dep_spec, trail + [dep_spec]]
```

注意:这是与调用`block.call(self, dep, dep_spec, trail + [dep_spec])`相同的代码

## 拯救名单吗？

数组包含了很多在这种情况下不需要的特性。我们所要做的就是记录已经被遍历的规范。Aaron 提交了一个相对流行的补丁来帮助解决这个问题:

> 如果我是对的(我认为我是对的)，这将从 RG:[https://t.co/RrPFb72quc](https://t.co/RrPFb72quc)[# emptystomachbeer](https://twitter.com/hashtag/emptystomachbeer?src=hash)/cc[@ searls](https://twitter.com/searls)
> 
> —亚伦·帕特森(@ tender love)[2015 年 3 月 13 日](https://twitter.com/tenderlove/status/576221155461242880)

`Gem::List`是一个[链表数据结构](https://en.wikipedia.org/wiki/Linked_list)。这种结构类似于一个数组，因为它保存顺序数据。在链表中，与数组相比，可以非常有效地添加和删除元素，因为整个数据结构不需要重新组织。链表由许多节点组成，每个节点都有一个值和对“下一个”或“尾部”节点的引用。您可以像这样从 Gem::Node source 中的[遍历整个链表:](https://github.com/rubygems/rubygems/blob/master/lib/rubygems/util/list.rb)

```
def each
  n = self
  while n
    yield n.value
    n = n.tail
  end
end
```

在 pull 请求中，这个`Gem::Node`用于跟踪遍历过程。看一下来源:

```
##
# This method is for traversing spec dependencies.  Don't use this, it is
# super private. I am super serious!

def self._traverse spec, trail, &block # :nodoc:
  spec.dependencies.each do |dep|
    next unless dep.runtime?
    dep.to_specs.each do |dep_spec|
      stack = Gem::List.new(dep_spec, trail)
      block[dep_spec, stack]
      spec_name = dep_spec.name
      _traverse(dep_spec, stack, &block) unless
        stack.any? { |s| s.name == spec_name }
    end
  end
end
private_class_method :_traverse
```

逻辑基本上与原始遍历相同。递归地浏览所有的 gem 规范，并查看它们的每一个规范。虽然“数百万次分配”的原因是 PR 中的一个 bug，但改进仍然存在，即减少每次调用的分配是一种改进。

## 效果

编写的测试强调了最坏的情况。我们有很多没有被“激活”但是加载了的宝石，也参考了很多其他的宝石。我们通过搜索一个不存在的文件来强制进行一次完整的遍历。

这对你的应用意味着什么？我修改了 Rubygems，以便在启动我的[codetriage.com](https://www.codetriage.com)应用时打印出`Gem::Specification.find_in_unresolved_tree path`被调用的次数。该方法被调用了两次，这导致了对`traverse`方法的总共 30 次调用。

通过使用`bundle exec`启动应用程序，可以将这种情况减少到 0 次调用。当使用`bundle exec`时，可以搜索的宝石范围缩小，因为只有在`Gemfile.lock`中找到的宝石才会出现在载入路径中。

## 这一切意味着什么？

这是我最近最喜欢的拉动请求之一，原因有很多。首先，它有一个没人在测试中发现的错误。这是什么意思？我们不能总是依赖现有的测试套件来处理每一个可能的场景。这种公开的拉取请求是有原因的。不能低估审查和衡量有效性的人的因素。

关于这个拉取请求，我第二喜欢的事情是描述中包含了用于推断节省的基准。这不仅意味着我们都可以从公关中学习，而且我们都可以独立测试它。这种开放性允许任何人运行代码，应用补丁，并单独验证节省。这是最初吸引我研究 Rubygems 行为的原因。这篇文章来源于我在[评估拉取请求](https://gist.github.com/schneems/38aaa586f25de6bc1916)时做的原始笔记。最后，我很高兴这项性能工作来自于发现和报告一个性能错误。作为 ruby 爱好者，我们为什么不像抱怨速度慢一样，花一半的时间进行基准测试和寻找性能缺陷呢？

就在宣布“RubyTogether”计划之前，我写下了这篇文章的第一稿，该计划寻求做很多事情，包括帮助 rubygems.org 实现可持续发展的“T2”。

对于项目核心开发人员之外的人来说，阅读并理解流行的拉取请求是很重要的。这是您学习新工具和技术的好方法，但也是了解项目不同内部部分如何工作的好时机。我鼓励你去找一个你不完全理解的公关:做些笔记，钻研，学习一些东西。开源贡献最好的部分不仅仅是我们从其他人的工作中受益，我们可以看到这项工作是如何产生的，我们有机会让自己变得更好。

* * *

如果你喜欢开源和 Ruby，就关注 [@schneems](https://twitter.com/schneems) 。如果你想深入了解开源问题并提出请求，请订阅 codetriage.com 的。

## 分享这篇文章