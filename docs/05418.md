# 使用 Vim 进行有效的 Rails 开发

> 原文：<https://www.sitepoint.com/effective-rails-development-vim/>

![Vimlogo.svg](img/95d43298147d0ec939a9d87469e8c4e8.png)

如果您是 Vim 的新手，请务必阅读我的简介，因为它将帮助您为本文做好准备。在这里，我主要关注使用 Vim 进行有效的 Rails 开发。

## Rails 插件

Tim Pope 编写了一个非常有用的 Vim 插件，名为[Rails.vim][1]，它提供了很多很棒的功能，包括增强的语法高亮显示、部分提取和 Rails 项目中更容易的导航。

#### 使用 Vundle 安装

正如我们在[之前的教程](https://www.sitepoint.com/getting-started-vim/)中看到的，使用 Vundle 安装任何插件都非常容易。只需在`call vundle#begin()`和`call vundle#end()`块内添加以下行:

```
Plugin 'tpope/vim-rails'
```

之后输入`:PluginInstall`，回车，剩下的就交给 Vundle 了。重启 vim 和 Rails，Vim 就可以使用了。实际上插件 URL 是`https://github.com/tpope/vim-rails`，但是正如我们在前面的教程中看到的，我们不需要添加 Github URL，因为 Vundle 会自动添加它，因为它默认使用 Github 作为插件源。

Rails.vim 中的所有命令都以字母`R`命名。因此，要在浏览任何 Rails ( `.rb, .erb, ..etc`)文件时访问任何命令，请键入`:R`并按 tab 键两次以查看它们的列表。您可以输入`:help Rails`来浏览文档

#### 使用`gf`跳转

每个 Rails.vim 命令以字母`:R`开头的规则有一个例外，它是`gf`(Go to File 的缩写)。当光标在文件名上键入`gf`时，Vim 的默认行为是打开该文件。然而，当使用 Rails.vim 时，这种行为被修改了，所以如果您在任何标识符上键入`gf`，它将带您到指定它的文件。例如，当输入`gf`并将光标放在单词“公司”上时，Vim 将带您到公司模型:

```
has_one    :company
```

同样，如果您在任何方法上尝试这样做，Vim 也会带您到定义该方法的文件，即使它是 Rails 内置方法。浏览 routes 文件，在任一标识符上点击`gf`,您会发现 Vim 现在知道它是在哪里定义的。

```
get "home",    to: "pages#home",    as: "home"
```

如果光标在`home`上，Vim 将带您到`PagesController`并将光标放在其中的`home`动作上。这也适用于片段、命名路线和许多其他 Rails 标识符。

> **注意:**
> 1-为了让它工作，确保从 Rails 项目目录中打开 Vim。
> 2-要返回上一个文件，在命令模式下点击`CTRL + o`。
> 3-要在已访问的文件中向前跳转，按`CTRL+ i`。
> 4-要查看已访问文件的列表，点击`:jumps`。

#### 项目文件中的导航

vim 允许在不同的项目文件中轻松导航，比如`models`、`view`或`controllers`。您可以很容易地跳转到其中的任何一个，以及特定的行或方法。让我解释一下怎么做。

| 导航命令 | 价值 |
| --- | --- |
| 控制器 | 跳转到相关的控制器，就好像你在一个模型文件中，你想跳转到它的控制器。 |
| 你的模型 | 跳到模型 |
| 回顾 | 只需添加视图名称(索引、显示、编辑)即可跳转到相关视图。 |
| 单元测试 | 跳到相关的单元测试。 |
| Rfunctionaltest | 跳转到相关测试。 |
| 集成测试 | 跳转到指定的集成测试、集成规范或 cucumber 特性。 |
| Rspec | 跳到给定的规格。 |
| 移民 | 使用 tab 补全功能从可用的迁移中进行选择，并跳转到其中一个迁移。 |
| (计划或理论的)纲要 | 跳转到项目模式。 |
| 拖车 | 跳转到给定的邮件程序。 |
| 助手 | 跳到给定的帮助者。 |
| java 描述语言 | 跳转到给定的 JavaScript 或 CoffeScript 文件。 |
| 样式表 | 跳转到给定的样式表。 |
| Rtask | 跳到给定的任务。 |
| 利里布里布里布里布里布里布里布里布里布里布里布里布里布里布里布里布里布里布里布 | 跳转到给定的库，如果没有指定参数，它跳转到 Gemfile。 |
| 布局 | 跳转到当前控制器的布局。 |

> **注意:**
> 您可以通过输入名称来指定您想要打开的控制器、模型、视图或测试文件。此外，Vim 在输入文件名时提供制表符补全功能。

#### 在拆分窗口中打开文件

如果你想在一个分割窗口中打开一个文件，只需输入`:RV`，而不是`:R`，以及你想打开的项目(模型、视图、控制器等)。)这会以垂直分割方式打开文件。如果你想水平分割，使用`:RS`

#### 在新标签中打开文件

你可以在新窗口中打开任何 Rails 文件，只需使用`:RT`而不是`:R`。

#### 打开当前缓冲区中的文件

您可以使用`:RD`打开当前缓冲区中的任何文件(这意味着将其内容附加到当前文件中的当前光标位置)。

### 运行测试

为什么离开文本编辑器只是为了运行测试？Rails.vim 让您能够直接从编辑器运行测试，只需输入`:Rake`。您还可以运行测试的子集，比如功能测试、单元测试、集成测试、黄瓜特性、RSpec 规范。

### 生成迁移

为什么让 Vim 来运行迁移？只需输入`:Rgenerate migration add_something_to_tablename`。它不仅会生成迁移，还会将您带到文件，这真的很有用，可以节省时间。

### 反转迁移

如果您想反转迁移，请使用`:Rinvert`。

### 运行、重新启动和终止服务器

*   您可以使用`:Rserver`从 Vim 运行服务器进程。
*   您可以使用`:Rserver!`停止现有的进程并运行新的进程(重启服务器)。
*   可以用`:Rserver!-`杀死正在运行的服务器。

### 查看当前相关路径

如果你想打开当前文件的正确 URL，比如`app/controllers/companies_controller.rb`，输入`:Rpreview`。如果服务器正在运行，它将打开“/公司”。

> **注意:**
> 这个命令是上下文感知的，所以如果你在`CompaniesController`的`index`动作上发出这个命令，它会把你带到相关的路线`/companies/index`

### 部分提取

vim 插件提供了一个非常方便的特性，叫做部分提取。将一些`ERB`提取到部分视图有点乏味，因为这涉及到一些步骤，比如剪切一些文本，创建一个名称以`_`开头的新文件，粘贴文本，然后重构原始视图以包含部分视图。为什么不让 Vim 用一个简单的命令来帮您处理呢？

如果这是您的文件，在`app/views/user/show.html.erb`中:

```
20   <div>
   21     <h2><%= @user.name %></h2>
   22     <p><%= @user.email %></p>
   23   </div>
```

您发出这个命令:

```
:21,22Rextract profile
```

您的文件更改为:

```
20   <div>
   21     <%= render 'profile' %>
   22   </div>
```

将创建一个新文件，相关路径中包含相关代码。`app/views/user/_profile.html.erb`现在包含:

```
1    <h2><%= @user.name %></h2>
   2    <p><%= @user.email %></p>
```

根据发出命令的文件,`Rextract`命令的行为会有所不同。如果你在一个视图中，它会把代码提取到一个部分。如果您在一个助手中，它将使用提取的代码创建一个助手。如果你在一个模型或者控制器中，它将创建一个新的关于提取代码的关注点。

## 同船水手

SnipMate 是一个非常有用的管理代码片段的插件。编写代码时，它可以节省大量时间，您会发现自己正在使用它的快捷方式来创建大型代码片段。SnipMate 还支持其他语言，比如 JavaScript 和 HTML。

### 装置

使用 Vundle 只需在`call vundle#begin()`和`call vundle#end()`模块内添加以下行:

```
Plugin 'garbas/vim-snipmate'
```

调用`:PluginInstall`并重启 Vim。

### Ruby 片段

现在你可以使用这些片段了。打开一个控制器文件，键入`def`，然后按 tab 键。Vim 将插入`def end`块，突出显示方法名，并为您进入插入模式。在输入方法名后，再次按 tab 键，光标将跳转到方法定义中。很有用，对吧？

还有许多其他有用的片段，如:

| 码组 | 捷径 |
| --- | --- |
| if 结束块 | 如果 |
| if else 结束块 | 内部因素评价矩阵 |
| 每个区块 | 电子艺界游戏公司 |
| 每个都有索引 | eawi |
| 地图 | 地图 |
| 收集 | 山口 |
| 挑选 | 系统事件日志(System Event Log) |
| 扫描 | （同 sickle-cellanemia）镰状红细胞贫血 |
| 班级 | 德国戴姆勒集团旗下品牌梅赛德斯 |

要查看可用 Ruby 代码片段的完整列表，请打开`~/.vim/snippets/ruby.snippets`文件。

您也可以将自己的代码片段添加到该文件中，只需遵循格式即可。让我们以`if end`片段为例:

```
snippet if
        if ${1:condition}
                ${2}
        end
```

`snippet`后面的单词`if`是片段名。下一行是代码片段本身。`${1`是光标将停留的第一个地方，单词“条件”将在那里高亮显示并准备替换。`${2}`是用户点击 tab 键后的下一个光标位置。我建议您添加自己的代码片段，以提高编辑效率。

## CTags

另一个有用的工具是 Exuberant Ctags。它使您能够轻松地浏览项目源代码。它将源代码对象索引到标签，这使得编辑器可以很容易地定位任何源代码对象并跳转到它的定义。

### 装置

首先，您需要确保 Ctags 是**而不是**安装在您的系统上。键入`which ctags`，如果它返回类似`ctags: aliased to /usr/local/bin/ctags`的结果，那么就不需要安装了。

如果您确实需要安装它，这里有各种平台的说明。如果您使用的是 Fedora Linux，请下载 RPM 包。如果你用的是 MacOSX，用 Hombrew 通过输入`brew install ctags`来安装。在 Ubuntu 上，有一个名为`exuberant-ctags`的包，你可以用 apt-get 包管理器安装它。

### 运行 Ctags

要在项目源代码上运行 Ctags，请在 Rails 目录的根目录下键入`ctags -R --exclude=.git --exclude=log *`。这里的大写`R`参数使得它是递归的。我们排除了。git 目录，原因很明显。

名为 **tags** 的文件现在已经生成，您应该将它添加到。gitignore 文件。

最后一步是将下面一行添加到`vimrc`文件中，告诉 Vim 在哪里寻找标记文件。

```
set tags=./tags;
```

Ctags 现在准备好了。转到任何源文件，将光标放在任何方法上，然后点击`CTRL + ]`。Vim 将跳转到定义。

例如:

```
flash[:alert] = 'alert'
```

如果光标在`flash`上，你点击`CTRL + ]`，Vim 将跳转到它的源定义。这对于查看 Rails 源代码和了解更多信息非常方便。

> **注意:**
> 你可以通过输入`tag method_name`跳转到定义。要跳转到`has_many`，请键入`:tag has_many`。您可以使用正则表达式，如:tag /validate*

## 确认字符（acknowledgementcharacter）

Ack 是`grep`的绝佳替代品。Ack 在文件中搜索包含与给定模式匹配的行。如果没有选择任何文件，Ack 会搜索没有被`--ignore-dir`和`--ignore-file`选项明确排除的常规文件，这些文件要么出现在`ackrc`文件中，要么出现在命令行上。

### 装置

在 MacOSX 上，您可以通过在终端中键入`brew install ack`来使用 Hombrew 安装 Ack。在 Ubuntu 上，你可以通过`sudo apt-get install ack-grep`来实现，你也可以参考这个【站点】【2】来获得其他支持的操作系统。

### 易于搜索的源代码

您可以从终端和 Vim 使用 Ack。要在 Vim 中使用它来替代 grep，只需在`vimrc`文件中添加下面一行:

```
" Use ack instead of grep
set grepprg=ack
```

您可以通过键入`ack --ruby controller`从终端使用 Ack，这将打印所有带有“controller”字样的行。

现在，如果您在 Vim 中键入`:grep controller`，它将使用 Ack 在项目文件中搜索“controller”的出现，并打印找到它的行。

### 编辑。ackrc 配置

我们可能想忽略日志和资产文件，对吗？我们可以通过放置在项目目录中的名为`ackrc`的配置文件来实现。它也可以放在您的主目录中进行全局配置。

忽略包含以下行的日志和资产文件:

```
--ignore-dir=log
--ignore-dir=public/assets
```

### 快速浏览结果

当在 Vim 中使用`grep some_text`时，它将打印其出现的行。当你点击`Enter`时，它会跳到第一个出现的地方。你如何转移到下一个？

只需输入`:cn`转到下一个结果，输入`:cp`跳到上一个结果。

## tComment

有一个用于注释和取消注释 Ruby 代码的插件，叫做 tComment。它的工作方式就像一个开关，允许通过一个快捷方式选择要注释/取消注释的大量代码。

### 装置

就像前面的例子一样，使用 Vundle 安装很容易。只需在`call vundle#begin()`和`call vundle#end()`块内的`vimrc`中添加以下代码行:

```
Plugin 'tomtom/tcomment_vim'
```

调用`:PluginInstall`并重启 Vim。

### 使用

以下是 tComment 插件提供的快捷方式列表:

| 捷径 | 描述 |
| --- | --- |
| gc {运动} | 切换注释 |
| （同 groundcontrolcenter）地面控制中心 | 切换当前行的注释 |
| gC {运动} | 注释区域 |
| （同 groundcontrolcenter）地面控制中心 | 注释当前行 |

## Vim RSpec

Thoughtbot 为从 Vim 运行 RSpec 测试创建了一个非常方便的插件。这是一个很大的时间节省，允许从 Vim 内部发射 RSpec 规格。Vim RSpec 插件受到 Gary Bernhardt 的[Destroy All Software](https://www.destroyallsoftware.com/screencasts)screencasts 的强烈影响。

### 装置

将下面一行添加到`call vundle#begin()`和`call vundle#end()`模块内的`vimrc`中:

```
Plugin 'thoughtbot/vim-rspec'
```

调用`:PluginInstall`并重启 Vim。

### 使用

下面是 RSpec 插件提供的默认键映射。您可以定制它，并将您喜欢的键映射添加到`.vimrc`文件中。

```
" RSpec.vim mappings
map <Leader>t :call RunCurrentSpecFile()<CR>
map <Leader>s :call RunNearestSpec()<CR>
map <Leader>l :call RunLastSpec()<CR>
map <Leader>a :call RunAllSpecs()<CR>
```

## Ruby 重构

Ruby Refactoring 是一个由恩里克·科姆巴·里彭豪森编写的插件，用来简化 Ruby 的重构。正如他在自述文件中所说的，Ruby Refactoring 受到了 Gary Bernhardt 在英国软件工艺用户组上展示的一些重构模式的启发。

### 装置

将下面一行添加到`call vundle#begin()`和`call vundle#end()`模块内的`vimrc`中:

```
Plugin 'ecomba/vim-ruby-refactoring'
```

调用`:PluginInstall`并重启 Vim。

### 使用

看看贾斯汀提供的[优秀文档](http://justinram.wordpress.com/2010/12/30/vim-ruby-refactoring-series/)，它涵盖了使用这个插件所需的一切。

## 学习资源

1- [使用 Vim 作为完整的 Ruby on Rails IDE](http://biodegradablegeek.com/2007/12/using-vim-as-a-complete-ruby-on-rails-ide/)
2-[Ruby autocomplete](http://www.cuberick.com/2008/10/ruby-autocomplete-in-vim.html)
3-[Intro–Rails Vim](http://robots.thoughtbot.com/post/166073596/intro-rails-vim)
4-[Vim Bundler 插件](https://github.com/tpope/vim-bundler)
5- [Endwise 插件](https://github.com/tpope/vim-endwise)

## 摘要

在本教程中，我们介绍了一些用于编写 Rails 的最有用的 Vim 插件。如果你遵循了我所有的建议，你的 Vim 编辑器现在是一个有效的 RoR IDE。祝你好运！

## 分享这篇文章