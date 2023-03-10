# 用突变体进行突变试验

> 原文：<https://www.sitepoint.com/mutation-testing-mutant/>

![mutant_logo](img/1cd1a6f55e5efb488c986773b8b12988.png)

作为 ruby 爱好者，我们对测试并不陌生。单元测试不仅仅是最佳实践，它还是教条。代码被认为是坏的，直到我们有测试证明不是这样。但是并不是所有的测试套件都是一样的。编写好的测试是一门艺术，糟糕的测试可能比没有测试更糟糕，会减慢开发速度，而不会增加我们对代码的信心。

那么谁来测试这些测试呢？这个问题可能看起来很无聊，但答案很简单，[变种人](https://github.com/mbj/mutant)有！

突变体是一个*突变测试器*。为了理解它的功能，让我们想象一下用手来做它的工作。在你的团队中指定一个人做破坏者，他们的工作是挑选一段经过充分测试的代码，并故意引入缺陷。如果可以做到这一点而不引发警报，换句话说，不导致测试失败，那么测试就是遗漏的。然后由原作者添加测试用例来检测破坏。

这样做的时间足够长，找到仍然可以被随意篡改的代码将变得非常困难。与传统的“线覆盖”工具形成对比。100%的代码覆盖率意味着代码不会被破坏吗？肯定不是！事实上，可以编写测试来执行每一行代码，而不对它们做出任何有用的断言。我们的破坏者会有多开心！

变种人自动化了这个过程，它以许多小的方式改变你的代码，创造出成群的变种人。如果这种反常代码导致测试失败，变异体被认为被杀死。只有在生产线的末端，没有一个变异体存活，你才能达到 100%的变异覆盖率。

我们将用现实世界中的一个例子来解释突变体，演示其工作方式和工作流程。我们正在运行的示例将是一个工具，它将一个本地 HTML 文件作为其输入，并将所有本地和远程资产捆绑在一个目录中，因此可以在没有网络连接的情况下查看该文档。下面是它的使用方法:

```
AssetPackager.new('foo/bar.html').write_to('baz')
```

结果是一个文件`baz.html`，和一个包含所有样式表、脚本和图像的目录`baz_assets`。当遇到类似

```
<link rel="stylesheet" src="http://example.com/style.css" />
```

它将下载样式表，根据其内容给它一个唯一的文件名:

```
<link rel="stylesheet" src="baz_assets/48d6215903dff56238e52e8891380c8f.css" />
```

我只有空间来复制这里有趣的部分。完整的修订历史可以在 Github 上找到[。](http://github.com/plexus/asset_packager)

作为第一步，我们将编写一个方法，可以处理我们想要处理的不同类型的 URI。HTTP 和 HTTPS URI 的需要被检索，相对的 URI 和 URI 的使用`file://`方案将在本地文件系统中被搜索。

这是实现:

```
module AssetPackager
  class Processor
    attr_reader :cwd

    # @param cwd [Pathname] The working directory for resolving relative paths
    def initialize(cwd)
      @cwd = cwd
    end

    def retrieve_asset(uri)
      uri = URI(uri)
      case
      when %w[http https].include?(uri.scheme) || uri.scheme.nil? && uri.host
        Net::HTTP.get(uri)
      when uri.scheme.nil? || uri.scheme == 'file'
        File.read(cwd.join(uri.path))
      end
    end
  end
end
```

我们测试的第一个版本。对于本地的 URI，我们将指向一个 fixture 文件。对于远程 URI，我们将模拟对`Net::HTTP.get`的调用。

```
describe AssetPackager::Processor do
  let(:cwd) { AssetPackager::ROOT }
  let(:processor) { AssetPackager::Processor.new(cwd) }

  describe '#retrieve_asset' do
    subject(:asset) { processor.retrieve_asset(uri) }

    shared_examples 'local files' do |uri|
      it 'should load the file from the local file system' do
        expect(processor.retrieve_asset(uri)).to eq 'section { color: blue; }'
      end
    end

    shared_examples 'remote URIs' do |uri|
      it 'should retrieve the file through Net::HTTP' do
        expect(Net::HTTP).to receive(:get).with(URI(uri)).and_return('abc')
        expect(processor.retrieve_asset(uri)).to eq 'abc'
      end
    end

    fixture_pathname = AssetPackager::ROOT.join 'spec/fixtures/section.css'

    include_examples 'local files', fixture_pathname.to_s
    include_examples 'local files', "file://#{fixture_pathname}"

    include_examples 'remote URIs', 'http://foo.bar/baz'
    include_examples 'remote URIs', 'https://foo.bar/baz'
  end
end
```

根据 rpsec 的说法，一切正常，我们当然覆盖了`retrieve_asset`的所有线路。让我们看看变种人怎么说。

```
mutant -I lib -r asset_packager --use rspec 'AssetPackager*'
```

那是一口。首先，告诉 Mutant 如何使用 Ruby 本身使用的相同的`-I`、`--include`和`-r`、`--require`标志加载我们的测试代码。

然后指定使用哪种“策略”来“杀死”变种人。目前只实施了 RSpec 策略，这使得挑选变得容易。最后，手变种人一个或多个“模式”。在这种情况下，告诉它在完整的`AssetPackager`名称空间上施展魔法(注意`*`)。

我们也可以将单个类、模块、类方法(`Foo::Bar.the_method`)或实例方法(`Foo::Bar#an_instance_method`)的名称传递给它。

基于*模式*，变种人将寻找*对象*拖到实验室，让他们的基因重新排列。突变体目前可以处理实例和类方法。不支持像`attr_accessor`或类级 DSL 这样的元编程结构，尽管有通过插件处理特定 DSL 的说法。

```
AssetPackager::Processor#initialize
........
(08/08) 100% - 0.45s
AssetPackager::Processor#retrieve_asset
...................F...........F.................
(47/49)  95% - 3.49s

evil:AssetPackager::Processor#retrieve_asset
@@ -1,10 +1,10 @@
 def retrieve_asset(uri)
   uri = URI(uri)
   case
-  when ["http", "https"].include?(uri.scheme) || (uri.scheme.nil? && uri.host)
+  when ["http", "https"].include?(uri.scheme)
     Net::HTTP.get(uri)
   when uri.scheme.nil? || (uri.scheme == "file")
     File.read(cwd.join(uri.path))
   end
 end

evil:AssetPackager::Processor#retrieve_asset
@@ -1,10 +1,10 @@
 def retrieve_asset(uri)
   uri = URI(uri)
   case
   when ["http", "https"].include?(uri.scheme) || (uri.scheme.nil? &&amp; uri.host)
     Net::HTTP.get(uri)
   when uri.scheme.nil? || (uri.scheme == "file")
-    File.read(cwd.join(uri.path))
+    File.read(uri.path)
   end
 end

(47/49)  95% - 3.49s
Subjects:  2
Mutations: 57
Kills:     55
Alive:     2
Overhead:  29.31%
Coverage:  96.49%
Expected:  100.00%
```

仔细查看突变体的输出，它发现了两个需要操作的*对象*、`#initialize`和`#retrieve_asset`。对于每一个，输出看起来很像任何旧的测试运行程序，用绿点和红 F 表示成功或失败。不过，在这种情况下，一个角色并不对应于一个成功或失败的测试，而是对应于一个完整的测试套件，针对该对象的变异版本进行测试。

我们的构造函数是一个足够简单的方法，但突变体仍然设法找到 8 种方法来改变它。这包括省略参数列表，或者分配 nil 而不是值。然而，这些怪异的版本都没有通过我们的防御。同样不能说的还有`#retrieve_asset`。那里创造了 49 个突变体，在运行结束时，两个还活着！这意味着我们的代码中存在测试未指定的行为，让我们在变种人回来用生产事件困扰我们之前修复它。

为了使生活更容易，还将突变体调用放在一个`Rakefile`中，当突变体覆盖率低于 100%时，告诉突变体失败。这样，我们可以从我们的配置项运行`rake mutant`,以确保所有内容都被完全覆盖。

```
desc 'Run mutation tests on the full AssetPackager namespace'
task :mutant do
  result = Mutant::CLI.run(%w[-Ilib -rasset_packager --use rspec --score 100 AssetPackager*])
  fail unless result == Mutant::CLI::EXIT_SUCCESS
end
```

现在来解剖活着的变种人。对于每一个通过我们防御的代码修改版本，突变体给了我们一个容易阅读的差异。

```
-  when ["http", "https"].include?(uri.scheme) || (uri.scheme.nil? && uri.host)
+  when ["http", "https"].include?(uri.scheme)
```

这里，我们的破坏性突变测试器删除了条件的后半部分，它应该识别形式为`//example.com/foo/bar`的 URIs。这确实是一个我们在测试中忘记覆盖的案例，但是很容易修复。

```
include_examples 'remote URIs', '//foo.bar/baz'
```

第二个不同之处最初让我们有点困惑。

```
-    File.read(cwd.join(uri.path))
+    File.read(uri.path)
```

我们需要能够解析绝对(`/foo/bar/style.css`)和相对(`assets/stuff.js`)本地文件。对于相对路径，我们从“当前工作目录”或`cwd`(一个[路径名](https://www.sitepoint.com/rubys-pathname-api/)实例)开始查找。对于绝对路径，`join`将简单地通过绝对路径。这段代码应该涵盖这两种情况，我们在测试中也涵盖了这两种情况，但是根据《突变体》的说法，删除对`cwd.join`的调用并没有什么不同。相对路径测试工作不正常。

仔细观察，我们测试中用作“工作目录”的路径与我们运行测试的位置相同。在变异版本中，`File.read`获得相对路径，并为我们解析它。为了确保我们的路径解析像预期的那样工作，我们需要改变测试以在不同的目录下工作。

```
describe 'with a relative path' do
  let(:cwd) { super().join('spec/fixtures') }
  let(:uri) { fixture_pathname.relative_path_from(cwd).to_s }
  include_examples 'local files'
end
```

有可能测试优先，观察测试失败的开发风格会发现这个错误。但是在一个更大项目的生命中，有些东西是注定要错过的。尤其是在重构之后，你会遇到许多活生生的变异体，它们显示出未经测试的行为。通过回顾完整的变异覆盖，您还会发现重构时出现的任何缺陷。

突变测试并不新鲜，事实上从 70 年代就有了，一个名为 Heckle 的实验宝石是第一个将突变测试引入 Ruby 的。然而，哈克也有一些明显的缺点。它从来没有支持过所有可能的 Ruby 语法，最新的版本可以追溯到 2009 年，这使得更新的 Ruby 版本完全被排除在外。

这使得 ROM 团队(前身为 DataMapper)的 Markus Schirp 开始研究[突变体](https://github.com/mbj/mutant)。编写一个健壮的、生产就绪的突变测试器的雄心勃勃的努力。Mutant 仍然是 1.0 之前的版本，但是已经成功地用于各种开源和商业项目。

获得像变种人这样的工具不是一件容易的事。早期发现的一个问题是，通过改变语法树，Mutant 可能会生成语法上无效的 Ruby 代码，如下所示:

```
def foo(a = 1, b, c = 2) # second optional argument deleted
```

这些问题现在似乎都已经解决了。在引擎盖下，Mutant 由优秀的[解析器](https://github.com/whitequark/parser)和[解解析器](https://github.com/mbj/unparser)gem 提供支持，它们已经针对 Rubyspec、Rails 代码库等进行了验证。

突变体目前可用于 MRI 和 Rubinius。JRuby 支持是有计划的，但是因为 JRuby 不支持`fork`系统调用而搁置了。对 Ruby 2.1.0 的支持不稳定。

如果你的 Ruby 版本支持的话，你需要在你的工作流程中加入突变体。它可能会挽救你的应用程序的生命。

## 分享这篇文章