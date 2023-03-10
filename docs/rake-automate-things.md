# Rake:自动化所有的事情

> 原文：<https://www.sitepoint.com/rake-automate-things/>

![Modern hi-tech background template in orange](img/2319dafb750fa33f7b407e8fe2e82c9f.png)

几年前，我收到一个朋友的电子邮件，说他正在学习 Rails，他认为我应该尝试一下。虽然我有很好的编程背景，但我的 web 开发技能很糟糕。我知道一些 HTML 和 CSS，但是如果你问我 POST 和 PUT 有什么共同点，我会告诉你它们都以 P 开头，以 t 结尾。

一位作者提出，掌握 Rails 需要掌握 web 开发的所有方面，但是从头开始学习一切的想法并没有让我兴奋。几天后，我在一个在线 Pastebin 应用程序上偶然发现了一些 Ruby 语法。我没有任何使用 Ruby 的经验，但是它的语法和风格是如此优雅和富有表现力，以至于我可以很容易地理解作者想要做什么。我开始摆弄 Ruby 书籍，并立刻爱上了这种编程语言。这就是我和露比的故事。

我知道你在想什么——“这跟耙子和轨道有什么关系？为什么我要把划痕这个词风格化？”

坦率地说，我讨厌从零开始。我不知道这是我一个人的问题，还是一个世界性的现象。当你第一次做一件事时，这叫做冒险。如果你决定再做一次，每个人都说这会增加你的经验。但在第三次运行时，它被证明是无聊的。

当您以开发人员、自由职业者或业余爱好者的身份步入 Ruby on Rails 世界时，您会立即感受到入门和运行的激动和兴奋

```
rails new webapp
```

但是，在你全力以赴设计你的新网络应用程序的惊人设计和概念之前，有一系列必要的，尽管看起来微不足道的细节需要解决。比如:

*   清理 gem 文件
*   初始化 git 存储库
*   为你的工具包如 Bootstrap 设置宝石
*   配置防护+火花等。

当然，这些步骤在你开发项目时对你是有益的，但是它们就像一个主要的障碍一样出现在你面前。处理这些琐事耗尽了我所有的兴奋和精力，让我跑去喝杯咖啡。

这就是 Rake 的用武之地。正如你可能从它的名字中猜到的，Rake 是一个受 Make 启发的应用程序，用 Ruby 编写，由 Jim Weirich 开发。Rake 是一个任务管理工具，可以做任何事情，从自动删除你的浏览器历史到构建人工智能。我只是在开玩笑；Rake 做不到这一点，但它功能强大。旨在从源文件构建可执行程序，它可以做得更多！

最初，Jim 不相信 Rake 会有用，但是 Ruby 社区很乐意通过将它与 Ruby 的标准库合并来证明他是错的。

> 好吧，让我从一开始就声明，我从来没有打算写这段代码。我不相信它有用，也不相信有人会对它感兴趣。我只能说为什么洋葱卡车一定是经过俄亥俄山谷。我在说什么？…一个 Ruby 版本的 Make。
> 
> 吉姆·威里奇

## Rake 入门

Rake 是一种嵌入式领域特定语言(EDSL ),因为在 Ruby 之外，它不存在。术语 EDSL 表明 Rake 是一种特定于领域的语言，嵌入在另一种范围更大的语言(Ruby)中。Rake 扩展了 Ruby，所以你可以使用 Ruby 附带的所有特性和扩展。您可以利用 Rake 来自动化一些不断挑战您的任务。正因为如此，你会发现使用它的新鲜感不会很快消失，你很快就会变得更有效率。

听起来很有趣？我们开始吧，好吗？

### 基础知识

这里有一些你以前可能见过的 rake 任务:

```
$ rake db:migrate

$ rake db:test:prepare
```

从命令行运行 rake 涉及到调用`rake`，后跟任务名称。随着任务数量的增加，维护它们和创建新的任务本身就变得相当乏味。Rake 允许您使用名称空间来组织您的任务，这使得创建多个具有相同名称的任务分配给不同的名称空间并避免命名冲突和歧义成为可能。

例如，您可以创建两个名为 cleanup 的任务。

1.  **main:clean up**–该任务清理您的 webapp 目录，删除无用文件和不必要的代码块。
2.  **temp:clean up**–这将清除 temp 目录下列出的所有文件和文件夹。
    这里是一个耙子任务的准系统结构。![image1](img/dace61e5f6fea3ca4aaf1629535d2df6.png)

它包括

*   命名空间块
*   关于任务的简短描述
*   任务的名称(注意，我使用了符号而不是字符串)
*   一个`do..end`块
*   任务应该执行的代码

当您调用时，`task :cleanup do..end`块之间的每段代码都会被执行

```
rake main:cleanup
```

如果您决定在`main`名称空间下添加另一个任务，那么它应该包含在外部的`do..end`块中。您可以在一个名称空间下添加任意数量的任务。

### 依赖与调用

任何构建工具的基本组成部分都是寻找依赖关系(也称为先决条件)并解决它们。一个任务可以有多个先决条件，它们应该在主任务之前执行。

**结构文件**

```
task :default => :third_task

task :first_task do
  puts "First task"
end

task :second_task do
  puts ">>Second task"
end

task :third_task => [:first_task, :second_task] do
  puts ">>>>Hurray. This is the third task"
  puts " This task depends on the first_task and second_task and won’t be executed unless both the dependencies are satisfied."
  print "The syntax for declaring dependency is "
  puts " :main_task => :dependency"
  puts "and if there are more than 1 dependency, place all the dependencies inside the [] separated by commas "
  puts ":main_task => [:dep1, :dep2]"
end
```

如果你想知道`:default`在那里做什么，这是一个红宝石符号，具有特殊的含义。默认情况下，在没有任何终端参数的情况下运行`rake`会执行`third_task`，因为我们已经用一个`:default`符号声明了它。

```
$ rake
```

有先决条件是有用的，但是如果我们能以传统的、直接的方式，比如从另一个任务中调用一个任务，这不是很棒吗？你可以做到这一点，而不必离开雷克的习惯用法。

```
namespace :main do
  task :test do
    if true
      puts "Calling test2 task."
      Rake::Task["main:test2"].invoke #invokes main:test2
    else
      abort()
    end
  end

  task :test2 do
      puts ">Test2 task invoked"
  end
end
```

如上所述，您甚至可以将任务调用放在条件语句下，在这种情况下，只有当条件为真时，它才会被执行。

### 参数化任务

如果我们想要，比如说，给我们的 Rake 任务传递参数，会怎么样呢？听起来不可能？不完全是。

```
task :tests, [:arg1, :arg2] do |t, args|
  puts "First argument: #{ args[:arg1] }"
  puts "Second argument: #{args[:arg2]}"
end
```

输出是:

```
$ rake tests[1,some_random_string]

First argument: 1

Second argument: some_random_string
```

太邪恶了，不是吗？参数放在[ ]内，用逗号分隔。它需要两个块变量:

*   对象是否与此任务相关
*   `args`是存储您的参数的散列

为本示例创建一个测试目录。

```
mkdir testapp && cd testapp
```

我们将在 **testapp** 中创建一个空的数据文件和一个 Rakefile，看看 Rake 能做什么。

```
$ ls
data.dat Rakefile
```

这是耙子

```
namespace :setup do
  desc "A test task to check whether a directory exists"
  task :check do
    puts "Enter the name of the destination directory: "
    @dir = STDIN.gets.strip  #Calling gets by itself would result in a call to "Kernel#gets" which is not what we want.

    if  File.directory?("../#{@dir}") #Checks whether the user requested directory exists and if not creates a new one.
      puts "The directory exists"
      setup_copy #Calls setup_copy method
    else
      puts "Creating the requested directory..."
      mkdir "../#{@dir}"
      setup_copy
    end
  end

  desc "A test task to copy things around"
  task :copy => :check do #A task dependency
    puts "Copying files..."
    cp_r '.', "../#{@dir}"
    puts "Done.! :)"
  end
end

def setup_copy
  Rake::Task["setup:copy"].invoke #A task invocation
end
```

为了即时的满足，试着跑步

```
rake setup:copy
```

从 **testapp** 目录。Rake 按照要求执行。

1.  当`setup:copy`被调用时，Rake 试图满足它的依赖关系`setup:check`。
2.  `setup:check`通过一个实例变量捕获目标目录的名称。在这种情况下使用局部变量意味着它的范围将被限制在特定的任务中。然而，让它对后续的子任务可用会节省我们大量的代码和时间。
3.  `if-else`块检查目录是否已经存在，如果条件不满足，就创建一个目录，并执行`setup_copy`函数。这证明了 Rake 对 Ruby 的顺从。
4.  控制返回到`setup:copy`。`cp_r`方法复制当前目录中的所有文件，并将它们放在用户请求的文件夹中。(`cp_r`中的“r”负责递归复制文件，这样就不会遗漏任何东西。)

注意:我不确定你是否注意到了，但是我们不需要任何标准库文件或者加载任何扩展来使`cp_r`和`mkdir`在我们的 Rakefile 中可访问。这是因为，默认情况下，Rakefile 会加载“fileutils”库。我强烈推荐浏览 fileutils 上的 [ruby 文档。在教程的某个阶段，你肯定会发现它很有用。](http://www.ruby-%20doc.org/stdlib-2.0/libdoc/fileutils/rdoc/FileUtils.html)

任务调用和依赖关系不会导致利益冲突吗？我想你可能会问些类似的问题。

不。他们可以一起生存而不互相殴打。我们将坚持使用任务作为先决条件，这样我们可以保持代码的明显和可触知。但是，你也可以反过来做。

假设您有下面代码片段中描述的 4 项任务:

```
namespace :setup do
  task :init do
    puts ">init: Task to initiate a process"
    puts ">Imagine that all other tasks depend on this task"
  end

  task :cleanup => :init do #This task depends on :init
    puts ">>cleanup:Tasks related to cleaning up Gemfile, deleting public/index.html etc."
  end

  task :git=> :init do #This one too depends on :init
    puts ">>>git: Tasks concerned with setting up GIT repository"
  end

  task :all => [ :init, :cleanup, :git ] do #Depends on init, cleanup and git tasks.
    puts ">>>>all: Done"
  end
end
```

试试跑步

```
rake setup:all
```

看看效果如何。

最后提到的命令连续执行所有的任务，因为我们已经用 3 个先决条件填充了那个任务，即`:init`、`:cleanup`和`:git`。太酷了。但是，如果我们想在不清理 Gemfile 的情况下建立 git repo 呢？

这也是可以实现的。继续把这个输入你的终端。

```
rake setup:git
```

由于`setup:init`是创建 git repo 任务的先决条件，它将被执行。

```
Initializing..

Tasks concerned with setting up GIT repository.
```

## 耙子和轨道:完美的组合

我们几乎完成了对耙子基础知识的学习。关于我们将要构建的脚本的设计，还有一些小问题需要讨论。

通常，开发人员倾向于拥有一个 Rails 专用的目录，在这个目录中，他们创建新的项目并维护现有的项目。有点像下面的截图。

![image2](img/edd6011c5cdb26d99b66d62794d73ebb.png)

那很好。我们将在~/Rails 目录下创建一个名为 **Rake** 的文件夹，在我看来，这是放置 Rakefile 和所有相关文件的理想位置。

```
$ cd ~/Rails

$ mkdir Rake
```

在~/Rails 下创建一个新的 Rails 应用程序，比如说 **testapp** 。(使用`rails new`命令)

```
$ ls

testapp Rake
```

我们的 **Rake** 目录和 Rails 应用目录是一个级别的。那么，我们开始吧。

Rakefile 和所有相关的文件将存放在 **Rake** 目录中。另一端是 **testapp** 目录，这是我们计划操作的目标目录。任务将启动这个过程。它会将驻留在 **Rake** 目录中的所有文件复制到目标目录的 **lib/tasks** 文件夹中。每个通过`generate`脚本生成的 Rails 应用程序都配有一个 **lib/tasks** 文件夹。Rails 惯例是将所有 Rake 任务放在这个目录中，默认情况下，这些任务可以从 Rails 应用程序目录的根目录访问。

![image3](img/4428b325b523ff26038908b07c9a109b.png)

下面是一个改进的:init 任务的草图。

```
namespace :setup do
  desc "Initiate setup. This task serves as a dependency for other tasks."
  task :init do
    print "Name of the destination directory: "
    name = STDIN.gets.strip
    if pwd().split('/').last == "Rake"
      puts "Copying the files to #{name}/lib/tasks."
      # Copying the rakefile over to lib/tasks of the destination directory. 
      # This is a Rails generated directory dedicated for rake tasks.
      cp_r '.', "../#{name}/lib/tasks", verbose: false 

      print "Do you want to continue with the setup?(y/n): "
      option = STDIN.gets.strip

      case option
      when /[^Yy]/
        abort_message #A call to the abort_message method.
      end
      # change directory to the root of the Rails application directory
      cd "../#{name}"
    end
  end

  def abort_message
    abort("Exiting. You can each task individually. See rake -T for more info") #A handy method to exit early from a rake task. You can read more about it here.
  end
end
```

除了一些 Ruby 构造，你应该不会有太多的解码问题。尽管在 **lib/tasks** 中保存 Rake 文件不是强制性的，但在开发阶段的后期，这可能会变得有利。

但是，有一个问题。如果您计划从下面演示的 **testapp** 目录中调用定制的 Rake 任务，它将不起作用。

```
$ cd testapp
rake setup:init

rake aborted!
Don't know how to build task 'setup:init'
```

虽然您可以从 **Rake** 目录调用任务而不会遇到任何问题，但是从 web 应用程序的根目录执行自定义 Rake 任务将会导致错误。Rake 将忽略当前放置在 **lib/tasks** 中的所有文件，包括 Rakefile，因为它将只加载具有“.耙子”分机。为了解决这个问题，我们可能需要调整我们的 Rakefile。目前，我们将让这件事过去，以后再处理它。

接下来，我们有清理任务。在开始实际开发应用程序之前，您可以设计这个任务来执行各种讨厌的清理工作。和大多数开发人员一样，我做的第一件事是编辑我的 gem 文件，删除注释的代码行，添加 gem，没有它们我很难生存。

```
desc "Cleanup Gemfile and other stuff."
task :cleanup => :init do
  print "Clean up the standard Gemfile for a new one?(y/n): "
  option = STDIN.gets.strip

  case option
     when /[^Yy]/
       abort_message
  end

  puts "Setting a new Gemfile."
  cp 'lib/tasks/Gemfile', '.', verbose: false #Copying the gemfile from lib/tasks to the root of the project directory.

  puts "Running bundle install. This may take a while...\n\n"
  sh "bundle install" #sh bridges your task with the command-line, bestowing upon you the access to all the terminal commands.
end
```

这是 gem 文件的编辑版本。

```
source 'https://rubygems.org'
ruby '2.0.0'

gem 'rails', '4.0.0'

group :development, :test do
  gem 'sqlite3', '1.3.7'
end

group :test do
  #test
end

gem 'sass-rails', '4.0.0'
gem 'uglifier', '2.1.1'
gem 'coffee-rails', '4.0.0'
gem 'jquery-rails', '2.2.1'
gem 'turbolinks', '1.1.1'
gem 'jbuilder', '1.0.2'

group :doc do
  gem 'sdoc', '0.3.20', require: false
end

group :production do
  #production
end
```

我们将把好看的 Gemfile 放在我们的 **~/Rails/Rake** 目录中，Rake 会处理剩下的事情。命令`rake setup:init`将把 gem 文件(连同 Rakefile)复制到 **lib/tasks** 中，而`:cleanup`将把它移动到根目录，替换原来的 gem 文件。

以下是我对与 git 初始化、git repo 创建以及将应用程序推入 github 存储库相关的任务的实现:

```
desc "Git tasks"
task :git => :init do
  print "Create a new GIT repository?(y/n): "
  option = STDIN.gets.strip

  case option
   when /[^Yy]/
     abort_message
  end

  sh 'git init'
  puts "Adding a few items to .gitignore."
  cp 'lib/tasks/.gitignore', '.', verbose: false

  puts "Setting up git"
  sh 'git add .'
  sh 'git commit -m "Init" '

  puts "Enter the link to your repository for this app."
  repo = STDIN.gets.strip
  sh "git remote add origin #{repo}"
  sh 'git push -u origin master'
end
```

由于您已经通过 [sh 方法](http://rake.rubyforge.org/classes/FileUtils.html)掌握了所有的终端命令，因此您可以编写一个任务来轻松创建 git 分支:

```
desc "Create a git branch"
task :git_branch => :git do
  puts "Creating a git branch, just to be safe!"
  sh 'git checkout -b Pre-development'
end

desc "Run all setup tasks"
task :all => [:init, :cleanup, :git, :git_branch] do
  puts "DOne"
end
```

如果给我们之前的准系统任务添加一点肉，这就是它们的样子:

```
namespace :setup do
  desc "Initiate setup. This task serves as a dependency for other tasks."
  task :init do
    print "Name of the destination directory: "
    name = STDIN.gets.strip
    #Calling gets by itself would result in a call to Kernel#gets which is not what we want. Switch to STDIN.gets instead.
    if pwd().split('/').last == "Rake"
      puts "Copying the files to #{name}/lib/tasks."
      # Copying the rakefile over to lib/tasks of the destination directory. This is a Rails generated directory dedicated for rake tasks.
      cp_r '.', "../#{name}/lib/tasks", verbose: false 
      print "Do you want to continue with the setup?(y/n): "
      option = STDIN.gets.strip

      case option
      when /[^Yy]/
        abort_message #A call to the abort_message method.
      end
      cd "../#{name}" #change directory to the root of the Rails app directory
    end
  end

  def abort_message
    abort("Exiting. You can each task individually. See rake -T for more info") #A handy method to exit early from a rake task. You can read more about it here.
  end

  desc "Cleanup Gemfile and other stuff."
  task :cleanup => :init do
    print "Clean up the standard Gemfile for a new one?(y/n): "
    option = STDIN.gets.strip

    case option
    when /[^Yy]/
      abort_message
    end

    puts "Setting a new Gemfile."
    # Copying the gemfile from lib/tasks to the root of the project directory.
    cp 'lib/tasks/Gemfile', '.', verbose: false

    puts "Running bundle install. This may take a while...\n\n"
    #sh bridges your task with the command-line bestowing upon you, the access to all the terminal commands.
    sh "bundle install" 
  end

  desc "Git tasks"
  task :git => :init do
    print "Create a new GIT repository?(y/n): "
    option = STDIN.gets.strip

    case option
      when /[^Yy]/
       abort_message
    end

    sh 'git init'
    puts "Adding a few items to .gitignore."
    cp 'lib/tasks/.gitignore', '.', verbose: false

    puts "Setting up git"
    sh 'git add .'
    sh 'git commit -m "Init" '

    puts "Enter the link to your repository for this app."
    repo = STDIN.gets.strip
    sh "git remote add origin #{repo}"
    sh 'git push -u origin master'
  end

  desc "Task to create a new git branch"
  task :git_branch => :git do
    puts "Creating a git branch, just to be safe!"
    sh 'git checkout -b Pre-development'
  end

  desc "Task to run all tasks under the setup namespace"
  task :all => [:init, :cleanup, :git, :git_branch] do
    puts "Done"
  end
end
```

代码太多了。然而，弄清楚它做了什么是相当容易的，因为 Ruby 并没有把事情复杂化，而是把它简化到一个完全陌生的人也能理解的程度。

## 不要忘记擦干你的代码

在我看来，耙子文件有点杂乱。因为它很快会变得非常糟糕，我们应该重构并清理一下。理想的解决方案是将任务整齐地分类到单独的文件中，并导入到主 Rakefile 中，如下所示:

```
Dir.glob('*.rake').each { |r| import r }
```

### 初始化耙子

```
namespace :setup do
  desc "Initiate setup. This task serves as a dependency for other tasks."
  task :init do
    print "Name of the destination directory: "
    name = STDIN.gets.strip
    if pwd().split('/').last == "Rake"
      puts "Copying the files to #{name}/lib/tasks."
      cp_r '.', "../#{name}/lib/tasks", verbose: false

      print "Do you want to continue with the setup?(y/n): "
      option = STDIN.gets.strip

      case_code(option)
      cd "../#{name}"
    else
      puts "We are already on the destination directory"
    end
  end

  def abort_message
    abort("Exiting. You can each task individually. See rake -T for more info")
  end

  def case_code(option)
    case option
      when /[^Yy]/
        abort_message
    end
  end
end
```

### 清理.耙子

```
namespace :setup do
  desc "Cleanup Gemfile and other stuff."
  task :cleanup => :init do
    print "Clean up the standard Gemfile for a new one?(y/n): "
    option = STDIN.gets.strip

    case_code(option)
    puts "Setting a new Gemfile."
    cp 'lib/tasks/Gemfile', '.', verbose: false

    puts "Running bundle install. This may take a while...\n\n"
    sh "bundle install"
  end
end
```

### 走吧，雷克斯

```
namespace :setup do
  desc "Git tasks"
  task :git => :init do
    print "Create a new GIT repository?(y/n): "
    option = STDIN.gets.strip

    case_code(option)
    sh 'git init'
    puts "Adding a few items to .gitignore."
    cp 'lib/tasks/.gitignore', '.', verbose: false

    puts "Setting up git"
    sh 'git add .'
    sh 'git commit -m "Init" '

    puts "Enter the link to your repository for this app."
    repo = STDIN.gets.strip
    sh "git remote add origin #{repo}"
    sh 'git push -u origin master'
  end

  task :git_branch => :git do
    puts "Creating a git branch, just to be safe!"
    sh 'git checkout -b Pre-development'
  end
end
```

### 所有耙子

```
namespace :setup do
  task :all => [:init, :cleanup, :git, :git_branch] do
    puts "Done"
  end
end
```

这对我们有两方面的好处，

1.  我们的 Rake 任务现在已经组织好了，很容易访问。
2.  您可以在 web 开发的任何阶段从项目的根目录调用这些任务。如果你还记得，我曾经提到过，驻留在 **lib/tasks** 中的文件只有在扩展名为`.rake`的情况下才会被加载。Rake 之前忽略了我们的自定义任务，因为我们已经将它们存储在 Rakefile 中。

Rake 可以自动处理大量你认为太琐碎或太复杂的任务。撰写本教程的目的是让您开始使用 Rake，通过减少人工干预来简化您的生活。既然您已经看到了 Rake 的实际应用，那么您应该不难拓宽自己的视野并充分利用它。

## 分享这篇文章