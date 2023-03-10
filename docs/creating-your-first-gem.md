# 创造你的第一颗宝石

> 原文：<https://www.sitepoint.com/creating-your-first-gem/>

你在红宝石世界里随处可见宝石。它们是几乎所有 Ruby 应用的支柱。我承认我第一次尝试制作宝石时有点害怕，但我很快发现这非常简单。在这个博客系列中，我将从介绍从头创建一个 gem 的基础开始，然后转到更高级的主题，包括 gem 生成工具和 Rails 引擎。首先，对于你们这些 Ruby 新手来说:什么是宝石？简单来说就是打包的 Ruby 代码。最起码，一个 gem 包括一个 Ruby 文件和一个 gemspec。gemspec (gem 规范)描述了 gem，RubyGems 包管理器使用它来安装 gem。

## RubyGems

RubyGems 包管理器可以下载并安装 Gems 到您的系统，并允许您在其他 Ruby 程序中使用 gems。Ruby 1.9 默认安装了 RubyGems。如果你使用的是 Ruby 1.9 之前的版本，你可以在这里下载 Ruby gems。要在 Ruby 1.9 之前的应用程序中使用 RubyGems，你需要在你的应用程序中添加这一行
:

```
require 'rubygems'
```

Ruby 1.9 不需要这一行，因为 RubyGems 包含在语言中。

## 宝石规格

正如我之前提到的，gem 规范描述了 gem。让我们来看一个基本的 gemspec 文件:

```
Gem::Specification.new do |s|
  s.name = %q{my_gem}
  s.version = "0.0.1"
  s.date = %q{2011-09-29}
  s.summary = %q{my_gem is an awesome gem}
  s.files = [
    "Gemfile",
    "Rakefile",
    "VERSION",
    "lib/my_gem.rb"
  ]
  s.require_paths = ["lib"]
end
```

gemspec 是一个相当简单的文件，描述了宝石的各个方面。我只是在上面的示例 gemspec 中列出了所需的属性和文件。前 4 个属性不言自明。“文件”属性列出了 gem 中包含的所有文件。“require_paths”属性指定了包含应该用 gem 加载的 Ruby 文件的目录。欲了解 gemspec 中可用属性的完整列表，请点击[此处](http://docs.rubygems.org/read/chapter/20)。

这大概是我在不睡觉的情况下所能得到的最学术性的东西了，所以让我们切入正题，进入正题。

## 从零开始打造宝石

### 1.创建 gem 的基本文件结构:

启动您的 shell 并创建您的 gem 中需要的目录:

```
$ mkdir awesome_gem
$ cd awesome_gem
$ mkdir lib
```

就是这样！您需要一个用于 gem 的根目录和一个用于保存 Ruby 文件的 lib 目录。

### 2.创建 gemspec

对于 gemspec 文件，我们将使用上一节中的模板。在您的 gem 的根目录下创建一个名为“awesome_gem.gemspec”的文件。然后添加一些代码来创建一个有效的 gemspec:

```
Gem::Specification.new do |s|
  s.name = %q{awesome_gem}
  s.version = "0.0.0"
  s.date = %q{2011-09-29}
  s.summary = %q{awesome_gem is the best}
  s.files = [
    "lib/awesome_gem.rb"
  ]
  s.require_paths = ["lib"]
end
```

该文件包含 gemspec 所需的标准属性，并显示我们在“lib”目录中有一个文件。lib 目录中的文件“awesome_gem.rb”是用于保存这个 gem 中的 Ruby 代码的主文件。

### 3.添加一些代码

为了简单起见，我们在这个 gem 中只使用一个 Ruby 文件:/lib/awesome_gem.rb
你会在你遇到的大多数 gem 中看到这种类型的结构。“lib”中的根文件通常与 gem 的名称相匹配。在本例中为“awesome_gem”和“/lib/awesome_gem.rb”。

继续创建该文件，并向其中添加以下代码:

```
module AwesomeGem
  class WhoIs
    def self.awesome?
      puts "YOU ARE AWESOME!!"
    end
  end
end
```

这并不完全是会改变世界的代码，但至少“棒极了？”方法会提升你的自尊！这个宝石将允许你使用类方法“真棒？”其他 Ruby 程序中的 WhoIs。正如我在第一节中提到的，RubyGems 会在你的应用程序中安装 gem，并让你访问你的 gem 中的类。

### 4.生成 gem 文件

现在你已经有了一些很棒的代码，你会想要创建一个 gem，这样你就可以在另一个 Ruby 程序中使用这些很棒的代码。Rubygems 有一个命令行界面，允许你创建一个 gem。在您的 gem 的根目录中发出以下命令:

```
$ gem build awesome_gem.gemspec
```

该命令将构建 gem 并输出一个 gem 文件，该文件将在文件名中包含版本号。因为 gemspec 包含值为“0.0.0”的版本属性，所以 gem 文件将被命名为 awesome_gem-0.0.0。您应该会看到以下输出和一些关于缺少属性的警告:

```
Successfully built RubyGem
  Name: awesome_gem
  Version: 0.0.0
  File: awesome_gem-0.0.0.gem
```

但你不在乎警告，因为你活在边缘，你“牛逼”。所以您决定继续前进，在您的系统上安装这个 gem。请注意，gem 文件是在当前目录中创建的。

### 5.安装宝石

现在您已经有了一个 gem 文件，您可以使用 RubyGems 在您的计算机上安装 gem。通常情况下，您会从外部资源安装 gems，但是您并不局限于此。如果您可以访问 gem 文件，则可以通过指定将要安装的 gem 文件的位置来本地安装它。下面是在本地安装 awesome_gem.gem 的命令:

```
$ gem install awesome_gem.gemspec
```

您应该得到以下输出:

```
Successfully installed awesome_gem-0.0.0
1 gem installed
Installing ri documentation for awesome_gem-0.0.0...
Installing RDoc documentation for awesome_gem-0.0.0...
```

啊耶！你刚刚创造了一个宝石！gem 现在已经安装在您的系统上，可以在另一个 Ruby 程序中使用了。

### 6.将 Gem 添加到另一个 Ruby 程序中

创建一个新的 Ruby 文件，用于测试我们的 gem。姑且称之为“be_awesome.rb”。您可以在系统的任何地方创建这个文件，然后添加下面的代码，这样我们就可以使用“太棒了？”我们的 gem 中的类方法。你只需要请求‘awesome _ gem ’, ruby gems 将能够找到这个 gem，并使这个类对你的程序可用。然后，您只需从命名空间类中调用类方法。代码如下:

```
require 'awesome_gem'

AwesomeGem::WhoIs.awesome?
```

现在你可以运行 Ruby 程序，测试你新创建的 gem，看看你有多棒。通过命令行启动它:

```
$ ruby be_awesome.rb
```

您应该在 shell 中看到以下输出:

```
YOUR ARE AWESOME!
```

恭喜你，你刚刚在一个程序中使用了你的新宝石！我不认为我会把它放在 github 上吹嘘，但是嘿..您仍然学习了如何从头开始创建一个 gem 并在另一个程序中使用它。现在你可以去做更大更好的事情了。

### 结论

虽然本教程相当简单，并且只涵盖了创建一个 gem 的基础知识，但是我认为对于那些刚接触 gem 开发的人来说，它仍然是非常重要的信息。基础知识将为你学习更高级的主题打下良好的基础。我使用 Jeweler 来创建宝石，虽然它是一个伟大的生产力工具，但我觉得在你从零开始创建宝石之前就开始使用生成工具是有害的。你需要了解如何以最简单的形式构建一个 gem，然后才能理解像 Jeweler 这样的生成工具可以给你的代码背后是什么。当你第一次学习 gem 开发的时候，我建议你从头开始构建一个 gem，但是在你理解了基础知识之后，我完全没有理由不使用生成器。发电机是一个巨大的时间节省器，因为它们给你一个好的框架开始。本系列的下一篇博文将涵盖与 gem 开发相关的更高级的主题，以及使用生成工具为您的 gem 提供一个起点的初级读本。后续文章将解释如何为 Ruby on Rails 开发 gems。

敬请关注……

## 分享这篇文章