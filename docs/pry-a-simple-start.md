# 一个简单的开始

> 原文：<https://www.sitepoint.com/pry-a-simple-start/>

![pry_logo (1)](img/be2533482eefad069a68add04e206923.png)

REPL——读取、评估、打印、循环，是一个用于编程语言的交互式 shell，用于评估语言中的表达式。REPLs 是学习语言、语法和 API 的好方法。

大多数编程语言都有自己的 REPL 或为相同目的编写的社区构建工具。对于 Ruby 来说，它是 IRB——交互式 Ruby Shell，让你在几秒钟内就能玩上 Ruby，并开始体验它的特性。

IRB 与 Ruby 捆绑在一起，可以使用命令`irb`从命令行启动。它提供了命令历史、行编辑和从内部执行 Ruby 程序。然而，我们今天不讨论 IRB。相反，我想把重点放在另一种选择上，叫做[窥探](http://pryrepl.org/)，以及它带来了什么。我们将会看到 Pry 今天所包含的一些漂亮的特性。

## 装置

显然，在我们开始使用 pry 之前，我们需要安装它。Pry 是红宝石，所以安装它和安装任何红宝石是一样的。只需键入:

```
gem install pry pry-doc 
```

我们添加了`pry-doc`来访问 Ruby 核心文档。要开始撬，只需在 gem 安装后输入`pry`。

您还可以通过将`gem "pry"`添加到应用程序的 Gemfile 中，使其成为应用程序依赖项的一部分。从应用程序内部安装 pry 有它自己的好处。事实上，有一个`pry-rails`宝石用 Pry 代替了 Rails 控制台。

我们今天不会深入讨论 Rails gem，但我将重点介绍 Pry 的一些最佳特性，并允许您自己探索更多特性。

## 探索文档和来源

Pry 中最有用的特性之一是能够在 shell 中浏览文档和源代码。Pry 通过在运行时从源代码中获取它使之成为可能。显示文档的命令是`show-doc`接受一个方法名。`show-doc`将很容易与 ri 和 [YARD](http://yardoc.org/) 文档一起工作，并且它还支持语法高亮和格式化。我们开始玩吧。

当我在 pry 控制台中键入`show-doc`后跟方法名时，该方法的文档会直接在控制台上返回:

```
pry(main)> show-doc Array#map

From: array.c (C Method):
Owner: Array
Visibility: public
Signature: map()
Number of lines: 12

Invokes the given block once for each element of self.

Creates a new array containing the values returned by the block.

See also Enumerable#collect.

If no block is given, an Enumerator is returned instead.

   a = [ "a", "b", "c", "d" ]
   a.collect { |x| x + "!" }         #=> ["a!", "b!", "c!", "d!"]
   a.map.with_index { |x, i| x * i } #=> ["", "b", "cc", "ddd"]
   a                                 #=> ["a", "b", "c", "d"] 
```

结果是不言自明的，它返回了文档的来源、签名、行数和所提供方法的描述。你会注意到这是 C 方法，因为我用的是核磁共振。您会很高兴知道核心的 Pry 命令可以与任何解释器一起工作，包括 JRuby 和 Rubinius。

所以，这个方法是语言核心的一部分，是用 C 写的，我们可以从结果中推断出来。我们可以直接从内部查看这个方法的源代码。只需键入`show-source`后跟方法名:

```
pry(main)> show-source Array#map

From: array.c (C Method):
Owner: Array
Visibility: public
Number of lines: 13

static VALUE
rb_ary_collect(VALUE ary)
{
    long i;
    VALUE collect;

    RETURN_SIZED_ENUMERATOR(ary, 0, 0, ary_enum_length);
    collect = rb_ary_new2(RARRAY_LEN(ary));
    for (i = 0; i < RARRAY_LEN(ary); i++) {
    rb_ary_push(collect, rb_yield(RARRAY_AREF(ary, i)));
    }
    return collect;
} 
```

就在那里。`Array#map`的源代码！

对于任何可用的方法，我们都可以使用`show-method`或`show-source`。继续，尝试一些你一直在用的方法。

## 钩住

我们今天要看的下一个特性是钩子。钩子只是与 Pry 中的事件相关联的可调用方法。每当 Pry 中发生一个事件，就会执行一个与之相关联的钩子。Pry 已经有了一些我们可以“挂钩”的事件，比如`:before_session`、`:before_eval`等等。让我们来看看并为这些预先存在的事件之一建立一个挂钩:

```
[1] pry(main)> Pry.hooks.add_hook(:after_read, "uphook") do |text, pry|
[1] pry(main)*   puts text.upcase
[1] pry(main)* end 
```

在这里，我们创建了一个钩子，它在 shell 读取每个语句后运行，并将字符串输出回来，将其转换为大写。为了测试它，我们必须启动一个新的撬会话来加载钩子:

```
[2] pry(main)> Pry.start
# New Session is started here
[1] pry(main)> "abc"
"ABC"
=> "abc" 
```

如您所见，它将输入的字符串大写。这是钩子的一个简单用例，但是你可以做得更多。Pry 扩展大量使用这些来提供它们强大特性。更重要的是，你甚至可以创建自己的事件，以及随后，你自己的钩子！点击阅读更多关于创建您自己的自定义活动[的信息](https://github.com/pry/pry/wiki/Hooks#Hooks_adding_custom_events)

## 状态导航

Pry 提供了一个很好的选项来浏览对象、切换上下文和改变级别，就像文件系统一样。让我们看一个例子。

首先，我们将了解一个名为`cd`的命令。`cd <OBJECT_NAME>`将 shell 的上下文更改为目标对象，您可以检查该对象上的可用方法，或者更深入一层:

```
pry(main)> class Fruit
pry(main)* end
pry(main)> class Apple < Fruit
pry(main)* end

pry(main)> self
=> main
pry(main)> cd Fruit
pry(Fruit):1> self
=> Fruit
pry(Fruit):1> cd Apple
pry(Apple):2> self
=> Apple 
```

如您所见，随着每个`cd`进入一个对象，shell 的上下文转移到该对象，您可以继续嵌套，直到您想要的程度。此外，在任何时候你都可以使用命令`nesting`快速查看你已经嵌套了多远以及你的嵌套列表。

```
pry(Apple):1> nesting
Nesting status:
--
0\. main (Pry top level)
1\. Fruit
2\. Apple

pry(Apple):1> cd ..
pry(Fruit):1> self
=> Fruit
pry(Fruit):1> cd -
pry(Apple):2> self
=> Apple 
```

类似于 Unix 的`cd`命令，要到达父对象只需输入`cd ..`并使用`cd -`到达当前对象之前访问过的前一个对象。

还有其他有用的命令来补充 Pry 的状态导航特性，比如`ls`——显示方法、模块等的列表。`find-method`命令用于查找一个方法的功能及其作用。状态导航命令的完整功能集记录在[这里](https://github.com/pry/pry/wiki/State-navigation)

## 历史

Pry 的另一个有用的特性是它的历史跟踪。默认情况下，当启动 pry 会话时，Pry 会将您使用过的命令记录在一个历史文件中，您可以在以后检查、分析甚至重放该文件。围绕历史跟踪，开箱即用提供了许多配置选项。默认情况下，历史存储在`/.pryhistory`文件中，但是您可以通过将以下内容添加到您的`.pryrc`文件中来轻松更改它:

```
Pry.config.history.file = "~/.filenamehere" 
```

此外，每次启动探查会话时，都会从历史记录文件中加载历史记录，并在会话结束时保存到该文件中。但是，您可以使用以下配置来覆盖此行为:

```
# To Disable the autoload
Pry.config.history.should_load = false

# To enable the autosave
Pry.config.history.should_save = false 
```

除了直接处理历史文件，您还可以使用方便的`hist`命令，它提供了一组标志，使处理 pry 的历史变得轻而易举。键入`hist`命令打印出全部存储的历史。

```
[9] pry(Rails):1> hist
1: hist
2: cd Rails
3: ls
4: cd Application
5: ls
6: show-method assets
7: cd ..
8: ls 
```

但是，您可以使用这些标志来进一步过滤和检查历史记录。下面是一些可用的标志。

要列出包含单词`cd`的所有命令，请使用`--grep`:

```
[10] pry(Rails):1> hist --grep cd
2: cd Rails
4: cd Application
7: cd .. 
```

要查看您最近使用的 5 个命令，请使用`--tail`选项。如果没有传递该值，默认数字是 10:

```
[11] pry(Rails):1> hist --tail 5
 6: show-method assets
 7: cd ..
 8: ls
 9: hist
10: hist --grep cd 
```

在 Pry 的`hist`界面中有更多可用的命令，在这里[记录了这些命令](https://github.com/pry/pry/wiki/History)。但是，我想给你们展示`hist`司令部最后一面很酷的旗帜——“T2”。`replay`标志允许您执行历史记录中的命令，可以是单个命令，也可以是根据行号排列的一系列命令。用法很简单只要做`hist --replay X..Y`:

```
[15] pry(main)> hist --replay 2..5
constants: API  Application  CommandsTasks  Configuration  Console  ConsoleMethods  Engine  Html  Info  InfoController  Initializable  MailersController  Paths  Rack  Railtie  VERSION  WelcomeController
ActiveSupport::Autoload#methods: autoload  autoload_at  autoload_under  autoloads  eager_autoload  eager_load!
Rails.methods:
  app_class  app_class=  application  application=  backtrace_cleaner  cache  cache=  configuration  env  env=  gem_version  groups  initialize!  initialized?  logger  logger=  public_path  root  version
instance variables: @_at_path  @_autoloads  @_eager_autoload  @_env  @_under_path  @app_class  @application  @cache  @logger  @parent_name
locals: _  __  _dir_  _ex_  _file_  _in_  _out_  _pry_
... 
```

## 结论

至此，我们的教程到此结束。Pry 实际上是默认 IRB shell 的一个强大的替代品，在增强您现有的工作流程方面效果很好。我们只讨论了 Pry 的几个特性，但是它包含了更多的特性，包括分步调试(使用 pry-debugger gem)和从 REPL 中编辑和重新加载文件的能力。我们已经有一个教程来帮助开始使用 Pry，你可以在这里阅读它[，这里有一个全面的 Pry wiki 文档页面，可以在这里](https://www.sitepoint.com/rubyists-time-pry-irb/)找到[。](https://github.com/pry/pry/wiki)

感谢您的阅读，我希望它服务于您的目的。

## 分享这篇文章