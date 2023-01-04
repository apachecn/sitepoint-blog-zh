# 代码旅行:HAML 入门

> 原文：<https://www.sitepoint.com/code-safari-getting-started-in-haml/>

随着最近 HAML 3.1 的发布，我决定冒险深入了解它，找出是什么让它成功的。什么野兽潜伏在模板系统的内部？

HAML 是一种模板语言，允许你使用简洁的语法编写 HTML:

```
%article
  %h1 My great article
  %p
    Here is the text of
    my article
```

它编译成:

```
<article>
  <h1>My great article</h1>
  <p>Here is the text of my article</p>
</article>
```

它允许一些额外的漂亮的东西，比如内联的 Ruby 块，用重要的空格封闭。毫无疑问，它有一些有趣的锦囊妙计。

让我们去旅行吧。

## 狩猎时间

像往常一样，从抓取代码开始:

```
git clone git://github.com/nex3/haml
```

我鼓励你和这篇文章一起阅读。

在研究一个库时，我总是从两个地方开始:自述文件和主要需求。不幸的是，大多数库在它们的自述文件中没有深入研究代码的指南，但是看看也无妨。对于 HAML，我们发现一些非常好的用户文档，但没有给我们指出正确的方向。不过没关系，因为我们在`lib/haml.rb`中收到了一个让我微笑的非常好的评论:

```
# lib/haml.rb
# The module that contains everything Haml-related:
#
# * {Haml::Engine} is the class used to render Haml within Ruby code.
# * {Haml::Helpers} contains Ruby helpers available within Haml templates.
# * {Haml::Template} interfaces with web frameworks (Rails in particular).
# * {Haml::Error} is raised when Haml encounters an error.
# * {Haml::HTML} handles conversion of HTML to Haml.
```

我们找到向导了！像这样的类和模块级头文件是天赐之物。你可以写出世界上最好的代码，但是它的重量会让新开发人员望而生畏。欢迎开发人员加入您的代码库。

看起来`Haml::Engine`将成为赚钱的门票，开放`lib/haml/engine.rb`我们将受到另一个支付头奖的评论的欢迎。

```
# This is the frontend for using Haml programmatically.
# It can be directly used by the user by creating a
# new instance and calling {#render} to render the template.
# For example:
#
#     template = File.read('templates/really_cool_template.haml')
#     haml_engine = Haml::Engine.new(template)
#     output = haml_engine.render
#     puts output
```

让我们在家使用`irb`，并确认建议的语法确实有效。从 HAML 目录中启动。`-I`是一个向加载路径添加目录的标志。

```
$ irb -Ilib
irb> require 'haml'
irb> Haml::Engine.new("%b hello").render
 => "<b>hello</b>"
```

在`lib/haml/engine.rb`中搜索“def initialize”找到我们的入口点。这里有很多行，当试图获得一个库的要点时，高效阅读的诀窍是能够快速跳过那些不重要的代码，以获得程序的精髓。通常这意味着跳过赋值并搜索方法调用。我也经常从底层开始工作，从返回值开始。典型的方法是结构化的 setup-action-return，现在我们对最后两个感兴趣。`#initialize`的大部分是变量设置，但在接近结尾时，你会发现一个非常有趣的行:

```
# lib/haml/engine.rb:124
compile(parse)
```

我们的第一次见识！看起来 HAML 把文档的解析和 HTML 的编译分开了。这是一个标准的技术，分离出两个非常不同的关注点。

## 从语法上分析

解析是获取一个表示(在这里是我们的 HAML 模板)并准备输出到另一个表示(HTML)的行为。您可以在`lib/haml/parser.rb`中找到解析代码，或者通过在项目中搜索“def parse ”,或者注意到在`Haml::Engine`顶部包含了`Parser`。从方法的底部开始，我们看到它正在返回实例变量`@root`。这很方便——因为`Parser`是作为模块包含在`Engine`类中的，我们应该能够很容易地检查这个实例变量。我们可以使用`instance_eval`方法在任何对象的上下文中评估代码，甚至允许我们访问私有方法和实例变量。对于生产代码来说，这是一个非常糟糕的想法，但是它是一个很好的探索工具。

```
irb> input = "%article ... sample from above ..."
irb> Haml::Engine.new(input).instance_eval { @root }
 => (root nil
  (tag {:name=>"article", :value=>nil}
    (tag {:name=>"h1", :value=>"My great article"})
    (tag {:name=>"p", :value=>nil}
      (plain {:text=>"Here is the text of"})
      (plain {:text=>"my article"})))
  (haml_comment {:text=>""})) 

irb> Haml::Engine.new(input).instance_eval { @root }.class
 => Haml::Parser::ParseNode 
irb> Haml::Engine.new(input).instance_eval { @root }.children.map(&amp;:class)
 => [Haml::Parser::ParseNode, Haml::Parser::ParseNode] 

# (I edited out some extra values from the hashes for clarity.)
```

`parse`方法创建了一个`Haml::Parser::ParseNode`树，创建了我们文档的抽象表示。换句话说，这种表示与我们的输入是字符串这一事实无关。这将 HAML 的语法从输出中分离出来，从而产生一个更好的架构。注意，总是有一个特殊的根节点来附加树的其余部分。

让我们更深入地分析一下。扫描`parse`方法，我们得到以下基本结构:

```
while next_line
  process_indent # decrease nesting if needed
  process_line
  if block_opened?
    increase nesting
  end
end

close open tags
```

这里有两个主要函数:处理缩进和解析行。在这里，我将把重点放在后者上，而把阅读缩进代码作为一个练习留给你去做(参见文章的结尾)。我再一次粗略地看一下`process_line`:

```
case first_char_of_line
when '%'; push tag(text)
when '.'; push div(text)
# ... other cases
else;     push plain(text)
end
```

`tag`、`div`和`plain`方法构造并返回`ParseNode`对象，而`push`将节点添加到当前节点的子节点。

## 制作我们自己的

我们现在已经对 HAML 的解析部分有了足够的了解，可以尝试自己编写一个脚本。这有助于确认我们已经正确地阅读了代码，也巩固了我们所学的任何知识。让我们创建一个简单的解析器，它能够从上面的示例文档转换成一个节点树，从一个简单的忽略缩进的例子开始。

```
require 'test/unit'

class HamlParserTest < Test::Unit::TestCase
  def test_one_line_plain
    tree = HamlParser.new("hello").parse
    assert_equal 1, tree.children.size
    assert_equal :plain,  tree.children[0].type
    assert_equal 'hello', tree.children[0].data[:value]
  end

  def test_one_line_tag_with_value
    tree = HamlParser.new("%em hello").parse
    assert_equal 1, tree.children.size
    assert_equal :tag,    tree.children[0].type
    assert_equal 'em',    tree.children[0].data[:name]
    assert_equal 'hello', tree.children[0].data[:value]
  end
end

class HamlParser
  class Node < Struct.new(:type, :data)
    attr_accessor :children
    attr_accessor :parent   # Used in next example

    def initialize(*args)
      super
      self.children = []
    end
  end

  def initialize(string)
    @string = string
  end

  def parse
    @root = Node.new(:root, {})
    @root.children = @string.lines.map do |line|
      parse_line(line)
    end
    @root
  end

  def parse_line(line)
    case line[0]
    when ?%
      name, value = line[1..-1].split(' ')
      Node.new(:tag, :name => name, :value => value)
    else
      Node.new(:plain, :value => line)
    end
  end
end
```

`Test::Unit`是 Ruby 的标准库中提供的单元测试框架。如果您运行这个文件，您将看到它自动运行指定的测试。这是快速构建一个像这样的小项目的好方法。我将代码的形状设计成类似于 HAML 代码，用一个`parse_line`方法打开行的第一个字符，用一个根节点保存树。

为了支持缩进，我们需要设置解析器，让它知道要添加子节点的当前节点(而不是像第一个例子那样总是添加到根节点)，以及当前深度。为了方便起见，我们将为节点添加一个`parent`访问器，这样我们就可以上下遍历树。这个版本实际上比 HAML 代码简单一点，但是它现在完成了工作。

```
require 'test/unit'

class HamlParser < Test::Unit::TestCase
  def test_tag_with_nested_value
    tree = HamlParser.new("%em

hello").parse
        assert<em>equal 1, tree.children.size
        assert</em>equal :tag,    tree.children[0].type
        assert<em>equal 'em',    tree.children[0].data[:name]
        assert</em>equal 'hello', tree.children[0].children[0].data[:value]
      end
    end
```

```
class HamlParser
  # Node and initialize as above

  def parse
    @root = Node.new(:root, {})
    @parent = @root
    @depth = 0
    @string.lines.each do |line|
      process_indent(line)

      push parse_line(line.strip)
    end
    @root
  end

  def process_indent(line)
    indent = line[/^s+/].to_s.length / 2
    if indent > @depth
      @parent = @parent.children.last
      @depth = indent
    end
  end

  def push(node)
    @parent.children << node
    node.parent = @parent
  end

  def parse_line(line)
    # ... as above
  end
end
```

这是一个好的开始，它解析了我们的初始示例代码，但是还有很多工作要做:

*   修正我们例子中的`process_indent`,这样它也能正确“降级”。
*   很难可视化我们的解析器输出，因为默认的 Ruby `inspect`实现不包括节点的子节点。覆盖`Node#inspect`来提供一个像 HAML 一样的好输出。
*   HAML 解析器实际上同时跟踪两行，而不是像我们的解析器那样跟踪一行。通读 HAML 代码，找到有用的例子。

请在评论中告诉我们你的进展。下周加入我，我将继续完成这个过程的第二部分:编译步骤。

喜欢这篇文章，有什么想说的？RubySource 目前正在为有偿工作寻找正规的 Ruby 作者，请查看[为我们写作](https://www.sitepoint.com/write-for-us/)页面并取得联系

## 分享这篇文章