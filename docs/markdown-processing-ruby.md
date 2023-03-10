# Ruby 中的降价处理

> 原文：<https://www.sitepoint.com/markdown-processing-ruby/>

![Markdown-mark](img/7004c4675e7fe7fcdbb043cfe2017cb0.png)

Markdown 是一种很棒的可以编译成 HTML 的标记语言。尽管其最初的实现是用 Perl 编写的，但 Markdown 已经被移植到多种语言中，具有各种特性。我们将关注 Markdown 的 4 个 Ruby 实现: [kramdown](http://kramdown.gettalong.org/) 、 [maruku](https://github.com/bhollis/maruku) 、 [rdiscount](https://github.com/davidfstr/rdiscount) 和 [redcarpet](https://github.com/vmg/redcarpet) 。

## 在 Ruby 中处理降价

显然，我们应该先安装这些宝石，所以`gem install kramdown maruku rdiscount redcarpet`。这可能需要一点时间，因为 RDiscount 和 Redcarpet 都使用 C 扩展来加快处理速度。这也意味着，如果你的 Ruby 解释器不支持 C 扩展，你将无法使用 RDiscount 或 Redcarpet。我在看着你，JRuby。

出于我们的处理需要，我们将使用保存到`markup.md`的约翰·格鲁伯的 Markdown 语法页面中的 [Markdown。](http://daringfireball.net/projects/markdown/syntax.text)

在每个实现中，我们可以用一行代码来处理这个 Markdown 文件。

```
markdown = File.read('markup.md')

# kramdown
require 'kramdown'
Kramdown::Document.new(markdown).to_html

# maruku
require 'maruku'
Maruku.new(markdown).to_html

# rdiscount
require 'rdiscount'
RDiscount.new(markdown).to_html

# redcarpet
require 'redcarpet'
Redcarpet::Markdown.new(Redcarpet::Render::HTML.new).render(markdown)
```

太棒了。我们可以看到每个框架(除了 Redcarpet)都有一个简单的 API 来处理 Markdown。

## 特征

Markdown 最初的实现在当时很棒，但人们最终决定他们想要更多。例如，Markdown 最初的实现包括对图像和代码块的支持，但是许多用户想要不同的语法和/或这些特性更好的可扩展性。因此，降价的各种实现的特征变化很大。

让我们看看几个流行的新增特性，以及它们在 Ruby 实现中的支持。

### 代码块

Markdown 的代码块语法最初涉及到代码缩进的使用，如下所示:

```
if foo == bar
    "Markdown is awesome."
  end
```

改进后的语法被称为*防护代码块*。它是这样写的:

```
```ruby
if foo == bar
  "Markdown is awesome."
end
```
```

注意 Ruby 作为第一组“栅栏”末尾语言的规范。这是可选的，但是允许 Markdown 实现突出显示特定语言的语法。

### 删除线

删除线是 Markdown 的新增功能。它是这样写的，将一个(或多个)单词用波浪号括起来:

```
~~Something outdated~~
```

### 桌子

表格是 Markdown 的附加功能。它们的创建方式类似于它们在 HTML 中的外观。

```
| Header 1 | Header 2 | Header 3 |
|----------|----------|----------|
| Foo      | Bar      | Baz      |
```

这会输出一个看起来非常相似的 HTML 表格。虽然这种语法对于简单的表格很方便，但我发现在编辑表格时很不方便，因为很难在纯文本中保持均匀的单元格宽度。然而，这是不相关的，因为下面也会呈现与上面相同的效果(它只是让你的减价看起来更丑):

```
| Header 1      | Header 2|Header 3 |
|-------|----|----------|
|Foo|Bar|Baz|
```

### 标题 id

该功能允许在降价生成的文档中使用 HTML 锚(*例如* `http://something.com/document.html#id-ref`)。这是自动完成的标题和副标题。尽管 Markdown 的每个实现都会产生不同的结果，所以最好查阅相关文档。

### 排版替换(“SmartyPants”样式)

事实证明，Markdown 用户也喜欢另一个名为 SmartyPants 的大胆火球项目。这个程序将普通的 ASCII 标点符号替换为“智能”印刷 HTML 输出。例如:

```
"Ruby", the programming language.
Becomes: “Ruby”, the programming language.
```

还会执行其他排版替换，例如`---`到—(长破折号)和`--`到—(短破折号)。

### 比较

让我们看看 Ruby Markdown 实现中对这些特性的支持。

|  | 围栏代码 | 删除线 | 桌子 | 标题 id | 排印替换 |
| --- | --- | --- | --- | --- | --- |
| 克拉姆敦 | 是 | 不 | 是 | 是 | 是 |
| 马鲁库 | 是 | 不 | 是 | 是 | 是 |
| 隆重的接待 | 是 | 是 | 是 | 是 | 是 |
| 打折 | 是 | 是 | 是 | 不 | 是 |

从这些数据来看，很明显红地毯是正确的选择。Redcarpet 不仅可扩展，而且速度惊人(我们将在下一步讨论这个)。

## 基准

我决定通过测试每个 Markdown 处理器处理这个文件的速度来测试它们的速度。事实证明，使用[基准模块](http://ruby-doc.org/stdlib-2.2.0/libdoc/benchmark/rdoc/Benchmark.html)，在 Ruby 中做到这一点非常容易。我是这样设置的:

```
require 'benchmark'

markdown = File.read('TestDoc.md')

Benchmark.bm(15) do |x|
  x.report('Kramdown') {
    require 'kramdown'
    Kramdown::Document.new(markdown.dup).to_html
  }

  x.report('Maruku') {
    require 'maruku'
    Maruku.new(markdown.dup).to_html
  }

  x.report('RDiscount') {
    require 'rdiscount'
    RDiscount.new(markdown.dup).to_html
  }

  x.report('RedCarpet') {
    require 'redcarpet'
    Redcarpet::Markdown.new(Redcarpet::Render::HTML.new).render(markdown.dup)
  }
end
```

由于执行时间存在差异，我运行了 5 次该基准测试(在 2013 年末的 MacBook Pro 上:2.4 GHz 英特尔酷睿 i5 运行 Ruby 2.2.0)，并对结果进行了平均:

|  | 平均值。时间(秒) |
| --- | --- |
| 克拉姆敦 | .1054152 |
| 马鲁库 | .1226444 |
| 打折 | .0131436 |
| 隆重的接待 | .007233 |

这些数字很酷，但要真正了解影响，我们需要一个图表:(条形图越短，速度越快)

[https://docs.google.com/spreadsheets/d/1WAQfMHf9H6aTAM4_6YrGfmM8ZNWgw2XeavDOI3PCTIw/pubchart?oid=110648552&format=interactive](https://docs.google.com/spreadsheets/d/1WAQfMHf9H6aTAM4_6YrGfmM8ZNWgw2XeavDOI3PCTIw/pubchart?oid=110648552&format=interactive)

哇哦。很明显，Redcarpet 真的很快，RDiscount 紧随其后。鉴于 Redcarpet 的特点，我强烈推荐它。

让我们客观地看待这些数字。下一个表格是 RedCarpet 在其他处理器处理**一个**的时间内可以处理的文档数量。

| 处理器 | 处理的文件数量 |
| --- | --- |
| 马鲁库 | 17 份文件 |
| 克拉姆敦 | 15 份文件 |
| 打折 | 2 份文件 |

所以，是的，红毯相当快。

## 高级红地毯用法

Redcarpet 不仅速度快，而且可扩展性也很强。让我们看看我们能否做出有史以来最酷的红地毯。

我们必须决定的第一件事是我们想从降价中得到什么额外的功能。让我们列出我们想要的:(你可以在这里参考这些功能)

*   表格(`:tables`)
*   防护代码块(`:fenced_code_blocks`)
*   自动链接(`:autolink`)
*   删除线(`:strikethrough`)

太棒了。接下来，我们需要为我们的 HTML 获取一个小的元，并决定我们希望它有什么属性。这些选项将被放入渲染器本身，而不是降价处理器。这只是 Redcarpet 惊人扩展性的一部分。然而，这些选项中的大部分都是安全特性(像`:safe_links_only`和`:filter_html`)，所以我们实际上并不需要在我们的例子中使用它们，但是了解它们是有好处的。

因此，使用我们的列表，让我们创建我们的降价处理器！

```
# our markdown extensions
md_options = {
  tables: true,
  fenced_code_blocks: true,
  autolink: true,
  strikethrough: true
}

# our markdown processor
processor = Redcarpet::Markdown.new(Redcarpet::Render::HTML, md_options)
```

现在我们可以用一个简单的`processor.render(some_markdown_string)`来解析我们的 Markdown。

## 结论

Markdown 是一种非常有用的标记语言。甚至这篇文章都是用 Markdown 写的。

随着它的语法变得越来越普遍，用各种语言解析它的能力也会越来越强。说到性能和可扩展性，Redcarpet 是无可匹敌的，但是不要让这阻止你尝试其他 Ruby Markdown 处理器。快乐渲染！

## 分享这篇文章