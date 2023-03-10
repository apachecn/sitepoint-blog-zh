# 其他人的命令行应用程序

> 原文：<https://www.sitepoint.com/command-line-applications-for-the-rest-of-us/>

我依稀记得我第一次使用命令行的时候。对于任何想要成为开发者的人来说，这都是可怕的一步。你离开了一个**安全**，友好，图形环境的荒凉和无情的景观。这只是其中之一。在你喝了一段时间的 Kool-Aid 之后，你就无法理解不在终端上你是如何生活的。

IDE 和图形客户端变得笨拙和“哦，多慢？”会是他们吗！命令行很快成为我们这些书呆子的荣誉徽章。你可能只是在浏览一堆文件，任何无知的旁观者都会认为你被黑客帝国或其他什么东西迷住了。

作为 Ruby 开发人员，我们对该语言的第一次介绍更像是一个终端环境。如果我需要检查一点语法，习语，或者仅仅是把 Ruby 的所有功能当作一个简单的计算器，我还是会选择 irb。

考虑到这一点，我仍然觉得命令行应用程序有点像二等公民。通常情况下，它们只不过是快速拼凑起来的脚本，所以我们必须浏览源代码，寻找如何使用它们的说明。我们都被 Rails 和 Sinatra 宠坏了，它们可以轻松地创建奇怪而精彩的界面，增强非技术用户和技术用户的体验。

然而，如果你正在做一些专门为极客设计的东西，命令行是我希望它存在的地方。我们认为常见的工具是理所当然的，比如 [git](http://git-scm.com/) 、Rails 生成器和 Rake 任务。我们很少考虑在制作如此复杂的命令行套件上投入的时间和精力。

## 给 CLI 一些爱

获得正确的接口至关重要。命令行界面的关键成分是命令(废话！)、标志(或选项)，当然还有参数。我们会吸一点蛋，举例说明这些。

### 命令

最明显的是命令本身。如果我们说的是 git，`pull`、`push`、`commit`都是命令。我们告诉 git 应用程序我们想要执行一些特定的动作。

### 标志或选项

在最基本的层面上，这些标志告诉命令以某种**非默认**行为执行。例如，*nix `mkdir`命令可以带有一个`-p`或`--parents`标志。在这里以长格式和短格式指定。它们做着同样的事情，只是其中一个读起来更清晰。

通过添加带有路径的`-p`将创建指定的层次结构，无论父节点是否存在。另一个例子是普通的`-v`或`--verbose`国旗。在命令执行期间，这些状态几乎被归类为全局状态。我倾向于混合搭配我所使用的旗帜。

看看我刚刚给出的例子，对于`mkdir`父标志，我总是使用短格式`-p`，但是每当我寻找大量的`verbose`输出时，我总是选择长格式。没有特别的原因，正如我所说的，它们是完全一样的，只是一种格式根据情况看起来“更合适”。

值得一提的是，应用程序和命令可以采取独立的选项。例如，我将坚持使用 git，因为几乎所有阅读本文的人都会使用或者至少已经使用过它。Git 本身可以获取标志`-p`或`--paginate`，这将把执行的任何输出传送到`less`。此外，`commit`命令使用`-a`和`-m`或甚至组合`-am`来存放所有修改的文件并提交后续消息。我们可以把整个事情组合成`git -p commit -am 'A well formed commit message'`。清楚地显示了 git 套件的应用程序标志和命令选项，尽管这是一个非常愚蠢的例子。**不要**使用`-p`提交。

### 争论

CLI 的第三个也是最后一个要素当然是我们希望传递给相关命令的参数。一个很好的例子是`ls /some/directory`，目录路径是命令将使用的参数。

## 红宝石色的 CLI

用 Ruby 创建命令行应用的第一步是创建一个我称之为引导文件的东西。这是用户进入我们的应用程序的第一步。实现这一点就像创建一个文本文档、设置权限和添加正确的 shebang 一样简单。作为一个单一的班轮看起来像:

```
touch test && chmod +x test && echo "#!/usr/bin/env ruby" >> test
```

当我们执行`./test`时，什么也没有发生。请随意填写`puts`语句，以检查其是否一切正常。

我所知道的所有好的命令行应用程序都是非常模块化的。他们在最好的情况下承担单一的责任。再次以 Git 为例，为了方便起见，一次提交实际上是将其他动作**装入一个命令短语中。**

现在，对于一个简单的命令行应用程序，我们将使用 Ruby 的`ARGV`。我们之前创建的`test`文件将实现以下内容来帮助我们开始。

```
ARGV.each { |arg| puts arg }
```

现在执行`./test hello 'hiya you' goodbye`将输出:

```
hello
hiya you
goodbye
```

我这样做只是为了展示参数被分割并分配到`ARGV`数组中，以及使用引号的效果。

在我们继续深入之前，您是否开始害怕解析`ARGV`中的标志、命令、命令选项和参数？我当然是。

幸运的是，Ruby 附带了一个非常漂亮的库，可以消除任何数组遍历/解析的麻烦。这当然就是 [`OptionParser`](http://ruby-doc.org/stdlib-1.9.3/libdoc/optparse/rdoc/OptionParser.html) 标准库。

作为一个例子，我们将模拟奇妙的 todo 应用程序， [todo.txt](http://todotxt.com/) 。顾名思义，它只是将您的待办事项存储在一个文本文件中，并允许您在一个命令中分配优先级、上下文和项目。我们的版本要简单得多(从消息解析的角度来看),并且只为项目及其消息分配优先级。我们的界面看起来会像这样:

```
./todo -p high -m 'Take out the garbage'`
```

在上面的语句中，我们给 todo 项目`Take out the garbage`分配了一个“高”优先级。首先，我们将查看代码，稍后再解释。

```
#!/usr/bin/env ruby

>require 'optparse'
require 'ostruct'

options = OpenStruct.new
OptionParser.new do |opts|

  opts.on('-h', 'Shows this help screen') do
    puts opts
    exit
  end

  opts.on('-p', '--priority PRIORITY', 'Set the items priority') do |priority|
    options.priority = priority
  end

  opts.on('-m', '--message MESSAGE', 'Set the todo item') do |message|
    options.message = message
  end

end.parse!

puts options.priority
puts options.message
```

分解一下，首先我们需要库`optparse`和`ostruct`。这个应用程序实际上并不需要`OpenStruct`，一个简单的散列就足够了，但是我喜欢使用`OpenStruct`来简化对选项的访问和赋值。然后我们创建一个`OptionParser`的实例，它使用一个块。在这个模块中，我们定义了应用程序及其命令将采用的选项。

第一个是帮助屏幕，它什么也不做，只是提供一些如何使用该应用程序的说明。接下来的两个更加有趣，因为我们要求这些选项分别将它们后面的内容解析为`PRIORITY`和`MESSAGE`。这些参数被适时地设置为我们的`options`结构中的属性。

我们最终在我们的`OptionParser`对象上调用`parse!`，并把选项传给`STDOUT`。为了引导，通过检查`-h`标志的输出，我们得到了应用程序所需输入的简短描述。

## 收紧输入

`OptionParser#on`方法非常灵活。假设我们希望将优先级限制在一组给定的“高”到“低”之间，我们可以使用以下方法来解决这个问题:

```
opts.on('-p', '--priority PRIORITY', [:high, :medium, :low], 'Set the items priority') do |priority|
  options.priority = priority
end
```

另一个常见的用途是让选项有默认值，以节省一些击键。同样，这是微不足道的:

```
opts.on('-p', '--priority [PRIORITY]', [:high, :medium, :low], 'Set the items priority') do |priority|
  options.priority = priority || :medium
end
```

简单地用大括号把参数括起来，我们使它成为可选的，用户所要做的就是传递`-p`标志。在大多数情况下，我没有发现这种类型的默认非常有用。只有在处理文件位置时，它才真正起作用。例如，用户决定(正常操作是不记录日志)他们想要记录到一个文件，无论它是在默认位置还是其他位置。对于我们的 todo 应用程序，除非另有说明，否则所有项目的优先级都是中。

要做到这一点，我们必须看看`OptionParser`之外，答案是用我们的默认属性初始化 options 结构。

```
defaults = { priority: :medium }
options = OpenStruct.new(defaults)
```

我特别喜欢我们在为命令行应用程序构建界面时定义帮助文本。当然，它不像 Rake 的`desc`那样精致，但是普通的老 Ruby 非常强大。

## 一抹亮色

想给终端增加一些输出吗？反馈对于我们的用户来说是必不可少的，让他们知道他们刚刚在那个可怕的命令行中输入了什么，实际上已经做了一些卓有成效的事情。当然，我们可以一直这样做，我们会的。但是在正常操作下，这只会用用户配置文件设置指定的颜色来触发文本。对于我们的 todo 应用程序，我们希望至少返回一条成功消息和基于优先级的项目彩色视图。红色代表高，以此类推。

对于第一遍，我们可以只使用普通的旧 ANSI 代码来格式化文本“puts”e[31m # { options . priority } e[0m”。但是说实话，当你破解了可怕的 ANSI 代码时，你已经放弃了你的命令行应用程序，并把它变成了基于云的。

令人欣慰的是，社区宝石来拯救我们了，有一大堆彩色图书馆供我们使用，所有这些图书馆都准备好了，可以随意进入。大概最广为人知的就是 [`term-ansicolor`](https://github.com/flori/term-ansicolor) 。然而，如今为了语法的简单，我倾向于使用 [`colored`](https://github.com/defunkt/colored) 来实现漂亮的语法:`puts "HIGH PRIORITY".red`。

## 结束命令行

到目前为止，我们已经建立了一个命令行界面，并添加了一些颜色，使其更具吸引力。如果我们想深入了解添加 todo 项的内部机制，我们可以简单地挂钩到 Ruby 类，这将是很好的测试驱动。当然，对于大多数简单的命令行应用程序来说,`OptionParser`已经足够了，但是随着你的工具套件继续增长，你会发现它变得越来越麻烦和痛苦。

再一次，ruby 社区的宝石财富是我们的救星。我们已经讨论了 Rake，但是我们还没有看到标准 Rails 安装中包含的“其他”命令行工具。 [`Thor`](https://github.com/wycats/thor) gem 在语法上类似于 Rake，但更强调通过约定编码，在继承自 Thor 的 Ruby 类中创建命令。创建命令行界面的一个类似的 DSL 方法是使用 GLI gem 。所有这些工具仅仅意味着 CLI 没有理由成为二等应用程序。

如果你做得好，你会在你的开发工作流程中得到一个真正灵活且长期有效的武器。如果你做得很好？然后它成为了 Ruby 社区的一部分。

## 分享这篇文章