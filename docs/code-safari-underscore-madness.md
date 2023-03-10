# 代码旅行:强调疯狂

> 原文：<https://www.sitepoint.com/code-safari-underscore-madness/>

上周，我在苏格兰 Ruby 会议上看到了一个精彩的演讲:Roland Swingler 的《闲散程序员的文学评论》。它向我介绍了一个疯狂的小 ruby 脚本，允许你完全用下划线写程序！

```
# hello.rb
require "_"
____ _ _____ ____ __ ____ ____ __ ___ ____ __ __ _ ______
_____ ___ _ _ ___ _____ ______ ____ _ _ ____ _ _ ____ _
____ __ __ ___ _ ______ ___ ____ __ ______ ____ _ ____ ____
__ _ ____ _ _ ___ _____ _____ _ ______ ____ _ ______ _____
```

这是一个 hello world 应用程序，就在那里。我也几乎不相信。Roland 在他的演讲中简要介绍了这个代码是如何工作的，但是考虑到我们之前的[混淆冒险](https://www.sitepoint.com/code-safari-almost-sinatra-almost-readable/)，我想更深入一点。

## 鸟瞰

我们将从 git 中复制一个库，这样我们就可以轻松地修改它。克隆它，并尝试运行上面的脚本:

```
> cd /tmp
> git clone https://github.com/mame/_
> ruby -I _/lib hello.rb
Hello, world!
```

世界真好！Ruby 的`-I`选项向加载路径添加了一个新目录，允许`require "_"`行成功地在 git 存储库中找到`_.rb`。随着我们的测试工具的运行，让我们打开代码来看看其中的疯狂之处。

```
# _/lib/_.rb
def __script__(src)
code = []
src = src.unpack("C*").map {|c| c.ord.to_s(6).rjust(3, "0").chars.to_a }
src.flatten(1).map {|n| n.to_i(6) + 1 }.each do |n|
code.empty? || code.last.size + n + 1 >= 60 ? code << "" : code.last << " "
code.last << "_" * n
end
([%q(require "_")] + code).join("
```

”
结束

```
$code, $fragment = [], []
def method_missing(mhd, *x)
if x.empty?
$code.concat($fragment.reverse)
$fragment.clear
end
$fragment << (mhd.to_s.size - 1).to_s
end

at_exit do
$code.concat($fragment.reverse)
eval($code.join.scan(/.../).map {|c| c.to_i(6) }.pack("C*"))
end
```

忽略`__script__`函数，它是创建下划线源文件的助手，只有 12 行代码完成了真正的工作。为了理解它们，我们需要熟悉一些有趣的 Ruby 概念。

第一个是`method_missing`，这是一个内置的方法，当一个方法与类中的任何内容都不匹配时，就调用这个方法。
在下划线的情况下，它在顶层定义了`method_missing`，在 Ruby 中，它仍然在对象的上下文中执行:

```
> ruby -e 'puts self.class'
Object
```

举例来说，我们可以编写一个脚本来回显我们调用的任何方法:

```
# echo.rb
def method_missing(method_name)
puts method_name.to_s
end

hello
world
```

要理解的第二个概念是 Ruby 中的方法命名。传统上用字符和下划线定义方法，但这不是 Ruby 的固有限制。正如下划线所表明的，仅包含下划线的方法名是有效的，您甚至可以定义包含空格的方法！当然，解析器不能调用它们，但是我们可以解决这个问题:

```
class Greeter
define_method("say hello") do
puts "hello"
end
end

Greeter.new.send("say hello")
```

最后一个重要的概念是`at_exit`。来自 [Ruby 文档](http://www.ruby-doc.org/core/classes/Kernel.html#M001431)

> 将 block 转换为 Proc 对象(并因此在调用点绑定它),并在程序退出时注册它以便执行。如果注册了多个处理程序，它们将以与注册相反的顺序执行。

这就是诸如`Test::Unit`和`RSpec`这样的测试框架通常用来在执行一个文件后自动运行测试的。

## 拼凑起来

我们现在可以理解下划线的一般结构:使用 method missing 来处理带有任意数量下划线的方法调用，在`$code`和`$fragment`变量中累积代码，然后在程序退出时执行这些代码。让我们通过在`eval`语句之前添加一个`puts`语句来了解它正在累积的代码。我们可以很容易地做到这一点，因为我们从 git 克隆了代码，并将其添加到我们的加载路径中；这种在图书馆的黑客行为更难做到(尽管仍然有可能！)用宝石的时候。

```
at_exit do
$code.concat($fragment.reverse)
puts $code.inspect
eval($code.join.scan(/.../).map {|c| c.to_i(6) }.pack("C*"))
end
```

这显示了由`method_missing`中的`mhd.to_s.size - 1`调用生成的号码列表

```
> ruby -I _/lib hello.rb
["3", "0", "4", "3", "1", "3" # and so on...
```

我们如何从这里开始编码？转换过程有几个步骤，我们将依次讨论。`irb`为我们提供了一种快速、简单的方法来逐步完成流程并可视化转换:

```
irb> input = ["3", "0", "4", "3", "1", "3"]
=> ["3", "0", "4", "3", "1", "3"]
irb> input.join
=> "304313"
irb> input = ["3", "0", "4", "3", "1", "3"]
=> ["3", "0", "4", "3", "1", "3"]
irb> input = input.join
=> "304313"
irb> input = input.scan(/.../)
=> ["304", "313"]
irb> input = input.map {|c| c.to_i(6) }
=> [112, 117]
irb> input.pack("C*")
=> "pu" # first two characters of our source code
```

里面有几个棘手的电话。如果你以前没有见过它，可能会有点困惑，特别是因为这个正则表达式看起来不熟悉。再次参考[文档](http://ruby-doc.org/core/classes/String.html#M001181):

> 遍历`str`，匹配模式(可能是 Regexp 或字符串)。对于每个匹配项，都会生成一个结果，该结果或者被添加到结果数组中，或者被传递给块。

我们没有障碍，所以这只是增加了结果。regex `/.../`匹配三个字符，所以这就是它将输入分成三组的方式。

这个`to_i`调用很有趣:这个`6`参数是什么？

> 返回将 str 中的前导字符解释为整数基数(介于 2 和 36 之间)的结果。

所以它把数字解释为基数 6，但是为什么这是必要的呢？让我们看看如果我们把它改回基数 10 会发生什么(注意方法签名中添加的参数`base`)。

```
# _/lib/_.rb
def __script__(src, base = 6)
code = []
src = src.unpack("C*").map {|c| c.ord.to_s(base).rjust(3, "0").chars.to_a }
puts src.inspect
src.flatten(1).map {|n| n.to_i(base) + 1 }.each do |n|
code.empty? || code.last.size + n + 1 >= 60 ? code << "" : code.last << " "
code.last << "_" * n
end
([%q(require "_")] + code).join("
```

”
结束

```
> ruby -r _/lib/_ -e 'puts __script__("puts "Hello, world!"", 10)'
require "_"
__ __ ___ __ __ ________ __ __ _______ __ __ ______ _ ____
___ _ ____ _____ _ ________ ___ __ _ __ __ _ _________ __ _
_________ __ __ __ _ _____ _____ _ ____ ___ __ __
__________ __ __ __ __ __ _____ __ _ _________ __ _ _ _
____ ____ _ ____ _____
```

解码很好，所以选择基数 6 似乎只是为了美观。

这个过程的最后一个阶段是神秘的`pack`方法，其参数`'C*'`令人费解。这是一种基于映射到数据类型(如不同类型的整数和字符串)的单字母代码将数组转换为二进制序列的方法。`C` [映射到](http://ruby-doc.org/core/classes/Array.html#M000206)“8 位无符号整数(unsigned char)”，`*`表示使用此映射转换所有剩余元素。由于我们所有的数字都是代码的 ASCII 码，这就有了将我们的数组转换成可以传递给`eval`的有效源代码的效果。`pack`很难用语言来理解——你最好的办法是在`irb`中摆弄它。

我们到达了终点，但是我们跳过了`method_missing`中的另一个重要分支。这里再次供参考:

```
$code, $fragment = [], []
def method_missing(mhd, *x)
if x.empty?
$code.concat($fragment.reverse)
$fragment.clear
end
$fragment << (mhd.to_s.size - 1).to_s
end
```

`x.empty?`检查在做什么？这与 ruby 中调用方法的顺序有关。将多个方法链接在一起而没有明确的括号通常是不好的形式，但是 Ruby 支持。它从右向左计算调用，因此下面两行是等价的:

```
method1 method2 method3
method1(method2(method3))
```

这意味着`method3`首先被执行，这给了我们一个从后到前的排序。这就是`$fragment.reverse`调用派上用场的地方，这样下划线的版本可以和它所代表的代码顺序相同。`x.empty?`只是检测行尾的一种方式——下划线不使用任何参数，而`method3`是上述方法中唯一没有任何参数的方法。

您可能已经注意到的另一件事是，这里没有对下划线进行实际检查。看起来我们可以使用任何我们选择的字符来编写我们的程序！这让我这个用柠檬墨水写隐藏字母的孩子兴奋不已。

```
require '_'

look a poem!

a monkey ark
a baboon yacht too _
a big aquian din!
a animal party
```

但是隐藏的信息是什么呢？那是让你去发现的！以下是你可以尝试的一些其他方法:

*   基数 6 编码比基数 10 编码短，但它是最短的吗？编写一个程序来计算给定程序的最短输出的基数。
*   下划线是在 1.8.7 上写的，1.9.2 上有一个输入“puts 1”的 bug。你能找到并修理它吗？
*   更多的诗！

请在评论中分享你的经历。下周和我一起在代码丛林中进行更多的冒险。

## 分享这篇文章