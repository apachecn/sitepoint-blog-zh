# 雷神简介

> 原文：<https://www.sitepoint.com/introduction-thor/>

![256px-Ruby_logo.svg](img/022aafa2bd6cce71639b79a370ce22b3.png)

查看 RubyGem 的“下载次数最多”的宝石列表，我们注意到许多熟悉的名字:“rake”、“rails”、“rack”、“actionmailer”等等。但是，《雷神》是个例外；我们中很少有人听说过它，用它编写代码的人就更少了。那么，雷神到底是什么？

这是用 Ruby 编写强大命令行工具的一种方式。它使参数解析变得非常容易，并为命令行参数指定了特定的格式。大量的 Ruby 项目使用 Thor 使编写命令行实用程序(例如“rails”命令)变得简单、快捷和有趣。

但是，为了理解为什么雷神如此令人敬畏，我们必须首先理解它解决了什么样的问题。

## 黄金时代

当动态语言是为那些不懂指针的人设计的时候，我们有一个叫做 [getopt_long](http://linux.die.net/man/3/getopt_long) 的过程来解析命令行选项。实际使用这个函数所需的代码通常是一个很大的`switch`语句，一旦写出来，就会被遗忘。当然，直到有一天不得不增加另一个选项。请看一个例子:

```
while (1)
         {
           static struct option long_options[] =
             {
               /* These options set a flag. */
               {"verbose", no_argument,       &verbose_flag, 1},
               {"brief",   no_argument,       &verbose_flag, 0},
               /* These options don't set a flag.
                  We distinguish them by their indices. */
               {"add",     no_argument,       0, 'a'},
               {"append",  no_argument,       0, 'b'},
               {"delete",  required_argument, 0, 'd'},
               {"create",  required_argument, 0, 'c'},
               {"file",    required_argument, 0, 'f'},
               {0, 0, 0, 0}
             };
           /* getopt_long stores the option index here. */
           int option_index = 0;

       c = getopt_long (argc, argv, "abc:d:f:",
                        long_options, &option_index);

       /* Detect the end of the options. */
       if (c == -1)
         break;

       switch (c)
         {
         case 0:
           /* If this option set a flag, do nothing else now. */
           if (long_options[option_index].flag != 0)
             break;
           printf ("option %s", long_options[option_index].name);
           if (optarg)
             printf (" with arg %s", optarg);
           printf ("\n");
           break;

         case 'a':
           puts ("option -a\n");
           break;

         case 'b':
           puts ("option -b\n");
           break;

         case 'c':
           printf ("option -c with value `%s'\n", optarg);
           break;

         case 'd':
           printf ("option -d with value `%s'\n", optarg);
           break;

         case 'f':
           printf ("option -f with value `%s'\n", optarg);
           break;

         case '?':
           /* getopt_long already printed an error message. */
           break;

         default:
           abort ();
         }
     }
```

就算你这辈子没看过 C 代码，看起来也不是什么愉快的经历。所以，他们想出了库来使这个操作简单一点。在 Perl 社区中，“成熟”包括编写自己的 command-opts 解析器。事实证明，Perl 在命令行实用程序中的大量使用源于其令人羡慕的 GetOpts::Long 模块。看看 Perldoc (Perl 的文档系统)中的这个例子:

```
use Getopt::Long;
my $data = "file.dat";
my $length = 24;
my $verbose;

GetOptions("length=i" => \$length, 
          "file=s" => \$data,
          "verbose" => \$verbose) or die("Error in command line arguments");
```

基本上，这段代码允许您指定长度、数据和详细选项，以及数字、字符串和布尔值。但是，这似乎还是不对。感觉不是很干净或面向对象，因为我们只是把东西塞进变量。

Thor 是进行命令行选项解析的“Ruby 方式”。它使一切都非常面向对象，并抽象出细节。这使您可以专注于您的伟大想法，而不是在选项和字符串解析上浪费时间。

让我们来看一个具体的例子，看看托尔是如何施展它的魔法的。

## 与托尔的第一步

让我们直接看一个简单的例子:

```
require 'thor'

class SayHi < Thor
  desc "hi NAME", "say hello to NAME"

  def hi(name)
    puts "Hi #{name}!"
  end
end

SayHi.start(ARGV)
```

如果您不带任何参数运行此命令，您应该得到类似如下的输出:

```
Commands:
  first_steps.rb help [COMMAND]  # Describe available commands or one specific command
  first_steps.rb hi NAME         # say hello to NAME
```

只需几行代码，我们就有了我们创建的命令的完整描述！当然，如果您运行带有参数“hi Dhaivat”的脚本，您应该得到一个回应“Hi Dhaivat！”。让我们分解代码。

我们创建了一个名为“SayHi”的类，它是从 Thor 类派生而来的。然后，下一行讲述描述一个特定的命令。“desc”函数的第一个参数是“hi NAME”，它描述了我们需要什么样的命令。在这种情况下，它说我们希望用户能够输入“hi ”,然后输入他们的名字，这将作为一个变量传入。另一个例子是“位置纬度经度”，用户可以传入“位置 64.39 21.34”，位置命令将接收由这些数字指定的纬度和经度。

我说的接收到底是什么意思？一旦我们传递了一个参数，Thor 就会计算出它适合哪种格式，并从我们的“SayHi”类中调用该方法。在这种情况下，它用“name”作为参数调用“hi”方法。

最后，我们有我们的“hi”方法，它只是标准的 Ruby 代码。

Thor 应用程序通常遵循这种模式；有很多特性可以使用，但是基本的概念是一样的。

让我们编写一个名为 file-op 的小工具，它有一个命令行选项来输出文件的内容。注意:我没有将这个实用程序命名为“cat ”,因为这个世界上有太多它的实现。

```
require 'thor'

class FileOp < Thor
  desc 'output FILE_NAME', 'print out the contents of FILE_NAME'

  def output(file_name)
    puts File.read(file_name)
  end
end

FileOp.start(ARGV)
```

它与“SayHi”示例的概念几乎相同，但是这次如果您使用“output FILENAME”运行它，它将输出一个文件的内容(这是通过“FileOp”类的“output”方法处理的)。

## 深入挖掘一下

雷神最牛逼的一个功能就是自动“帮助生成”。早先使用的“desc”方法有第二个参数，它实际上描述了每个命令的目的。因此，使用我们的文件操作实用程序，如果运行:

```
ruby file-op.rb help output
```

Thor 打印出一份精美的使用说明:

```
Usage:
  file_op_v1.rb output FILE_NAME

print out the contents of FILE_NAME
```

我们不仅清楚地记录了代码中每个命令做了什么，用户也知道每个命令做了什么！

显然，很少有重要的命令行应用程序会对这种基本的选项结构感到满意。幸运的是，托尔也支持我们。如果我们可以在“output”命令中添加一个标志，将文件输出到 stderr，这不是很好吗？我知道，这不会真的那么好，但我们还是做吧:

```
require 'thor'

class FileOp < Thor
  desc 'output FILE_NAME', 'print out the contents of FILE_NAME'
  option :stderr, :type => :boolean

  def output(file_name)
    #options[:stderr] is either true or false depending
    #on whether or not --stderr was passed
    contents = File.read(file_name)
    if options[:stderr]
      $stderr.puts contents
    else
      $stdout.puts contents 
    end
  end
end

FileOp.start(ARGV)
```

我们添加了几行，但最重要的是`option :stderr`。这一行告诉 Thor 我们刚刚定义的任何命令(即本例中的“output ”)都可以有一个作为布尔值传入的标志。换句话说，要么传入，要么不传入；它不像`--times 15`那样有另一个值。所以，我们可以运行:

```
ruby file-ops-v2.rb output --stderr filename
```

它会将“filename”的内容打印到 stderr。

但是，这整个“选择”的事情让托尔有点奇怪。它如何知道我们将选项添加到哪个命令？每当你使用`option`时，它指的是它之前的`desc`调用。让我们向我们的`file-op`实用程序添加另一个命令，它只是创建一个空文件。这相当于*nix 系统上的 touch，所以我们将该命令称为“touch”:

```
require 'thor'

class FileOp < Thor
  desc 'output FILE_NAME', 'print out the contents of FILE_NAME'
  option :stderr, :type => :boolean
  def output(file_name)
    #options[:stderr] is either true or false depending
    #on whether or not --stderr was passed
    contents = File.read(file_name)
    if options[:stderr]
      $stderr.puts contents
    else
      $stdout.puts contents 
    end
  end

  desc 'touch FILE_NAME', 'creates an empty file named FILE_NAME'
  option :chmod, :type => :numeric
  def touch(file_name)
    f = File.new(file_name, "w")
    f.chmod(options[:chmod]) if options[:chmod]
  end
end

FileOp.start(ARGV)
```

实现非常简单。我所做的就是用`output`代码和*结束，然后*调用`desc`进行“触摸”。

作为一个额外的奖励，我甚至包括了一个`--chmod`选项，允许你设置你“接触”的文件的权限(例如，传入一个 000 实际上创建了一个锁定的文件)。就个人喜好而言，我不喜欢 Thor 保持`option`和它所影响的方法之间的联系的方式，尽管我不确定是否有其他更好的解决方案。

## 类别选项

可以应用于任何命令的命令呢？类似于:

```
some_utility.rb -v
```

托尔掩护我们。这些选项与特定命令无关，称为*类选项*。当我们的实用程序运行时，我们需要更多的信息。但是，我们通常不想要这些信息，所以我们将包括一个详细选项:

```
require 'thor'

class FileOp < Thor
  class_option :verbose, :type => :boolean
  desc 'output FILE_NAME', 'print out the contents of FILE_NAME'
  option :stderr, :type => :boolean
  def output(file_name)
    log("Starting to read file...")
    #options[:stderr] is either true or false depending
    #on whether or not --stderr was passed
    contents = File.read(file_name)
    log("File contents:")
    if options[:stderr]
      log("(in stderr)")
      $stderr.puts contents
    else
      log("(in stdout)")
      $stdout.puts contents 
    end
  end

  no_commands do 
    def log(str)
      puts str if options[:verbose]
    end
  end

  desc 'touch FILE_NAME', 'creates an empty file named FILE_NAME'
  option :chmod, :type => :numeric
  def touch(file_name)
    log("Touching file...")
    f = File.new(file_name, "w")
    f.chmod(options[:chmod]) if options[:chmod]
  end
end

FileOp.start(ARGV)
```

如果您运行以下命令:

```
ruby file_op_v5.rb output some_file --verbose
```

除了文件内容之外，您还应该看到一些日志消息。让我们看一下代码。

我们已经添加了`log`方法，但是它包含在一个`nocommands`块中。为了告诉托尔`log`与命令无关，我们必须把它放在这个块中。在`log`方法中，如果 Thor 接收到`--verbose`，我们打印出给定的字符串。然后我们在输出和触摸命令中使用这个方法。

## 包装它

Thor 是一个非常通用的库，它使得命令行解析变得简单而直观。然而，有一些小问题，比如`nocommands`阻塞，应该注意以避免潜在的陷阱。

我在许多不同的地方使用过 Thor，很多次都是为了移动一些文件或者搜索一些数据。我发现它是一个非常有用的工具，希望你也会如此。

这篇文章的来源可以在这里找到。

## 分享这篇文章