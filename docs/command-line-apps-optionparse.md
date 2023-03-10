# 带选项 Parse 的命令行应用程序

> 原文：<https://www.sitepoint.com/command-line-apps-optionparse/>

![Vector Command Line Icon](img/4d9dbf3caa2fc7fcc6290bc2d4d094d7.png)

命令行应用程序可能不再像以前那样常见，但程序员几乎每天都在使用它们。即使你是 ruby 的新手，你也可能使用过 IRB 和 git(如果你还没有，你会使用的！)“Options”、“option flags”或者仅仅是“flags”可以让与这些实用工具的交互更快、更简单、更强大，Ruby 就是其中的乐趣之一。

当大多数人想到 ruby 时，他们会想到 web 应用程序和脚本。诚然，ruby 在这两方面都很棒，但你也可以用它来构建命令行实用程序，而不是使用 bash 或 c 之类的东西。每次从头开始将选项逻辑构建到你的应用程序中都会很麻烦，幸运的是，标准的 ruby 库包括一个名为`OptionParse`的模块，它可以为我们完成繁重的工作。

首先让我们看看什么是期权，它有什么作用。CLI 中的“选项”和“参数”看起来很相似，但它们是不同的。下面是一个常见的`git`终端命令:

```
$ git commit -m "Initial commit"
```

在本例中:

*   `git`是应用程序
*   `commit`是应用程序运行的命令
*   `-m`是选项标志
*   `"Initial commit"`是通过选项传递给命令的参数

上面的终端命令告诉`git`提交当前暂存的文件，而`-m`标志让您键入提交消息(注意他们如何使用`-m`作为“消息”)。*聪明！*)都在一行。如果你在终端中输入`git commit`,它会打开一个编辑器，要求提交消息，以及一些帮助文本。它看起来像这样:

```
# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
# On branch master
#
# Initial commit
#
# Changes to be committed:
# new file:   README.markdown
```

当您刚接触 git 时，这很有用。然而，有可能在你提交了几次之后，你就不需要一次又一次地被扔进编辑器并看到这个帮助文本了。通过使用`-m`选项标志，就像我们在上面做的那样，事情被大大加速了。

OptionParse 模块使创建选项标志和帮助文本变得容易，帮助文本向用户解释每个选项的作用。

因为 OptionParse 是标准库的一部分，所以可以用

```
require 'optparse'
```

为了展示`optparse`库是如何工作的，让我们来看看我专门为此编写的一个简单的文字处理工具。

```
require 'optparse'

options = {}
OptionParser.new do |opts|
  opts.banner = "\nSimple word manipulator Ruby CLI app.\n"

  opts.on("-u", "--upcase", "Capitalize all letters") do |u|
    options[:upcase] = u
  end

  opts.on("-d", "--downcase", "Downcase all letters") do |d|
    options[:downcase] = d
  end

  opts.on("-c", "--capitalize", "Capitalize first character") do |c|
    options[:capitalize] = c
  end

  opts.on("-r", "--reverse", "Reverse the order of letters") do |r|
    options[:reverse] = r
  end

  opts.on("-h", "--help", "Displays help") do
    puts opts
    exit
  end
end.parse!

words = []

ARGV.each do |word|
  words << word.dup
end

words.each do |word|
  word.upcase! if options[:upcase]
  word.downcase! if options[:downcase]
  word.capitalize! if options[:capitalize]
  word.reverse! if options[:reverse]

  puts word
end
```

那么，这段代码是做什么的？它从命令行接受字符串形式的参数，进行一次或多次转换，然后将结果发送到终端。如果我们使用“Hello”和“World”作为参数，使用`--reverse`选项运行这段代码，我们会得到以下结果:

```
$ ruby words.rb Hello World -r
#=> olleH
#=> dlroW
```

前两行不需要过多解释:我们需要`optparse`模块并实例化一个名为`options`的空散列。

```
require 'optparse'

options = {}
```

跳过`OptionParse`块，你会看到`ARGV`全局。在命令行输入的参数(但*不是*选项，稍后会详细介绍)被收集到一个数组中。这里发生了一件特殊的事情:参数都是(# freeze)[http://www . ruby-doc . org/core-1 . 9 . 3/Object . html # method-I-freeze]这就是为什么我要通过 [Object#dup](http://www.ruby-doc.org/core-1.9.3/Object.html#method-i-dup) 方法将参数复制到另一个数组中。

```
words = []

ARGV.each do |word|
  words << word.dup
end
```

最后一个块遍历`words`数组中的每个单词(在命令行输入的参数的副本)，根据任何选项进行操作，并将结果发送给终端`puts`。

```
words.each do |word|
  word.upcase! if options[:upcase]
  word.downcase! if options[:downcase]
  word.capitalize! if options[:capitalize]
  word.reverse! if options[:reverse]

  puts word
end
```

最后，魔术。从`OptionParser.new do |opts|`到`end.parse!`的代码块是定义选项的地方，在运行时，从命令行输入解析。`OptionParser`类内置了许多便利:

*   `OptionParser#banner`允许您在用户查看帮助时向他们显示任意文本
*   `OptionParser#on` block 允许您声明每个选项，包括缩写标志(即`-u`)、详细标志(即`--upcase`)、描述性帮助文本(即“大写所有字母”)以及选项的行为
*   智能地从参数列表中提取选项标志，将实际的参数留给代码处理。这意味着，如果您需要多个标志，您可以执行以下任一操作，并且仍然会得到相同的输出:
    *   `$ ruby words.rb Hello World -r -u`
    *   `$ ruby words.rb -r Hello World -u`
    *   `$ ruby words.rb -ru Hello World`

让我们最后看一下`OptionParser.new`模块:

```
OptionParser.new do |opts|
  opts.banner = "\nSimple word manipulator Ruby CLI app."

  opts.on("-u", "--upcase", "Capitalize all letters") do |u|
    options[:upcase] = u
  end

  opts.on("-d", "--downcase", "Downcase all letters") do |d|
    options[:downcase] = d
  end

  opts.on("-c", "--capitalize", "Capitalize first character") do |c|
    options[:capitalize] = c
  end

  opts.on("-r", "--reverse", "Reverse the order of letters") do |r|
    options[:reverse] = r
  end

  opts.on("-h", "--help", "Displays help") do
    puts opts
    exit
  end
end.parse!
```

在我们的例子中，每个选项都被用作布尔值，这是一种常见的用法，但是您可以做任何您想做的事情。在我为以前的工作编写的一个简单的应用程序中，我的大多数选项标志在其余代码执行之前从全局数组中添加或删除文件扩展名。

这个示例命令行应用程序工作(复制并粘贴到您最喜欢的文本编辑器，并尝试它！)，但可以改进。首先，由于检查的顺序或方法改变字符串的方式，某些标志不能一起运行。你将如何改变这段代码，使它更健壮、更灵活？如果你有特殊字符的字符串会怎么样？在代码爆炸之前，您可以发送多少个参数(如果有限制，您有可能在终端中手工输入达到它吗？)在评论里告诉我吧！

## 分享这篇文章