# 用 Parslet Gem 解析

> 原文：<https://www.sitepoint.com/parsing-parslet-gem/>

![parsley_logo](img/181756b04af59d228f0c5431c1dacd04.png)

解析是…复杂的。然而，如果没有它，我们就不会有现代世界的许多方面。我们从计算机中获取行为的能力受到编程语言表达能力的限制。我们可用的编程语言受到解析技术的限制。

编程语言的构造至少包括三个部分:

*   词法分析程序
*   句法分析程序
*   解释器/编译器

lexer，也就是通常所说的扫描器，通常是一个简单的例程，它将输入理解为一串字符。词法分析器将字符流转换成令牌流，并发送给解析器。

解析器由确定令牌流是否以可接受的顺序排列的规则组成。此外，解析器负责组装节点图，在许多情况下是抽象语法树(通常是 AST)，以有意义的方式表示输入。

解析器生成的语法图一般被转换成可以在虚拟机上运行的字节码，或者被编译成机器码。对于一个小的 DSL，可以用宿主语言实现一个简单的运行时来处理。

手工编写这些组件可能是一个挑战，尤其是对新手来说。有许多工具可以帮助创建它们，最初是 lexer 和解析器生成器，最近是 LLVM 之类的代码生成器。

gem 为用 Ruby 创建编程语言提供了 DSL。在本文中，我们将了解解析的一些历史，Parslet 的适用范围，以及如何使用它来创建自己的编程语言。

本教程的源代码可以在 [github](https://github.com/rlqualls/sitepoint_parslet_tutorial) 获得。

## 时间与空间

1965 年， [Donald Knuth 发明了 LR 解析器](http://www.cs.dartmouth.edu/~mckeeman/cs48/mxcom/doc/knuth65.pdf)。LR 是从左到右，最右边派生的缩写。LR 解析器可以在线性时间内识别任何确定的上下文无关文法。由于要维护状态转换表，LR 解析器速度很快，但内存需求过大(对于 20 世纪 60 年代)，因此被认为太不实用而被摒弃。

1969 年，Frank DeRemer 提出了 LR 解析器的简化版本:前瞻 LR (LALR)和简单 LR (SLR)。这些解析器以语言识别为代价降低了内存需求。最流行的变体变成了 LALR(1)，LALR 带有一个前瞻终端。

1970 年，Stephen C. Johnson 在 AT&T 研究 B 语言时创造了 YACC(又一个编译器)。他和 Al Aho 最初尝试手工实现 LALR 表，但是仅获得 30 个语法规则就花了 2 天时间，并且产生了相当多的错误，所以他决定花时间开发 LALR(1)解析器生成器，我们现在称之为 YACC。

最初，YACC 相当慢，但是经过多年的工作，Johnson 已经能够将它的速度提高几个数量级，并使它成为解析器生成的领先工具。等到计算机有了显著的进步，替代的解析算法也可用的时候， [YACC 和 LALR(1)已经在计算机科学文化中根深蒂固](http://en.wikipedia.org/wiki/Comparison_of_parser_generators)。

为 YACC 写一个有用的语法并不容易。LALR(1)的陷阱(即 shift/reduce 和 reduce/reduce 冲突)为那些对解析一种重要语言感兴趣的人提供了很高的入门门槛。结果，世界上的 YACCs 越来越多地与替代方法竞争，包括从头开始编写解析器。

## 新时代

今天，YACC 生活在 GNU Bison(和其他变种)中，由于它的速度和历史，仍然很受欢迎。然而，性能退居可维护性之后，这一计算机科学的一贯主题正在上演。

最初，GCC 项目使用 Bison 为 C、C++和 Objective C 生成解析器，但它在 3.4-4.1 版本中[逐渐转向手写递归下降解析器](http://gcc.gnu.org/wiki/New_C_Parser)。他们的基础是 LALR(1)解析器需要太多的修改才能符合任何 C 语言。

Clang 的维护者已经决定坚持类似的方向，目标是最终为 C 语言家族及其多种方言建立一个统一的递归下降解析器。

ANTLR 项目使用 LL(*)语法生成递归下降解析器。在 v4 中，ANTLR 的创造者 Terence Parr 决定专注于语法灵活性而不是性能，并恰当地将该版本命名为“蜜獾”。

递归下降并不是解析高要求语言的唯一现代答案。随着更多的 RAM(没有双关语)和时钟周期的增加，人们对 20 世纪中期以来提出的一些更昂贵的算法重新产生了兴趣。这些包括 CYK 算法和厄尔利解析器。

Bison 现在除了支持 LALR(1)之外，还支持广义 LR (GLR)。GLR 的工作方式类似于 LR，除了它在遇到冲突时分叉解析栈。每个解析栈读取令牌，直到它完成输入或达到无效状态。这使得 GLR 能够以增加内存消耗为代价来避免移位/归约和归约/归约冲突。

## 解析表达式语法

为了理解为什么解析表达式语法是有趣的，了解另一种选择是有帮助的。在 20 世纪 50 年代，诺姆·乔姆斯基提出了他现在著名的乔姆斯基层次结构，包括形式语法，它们可以生成的语言种类，以及识别语言需要什么样的机器(自动机)。

大多数解析器生成器期望解析器由上下文无关的语法来描述，这在乔姆斯基的层次结构中是第二复杂的。乔姆斯基语法的问题在于它们都是生成性的。它们被设计用来表达语言中的歧义，因为乔姆斯基最感兴趣的是将它们应用于自然语言。从历史上看，CFG 为解析器生成器表达的模糊性与它们背后的解析算法(如 LALR(1))并不匹配。

2004 年，布莱恩·福特[发表了一篇关于解析表达式语法的论文](http://bford.info/pub/lang/peg.pdf)。他认为上下文无关文法作为语言生成器的角色与解析器作为语言识别器的角色是不一致的。在论文中，他提出了另一种基于认知的形式主义，我们现在称之为 PEG。

解析表达式语法(PEG)带来了两个重要的创新:

1.  词法分析和解析是相同的活动(而不需要像 lex 和 yacc 这样的独立工具)
2.  暧昧被贪婪取代

CFG 中的选择操作符是无序的(可交换的)。这存在假设多个潜在解析树的缺陷(当可能只有一个解析树时)。然而，PEGs 中的选择操作符是贪婪的。如果第一个选项有效，第二个选项将被忽略，就像大多数编程语言一样。需要注意的是，这并不是一个神奇的修复，而且很有可能让你自己陷入麻烦。

与 CFG 不同，peg 并不特别适合解析自然语言。然而，它们更适合解析编程语言。CFG 类似于语言中生成字符串的规范，而 PEG 则类似于创建递归下降解析器的规则集合。

## 欧芹

Parslet 提供了一个 DSL，用于定义带有解析表达式语法的解析器。

首先，安装 parslet gem。

```
gem install parslet
```

这是 Parslet 中一个简单解析器的样子。

```
#demo_parser.rb
require 'parslet'

class DemoParser < Parslet::Parser
  rule(:identifier) { match('[a-z]').repeat(1) }
  rule(:number) { match('[0-9]').repeat(1) }
  rule(:space) { match('\s').repeat(0) }
  rule(:line) { identifier >> space >> number }

  root(:line)
end

demo_parser = DemoParser.new
result = demo_parser.parse("foo 1")

puts result.inspect
```

## 规则

Parslet 解析器是通过一组规则和定义块建立的。

```
rule(name) { definition_block }
```

定义块包含该规则的预期语法，包括:
–元素预期到达的顺序。
–任何元素预期出现的次数
–元素之间的任何选择

下面是你如何使用#repeat 得到一个或多个数字。

```
rule(:number) { match('[0-9]').repeat(1) }
```

在定义块中，可以通过规则的名称来引用规则。

```
rule(:number) { match('[0-9]').repeat(1) }
rule(:zipcode) { number }
```

每个解析器都需要一个根规则。根是解析器期望的第一个规则。

```
rule(:line) { match('[a-zA-Z0-9]').repeat >> '\n' }
rule(:lines) { line.repeat }
root(:lines)
```

## 原子

Parslet 将语言语法的元素称为原子或 pars let(有趣的是，文档中通常使用“原子”)。

原子有以下几种:

*   正则表达式–例如:`match('[a-zA-Z0-9]')`
*   字符串–例如:`str('foo')`
*   任何字符–对应于 regexp /。/ ex: `any.repeat(0)`

解析器的行为由其原子和规则之间的关系驱动。

*   `atom1 >> atom2`–atom 1 之后应该是 atom2
*   `atom.repeat(x[,y])`–atom 需要 x 次或更多次(或最多 y 次)
*   `atom.maybe`–相当于 atom.repeat(0，1)
*   `atom1 | atom2`–任何一个原子都是预期的，但是如果看到 atom1，则 atom2 被忽略

虽然看起来隐含的意思是，Parslet 文档似乎没有明确指出原子是纯粹的终结符(因此，规则作为非终结符，不能算作原子)，但是似乎任何可以应用于原子的东西，都可以应用于规则。

期望“a”或规则 b:

```
rule(:aorb) { str('a') | b }
```

期待一个后跟冒号的单词:

```
rule(:wordcolon) { match('[a-zA-Z]') >> str(':') }
```

## 棘手的空白

使用`#repeat(0)`让您的解析器变得灵活非常诱人，但是必须小心。当处理空白时，通常有这样的规则是个好主意:

```
rule(:space) { match('\s').repeat(1) }
rule(:space?) { space.maybe }
```

如果你只是使用一个包罗万象的空白规则，你就有陷入歧义的风险。在处理空白时，我设法创建了一个可以挂起的解析器。

```
# tricky_whitespace.rb
# Be careful with #repeat(0)
require 'parslet'

class BadWhitespaceParser < Parslet::Parser
  rule(:space) { match('\s').repeat(0) }
  rule(:identifier) { match('[a-zA-Z0-9]').repeat(1) }
  rule(:line) { identifier | space }
  rule(:lines) { (line >> space).repeat(0) }

  root(:lines)
end

doc = <<-eof 
east
west
eof

parser = BadWhitespaceParser.new
# This will hang. Use ctrl-c to interrupt.
puts parser.parse(doc).inspect
```

## 错误报告

如果使用 Parslet，它会提供出色的错误报告。如果您只是让 Parslet 失败，它会告诉您它没有匹配到什么序列。但是如果营救`Parslet::ParseFailed`，可以得到一棵失败树。

```
# parse_failed.rb
require 'parslet'

class FailParser < Parslet::Parser
  rule(:alpha) { match('[a-zA-Z]').repeat(1) }
  rule(:number) { digit.repeat(1) >> (str('.') >> digit.repeat(1)).maybe }
  rule(:digit) { match('[0-9]').repeat(1) }
  rule(:space) { str(' ') }
  rule(:line) { alpha >> space >> number >> space >> alpha }

  root(:line)
end

input = "ab 1.2 d1"
parser = FailParser.new

# not so great error reporting
puts "--STANDARD ERROR REPORTING--"
begin
  parser.parse(input)
rescue Exception => error
  puts error
end

puts ("\n")

# better error reporting
puts "--ASCII TREE ERROR REPORTING--"
begin
  parser.parse(input)
rescue Parslet::ParseFailed => error
  puts error.cause.ascii_tree
end
```

这将打印出:

```
--STANDARD ERROR REPORTING--
Failed to match sequence (ALPHA SPACE NUMBER SPACE ALPHA) at line 1 char 8.

--ASCII TREE ERROR REPORTING--
Failed to match sequence (ALPHA SPACE NUMBER SPACE ALPHA) at line 1 char 8.
`- Extra input after last repetition at line 1 char 9.
  `- Failed to match [a-zA-Z] at line 1 char 9.
```

Parslet 提供了一种方便的方法，这样您就不需要每次都写出异常处理。

```
require 'parslet/convenience'
parser.parse_with_debug(input)
```

## 生成树

默认情况下，Parslet 只检查输入。它不会生成语法树。方法在树上创建一个节点。

```
rule(:assignment) { left.as(:left) >> str('=') >> right.as(:right) }
```

这里有一个更完整的例子。

```
# assignment_parser.rb
require 'parslet'

class AssignmentParser < Parslet::Parser
  rule(:identifier) { match('[a-zA-Z0-9_]').repeat(1) }
  rule(:value) { match('[0-9]').repeat(1) }
  rule(:assignment) { 
    identifier.as(:left) >> 
    str('=') >> 
    value.as(:right) >> 
    str("\n").maybe 
  }
  rule(:assignments) { assignment.as(:assignment).repeat }
  root(:assignments)
end

doc = <<-eof
a=23
b=56
eof

parser = AssignmentParser.new
puts parser.parse(doc).inspect
```

如果运行 assignment_parser.rb，得到的不是输入，而是赋值散列的数组。

[{:assignment=>{:left=>"a"@0，:right = > " 23 "@ 2 } }，{:assignment=>{:left=>"b"@5，:right = > " 56 "@ 7 } }]

## 转换

Parslet 提供了 Parslet::Transform 来理解由`Parslet::Parser`生成的语法图。您可以用它来制作解释器或字节码生成器。

与 Parslet::Parser 一样，Parslet::Transform 也由规则和块组成。

```
rule(nodes => capture_method) { action_block }
```

捕获方法确定规则将匹配的内容，可以是下列方法之一:

*   简单——除了散列或数组以外的任何对象
*   序列——散列或数组
*   子树——一切

操作块的结果将替换规则中指定的节点模式。如果只指定了叶节点，则只替换叶节点。

```
# transform_replace.rb
require 'parslet'

ast = {:document => {:words => "hello world"}}

# replaces the words node
class LeafTransform < Parslet::Transform
  rule(:words => simple(:x)) { x.upcase }
end

# replaces document and words nodes
class TreeTransform < Parslet::Transform
  rule(:document => {:words => simple(:x)}) { x.upcase }
end

leaf_transform = LeafTransform.new
puts leaf_transform.apply(ast).inspect

tree_transform = TreeTransform.new
puts tree_transform.apply(ast).inspect
```

这里 LeafTransform 只是替换了{:words = >“hello world”}，而 TreeTransform 替换了整棵树。

```
{:document=>"HELLO WORLD"}
"HELLO WORLD"
```

让我们转换由赋值解析器产生的树(注意:解析器“@”装饰被移除)。

```
# assignment_transform.rb
require 'parslet'

tree = [{:assignment=>{:left=>"a", :right=>"23"}}, {:assignment=>{:left=>"b", :right=>"56"}}]

class Assignment
  def initialize(left, right)
    @left = left
    @right = right
  end

  def generate_code
    # spit out bytecode/machinecode associated with assignment operation
  end
end

class AssignmentTransform < Parslet::Transform
  rule(:left => simple(:left), :right => simple(:right)) do 
    Assignment.new(left, Integer(right))
  end
end

transform = AssignmentTransform.new
puts transform.apply(tree).inspect
```

#apply 方法返回带有赋值对象而不是散列的转换树。

```
[{:assignment=>#<Assignment:0x000000019df520 @left="a",@right=23>}, 
 {:assignment=>#<Assignment:0x000000019c9018 @left="b", @right=56>}]
```

有关 Parlset::Transform 的更多信息，请查看文档。

## 结论

如果您是编程语言的新手，Parslet 是一个很好的入门方式。它比典型的 LALR(1)生成器提供了更多的语法灵活性，并且可以使用您的 Ruby 代码。然而，Parslet 并不是 Ruby 唯一的钉住游戏。以下是其他一些例子:

*   [树梢](http://treetop.rubyforge.org/)
*   [柑橘](https://github.com/mjijackson/citrus)
*   [rsec](https://github.com/luikore/rsec)

如果您想了解更多关于使用 Parslet 创建语言的信息，这里有一些额外的资源:

*   [用 Parslet 解析 TOML】](http://zerowidth.com/2013/02/24/parsing-toml-in-ruby-with-parslet.html)
*   [使用 Parslet 编写自己的语言](https://www.openshift.com/blogs/using-parslet-to-write-your-own-language)
*   [使用 Parslet 编写 DSL——Wicked Good Ruby 2013](http://confreaks.com/videos/2730-wickedgoodruby-writing-dsl-s-with-parslet)

## 分享这篇文章