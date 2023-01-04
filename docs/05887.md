# Ruby 命令行界面 Gems

> 原文：<https://www.sitepoint.com/ruby-command-line-interface-gems/>

![Computer Monitor](img/8920f9837a38220edadfbee021a5239f.png)

所以你写了这个神奇的库，但是缺少了一些东西…一个命令行界面！Ruby 非常适合这类事情。虽然有基本的`ARGV`方法，但是有一些很棒的 RubyGems 可以帮助轻松创建命令行界面(CLI)。在本文中，我们将看看几个 Ruby CLI gems。

### 先决条件

*   Ruby(至少 1.9.3，但建议高于 2.0.0)
*   在这篇文章中，我提到了各种 RubyGems。预计运行`$ gem install GEMNAME`不会有太多麻烦。

### 技巧

*   大多数 Ruby 项目都有一个存储 CLI 二进制文件的`bin`目录。(是的，我知道它在技术上不是真正的“二进制文件”)
*   如果您最终一字不差地使用了这段代码，记得让您的文件可执行。
*   你可能需要为你的二进制文件`#!/usr/bin/env ruby`准备一个 [Ruby shebang](http://www.cyberciti.biz/tips/finding-bash-perl-python-portably-using-env.html) 。
*   尽量保持二进制文件中的代码尽可能简洁。这不仅有助于你以后修复 bug，也有助于合作者。
*   下面的 CLI gems 有一个代码块，您可以在其中传递命令运行时要执行的代码。试着把它简化为一个方法调用。可能将选项从命令行传递到您的方法中的选项散列中，在那里选项被实际解析。

## 内置 ARGV

`ARGV`是 Ruby 中的内置数组，用于解析命令行参数。例如，`ARGV[0]`返回传递给 Ruby 脚本的第一个参数。对于极其简单的参数解析，您可以尝试如下方式:

```
puts "Hello #{ARGV[0]}!"
```

```
$ ./my-app world
Hello world!
```

一个很简单的例子，但是相当强大。这种类型的参数读取的唯一问题是错误处理。特别是在没有给出所需参数的情况下。这很容易通过使用简单的 Ruby 解决(参见[数组文档](http://www.ruby-doc.org/core-2.1.0/Array.html)和 [ARGV 文档](http://www.ruby-doc.org/core-2.1.0/ARGF.html))。我不会对`ARGV`太深入，但你可以看到可能性。请记住`ARGV`只是一个数组，所以在读取参数和选项时，您应该像对待数组一样对待它。

```
command = ARGV[0]
name = ARGV[1]

name = "world" unless name

case command
when "hello"
  puts "Hello #{name}!"
when "goodbye"
  puts "Goodbye #{name}!"
end

# And so on...
```

## 托尔

你可能听说过雷神托尔。如果你看一下[的 RubyGems 统计](http://rubygems.org/stats)，你会看到托尔在顶部。由于它的可扩展性，这也是我个人的最爱。以下是一些基本语法:

CLI 封装在一个继承自`Thor`超类的类中。

```
require "thor"

class MyApp < Thor

end
```

通过向类添加方法来添加命令。

```
require "thor"

class MyApp < Thor
  def hello
      puts "Hello world!"
  end
end
```

在文件的底部(通常)，调用`MyApp#start`以允许它启动 CLI。

```
# ... (MyApp class code above)

MyApp.start

$ ./my-app hello
Hello world!
```

我们可以通过简单地向方法添加参数来添加参数。

```
require "thor"

class MyApp < Thor
  desc "hello", "Say hello"
  def hello(name)
    puts "Hello #{name}"
  end
end
```

```
$ ./my-app hello universe
Hello universe!
```

或者我们可以用期权做同样的事情。

```
require "thor"

class MyApp < Thor
  desc: "Say Hello"
  method_option :name, :aliases => "-n", :desc => "Specify a name"
  def hello
    puts "Hello #{options[:name]}"
  end
end
```

您可以通过添加到`method_option`来指定选项的类型。文档可以在[这里](https://github.com/erikhuda/thor/wiki/Method-Options)找到。例如，在上面的示例代码中，我们创建了一个选项`name`，它的别名是`-n`，描述是`"Specify a name"`。

你可能已经注意到我们已经使用了几次`desc`。这是 Thor 的另一个很酷的特性，它是自文档化的。如果您要运行`my-app help hello`，那么您会看到一些关于它的文档。

```
$ ./my-app help hello
Usage:
  my-app hello

Options:
  -n, [--name=NAME]  # Specify a name

Say hello
```

这是我研究过的很多客户端的一个非常酷的特性。Thor 使用了`desc`选项，但是正如你将看到的，其他宝石使用不同的记录选项。

总而言之，Thor 是一个很棒的工具，我相信它也支持 Rails 命令行界面。内置的自文档是一个不错的选择，我只是触及了它所有特性的表面。如果你想看完整的文档，你真的应该看看它的 wiki。SitePoint 上还有一个[非常不错的教程](https://www.sitepoint.com/introduction-thor/)。

## 指挥官

Commander 是一个用于编写 CLI 应用程序的成熟套件。我在里面写了一个 CLI，[杰基尔](http://jekyllrb.com/)也用它。它有一个简洁的 DSL，并且像 Thor 一样，是自文档化的。它也有非常好的错误处理，而且非常简单，你可以在 15 秒内创建一个命令行程序。

要创建一个新的 CLI，进入你的应用程序的`bin/`目录(或者任何你需要它的地方)，然后运行`$ commander init my-app`(语法:`$ commander init FILENAME`)。

您将得到一些信息提示:

```
$ commander init my-app
Machine name of program: my-app
Describe your program: Does so many things.
List the commands you wish to create: hello goodbye # Notice no commas
Initialized template in my-app
```

这将创建文件`my-app`,其内容如下:

```
#!/usr/bin/env ruby

require 'rubygems'
require 'commander/import'

program :version, '0.0.1'
program :description, 'Does oh so many things.'

command :hello do |c|
  c.syntax = 'my-app hello [options]'
  c.summary = ''
  c.description = ''
  c.example 'description', 'command example'
  c.option '--some-switch', 'Some switch that does something'
  c.action do |args, options|
    # Do something or c.when_called My-app::Commands::Hello
  end
end

command :goodbye do |c|
  c.syntax = 'my-app goodbye [options]'
  c.summary = ''
  c.description = ''
  c.example 'description', 'command example'
  c.option '--some-switch', 'Some switch that does something'
  c.action do |args, options|
    # Do something or c.when_called My-app::Commands::Goodbye
  end
end
```

如果您立即进入您的终端，您现在有一个什么也不做的 CLI。

```
$ ./my-app --help
  NAME:

    my-app

  DESCRIPTION:

    Does oh so many things.

  COMMANDS:

    goodbye                             
    hello                               
    help                 Display global or [command] help documentation.

  GLOBAL OPTIONS:

    -h, --help 
        Display help documentation

    -v, --version
```

接下来，我们将编辑该文件，使其具有以下功能:

```
$ ./my-app hello world
Hello world!

$ ./my-app goodbye -n universe
Goodbye universe!
```

对于第一个命令`hello`，我们将一个*参数*作为名称。第二个命令`goodbye`，使用一个*选项*作为名称。

开始了。

```
#!/usr/bin/env ruby

require 'rubygems'
require 'commander/import'

program :version, '0.0.1'
program :description, 'Does oh so many things.'

command :hello do |c|
  c.syntax = 'my-app hello NAME [options]'
  c.summary = 'Says hello'
  c.description = c.summary # Because we're lazy
  c.example 'Says hello to "world"', 'my-app hello world'
  c.action do |args, options|
    name = args.shift # or args.first or args[0] if you wish
    puts "Hello #{name}!"
    # Or in a real app, you would call a method and pass command line arguments to it.
  end
end

command :goodbye do |c|
  c.syntax = 'my-app goodbye NAME [options]'
  c.summary = 'Says goodbye'
  c.description = c.summary
  c.example 'Say goodbye to world', 'my-app goodbye -n world'
  c.example 'Say goodbye to world', 'my-app goodbye --name world'
  c.option '-n', '--name NAME', String, 'Specify a name' # Option aliasing
  c.action do |args, options|
    puts "Hello #{options.name}!"
  end
end
```

默认情况下，选项是布尔值。因此，当我们向它添加`String`类型时，它允许用一个参数调用选项。您也可以这样定义一个选项:

```
# ...
  c.option '--option', 'A boolean by default'
# ...
```

Commander 还内置了 HighLine，所以你可以做像这样很酷的事情:

```
ask_for_array "Commands: " # => Commands: hello goodbye
# => ["hello", "goodbye"]
say "Hello!" # => Hello!
ask "Password: " { |char| char.echo = "*" } # => Password: ***********
# => "supersecret"
```

当然你可以用指挥官做无数的事情，就像用雷神一样。你可以在这里查看
[的那些。](https://github.com/visionmedia/commander/blob/master/README.md)

## 溢出

Slop 是另一种在 Ruby 中创建 CLI 应用程序的 DSL。你可能已经从其他 gem 的依赖者那里得到了它(就像 thor 和 commander 一样)。好像很受欢迎，那我们就来跳一跳吧！

除非你要求，否则流食是很少的，对你没什么用。让我们从添加一个版本命令开始。这将是`-v`和`--version`。

```
require "slop"

Slop.parse do
  on "-v", "--version" do
    puts "my-app v0.0.1"
  end
end
```

让我们创建与之前相同的应用程序，但是使用 slop。

```
require "slop"

opts = Slop.parse do
  on "-v", "--version" do
    puts "my-app v0.0.2"
  end

  command 'hello' do
    run do |opts, args|
      puts "Hello #{args.shift}"
    end
  end

  command 'goodbye' do
    on 'n=', 'name='

    run do |opts, args|
      puts "Goodbye #{opts[:name]}"
    end
  end
end
```

这是极其微小的。CLI 甚至不关心选项的参数是否为空！但是，这就是 Slop 的要点:简单的选项解析。这是我最喜欢泔水的地方之一。如果指挥官是 Rails，那么 Slop 就是 Sinatra。

我建议简单的 CLI 用 Slop，复杂的用 Commander。你真的应该看看它的自述文件，看看它的全部特性和选项。

## 结论

有很多优秀的工具可以用来构建命令行界面。你选择哪一个完全取决于你的风格。有 Ruby-like (Thor)、minimal (Slop)和 full on suite (Commander) CLI 框架供您使用。最后，您决定使用什么类型的库来构建 CLI(如果有的话)( Homebrew 使用`ARGV`选项！).还有很多我在本文中没有提到的，但我希望这能让您对一些很好的选择有一个基本的了解。

## 分享这篇文章