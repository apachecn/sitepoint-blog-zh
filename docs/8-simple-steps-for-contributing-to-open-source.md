# 为开源做贡献的 8 个简单步骤

> 原文：<https://www.sitepoint.com/8-simple-steps-for-contributing-to-open-source/>

![fork](img/413de55a00938f99e42223ae5fce5f85.png)Ruby 社区和其他社区一样拥抱开源。如今，任何值得信赖的 Ruby 开发人员都有望创作或贡献一个开源库。Git 和 git hub(T2)使得代码共享变得简单，贡献也变得简单。

然而，第一次为开源库做贡献可能会非常困难。如果你和我一样，我被自我怀疑和害怕自己会“做错”所困扰。我担心其他开发人员的嘲笑，这些都是多年开源贡献的结果。他们会撕碎我的代码，用我的头像作为可怕代码的迷因。

我最终克服了这种恐惧。我参与了一些开源项目( [AngularJS](http://angularjs.org "AngularJS") 、 [neography](https://github.com/maxdemarzi/neography "Neography") 、 [jquery-ui](http://jqueryui.com "JQuery UI") ，仅举几例)，我可以告诉你这并不坏。如果你陷在自我怀疑阶段，但想跳进去，你可能会问“第一步是什么？”或者“我如何做出贡献？”好吧，我的目标是通过向你展示我对奇妙的 [guard-jruby-rspec](http://github.com/jkutner/guard-jruby-rspec) gem 的贡献来回答这些问题。

步骤是:
1。搜索
2。叉子
3。准备好你当地的环境
4。写个测试
5。修好它
6。提交并推动它
7。测试一下
8。发出拉取请求

## 宝石

我目前正在做一个使用 RSpec 的 JRuby 项目。连续地、自动地和(最重要的)快速地运行测试是最重要的。为此，我引入了 guard-jruby-rspec gem，它运行我的测试的速度比我在任何 ruby 项目中见过的任何其他解决方案都要快。《与 JRuby 一起部署的 T2》一书的作者乔·考特纳在 gem 方面做得非常出色。(Joe 也评论了这篇文章，也非常感谢！)

简单地说，让我解释一下 guard-jruby-rspec 是做什么的。gem 是一个需要保护的插件，根据其 Github 页面，它是“一个命令行工具，可以轻松处理文件系统修改事件。”它最常用于监控 Ruby 或 Rails 项目中的文件，并在文件改变时触发受影响的测试。如果控制器文件被更改，相应的测试文件将自动运行。这是非常宝贵的，因为您在编码时会得到持续的反馈。自动化所有的事情。

Guard 有一个欣欣向荣的插件框架，guard-jruby-rspec 就是众多插件中的一个。正如您可能已经猜到的，guard-jruby-rspec 基于 Guardfile 中的“watchers”对文件运行 rspec。它利用 JRuby 来最小化每个测试的加载时间。

例如，在 Rails 应用程序中，如果 Guardfile 看起来像:

```
interactor :simple
guard 'jruby-rspec' do
  watch(%r{^spec/.+_spec.rb$})
  watch(%r{^lib/(.+).rb$})     { |m| "spec/lib/#{m[1]}_spec.rb" }
  watch('spec/spec_helper.rb')  { "spec" }

  # Rails example
  watch(%r{^app/(.+).rb$})                           { |m| "spec/#{m[1]}_spec.rb" }
  watch(%r{^app/(.*)(.erb|.haml)$})                 { |m| "spec/#{m[1]}#{m[2]}_spec.rb" }
  # HEY THIS LINE IS IMPORTANT
  watch(%r{^app/controllers/(.+)_(controller).rb$})  { |m| ["spec/routing/#{m[1]}_routing_spec.rb", "spec/#{m[2]}s/#{m[1]}_#{m[2]}_spec.rb", "spec/acceptance/#{m[1]}_spec.rb"] }
  watch(%r{^spec/support/(.+).rb$})                  { "spec" }
  watch('app/controllers/application_controller.rb')  { "spec/controllers" }

  # Capybara features specs
  watch(%r{^app/views/(.+)/.*.(erb|haml)$})          { |m| "spec/features/#{m[1]}_spec.rb" }
end
```

每当匹配观察者的文件改变时，规范就会运行。如果你传递一个块给观察者，你可以缩小将要运行的规格。换句话说，当我更改一个模型文件时，它将只运行模型规范。

## 问题是

在我工作的时候，我注意到每当我更改并保存控制器文件时，Guard 都会失败。错误如下:

```
16:29:43 - ERROR - Guard::JRubyRSpec failed to achieve its &lt;run_on_changes&gt;, exception was:
[# NoMethodError: undefined method `match' for #&lt;Array:0x4816505b&gt;
[#] /home/vagrant/ws/rental_express/ROOT/rails/.bundle/jruby/1.8/gems/guard-rspec-2.5.4/lib/guard/rspec/inspector.rb:50:in `spec_folder?'
[#] /home/vagrant/ws/rental_express/ROOT/rails/.bundle/jruby/1.8/gems/guard-rspec-2.5.4/lib/guard/rspec/inspector.rb:38:in `should_run_spec_file?'
[#] /home/vagrant/ws/rental_express/ROOT/rails/.bundle/jruby/1.8/gems/guard-rspec-2.5.4/lib/guard/rspec/inspector.rb:30:in `clean'
[#] org/jruby/RubyArray.java:2395:in `select'
[#] /home/vagrant/ws/rental_express/ROOT/rails/.bundle/jruby/1.8/gems/guard-rspec-2.5.4/lib/guard/rspec/inspector.rb:30:in `clean'
[#] /home/vagrant/ws/rental_express/ROOT/rails/.bundle/jruby/1.8/gems/guard-rspec-2.5.4/lib/guard/rspec/inspector.rb:62:in `clear_spec_files_list_after'
[#] /home/vagrant/ws/rental_express/ROOT/rails/.bundle/jruby/1.8/gems/guard-rspec-2.5.4/lib/guard/rspec/inspector.rb:29:in `clean'
[#] /home/vagrant/ws/rental_express/ROOT/rails/.bundle/jruby/1.8/gems/guard-rspec-2.5.4/lib/guard/rspec.rb:85:in `run_on_changes'
[#] /vagrant/git/guard-jruby-rspec/lib/guard/jruby-rspec.rb:74:in `run_on_changes'
[#] org/jruby/RubyKernel.java:2080:in `send'
...
```

我开始四处寻找，看看我是否能找出如何修复这个错误，这就是我怎么做的。

## 第一步:搜索

在许多情况下，你不是第一个发现问题的人。尤其是如果问题出在使用良好的 gem 或框架上，比如 Rails。在您开始研究代码之前，明智的做法是询问 Google 是否有人遇到了这个问题。

此外，您应该在 Github 上找到代码库(绝大多数开源 gems/代码都可以在 Github 上找到),并为您的问题搜索它的问题。有可能其他人也遇到了这个问题和/或正在解决这个问题。

你在寻找什么？我发现使用错误消息的内容(如果有的话)有很高的成功率。取出行号和您的本地计算机/目录名，并搜索错误。在本例中，我搜索了以下内容:

*   NoMethodError:数组的未定义方法“match”
*   JRuby Guard NoMethodError:数组的未定义方法“match”

他没说。

如果你没有错误或者你的错误没有帮助，试着搜索相关的重要信息。在这种情况下，我可能会搜索“Guard JRuby Rspec Rails Array match”并查看弹出的内容。大多数情况下，你会得到几个 [StackOverflow](http://stackoverflow.com) 的点击，然后你就踏上了康复之路。然而，这不是我的情况。悲伤的熊猫。

我在 Github 上找到了[代码，并在存储库中搜索了“数组的未定义方法匹配”和 BINGO！果然，其他人也有同样的](http://github.com/jkutner/guard-jruby-rspec)[问题](https://github.com/jkutner/guard-jruby-rspec/issues/34)。它也只有一天大，所以水里还有血。

我添加了“我也收到了”的评论，让他们知道我使用的是什么版本的 gem。在这种情况下，我还添加了“我计划进一步调查”，让他们知道我正在尝试修复它。继续第 2 步…

## 叉子

由于我正在研究的 gem 的代码在 Github 上，我可以很容易地复制它。这被称为“分叉”,是 Github 的奇妙之处使之成为可能的过程中的一个步骤。有些人认为分叉是一个饭桶的事情，但它不是。forking 所做的只是将存储库复制/克隆到 Github 上您的用户下的一个位置。在我的例子中，我现在有了一个可以随心所欲地破解的库。

一旦我有了一个分叉，我喜欢把它添加到我的 Gemfile，并确保问题仍然发生。为了将来自 github 存储库的 gem 添加到您的 gem 文件中，您需要执行以下操作:

```
gem 'guard-jruby-rspec', github: 'ruprict/guard-jruby-rspec'
```

在快速的`bundle`之后，我运行`guard`并确保问题仍然存在。确实是。

现在，我需要把这段代码放到我的笔记本电脑上，这样我就可以开始工作了。这是我本地终端的一个简单的`git clone git@github.com:ruprict/guard-jruby-rspec.git`,我们用煤气做饭。第三步，该你了。

## 步骤 3:准备您的本地环境

好了，我已经克隆了回购并切换到我的本地目录。当我在做这样的事情时，我喜欢把我的工作封闭起来，所以我用 [RVM](http://rvm.io) 因为它太棒了。

由于这是一个 JRuby gem，我需要确保我已经安装了 JRuby(我使用的是 1.7.4，所以`rvm install jruby`)，然后创建一个 gemset。我将我的 gemset 命名为“guard-jruby-rspec ”,并在目录中添加了一个`.ruby-version`和`.ruby-gemset`,这样当我在这个目录中时，它就会切换到正确的环境。你也可以使用`.rvmrc`文件(链接)来做这件事，但是一切似乎都在朝着`.ruby-version`的方向发展。

一旦你在新的 gemset(或其他封闭环境)中，运行`bundle install`来安装 gem 的所有依赖项。大多数 gemspec 都有一个 Gemfile，该文件指向 gem spec 文件中列出的依赖项。你会看到你所有的依赖都过去了，就像这样:
![Bundle install](img/832c19b2cbd4c35c884e13af56ffa82a.png "Bundler")

我们现在到了准备开发环境的最重要的一步:运行测试/规范。这是红宝石，所以所有的宝石都应该有一些测试。在现有测试在您的环境中成功运行之前，您不应该开始开发。最常见的是运行`rake test`或`rake spec`的简单情况，或者在这种情况下，运行`rspec`。

![Tests passing](img/312bd8a2c747d6a75268425a970a9b20.png "Tests Passing")

好的，测试通过了。太棒了。现在我们可以黑了。

我喜欢为我的工作创建一个 git 分支，这样“原始的”环境就离我不远了。

```
git checkout -b array_error_in_custom_watcher
git push -u origin array_error_in_custom_watcher
```

第二个命令强制我的本地分支跟踪我的 github 存储库中同名的分支。我喜欢。

许多 gems/存储库在它们的自述文件中会有一个关于如何贡献的部分，所以请确保您已经阅读了该部分并遵循了所要求的过程。guard-jruby-rspec 没有我能找到的(但是 Angular 有一个[怪物](http://docs.angularjs.org/misc/contribute "AngularJS Contribution Guide"))，所以我按照我所知道的“最常见”的过程。

## 步骤 4:编写测试

测试驱动开发告诉我们，您应该编写一个测试来重现您正在修复的任何 bug。有时候，这很容易。其他时候就没那么多了。在这种情况下，它在中间的某个地方。我不知道 Guard 是如何工作的，所以我必须调查 Guard 是如何组合在一起的，以便找出如何进行测试来重现错误。

在这种情况下有点困难，因为错误实际上发生在 guard-rspec 代码中。不过，guard-rspec 不太可能有这个问题，因为似乎没有人抱怨这个存储库上的这个错误。

更令人沮丧的是，我想我知道解决办法。在这种情况下，当一个测试目标数组从一个观察程序块中传递过来时，我们在`Array`上得到一个`NoMethodError`。`Array`发布错误，我们在观察器中返回一个`Array`，这不仅仅是巧合。回过头来看看 Guardfile，看看我说的那一行。

查看 stacktrace，最后一次测试是在 guard 上——JRuby-r spec 代码在`run_on_changes`方法的第 74 行，看起来像:

```
def run_on_changes(raw_paths)
  unload_previous_examples
  @reloaders.reload(raw_paths)

  unless @custom_watchers.nil? or @custom_watchers.empty?
    paths = []
    raw_paths.each do |p|
      @custom_watchers.each do |w|
        if (m = w.match(p))
          paths << (w.action.nil? ? p : w.call_action(m))
        end
      end
    end
    super(paths) # THIS IS LINE 74
  end
end
```

这个`paths`变量是一个文件数组，它基于传递给函数的`raw_paths`参数中的更改来运行。通过输入各种`puts`语句，我知道`paths`失败时是一个数组的数组。问题是，我如何编写一个测试来重现这个问题？

首先，我查看现有的测试。我想尝试使用相同的风格，当然也想利用相同的技巧(嘲讽等)，所以我浏览测试文件，找到我认为最适合测试的地方。我最初的几次尝试要么“错误地”中断，要么在应该失败的时候通过。一些现有的测试模拟两个对象`runner`和`inspector`。通过在 guard-rspec gem 中跟踪代码(`super`调用为`Guard::RSpec`调用`run_on_changes`方法)，我意识到对`inspector.clean`的嘲弄调用是我想要的关键。

该调用接受一个路径数组，如果一切正常，它不应该将数组作为外部数组中的元素。有了这些知识，我就可以编写一个期望测试，确保对 inspector 的模拟调用具有适当的参数。

```
# guard-jruby-rspec/spec/guard/jruby-guard_spec.rb:226
it "works with watchers that have an array of test targets" do
  subject = described_class.new([Guard::Watcher.new(%r{^spec/(.+)$}, lambda { |m| ["spec/#{m[1]}_match", "spec/#{m[1]}_another.rb"]})])

  test_targets = ["spec/quack_spec_match", "spec/quack_spec_another.rb"]

  inspector.should_receive(:clean).with(test_targets).and_return(test_targets) # THIS IS THE TEST
  runner.should_receive(:run).with(test_targets) { true }
  subject.run_on_change(['spec/quack_spec'])
end
```

该测试创建一个观察器，它返回一个测试位置数组。`test_targets`变量是检查员在工作时应该收到的信息。当我运行这个测试时，我得到了一个期望失败，因为对`inspector.clean`的调用得到了一个数组，该数组的第一个元素是数组。

如果这看起来令人畏惧，其实不然。实际过程花了我一个小时左右，有很多失败，追雁等。每一次失败，我都会学到一点点关于代码的知识，直到我明白我需要如何构建我的测试。有一些工具可以使问题的调查变得更加容易。

例如，我最近发现了蒂姆·波普的《我应该获得一枚勋章》中的[维姆·邦德勒](https://github.com/tpope/vim-bundler)。这个出色的插件允许我用一个简单的`Btabedit <gem name>`打开我的包中的宝石，我可以在这里编辑宝石，并且永远不会离开我的编辑器。当我找到维姆·邦德勒时，我像一个狂热的披头士迷一样尖叫起来。真的。我的大部分调查是放置`puts`语句等。其他的 gem(比如`Guard::RSpec`)，这让我可以看到在我修改代码时发生了什么。

啊哈，测试显示了这个问题。第五步将是轻而易举的。

## 第五步:修复

正如我提到的，编写测试来发现问题几乎总是比修复代码更难。这是真的，因为修复只是简单地将`.flatten`添加到`paths`数组中，我们传递了我们的继承链。

```
def run_on_changes(raw_paths)
  unload_previous_examples
  @reloaders.reload(raw_paths)

  unless @custom_watchers.nil? or @custom_watchers.empty?
    paths = []

    raw_paths.each do |p|
      @custom_watchers.each do |w|
        if (m = w.match(p))
          paths << (w.action.nil? ? p : w.call_action(m))
        end
      end
    end
    super(paths.flatten) # Here be the change, mon.
  end
end
```

测试再次运行并通过。我坐下来，沉浸在美好的感觉中。

值得注意的是，这可能是也可能不是最好的解决方案。在这种情况下，代码不多，我有一个测试。我对代码很满意，可以继续第 6 步了。

## 第六步:提交并推动它(站起来！)

我现在可以提交代码了。这里需要注意的是:确保您的更改只包含修复所需的内容。在我最早的一篇文章中，我添加了`puts`语句或其他调试语句，留下了一些空白。这使得我的提交接触了更多不必要的文件，这并不酷。

既然这样，下面是我的`git diff`:

![Git Diff](img/4c08b1aaa701e6ce09c1bba09e9c317b.png "Git Diff")

如你所见，我的改变只是测试和改变。漂亮又干净。

此外，确保您不会意外提交任何新文件。如果你有做`git add .`的习惯，那么你就有提交临时编辑器文件或其他任何可能在你工作时悄悄进入你的路径的东西的危险。

最后，让你的提交信息具有信息性。一个很好的例子是 [AngularJS 提交消息约定](https://docs.google.com/a/goodrichs.net/document/d/1QrDFcIiPjSLDn3EL15IJygNPiHORgU1_OOAqWjiDU5Y/edit?pli=1#)。这个例子可能是 SuperDuper Commit 消息谱的末端，但是如果您在 Commit 消息中放入太多信息，不太可能有人会抱怨。

以下是我的提交消息:

![Commit message](img/96dfb6dab44e6b3b7136bb089355129d.png "Commit Message")

## 测试它

我喜欢回到我最初发现 bug 的项目，将我的 github 库添加到 Gemfile，bundle 中，然后确保问题不再出现。在这种情况下，我将
`gem "guard-jruby-rspec, github: "ruprict/guard-jruby-rspec, branch: "array_error_in_custom_watcher"`
添加到 Gemfile 并运行`bundle update guard-jruby-rspec`。

现在，重新创建或者回到测试 bug 的环境。对我来说，我简单地添加了自定义观察器，它将一组测试目标返回到我的项目中的 Guardfile，启动 guard ( `guard`)，并更改了一个控制器文件。我放入一个明显的失败，然后删除它。

![Controller test](img/f89e5d8a80d868bb1e82a26a8b3a3794.png "No more watcher error")

因为守卫没有到处爆炸，我很好。错误，她被征服了！

## 步骤 8:发出拉取请求

这一刻终于到来了。你即将踏上开源贡献之旅，它将带你去遥远的地方，并向你介绍外星人。或者什么的。
转到 GH repo 的分行，点击*拉动请求*按钮。

在您的邮件中，提及您可能已经看到的任何问题，这些问题已经得到解决或受到 PR 的影响。在我的例子中，我提到了我发现有同样错误的问题。你可以在评论中简单地通过链接来提及某个问题。如果您的提交消息是正确的，那么除了问题引用之外，您不需要输入太多内容。在这里，你的自我怀疑可能会浮出水面，但尽管去做吧。我还没有遇到一个 OSS 作者不感激试图帮助他的人。即使你搞砸了，也要学习、修正并重新提交。这是你成为 OSS 贡献机器的唯一方法。

![Pull Request](img/7f186daeae5b9efef61c908c967d8594.png "Pull Request")

恭喜你！可选的第 9 步是喝一杯成人庆祝饮料！

## 包裹

我希望这篇文章能帮助人们克服对开源的恐惧和执着。相信我，如果我能做到，任何人都能做到。如果你很难找到需要解决的 bug，我建议你注册[代码分类](http://www.codetriage.com/ "Code Triage")，它的存在是为了帮助你在流行的开源库中找到合适的地方。

祝你好运！

## 分享这篇文章