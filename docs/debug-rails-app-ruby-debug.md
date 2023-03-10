# 用 ruby-debug 调试你的 Rails 应用

> 原文：<https://www.sitepoint.com/debug-rails-app-ruby-debug/>

**今年年初，我通过 SitePoint 出版了一本名为 [*构建你自己的 Ruby On Rails Web 应用*](https://www.sitepoint.com/books/rails1/) 的书。如果我有一点偏见，请原谅我，但我相信，它仍然是最好的初学者 Rails 书籍，尽管自那以后已经发布了许多其他关于 Rails 的书籍。**

Ruby on Rails 框架最棒的一点是它一直在变化——它一直在改进和调整。不幸的是，这一变化意味着任何在我的书首次出版时购买了它的人都将面临使用哪个版本的 Ruby 的困惑:

*   你可以遵循这本书的建议，坚持使用 Ruby 1.8.4。这将允许你愉快地阅读这本书(包括关于调试的章节),但这也意味着你将无法获得最新安装的 Ruby 所带来的错误修复和安全更新的好处。
*   或者，你可以升级到一个更新的 Ruby 版本。然而，这样做会阻止你使用 breakpointer 客户端——这是第 11 章的主要内容。

在这本书出版的时候，breakpointer 客户端是调试 Rails 应用程序的最佳工具。但是，breakpointer 与最新版本的 Ruby 编程语言不兼容。

那么 Rails 开发人员应该做些什么呢？

在本文中，我们将研究一种替代的调试工具，用于调试 Rails 应用程序(或者任何 Ruby 脚本)，同时仍然保持最新版本的 Ruby 解释器。这个工具甚至计划与 Rails 的未来版本捆绑在一起。

我提到的神奇新工具叫做 [ruby-debug](http://rubyforge.org/projects/ruby-debug/) 。

快速警告:这不是一篇初学者的文章。我假设你已经读过我的书，或者对如何使用 Ruby on Rails 开发 web 应用程序有很深的理解。理想情况下，您将安装 Rails，并且能够舒适地使用交互式 Ruby (irb) shell。如果您熟悉本书中使用的示例应用程序 shovell，这也会有所帮助；[下载样本在家一起玩](https://www.sitepoint.com/examples/ruby-debug/shovell_ruby-debug_demo.zip)(注:下载大小为 2.2MB)。

##### 什么打破了断点？

在我们深入研究 ruby-debug 的内部工作原理之前，让我们简要地看一下为什么像 breakpointer 这样有用的工具会与它的设计语言不兼容。

断点库，breakpointer 客户端是其中的一部分，由 [Florian Gross](http://flgr.0x42.net/) 开发并向公众发布。它建立在名为`binding.of_caller`的 Ruby 扩展之上，该扩展依赖于 Ruby 的跟踪调用实现中的一个 bug。然而，这个错误在 Ruby 1.8.5 中被修复了！结果是`binding.of_caller`扩展和断点库都变得不可用。图书馆也不再被维护，所以它实际上已经死了。

##### 向 ruby-debug 问好

早在 2006 年 7 月， [Kent Sibilev](http://www.datanoise.com/) 发布了 ruby-debug 的第一个版本，将它描述为“标准 debug.rb 的一个更快的实现”。今天，ruby-debug 是调试 ruby 脚本最可行的选择——包括 Rails 应用程序。

虽然解释 ruby-debug 如何在幕后发挥它的魔力超出了本文的范围，但可以说 ruby-debug 使用了一个用 c 编写的本机编译的 ruby 扩展，结果是它表现得非常好，即使使用非常大的 ruby 脚本。

##### 比较功能集

正如我在书中的教程中所描述的，breakpointer 客户端允许我们在运行时进入应用程序，并从命令行对其进行探索。虽然 ruby-debug 工具包含了类似的功能，但它远远超出了老式 breakpointer 所能提供的功能。

与 breakpointer 不同，它从一个简单的 irb 提示符开始工作，ruby-debug 为你提供了一个更高级的 shell，类似于 C 编程语言的 GNU 调试器 [GDB](http://sourceware.org/gdb/) 提供的 shell。

在这个 shell 中，您可以:

*   在代码中前进和后退。
*   执行并跳过代码行(无需从代码编辑器窗口中复制和粘贴它们)。
*   列出停止应用程序的实际源上下文。
*   进入`irb`模式，使用 breakpointer 使用的 shell(如果你发现旧习惯很难改变)。

##### 安装 ruby-debug

下面的步骤将配置您的系统，以便使用 ruby-debug 进行调试。首先，我们需要安装 ruby-debug 库。因为它是作为一个 [RubyGems 包](http://rubyforge.org/frs/?group_id=126)发布的，安装就像输入下面的命令一样简单:

```
$ sudo gem install ruby-debug -y
```

安装脚本将提示您指明安装 gem 的平台。如果你在 Mac 或 Linux 系统上，选择选项 1；如果您在 Windows 机器上，请选择选项 2。安装过程应该类似于图 1 所示。

![Installing ruby-debug](img/5779047cdd90842001979e7d590166b6.png)

*Y 代表是，请！*
*通过使用`-y`开关调用 gem 命令，我们指示实用程序自动安装 ruby-debug 依赖的所有其他包。在这种情况下，这些包包括包含实际调试器代码的 ruby-debug-base 包；ruby-debug 包包含 CLI(命令行界面)。*

随着 ruby-debug 的顺利安装，我们现在可以在 Rails 应用程序中放置一些钩子了。对于这个例子，我们将使用书中完整的 shovell 应用程序作为使用 ruby-debug 进行调试的过程示例。如果您想了解这个示例，那么在进一步阅读之前，您应该下载示例应用程序 sleaglel。

##### 在铁轨上

让 Rails 应用程序知道 ruby-debug 就像在应用程序的`config/environments/development.rb`文件中添加一行一样简单:

```
# config/environments/development.rb 

require "ruby-debug"
```

对我们的开发环境进行这样的更改是有意义的(与`config/environment.rb`相比)，因为这是您应该执行调试的首选环境。目标应该是复制在其他环境中发生的任何错误，因此调试应该很少发生在测试或生产环境中。

ruby-debug 是 EdgeRails
*的一部分，许多开发人员喜欢走在前沿，并乐于使用不能保证像正式版本一样稳定的 Rails 版本来开发他们的应用程序，以便获得最新的功能补充。这个版本的框架被称为 EdgeRails。*

*如果您运行的是 EdgeRails，那么显式地将 ruby-debug 添加到您的应用程序环境中是不必要的——只需使用`--debugger`参数启动应用服务器来加载 ruby-debug 库。*

有了这个设置，就可以从 Rails 应用程序的根文件夹启动应用服务器了:

```
$ ruby script/server
```

图 2 显示了启动 Mongrel 服务器时的控制台输出。

![Starting the Mongrel application server](img/3ad82dc162dc49ea85f84f5472d73d5b.png)

*砖头和杂种狗而已*
如果你的开发环境使用了替代服务器，比如带有 FastCGI 的 lighttpd，你就不能把它作为调试平台。你需要运行韦伯里克或者[杂种狗](http://mongrel.rubyforge.org/)。

乍一看，控制台输出与我们运行该命令时所看到的没有太大区别。但是，在我们进行本练习时，请密切关注此窗口。

##### 调试应用程序

为了查看我们的调试器的运行情况，我们来看看我偷偷引入到应用程序代码中的一个问题。如果您查看图 3，您会注意到，尽管我们已经为提交的新故事提供了描述，但是它并没有显示在最终的故事页面上。如果你读过这本书，你会注意到这个例子和第 11 章的例子是一样的；我们将使用 ruby-debug，而不是用 breakpointer 来解决这个问题。

![: Story description missing from a newly submitted story](img/555f2af0bd91b96d8eea2141761743bb.png)

所以，让我们来解决这个问题。首先，将 debugger 关键字添加到`app/controllers/story_controller.rb`中的新动作，如下所示:

```
def new  

  @story = Story.new(params[:story])  

  @story.user = @current_user  

  if request.post? and @story.save  

    **debugger**  

    @story.tag_with params[:tags] if params[:tags]  

    flash[:notice] = "Story submission succeeded"  

    redirect_to :action => 'index'  

  end  

end
```

正如我们在这个问题上使用 breakpointer 时的情况一样，当你试图提交一个故事时，你现在会经历“挂起浏览器综合症”，这表明你的调试器语句已经生效，你准备好调试了。ruby-debug 没有启动单独的客户端来连接应用程序的内部工作，而是在启动应用服务器的终端窗口中打开了这个调试器 shell，如图 4 所示。

![ The ruby-debug interactive prompt appears within the server console](img/10673c761d3d9973b92a4f250cf2b5db.png)

在这个提示符下，您可以使用各种命令在应用程序执行过程中暂停时探索它。在整个示例中，我将使用字符`(rdb)`来指示 ruby-debug shell 提示符，在该提示符下键入的命令将以粗体显示，如下所示:

```
(rdb) **list**
```

##### ruby-debug 命令

接下来是对最重要的 ruby-debug 命令的简要概述，以及它们的功能描述。不要太担心记住每一个细节，内置的 help 命令会为您列出所有可用的命令。您还可以使用`help <commandname>`语法来获得特定命令的帮助。

*   `backtrace`:显示执行堆栈的踪迹，类似于应用程序引发异常时显示的内容。
*   `break/delete`:显示应用程序中已经设置的断点列表。这个命令也用于在 ruby-debug shell 中设置新的断点，或者删除现有的断点。
*   `cont`:离开当前调试器外壳，继续执行应用程序，直到遇到下一个断点。
*   `irb`:在当前执行点调用一个交互式的 Ruby 解释器，类似于断点库使用的 shell。
*   `list`:显示当前执行点周围的代码片段。(我们一会儿会用到这个命令。)
*   `method/method instance`:分别探索可用的类方法和实例方法。
*   继续一步一步地执行——这是对断点库的巨大改进。
*   `p/pp`:分别是 *print* 和 *pretty print* 的简称，这些命令可以用来对 Ruby 表达式求值，并向控制台显示变量值。
*   `quit`:退出调试器。请注意，如果从命令行调用，这也将退出应用服务器，如上所示。要退出当前的调试会话，使用`cont`。
*   `reload`:从磁盘重新加载 Ruby 源文件。如果您已经更改了类定义，并且希望在不离开当前调试会话的情况下动态地重新加载它们，这将非常有用。

要查看所有可用命令和选项的列表，请使用`help`命令。

##### 在壳中四处移动

既然我们已经被丢进了一个 shell，那么是时候利用我们刚刚讨论过的一些命令来找到问题的根源了(我们的故事没有描述就结束了)。

首先，让我们找出我们在故事提交执行中的确切位置。这是 list 命令的工作，如图 5 所示。

![The list command displaying the current location in a paused application](img/04203a3c4f417e29ec071ee14bf7ccea.png)

正如您所看到的，list 命令显示了一个源代码列表，其中有一个箭头指向下一个要执行的代码行。

此时，我们可以从 irb shell 中检查工作环境的一部分，比如`@story`实例变量或`params`散列。在提示符下键入`irb`，让我们研究一下存储在`@story`变量中的`story`对象的`description`属性:

```
(rdb) **irb**  

irb> @**story.description**  

=> nil
```

检查这个变量的输出如图 6 所示。

![Using irb from within ruby-debug to inspect a variable](img/1ce99f569aa8e63a14d323b27066edaa.png)

正如您所看到的，即使我们在表单中输入了一个措辞优美的故事描述，新的`story`对象的相关描述属性是`nil`，或者是空的。

但是等一下！ruby-debug 中没有一个命令可以让我们计算 ruby 表达式和检查变量，而不需要经历使用 irb 的麻烦吗？确实有！让我们退出 irb 外壳(使用命令`exit`)并继续从外壳外部探索。

回到原生的 ruby-debug shell，我们可以使用`pp` (pretty print)命令来显示我们的故事描述的值，一旦它通过 web 表单被填充:

```
(rdb) **pp params[:story][:description]**  

=> nil
```

如果您在 ruby-debug shell 中键入这个内容，您会看到它也返回一个空对象。所以，作为最后一招，让我们看一下完整的`params`散列，它包含所有已提交的表单字段的值，不管它们位于哪个范围:

```
(rdb) pp **params**
```

![Using the pp command to inspect variables](img/68d410c39e1abf4a402cb66011927a28.png)

正如您所看到的，pp 实际上为我们格式化了输出，使其比我们习惯在标准 irb shell 中看到的输出更具可读性(因此有了“pretty”这个词)。虽然这个特性并不是 ruby-debug 独有的(`pp`库也可以在 ruby-debug 之外加载),但是 ruby-debug 自动使用它确实很方便。

我在图 7 中突出显示的部分是问题的根源。如您所见，描述确实出现在`params`散列中，但这不是我们故事的一部分。虽然故事的`name`和`link`属性在`params[:story]`哈希中很好地结合在一起，但是描述在`params[:description]`中是分开的。

这是怎么发生的？如果我们看一下我们的表单模板(位于`app/views/story/new.rhtml`)，似乎我“不小心”删除了几个字符:

new . rhtml


new . rhtml


我的代码没有遍历`form_for`助手提供的`FormBuilder`对象，而是调用了`text_area`而没有应用表单字段的范围。结果，描述作为一个单独的条目出现在`params`散列中，我们的故事从未得到它的价值。

##### 但是 ruby-debug 中的所有花哨工具呢？

诚然，我们不需要使用 ruby-debug 的任何更高级的特性来调试这个示例问题。但是当我们被迫调试更复杂的代码时，ruby-debug 的高级特性变得非常方便。

我们先来看看踩法。为此，我们需要将调试器语句移动到一个方法中，该方法包含的代码比前一个示例多一点(这样我们就可以实际单步执行每一行)。这个任务的最佳人选是我们的`StoryController`的`vote`行动；下面是这个方法的一个版本，我在其中添加了调试器语句:

story_controller.rb(节选)
` 
def vote  
 **debugger**  
 @story = Story.find(params[:id])  
 @story.votes.create(:user => @current_user)  

 respond_to do |wants|  
   wants.html  { redirect_to :action => 'show', :permalink => @story.permalink }  
   wants.js    { render }  
 end  
end`

要在这个新位置调用调试器，使用`cont`命令退出当前的调试会话(如果还没有退出的话)。这将使你停止的浏览器复活，并允许你继续浏览铲应用程序。现在，从即将发布的故事队列中选择一个故事，然后点击投票！按钮再次使用调试器。

之前，我们看到了如何使用`list`命令来指示我们的应用程序当前暂停在源代码的什么地方。暂停时，我们可以使用`next`命令前进到下一行代码。键入`next`将显示下一行的常规 Rails 日志输出，然后返回到 ruby-debug 提示符。从这里，您可以再次使用 list 来检查您在应用程序中的新位置，就像我在图 8 中所做的那样。

![Using next to advance one line of code](img/a118ece168cdca76becd1cb5bc694aae.png)

要探索您感兴趣的对象提供的方法，您可以使用`method`命令。下面的命令将产生一个由`@story`对象提供的实例方法列表，按字母顺序排序，如图 9 所示:

```
(rdb) **method instance @story**

`method`命令也可以用来列出类方法。下面的命令将产生一个由`Story`类提供的按字母顺序排序的类方法列表，如图 10 所示。

```

```
(rdb) **method Story**

##### 手动设置断点

虽然使用`next`命令在您知道应用程序中的确切位置时会很有用，但在 Rails 应用程序中就没那么有用了。在某些情况下，单步执行的级别可能过于细粒度，可能导致您单步执行多行核心库文件，而不是您自己的代码。

为了更好地控制调试器停止执行的位置，您可以在需要的位置手动设置断点。可以通过指定以下任一项来设置断点:

*   文件名和行号的组合

*   类名和实例方法或类方法的名称

作为设置手动断点的一个实际例子，我们将把暂停点从其当前位置(在`StoryController`的`vote`动作内)移动到 RJS 模板，当请求相同的动作来呈现 JavaScript 响应时，就会呈现该模板。我们将在不打开文本编辑器的情况下完成所有这些工作，或者遍历当前执行点和 RJS 模板代码之间的每一行。

RJS 模板的最后一行`app/views/story/vote.rjs`写道:

```
vote.rjs (excerpt)   

page[:vote_history].replace_html :partial => 'vote',   

  :collection => @story.latest_votes
```

因此选择一个`Story`对象的`latest_votes`实例方法作为我们的新断点是有意义的。为此，在 ruby-debug shell 中执行以下命令:

```
(rdb) **break Story#latest_votes**
```

现在，您可以通过在 ruby-debug shell 中键入`cont`命令来释放当前断点。执行将继续，直到执行了`@story.latest_votes`调用，此时应用程序将再次暂停。要验证我们是否停在了我们期望的地方，输入`list`。图 11 确认了我已经在`latest_votes`方法的开始停止了我的应用程序。

![Stopping at a breakpoint that was set by specifying class and method name](img/7f04aefae4298d798b74758aca40e1fc.png)

##### 源代码重载

Rails 应用程序在开发模式下运行时，会自动采用对源文件所做的所有更改，而不需要您重新启动应用服务器。ruby-debug 包含一个类似的特性，可以避免在 list 命令输出的堆栈跟踪和清单中显示过时的代码。如果可以的话(性能方面)，在 ruby-debug 提示符下键入以下命令:

```
(rdb) **set autoreload**
```

有了这个设置，ruby-debug 会在需要的时候自动从磁盘重新加载你的 ruby 脚本。如果这明显减慢了您的开发进度，您可以在任何您认为您的代码已经过时的时候周期性地调用`reload`命令。

##### TextMate 集成

那些在 Mac 上使用 [TextMate](http://macromates.com/) 编辑器开发 Rails 应用程序的开发人员会很高兴地知道，ruby-debug 的作者碰巧也是 TextMate 的粉丝。幸运的是，ruby-debug 附带了一些不错的钩子，您可以使用它们将调试器与编辑器集成在一起。

首先，您可以使用`tmate`命令打开您的应用程序当前暂停的文件。这大大简化了终端窗口和编辑器之间的往返。

我还建议您为 TextMate 安装 [Ruby 调试包。这个包给你从 TextMate 内部设置断点的最终控制权。](http://datanoise.com/assets/2007/1/27/Ruby_Debug.zip)

安装完这个包之后，您需要以稍微不同的方式启动应用程序。下面是如何启动您的应用服务器来利用 ruby-debug 的远程调试工具(`$`表示我们正在操作系统外壳中键入这个工具):

```
$ **rdebug -sn ./script/server**
```

与我们目前使用的本地调试工具不同，您可以安全地最小化启动应用服务器的终端窗口——我们需要启动一个单独的调试客户端(从新的命令提示符)来启用 TextMate 和 ruby-debug 之间的通信。

打开新的终端窗口，键入以下命令:

```
$ **rdebug -c**
```

您应该看到消息“Connected”，如图 12 所示。

您需要让这个窗口保持打开并可访问，因为一旦遇到断点，这个窗口将显示 ruby-debug 控制台输出。

![Using the TextMate Ruby bundle to connect to ruby-debug’s remote debugging tool](img/5eac2d049a0190264f93fea7b6c3a18d.png)

一旦完成，您就可以使用该包唯一的键盘快捷键 Cmd-Shift-B 来打开所有可用命令的菜单，如图 13 所示。

![Displaying the TextMate Ruby bundle's menu options](img/89d82179468ae4f7f17ace978a3f66e8.png)

您可以使用此菜单在当前 TextMate 编辑窗口中的光标位置设置断点。您还可以显示或删除所有已经设置的断点，甚至可以从文本编辑窗口中方便地中断或退出调试 shell。

##### 结论

ruby-debug 是使用断点库调试 ruby 应用程序的老技术(当然也是不成熟的技术)的优秀继承者。更好的是，它兼容所有最新发布的 Ruby 语言解释器。ruby-debug 附带了许多受欢迎的快捷方式和强大的导航命令，使调试 ruby 脚本和 Rails 应用程序成为一种愉快而有益的体验。

为了进一步阅读 ruby-debug 和许多有用的文章以及 ruby 资源的链接，我强烈推荐你订阅 Kent Sibilev 的博客。

## 分享这篇文章

```