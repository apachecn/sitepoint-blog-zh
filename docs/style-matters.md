# 为什么风格很重要

> 原文：<https://www.sitepoint.com/style-matters/>

表达自由是一件好事，我们都有自己的编码风格。Ruby 的一个优点是它在语法上给了你很大的自由度。有些人总是省略括号，除非他们是必需的，而其他人总是包括它们，即使它们是多余的。

[![](img/6e22110a67414f7cfd3f5aef9baa1674.png)](https://farm8.staticflickr.com/7085/7020137471_525930a745.jpg)

低劣的

*图片由[人活着！](https://www.flickr.com/photos/24365773@N03/)*

当你与其他人在一个项目上合作时，团队应该就编码标准达成一致，并严格遵守。标准中的具体内容并不重要，但是**每个人**都同意并遵循它才是关键。

打开项目中的文件，在每个文件中找到不同的样式，会大大降低可读性，这在与他人协作时是非常重要的。你很可能会花更多的时间去阅读别人的代码，而不是你自己的，所以从长远来看，每次打开一个文件都畏缩不前是不好玩的…

## 保持一致

[![](img/e148106ebc7a80372330eeba7507204f.png)](https://farm1.staticflickr.com/7/8565357_914d809551.jpg)

制服

*图片由[麦克纳特](https://www.flickr.com/photos/nutt/)*

一致性是关键。我见过一个作者写的代码，他设法与自己不一致！不同级别的缩进、不同的间距、不同的变量命名，例如

```
def foo(var_a,var_b, var_c, varD ) 
    fooBar = "a(#{a}) "+ 'b(' +b+') cd(' +bar( var_c,varD)+")"
  return fooBar
end
```

(代码已更改，以保护罪犯)

将此与下面的进行比较:

```
def foo(a, b, c, d)
  "a(#{a}) b(#{b}) cd(#{bar(c, d)})"
end
```

差别很大，对吧？

## 拼音风格指南

在 Ruby 中，做同样的事情有不止一种方法。举个例子，

*   使用`$:`或`$LOAD_PATH`改变加载路径
*   使用`proc`或`Proc.new`创建一个过程
*   对于只有类方法的对象，使用带有`module_function :foo`的`module`或带有`def self.foo`的`class`。

然后，有些事情是一个味道的问题。例如，如何缩进，用分号结束行，是否对没有参数的方法使用括号，逗号后的空格等等。你选择什么由你自己决定，但是选择一个而不是另一个是有充分理由的。

有许多 Ruby 风格的指南可用，选择哪一个取决于您和您的团队:

*   [https://github.com/bbatsov/ruby-style-guide](https://github.com/bbatsov/ruby-style-guide)
*   [https://rails.lighthouseapp.com/projects/8994/source-style](https://rails.lighthouseapp.com/projects/8994/source-style)
*   [https://github.com/chneukirchen/styleguide/](https://github.com/chneukirchen/styleguide/)
*   [https://www.caliban.org/ruby/rubyguide.shtml](https://www.caliban.org/ruby/rubyguide.shtml)

这远远不是一个完整的列表。最后，一个重要的考虑是您是否能够自动化风格一致性验证，因为手动检查既慢又有缺陷。

## 自动样式检查

为了实施所选择的样式，您应该部署一个自动样式检查器。对于一些语言来说，像 [go](https://www.golang.org) ，这真的很简单，因为语言本身就有这个功能(参见 [`go fmt`](https://golang.org/cmd/go/#hdr-Run_gofmt_on_package_sources) 命令)。

在 Ruby 中，你必须依赖外部工具。

### 机器人足球赛

我更喜欢使用 [`rubocop`](https://github.com/bbatsov/rubocop) ,因为它速度快，高度可配置，并且有合理的默认值。

#### 连续累计

为了防止格式错误的代码进入回购，您可以使用预提交钩子(如果您使用的是 [git](https://git-scm.com/) )。您还应该在您的持续集成服务器中包含一个自动检查。使用 Rubocop，这真的很容易，因为有一个 rake 任务可以为您完成:

```
require 'rspec/core/rake_task'
require 'rubocop/rake_task'

task default: [:rubocop, :spec]

RSpec::Core::RakeTask.new
Rubocop::RakeTask.new
```

这将在运行(`rspec`)测试之前运行样式检查。

类似地，如果你运行的是 [guard](https://github.com/guard/guard) ，使用像 [guard-rubocop](https://github.com/yujinakayama/guard-rubocop) 这样的 gem 将 Rubocop 添加到 Guardfile 是很简单的。关键是，将自动化的样式检查融入到您的日常工作中很简单，并且回报是值得的。

#### 抽样输出

运行我在开头向您展示的这段可怕的代码到`rubocop`会产生以下违规列表:

```
$ rubocop foo.rb
Inspecting 1 file
C

Offences:

foo.rb:1:14: C: Space missing after comma.
def foo(var_a,var_b, var_c, varD )
             ^
foo.rb:1:33: C: Space inside parentheses detected.
def foo(var_a,var_b, var_c, varD )
                                ^
foo.rb:1:35: C: Trailing whitespace detected.
def foo(var_a,var_b, var_c, varD )
                                  ^
foo.rb:2:1: C: Use 2 (not 3) spaces for indentation.
   fooBar = "a(#{a}) "+ 'b(' +b+') cd(' +bar( var_c,varD)+")"
^^^
foo.rb:2:4: C: Use snake_case for variables.
   fooBar = "a(#{a}) "+ 'b(' +b+') cd(' +bar( var_c,varD)+")"
   ^^^^^^
foo.rb:2:23: C: Surrounding space missing for operator '+'.
   fooBar = "a(#{a}) "+ 'b(' +b+') cd(' +bar( var_c,varD)+")"
                      ^
foo.rb:2:30: C: Surrounding space missing for operator '+'.
   fooBar = "a(#{a}) "+ 'b(' +b+') cd(' +bar( var_c,varD)+")"
                             ^
foo.rb:2:32: C: Surrounding space missing for operator '+'.
   fooBar = "a(#{a}) "+ 'b(' +b+') cd(' +bar( var_c,varD)+")"
                               ^
foo.rb:2:41: C: Surrounding space missing for operator '+'.
   fooBar = "a(#{a}) "+ 'b(' +b+') cd(' +bar( var_c,varD)+")"
                                        ^
foo.rb:2:46: C: Space inside parentheses detected.
   fooBar = "a(#{a}) "+ 'b(' +b+') cd(' +bar( var_c,varD)+")"
                                             ^
foo.rb:2:52: C: Space missing after comma.
   fooBar = "a(#{a}) "+ 'b(' +b+') cd(' +bar( var_c,varD)+")"
                                                   ^
foo.rb:2:58: C: Surrounding space missing for operator '+'.
   fooBar = "a(#{a}) "+ 'b(' +b+') cd(' +bar( var_c,varD)+")"
                                                         ^
foo.rb:2:59: C: Prefer single-quoted strings when you don't need string interpolation or special symbols.
   fooBar = "a(#{a}) "+ 'b(' +b+') cd(' +bar( var_c,varD)+")"
                                                          ^^^
foo.rb:3:1: C: Inconsistent indentation detected.
  return fooBar
^^
foo.rb:3:3: C: Redundant `return` detected.
  return fooBar
  ^^^^^^
foo.rb:5:1: C: 1 trailing blank lines detected.

1 file inspected, 16 offences detected
```

## 包扎

每当您开始一个新项目时，一定要在一开始就包括自动样式检查，否则将需要很大的努力才能使所有文件都符合要求。

如果您继承了一个具有不同风格的现有项目，那么您可以排除那些产生最多失败的检查，这样您就可以慢慢地使它们有序。

不管你怎么做，坚持你的风格。你会很高兴你做了。

## 分享这篇文章