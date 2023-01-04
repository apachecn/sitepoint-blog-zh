# Ruby 新手的 Ruby 生态系统

> 原文：<https://www.sitepoint.com/ruby-ecosystem-new-rubyists/>

![newrubyist](img/0ad645cb5b0edc97b1226f75ec21bca8.png)

Ruby 不仅仅是一种语言。它有一系列的工具和过程来支持它所制作的复杂软件的创作。这对新人来说可能是难以承受的，所以我写了一篇文章，希望能让事情变得更清楚一些。

## 版本管理

假设你有两个项目依赖于两个不同版本的 gem。使用新版本的项目与旧版本的 gem 不兼容。你是做什么的？

一种选择是安装你当时需要的任何一个 gem。这不是一个好主意，因为 gem 的一个或两个版本可能在其`Gemfile`或`gemspec`中有依赖版本错误/不精确。那种问题很难追查。

相反，大多数 rely 爱好者依赖于某种版本管理器。版本管理器不仅保持 gem 的整洁，还将 Ruby 的实现分开。这使得测试 Ruby 2.0 和 Ruby 2.1 之间的差异变得很容易。

流行的版本管理解决方案有 [RVM](http://rvm.io/) 、 [rbenv](https://github.com/sstephenson/rbenv) 、 [Uru](https://bitbucket.org/jonforums/uru) (Windows)。

以下是你开始用 RVM 管理红宝石的方法:

安装 RVM。

```
$ \curl -sSL https://get.rvm.io | bash
```

启动时在 bash 兼容的 shell 中获取 RVM 脚本。

```
$ echo "source $HOME/.rvm/scripts/rvm" >> ~/.bash_profile
```

重新加载外壳。

```
$ source ~/.bash_profile
```

验证 RVM 脚本已获得来源。它应该显示“rvm 是一个函数”

```
$ type rvm | head -n 1
```

安装 Ruby 2.0.0。

```
$ rvm install 2.0.0
```

切换到 2.0.0。

```
$ rvm use 2.0.0
```

为 2.0.0 创建一个名为“experimental”的新 gemset

```
$ rvm gemset create experimental
```

切换到新的 gemset。

```
$ rvm gemset use experimental
```

## 制作宝石

Ruby 并不总是打包代码。2003 年，[rubyforge.org](http://rubyforge.org/)作为 Ruby 开发者分享代码的地方推出。尽管这种情况有所改善，但在弄清楚如何运行彼此的代码时，开发人员仍然只能靠自己。2003 年 11 月，一些 Ruby 开发人员聚在一起，决定永远解决这个问题。2004 年[rubygems.org](http://rubygems.org/)推出了`gem`工具。

RubyGems 是 Ruby 库和程序的包管理器。创建红宝石可能有几个原因:

1.  轻松地与其他开发人员共享代码
2.  避免在项目之间重复代码

安装宝石很容易:

```
$ gem install gem_name
```

典型的 gem 项目的布局如下所示:

```
- spec
  - gem_name_spec.rb
  - spec_helper.rb
- bin
  - gem_name
- lib
  - gem_name.rb
  - gem_name
    - source_file1.rb
    - source_file2.rb
    - source_file...
    - version.rb
- Gemfile
- gem_name.gemspec
- README.md
- LICENSE
- Rakefile
```

没有必要记住 gem 目录结构。生成基本 gem 模板的一个简单方法是使用[捆绑器](http://bundler.io/)工具。

```
$ bundle gem gem_name
```

注意:Bundler 可能会预装在 Ruby 安装中，但是如果没有或者想要最新的版本，可以安装 gem:

```
$ gem install bundler
```

如果你不喜欢 Bundler 创建的模板，还有其他工具可用，包括[珠宝商](https://github.com/technicalpickles/jeweler)和[锄头](https://github.com/seattlerb/hoe)。

### 负载路径

我们需要检查新的 gem 开发者面临的一个不明显的问题:Ruby 的**加载路径**。假设您在同一个目录中有几个文件(不是创建一个 gem，只是一般的)。我们称它们为 **source1.rb** 和 **source2.rb** 。

```
# source1.rb
require 'source2'

# source2.rb
puts "hello world"
```

看起来很棒，但是试着使用这个文件，看看会发生什么。

```
$ ruby source1.rb
...'require': cannot load such file -- source2 (LoadError)...
```

等等，这是怎么回事？即使在同一个目录下， **source1.rb** 也看不到 **source2.rb** 。原来 **Ruby 并没有自动将它执行的源文件的目录包含在它的加载路径**中。我们可以通过命令行选项`-I`(包含目录)和`.`(哪个目录-`.`是当前目录)来告诉它这样做，从而让这个例子工作起来:

```
$ ruby -I . source1.rb
hello world
```

或者，您可以`require`文件的直接路径:

```
# source1.rb 
require './source2'
```

那么开发宝石需要做这样的事情吗？不。为了测试开发中的 gem，可以通过编程将源目录添加到 Ruby 的`$LOAD_PATH`全局变量中。

```
# source1.rb
$LOAD_PATH.unshift(".")
require "source2"
```

这用于包含 **lib** 文件夹及其所有子目录。您通常会遇到`File::expand_path`将 **lib** 文件夹的相对路径从文件转换为绝对路径。

```
# spec/spec_helper.rb
lib = File.expand_path('../../lib', __FILE__)
$LOAD_PATH.unshift(lib) unless $LOAD_PATH.include?(lib)

# now we can require lib/gem_name.rb which can require other code as needed
require 'gem_name'
```

`$LOAD_PATH`变量只是一个数组。`$LOAD_PATH.unshift(lib)`将目录添加到数组的开头，这样它会在其他任何东西之前被加载。**注意，`File.expand_path('../../lib', __FILE__)`指的是上一层的`lib`目录，而不是看起来像**的两层目录。这是一个常见的失误。`__FILE__`第二个参数指定了开始的目录，否则使用当前的工作目录(执行 Ruby 的目录)。

注意，你不会总是在宝石里看到`$LOAD_PATH`。一个流行的别名是`$:`，尽管难以置信地难以描述。

```
$:.unshift(File.expand_path('../../lib', __FILE__)
```

任何支持 gem 的代码都应该放在与 gem 同名的文件夹中。

```
- lib
  - gem_name.rb
  - gem_name
    - some_file.rb

# lib/gem_name.rb
require 'gem_name/some_file'
```

当安装 gem 时，`lib`目录的内容被放在一个已经在 Ruby 的加载路径中的目录中。因此，加载路径不应该在实际的 gem 代码中的任何地方被修改，并且 gem 的名称应该是唯一的。

### gemspec

rubygems `gemspec`文件是实际定义 gem 的地方。**gem spec 是建造宝石**必须存在的文件。

```
# gem_name.gemspec
lib = File.expand_path("../lib", __FILE__)
$LOAD_PATH.unshift(lib) unless $LOAD_PATH.include?(lib)
require 'gem_name/version'

Gem::Specification.new do |s|    
  s.platform       = Gem::Platform::RUBY
  s.name           = 'gem_name'
  s.version        = GemName::VERSION
  s.authors        = ['Your Name']
  s.email          = ['your@email.com']
  s.homepage       = 'https://github.com/your_name/gem_name'
  s.summary        = 'Gem in a few words'
  s.description    = 'Longer decription of gem'
  s.required_ruby_version = '>= 1.9.3'
  s.require_path   = 'lib'
  s.files          = Dir[LICENSE, README.md, 'lib/**/*']
  s.executables    = ['gem_name']

  s.add_dependency('sqlite3')
  s.add_development_dependency("rspec", ["~> 2.0"])
  s.add_development_dependency("simplecov")
end
```

并非所有这些字段都是必需的。例如，不是每个 gem 都有可执行文件，有些可能更喜欢将它们的依赖关系放在一个**gem 文件**中。请务必查看 rubygems.org 规格参考中的[。](http://guides.rubygems.org/specification-reference/)

### Gemfile

Bundler `Gemfile`包含了 gem 的依赖项、它们的版本以及从哪里获得它们的列表。这使得其他开发人员很容易将他们的环境与您的环境同步，以确保相同的行为。它还使得 gem 的用户可以安装所有的依赖项，而不必知道它们是什么。

gem 文件中的 gem 依赖项如下所示:

```
gem <gem_name>, <version constraint>
```

以下是一个 Gemfile 示例:

```
# Gemfile
source 'https://rubygems.org'

gem 'nokogiri', '~> 1.4.2'

group :development do
  gem 'sqlite3'  
end

group :test do
  gem 'rspec'
  gem 'cucumber'
end

group :production do
  gem 'pg'
end

gemspec
```

Gemfiles 中一个不明显的语法是所谓的**精子操作符** ( `~>`)。该运算符将增加最后一位数字，直到它翻转过来。所以`gem 'nokogiri', '~> 1.4.2'`在语义上等同于`gem 'nokogiri", '>=1.4.2', '<1.5.0'`。

`gemspec`行只是告诉 Bundler 安装 gemspec 文件中列出的依赖项。许多人喜欢将所有的依赖项放在一个 Gemfile 中，所以这一行不是必需的。

当您想要将您的环境与 gem 的依赖项同步时，只需导航到包含 gem 文件的目录并运行:

```
$ bundle install
```

有时，项目具有只对特定环境有意义的依赖关系。如果您不打算在生产中使用 gem，您可以避免安装生产组中列出的 gem。

```
$ bundle install --without production
```

当 Bundler 计算依赖关系时，它会创建一个名为`Gemfile.lock`的文件。该文件的目的是帮助避免开发环境之间的细微差异。一个常见的问题是，这是否应该放在存储库中。如果这个项目是一个 gem，不，但是如果它是一个 app，是的——原因是一个 app 通常有自己独立的 gemsets，并且每个开发 app 的人都有每个依赖项的完全相同的版本是很重要的。

更多信息，请参见[捆绑器 Gemfile 参考](http://bundler.io/v1.5/gemfile.html)和[捆绑器基本原理](http://bundler.io/v1.5/rationale.html)。

### makefile 先生

`rake`是 Ruby world 的 GNU `make`的等价物。与 Gemfiles 和 gemspecs 不同，Rakefiles 的存在主要是为了方便。拥有像`rake test`这样简单的命令使得自动化系统(或其他开发人员)测试代码变得容易，而不需要知道使用哪种测试系统。

```
# Rakefile
lib = File.expand_path("../lib", __FILE__)
$LOAD_PATH.unshift(lib) unless $LOAD_PATH.include?(lib)
require "gem_name/version"

task :test do
  system "rspec"
end

task :build do
  system "gem build gem_name.gemspec"
end

task :release => :build do
  system "gem push gem_name-#{GemName::VERSION}"
end
```

如果你想对 Rakefiles 如何工作有一个很好的感觉，这里有一个很长的教程。

### 建筑宝石

一旦一个宝石被适当地组织和测试，它就能被建造。`gem build`命令将生成一个. gem 文件，该文件可以安装在本地或推送到 gem 存储库中。文件名将包括 gemspec 中设置的版本。

```
$ gem build gem_name.gemspec
$ gem install gem_name-version.gem
$ gem push gem_name-version.gem
```

现在，在项目中，您或任何安装 gem 的人都可以使用它:

```
require 'gem_name'
```

**注意**:你有时会在代码中看到`require 'rubygems'`。这是 RubyGems 作为一个独立项目时的遗留问题。从 Ruby 1.9 开始，RubyGems 就是标准库的一部分。

### 捆绑包执行

你经常会将命令写成像`bundle exec rake db:migrate`而不是`rake db:migrate`。这是因为`bundle exec`保证命令将使用`Gemfile`中指定的 gems 版本来执行。RVM 用 [rubygems-bundler](https://github.com/mpapis/rubygems-bundler) 创建了 gemsets 来避免这个问题，如果你使用的是 Ruby 2.2，这也不应该是个问题，但是值得了解一下。

## Ruby 实现

Ruby 语言和类 Ruby 语言有许多实现。其中最受欢迎的包括:

*   MRI/cru by——用 C 语言编写的 Ruby 的参考实现
*   JRuby–运行在 Java 虚拟机上的 Ruby
*   Rubin ius——用 Ruby 编写的 Ruby，运行在 LLVM 上
*   ruby motion——制作原生苹果/安卓应用的商业实现

还有[很多其他的](https://github.com/cogitator/ruby-implementations/wiki/List-of-Ruby-implementations)。

似乎有一种普遍的误解，认为线程在 Ruby 中不起作用。线程在 CRuby (MRI)中受到限制，但它们确实提供了同时处理两个问题的能力。理解**并发**和**并行**之间的区别很重要:

*   并发性——在重叠的时间段内完成两个或多个任务(即下载一个文件，同时等待查看用户是否与界面交互)。
*   并行性——在多个处理器内核上同时完成两个或更多任务

Ruby 的参考实现 MRI 有一个**全局解释器锁**，所以一次只能使用一个原生线程。这意味着 Ruby 线程可以并发运行，但不能并行运行。如果在 MRI 中需要在多个处理器上执行代码，则必须使用分叉或其他进程协调的变体(至少直到 GIL 被移除，如果有的话)。有些 Ruby 实现没有 GIL，包括上面列出的所有其他实现。最重要的是，JVM 在技术上不支持分叉，所以在 JRuby 中，无论如何你都必须使用线程。

我在这里写了一篇关于 Ruby 分叉的文章。

## 证明文件

当一个 gem 被安装时，你经常会注意到一个关于`rdoc`和`ri`文档被安装的通知。 [RDoc](http://rdoc.sourceforge.net/doc/) 是一个用于 Ruby 的嵌入式文档生成器。`ri`是一个类似`man`的工具，用于在终端中读取离线文档。

```
$ ri Array
$ ri Array#length
$ ri Math::sqrt
```

使用版本管理器时，可能需要手动生成核心文档。否则，`ri`将只打印“对[无论如何]一无所知”。例如，使用`rvm`，核心文档可以通过以下方式生成:

```
$ rvm docs generate
```

这可能需要一段时间。同样，当安装一个 gem 时，如果 gem 足够复杂，那么生成文档会花费很长时间。您可以跳过 gem 的文档:

```
$ gem install gem_name --no-rdoc --no-ri
```

## 窥探

探索 Ruby 文档的另一种方法是安装[proy](https://github.com/pry/pry)。Pry 是对`irb`控制台的替代，具有各种增强功能，包括查看方法的原始 C 源代码的能力。

```
$ gem install pry pry-doc
$ pry

[1] pry(main)> show-method Array#map

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

1] pry(main)> cd Array
[2] pry(Array):1> show-doc map

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
  a.collect { |x| x + "!" }        #=> ["a!", "b!", "c!", "d!"]
  a.map.with_index{ |x, i| x * i } #=> ["", "b", "cc", "ddd"]
  a                                #=> ["a", "b", "c", "d"]
```

## 测试

假设您克隆了一些随机存储库，运行了一些代码度量，并修复了一些重复。除了您处理的文件之外，您对项目并不熟悉。你怎么知道你没有打碎任何东西？

**测试**。

Ruby 社区非常重视测试。Ruby 的测试框架包括 [Test::Unit](http://test-unit.github.io/) 、 [RSpec](http://rspec.info/) 、 [Minitest](https://github.com/seattlerb/minitest) 和 [Cucumber](http://cukes.info/) 。`Test::Unit`是 Ruby 最初的标准单元测试框架，但是它已经被弃用，取而代之的是`Minitest`。

通常，测试放在项目根层的 **/test** 或 **/spec** 文件夹中。通常有一个 **/test/test_helper.rb** 或者 **/spec/spec_helper.rb** 在处理测试用例或者规格说明之前运行。这是配置全局测试环境的好地方。

让我们看一个用`Minitest`测试的例子。`Minitest`能够进行单元测试和规格测试，以及基准测试。

> 注意:虽然 Ruby 附带了`Minitest`，但是我在使用`Minitest::Test`而不是`Minitest::Unit::TestCase`时出错了。通过安装最新版本的 gem 修复了此问题。

```
$ gem install minitest --version 5.4.2
```

我们将从一个使用单元测试格式的简单测试文件开始。

```
require "minitest/autorun"

class Foo
  def hello
    "goodbye"
  end
end

class TestFoo < Minitest::Test
  def setup
    @foo = Foo.new
  end

  def test_hello
    assert_equal "hello", @foo.hello
  end
end
```

如果你运行这个文件，你应该得到一个失败。

```
1) Failure:
TestFoo#test_hello [test.rb:15]:
Expected: "hello"
  Actual: "goodbye"

1 tests, 1 assertions, 1 failures, 0 errors, 0 skips
```

很酷，但是 RSpec 可以说是 Ruby 领域中最流行的测试框架。这很大程度上得益于一个蓬勃发展的社区以及与其他测试库的各种集成，如 [Capybara](https://github.com/jnicklas/capybara) 。与`Minitest`不同，RSpec 包括一个测试运行器命令`rspec`，它将检查名为`*_spec.rb`的`spec/`目录层次中的所有规范。

首先，安装`rspec`宝石。

```
$ gem install rspec --version 3.1.0
```

通常情况下，`spec/`目录的布局如下:

```
- spec
    - spec_helper.rb  
    - gem_or_app_name
      - models
        - some_model_spec.rb
      - controllers
        - some_controller_spec.rb
```

对于演示，只需创建一个示例规范和一个空白的`spec_helper.rb`。

```
# spec/example_spec.rb
require "spec_helper"

class Foo
  def hello
    "goodbye"
  end
end

describe Foo do
  before(:each) do
    @foo = Foo.new
  end

  it "says hello" do
    expect(@foo.hello).to eq "hello"
  end
end
```

要检查规范，只需在根项目目录中运行`rspec`。

```
$ rspec

Failures:

  1) Foo says hello
    Failure/Error: expect(@foo.hello).to eq "hello"

      expected: "hello"
            got: "goodbye"

      (compared using ==)
    # ./spec/example_spec.rb:13:in `block (2 levels) in <top (required)>'

Finished in 0.00079 seconds (files took 0.06974 seconds to load)
1 example, 1 failure</top>
```

那么 **spec/spec_helper.rb** 呢？为了了解它的用途，我们将添加代码覆盖率。SimpleCov 是一个流行的代码覆盖工具，它将在项目根目录下的`coverage/`目录中生成覆盖 HTML。

首先，安装`simplecov`宝石。

```
$ gem install simplecov --version 0.9.1
```

现在，只需调用 **spec/spec_helper.rb** 中的`SimpleCov.start`。

```
# spec/spec_helper.rb
require "simplecov"

SimpleCov.start
```

每次运行`rspec`时都会计算覆盖率。在这种情况下，没有什么可覆盖的，但是工具将被激活。

记住`rspec`不会自动执行`spec/spec_helper.rb`。需要在每个规范中通过在规范文件的顶部添加`require 'spec_helper'`来要求它。

## 摘要

版本管理:
——使用系统 Ruby 安装充满危险——使用版本管理器。
–为每个主要项目使用不同的宝石。隔离有助于避免冲突以及错误或不精确指定的依赖关系问题。

**制作 Gems**:
–开发 Gems 时，必须手动将库目录添加到`$LOAD_PATH`中，代码才能像安装了 gem 一样工作。这可以在命令行或以编程方式完成。
–`File.expand_path('../../lib', __FILE__)`指的是向上一个目录的 lib 文件夹，而不是看起来向上两个目录的文件夹。
–只有一个文件`gem_name.rb`应该在`lib`目录中。其余的源代码应该去`lib/gem_name/`。
–如果使用 Ruby 1.8.7，`require 'rubygems'`应该在需要任何宝石之前进行。
–Gem 依赖项可以放在 Bundler Gemfile 或 RubyGems gemspec 中。
–在 gemspecs 中，`#executable_files=`需要文件名，而不是路径。假设有一个名为`bin`的文件夹。
–rake file 类似于 Makefiles，定义了测试、构建和发布等常见任务。
–通常使用`git ls-files`来获取 gemspec 的文件数组，但是 Ruby 有`Dir`更便于移植。
–`bundle exec [command]`使用 gem 文件中指定的 gem 版本执行命令。对于 RVM 或 Ruby > = 2.2.0，这是不必要的。

**Ruby 实现**:
–Ruby 的参考实现(CRuby / MRI)有一个全局解释器锁，所以虽然线程可以并发，但并行性(一次多个处理器)是不可能的。
——如果 MRI 需要并行，使用`Kernel#fork`
进行分叉——并行运行线程可以通过 JRuby、Rubinius、RubyMotion 实现。

**文档**
—`ri`工具可用于离线浏览核心文档。
–`rdoc`是 Ruby 的标准嵌入式文档生成器。
–Ruby 实现的文档可能需要由版本管理器安装，就像`rvm docs generate`一样。
–`pry`是一个复杂的控制台，可以在运行时浏览文档和程序。

**测试**
–测试或规格放在项目的 **test/** 或 **spec/** 文件夹中。
–设置和配置进入 **test/test_helper.rb** 或**spec/spec _ helper . Rb**–`MiniTest`是标准测试库，但您可能会遇到使用`Test::Unit`的遗留代码。
–在包含`spec/`的项目根目录下运行`rspec`可以检查所有的 RSpec 规范。
–所有规格必须以 ***_spec.rb** 结尾，否则如果不直接运行将被跳过。
–r spec 不自动执行 **spec/spec_helper.rb** 。`require 'spec_helper'`需要放在每个规格文件中。SimpleCov 是一个测试覆盖库，可以通过安装 gem 并将其添加到测试套件助手文件中来使用。

## 分享这篇文章