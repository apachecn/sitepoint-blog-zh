# 代码旅行:HAML，编译完成

> 原文：<https://www.sitepoint.com/code-safari-haml-compiling-to-completion/>

# Haml:第 2 周

上周，我们开始研究 HAML 的实现，这是一种流行的 HTML 模板语言。我们已经完成了解析步骤，剩下的编译步骤将在本周讨论。如果你还没有[读过之前的文章](https://www.sitepoint.com/code-safari-getting-started-in-haml/)，我鼓励你现在就去读。这样这篇文章会更有意义。作为参考，下面是我们一直使用的示例模板:

```
%article
  %h1 My great article
  %p
    Here is the text of
    my article
```

这是我们停下来的那一行:

```
# lib/haml/engine.rb:124
compile(parse)
```

## 收集

我们上次发现了这条线，调查了`parse`方法。我们直接跳到另一边:`compile`。在项目中搜索`def compile`，我们在预期的模块`Haml::Compiler`中找到了这个方法。

```
# lib/haml/compiler.rb:444
def compile(node)
  parent, @node = @node, node
  block = proc {node.children.each {|c| compile c}}
  send("compile_#{node.type}", &amp;(block unless node.children.empty?))
ensure
  @node = parent
end
```

这是一个简洁的小函数，它编译一个节点，提供了一个块来编译所有节点的子节点，这些子节点可以根据需要生成。因此，编译带有嵌套子元素的标签时，编译器可以发出开始标签，让步以编译(并发出)子元素，然后在让步完成后添加结束标签。

有趣的调用是第三行的`send`。这将调用不同的方法来编译每种类型的节点。这是一种在 Ruby 中实现[访问者模式](http://en.wikipedia.org/wiki/Visitor_pattern)的可爱方式，它允许绕过算法的传统`accept`调用。HAML 将所有的编译器逻辑放在一个地方，而不是为每种知道如何编译自己的节点类型创建一个类层次结构。这允许对相似的代码进行概念性的分组，也允许编译器容易地维护状态(实例变量),而不必在类外部公开它们。

让我们看看我们的例子中的两种类型的节点的编译方法:`tag`和`plain`。

在`compile_tag`方法中有相当多的东西在进行，所以我们需要运用我们的技能将其过滤到本质上。有许多*条件来处理不同类型的格式:我们要去除空白吗？我们输出的是 HTML5 还是 XHTML？我们处于丑陋的模式吗？目前这些对我们来说并不重要，因为它们对算法来说并不重要。*

```
# lib/haml/compiler.rb:91
# Heavily edited
def compile_tag
  t = @node.value

  object_ref = t[:object_ref]
  value = t[:value]

  tag_closed = !block_given?

  open_tag = prerender_tag(t[:name], t[:self_closing], t[:attributes])

  if tag_closed &amp;&amp; value
    open_tag << "#{value}</#{t[:name]}>" # Point A
  end

  push_merged_text(open_tag, 0, true)
  return if tag_closed

  if value.nil?
    # Point B
    @output_tabs += 1 unless t[:nuke_inner_whitespace]
    yield if block_given?
    @output_tabs -= 1 unless t[:nuke_inner_whitespace]

    push_merged_text("</#{t[:name]}>", 0, true)
  end
end
```

我对前面的清单进行了大量的删减，只突出了两条代码路径。与原始源代码进行比较，以了解我如何通过删除无关代码来“提取”该方法的精髓。这是一项很难的技能，但随着练习会变得容易。

在新的框架版本中，我们可以看到两个主要分支:一个是为节点提供值时的分支(点 A)，另一个是值嵌套在标签下时的分支(点 B)。这些按顺序对应于我们示例模板中的`h1`和`p`标签。B 点下面的`yield`将运行`compile`方法(上面的清单)第 2 行定义的`proc`，它将依次编译任何子节点(我们的文本节点为`p`)。几乎只是将文本输出到缓冲区，并带有一些额外的逻辑，比如确保保留适当的缩进。

这很难理解，但是当我们尝试将这个概念应用到我们自己的 HAML 解释器时，下面的逻辑会变得更加清晰。

在巨兽`compile_tag`方法之后，`compile_plain`是令人耳目一新的短:

```
def compile_plain
  push_text @node.value[:text]
end
```

是的，没什么大不了的。虽然它确实有助于说明在尝试发出任何输出之前创建解析树的价值——解析的复杂性可能与编译的复杂性非常不同，因此它有助于区分这些问题。

## 建立我们自己的

上周，我们建立了自己的 HAML 解释器的开端。我们已经能够创建解析节点树，本周我们需要编译这些节点。作为参考，以下是我们上次讨论的完整列表:

```
class HamlParser
  class Node < Struct.new(:type, :data)
    attr_accessor :children
    attr_accessor :parent

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

我们不会修改代码，而是添加一个新的`HamlCompiler`类，将我们生成的解析树转换成 HTML。首先，我们将实现`compile_plain`方法，将纯文本输入转换为纯文本输出。就像上周一样，我们正在使用`test/unit`来确保我们把事情做好。运行这个脚本，您将看到测试正在自动运行。

```
require 'test/unit'

class HamlCompilerTest < Test::Unit::TestCase
  def compile(input)
    HamlCompiler.new.compile(HamlParser.new(input).parse)
  end

  def test_compile_one_line_plain
    assert_equal 'hello', compile("hello")
  end
end

class HamlCompiler
  def compile(node)
    block = if node.children.any?
      proc { node.children.each {|x| compile(x) }}
    end
    send("compile_#{node.type}", node, &amp;block)
    @output
  end

  def compile_root(node)
    @output = ""
    yield
  end

  def compile_plain(node)
    @output << node.data[:value]
  end
end
```

注意，我们通过在`@output`变量中存储状态来利用访问者模式。这意味着我们不必将它作为参数传递给所有的编译方法，如果它们是`ParseNode`上的方法，我们就必须这样做。随着需要更多的状态(比如跟踪缩进)，这种方法真正开始产生效益。您还可以看到，总是从根节点开始可以实现一个真正简单的算法，不需要任何特殊情况就可以开始，甚至还提供了额外的好处，例如为我们提供了一个初始化输出的好地方。

让我们添加一个`compile_tag`方法，这样我们就可以完整地解释我们的示例模板。希望它会比 HAML 使用的简单一些！

```
class HamlCompilerTest < Test::Unit::TestCase
  # ... as above

  def test_compile_one_line_tag_with_value
    assert_equal '<em>hello there</em>', compile("%em hello there")
  end

  def test_compile_tag_with_nested_value
    assert_equal '<em>hello there</em>', compile("%em
```

你好】)
end
end

```
class HamlCompiler
  # ... as above

  def compile_tag(node)
    tag_name = node.data[:name]

    @output << "<#{tag_name}>"
    if block_given?
      yield
    else
      @output << node.data[:value].to_s
    end
    @output << "</#{tag_name}>"
  end
end
```

由于我们的解释器更简单，我们能够用一个简单的条件来处理一行和带有嵌套文本的标签。由于算法的递归性质，它还可以处理任意嵌套的节点，例如`article`中的`h1`和`p`标记，无需特殊处理！

## 包装它

在这个由两部分组成的系列中，我们深入研究了驱动 HAML 模板引擎的代码，并发现它使用两遍方法来呈现模板。第一次，它创建一个表示文档的解析节点的抽象树，第二次，它将这些节点转换成 HTML。这种职责分离允许一个简洁的架构。

在这个过程中，我们利用学到的知识，创建了自己的迷你 HAML 解释器，能够解析和编译简单的文档。

为了加分，这里有一些你可以处理的额外任务:

*   为我们的解释器添加对 HTML 属性的支持。
*   我们的解释器不会发出任何有意义的空白。修复它，以很好地缩进嵌套标签。
*   被 Rails 和 ActiveRecord 使用的 Ruby 的关系代数 ARel ，也使用 visitor 模式编译 SQL。将其与 HAML 的编译器进行比较。

下周请继续关注代码丛林中的更多冒险。

## 分享这篇文章