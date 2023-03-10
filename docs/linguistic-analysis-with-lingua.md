# Code Safari:使用 Lingua 进行语言分析

> 原文：<https://www.sitepoint.com/linguistic-analysis-with-lingua/>

欢迎来到 Code Safari。在这个系列中，Xavier Shay 将引导您浏览流行的 gem 和库的源代码，发现新的技术和习惯用法。

在我为之写作的另一个博客上，我很好奇如何计算一些关于我文章可读性的统计数据。它在[弗莱施-金凯](http://en.wikipedia.org/wiki/Flesch%E2%80%93Kincaid_readability_test)量表上的排名如何？我的文章有多长？我和我的合著者相比如何？我喜欢这类问题——不一定是*那种*有趣的问题，但可能在足够短的时间内实现，值得一试。即使没有有用的输出，这也是一个很好的训练问题，可以锻炼你的编程能力。

该过程有两个步骤:将数据转换成适合处理的格式，然后从该数据生成统计数据。我在过去处理过类似的问题，所以有一个好主意从哪里开始。这种对现有语言库的熟悉是尝试你脑海中出现的任何疯狂实验的唯一理由。

我使用 [nanoc](http://nanoc.stoneship.org/) 来编辑博客，我的源数据是以带有 YAML 序言的 [Markdown](http://daringfireball.net/projects/markdown/) 文件的形式。以下是一个样本文档:

```
---
title: My Blog Post
created_at: 2011-04-01 10:00
---
&nbsp;
Indubitably I am writing a blog!
&nbsp;
    puts "This is code and shouldn't be included"
&nbsp;
This is the conclusion of my fascinating blog.
```

Ruby 有一个很棒的解析 Markdown 的库(实际上它有几个！)叫做 [kramdown](http://kramdown.rubyforge.org/) 。为了能够使用它，我首先必须提取元数据(标题，创建于),并将其从文档中剥离出来。这可能并不太难，但是为什么要自己编写解析算法呢？Nanoc 肯定已经有一些代码来做这件事了…

我打开了纳米科源代码树，然后去洞穴探险。Nanoc 很少使用 YAML，所以我推断它可能是一个值得搜索的好东西。

```
$ gem unpack nanoc
$ cd nanoc3-3.1.3
$ ack YAML
lib/nanoc3/base/ordered_hash.rb
172:              YAML::quick_emit(object_id, opts) {|emitter|
&nbsp;
lib/nanoc3/base/site.rb
369:        @config = DEFAULT_CONFIG.merge(YAML.load_file(config_path).symbolize_keys)
&nbsp;
lib/nanoc3/cli/commands/create_site.rb
11:      # Converts the given array to YAML format
&nbsp;
lib/nanoc3/data_sources/filesystem.rb
86:          meta                = (meta_filename &amp;&amp; YAML.load_file(meta_filename)) || {}
233:        meta    = YAML.load_file(meta_filename) || {}
255:      meta    = YAML.load(pieces[2]) || {}
&nbsp;
lib/nanoc3/data_sources/filesystem_unified.rb
85:          io.write(YAML.dump(meta).strip + "n")
&nbsp;
lib/nanoc3/data_sources/filesystem_verbose.rb
18:  # or the layout’s metadata, formatted as YAML.
61:      File.open(meta_filename,    'w') { |io| io.write(YAML.dump(attributes.stringify_keys)) }
$
```

`YAML.load_file(meta_filename)`对我来说似乎是个不错的候选，文件名(`data_sources/filesystem.rb`)更有希望。打开文件，我们找到了一个方法，这正是我们想要的。包含所有的错误检查有点长，所以我在这里只包括一个编辑过的版本:

```
# nanoc/lib/nanoc3/data_sources/filesystem.rb

# Parses the file named `filename` and returns an array with its first
# element a hash with the file's metadata, and with its second element the
# file content itself.
def parse(content_filename, meta_filename, kind)
  data = File.read(content_filename)
  pieces = data.split(/^(-{5}|-{3})s*$/)
&nbsp;
  meta    = YAML.load(pieces[2]) || {}
  content = pieces[4..-1].join.strip
&nbsp;
  [ meta, content ]
end
```

我将整个方法一字不差地复制到我的脚本中，并引用了它的源位置，以防我需要重新使用它。对于像这样的快速原型脚本，我的目标是让它尽可能快地工作。任何关于代码重用或架构的想法都可以暂时搁置。

使用这种方法，我们可以迈出构造解析器的第一步:

```
require 'kramdown'
files = Dir["content/articles/*.md"]
&nbsp;
files.each do |file_name|
  meta, content = parse(file_name, nil, nil)
  doc = Kramdown::Document.new(content)
  puts doc.inspect
end
```

inspect 输出虽然密集，但却提供了关于下一步的重要线索。我们希望从统计数据中排除任何非文本元素(比如代码块)。

```
<KD:Document: options={:template=>"", :auto_ids=>true, :auto_id_prefix=>"",
:parse_block_html=>false, :parse_span_html=>true, :html_to_native=>false,
:footnote_nr=>1, :coderay_wrap=>:div, :coderay_line_numbers=>:inline,
:coderay_line_number_start=>1, :coderay_tab_width=>8, :coderay_bold_every=>10,
:coderay_css=>:style, :entity_output=>:as_char, :toc_levels=>[1, 2, 3,
4, 5, 6], :line_width=>72, :latex_headers=>["section", "subsection", "subsubsection",
"paragraph", "subparagraph", "subparagraph"], :smart_quotes=>["lsquo",
"rsquo", "ldquo", "rdquo"]} root=<kd:root nil {:encoding=>#<Encoding:UTF-8>,
:abbrev_defs=>{}} [<kd:p nil [<kd:text "Indubitably I am writing a blog!"
nil>]>, <kd:blank "n" nil>, <kd:codeblock "puts "This is code and shouldn't
be included"n" nil>, <kd:blank "n" nil>, <kd:p nil [<kd:text "This
is the conclusion of my fascinating blog." nil>]>]> warnings=[]>
```

我们可以看到 kramdown 为内容创建了不同类型的节点，我们唯一感兴趣的是`kd:text`。所有的节点都出现在从`kd:root`派生的树结构中，所以递归过滤函数应该足以提取所有的文本节点。你可以参考 [kramdown 文档](http://kramdown.rubyforge.org/rdoc/Kramdown/Element.html)来获得`Document`的确切 API，但是你也可以通过猜测获得很大的帮助。`root`、`type`和`children`对于这种类型的树结构来说是足够常见的名称，这也不例外。

```
def extract_text(elem)
  value = elem.type == :text ? [elem.value] : []
  value + elem.children.map {|x| extract_text(x) }.flatten
end
&nbsp;
extract_text(doc.root).join(' ')
# => "Indubitably I am writing a blog! This is the conclusion of my fascinating blog."
```

非常好。让我们接着分析课文。

## 第二部分

从过去的一个项目中，我已经知道了 [Lingua 库](http://www.pressure.to/ruby/)。

> 是一个 Ruby 模块，它计算英文文本的统计数据。它可以提供单词、句子和音节的计数。它还可以计算几个可读性指标，如雾化指数和 Flesch-Kincaid 等级。

它来自 Rubygems 之前的时代，并建议下载`tar.gz`来安装。这并不难，但理想情况下，我们会保持在我们选择的依赖系统内。对于许多这些老项目，人们已经将它们分叉，以便正确地打包它们或者使它们与最新版本的 Ruby 一起工作。 [GitHub](http://github.com) 是找到这些的最佳地点。

[搜索“Lingua”](https://github.com/search?q=lingua)会产生一些结果，排名第一的是赢家。它在原始库的基础上有一个`gemspec`和一些错误修复。我们可以像安装其他 Ruby 库一样安装它。

```
gem install lingua
```

用法是微不足道的，并完成我们的报告:

```
require 'lingua'
require 'kramdown'
files = Dir["content/articles/*.md"]
&nbsp;
def parse(content_filename, meta_filename, kind)
  # ... from above
end
&nbsp;
files.each do |file_name|
  meta, content = parse(file_name, nil, nil)
  doc = Kramdown::Document.new(content)
  text = extract_text(doc.root).join(" ")
  report = Lingua::EN::Readability.new(text)
&nbsp;
  puts "%s: %.2f" % [meta['title'], report.kincaid]
end
&nbsp;
# My Blog Post: 7.37
```

普通七年级学生也能读懂。不算太寒酸！这个帖子本身的评分是 8.11，我相信大多数观众都能看得懂。

## 包扎

尝试像这样的小型半实用项目是了解您的编程生态系统并提高您的算法能力的好方法。它们是程序员的音乐家的音阶。以下是一些额外的问题，您可以尝试进行更多的练习:

*   去掉标点符号，意思是缩写在结果文本中不正确(“I'm”变成了“I m”)。这对于这种分析来说很好，但是如何将其输入到文本到语音转换器中呢？(如果你在 mac 上，请在命令行中尝试`say hello`)
*   `created_at`仍然是元数据中的文本值。将其转换为适当的`Time`格式。
*   如果你自己有一个博客，试着在上面运行上面的分析。

请在评论中告诉我们你的进展。下周请继续关注代码丛林中更激动人心的冒险。

## 分享这篇文章