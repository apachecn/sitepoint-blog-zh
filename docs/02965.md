# Ruby 中基于属性的测试的方式和原因

> 原文：<https://www.sitepoint.com/the-how-and-why-of-property-based-testing-in-ruby/>

![](img/f0c2e6acef484c92ee1dd00f8d2e72ec.png)

想想你最后一次写单元测试(希望是最近)。你必须想出快乐之路，悲剧之路，以及那些很难找到的边缘案例。因为我们不是不会犯错的人，所以我们往往会错过一些事情。我们在这里错过了一个边缘案例，忘记了处理那里的一个错误，并且发现了我们在生产过程中的遗漏。

基于属性的测试完全颠覆了单元测试的概念。你可以写一个*属性*，而不是写一个测试的具体*例子*。在这个上下文中，属性表示为您定义的所有输入保持*的条件。*

这里的一个例子当然是有帮助的，即使不是必需的。假设你想测试一个反转数组的函数。有哪些有用的属性？嗯，我能想到以下几点:

*   数组的第一个元素成为最后一个元素
*   数组的长度在反转前后保持不变
*   反转数组两次将会还原原始数组

你或许可以再想出几个。使用基于属性的测试工具，您可以将上述内容表达为代码，并告诉它使用随机生成的数组生成数百甚至数千个测试用例。

许多基于属性的测试工具的一个很酷的特性正在萎缩。这意味着该工具将尽其所能找到导致属性不满足的最小的输入。

### 这听起来很熟悉…

如果上面的描述听起来很熟悉，那么您可能听说过一个叫做 QuickCheck 的测试工具。QuickCheck 最初是用 Haskell 编写的，但是现在已经出现在像 T2、Erlang 和 T3 这样的语言中。还有其他的 QuickCheck 实现，比如 Scala 的 [ScalaCheck](https://scalacheck.org/) 和 F#的 [FsCheck](https://github.com/fscheck/FsCheck) 。

虽然上述语言主要是函数式语言，但这并没有阻止 [Rantly](https://github.com/abargnesi/rantly) 的创造者为 Ruby 开发一个类似 QuickCheck 的工具，并给它起了一个可爱的名字。

在本文中，我们将通过在 Ruby 中尝试基于属性的测试来扩展我们的测试库。

## 使用 RSpec 设置 Rantly

我们将首先设置一个演示项目来与 RSpec 一起使用 Rantly。MiniTest 和 TestUnit 的说明类似。让我们创建一个演示项目:

```
% mkdir rantly-demo && cd rantly-demo 
```

然后创建一个骨架 **Gemfile** :

```
% bundle init
Writing new rantly-demo/Gemfile 
```

我们只需要`rantly`:

```
source "https://rubygems.org"

gem "rantly" 
```

安装依赖项的时间:

```
% bundle
Fetching gem metadata from https://rubygems.org/..
Fetching version metadata from https://rubygems.org/.
Resolving dependencies...
Using rantly 0.3.2
Using bundler 1.10.6 
```

假设您已经安装了`rspec`,您可以快速设置您的项目以使用 RSpec:

```
rspec --init 
```

## 使用 Rantly

下面是你如何表达一个慷慨的属性:

 `it "define your property here" do
property_of {
Rantly { <GENERATOR GOES HERE> }
}.check { |a_generated_value|
<EXPECTATION GOES HERE>
}
end` 

因此，一个例子是:

 `it "integer property only returns Integer type" do
property_of {
integer # the generator
}.check { |i| # i is the generated value
expect(i).to be_a(Integer) # the expectation
}
end` 

## 失败的测试和萎缩

让我们看看当一个属性失败时 Rantly 告诉我们什么。我们将告诉 Rantly 创建整数数组，然后检查*每个*生成的数组都有*完全*偶数元素。显然，这将会失败。有趣的是*它将如何*失败？让我们把黑幕属性写在 **spec/array_spec.rb** 里:

```
require 'rantly'
require 'rantly/rspec_extensions'
require 'rantly/shrinks'

RSpec.describe "Array" do
  it "even numbers" do
    property_of {
      Rantly { array { integer } }
    }.check { |i|
      expect(i).to all(be_even)
    }
  end
end 
```

运行该文件时，输出如下:

```
[0, 0, 0, 0, -1324248444, -819907805037675589]
found a reduced failure case:
...
[0, 0, 0, 0, -10102, -819907805037675589]
found a reduced failure case:
...
[0, 0, 0, 0, -77, -819907805037675589]
found a reduced failure case:
...
[0, 0, 0, 0, -1, -819907805037675589]
found a reduced failure case:
...
minimal failed data is:
[0, 0, 0, 0, 0, -819907805037675589]
F

Failures:

  1) Array even numbers
     Failure/Error: expect(i).to all(be_even)
       expected [-1384706466568309853, -2143298094606122148, 2181188094126790798, 1908884087348911076, -710950470620772656, -819907805037675589] to all be even

          object at index 0 failed to match:
             expected `-1384706466568309853.even?` to return true, got false

          object at index 5 failed to match:
             expected `-819907805037675589.even?` to return true, got false 
```

在这里，我们可以看到 Rantly 试图通过将元素的数量减少到尽可能小来找到最小的失败案例。这个过程叫做*收缩*。Rantly 能够对整数、字符串、数组和散列执行收缩。

不幸的是，这并没有减少最后一个 T2 元素。不过比较明显的是(经过一番眯眼)`-819907805037675589`是负数。

## 提出属性

到目前为止，在进行基于属性的测试时，最困难的任务是首先提出属性。在这一节中，我们将介绍一些有用的技术，以确定应该编写什么样的属性。这个列表并不详尽，但可以作为一个很好的起点。

### 1.反函数

这些通常很容易被发现。反函数的例子包括:

*   编码和解码(例如 Base64)
*   序列化和非序列化(例如 JSON)
*   添加和删除

虽然编写一个利用“逆向性”的属性并不能真正涵盖很多内容，但是这个属性作为一个健全性检查是非常有用的。再加上*100*或更多生成的测试用例，你应该会感到相当自信。

这里有一个如何测试 Base 64 编码和解码的例子。在**规格**中创建 **base64_spec.rb** :

```
require 'rantly'
require 'rantly/rspec_extensions'
require 'rantly/shrinks'

require 'base64'

RSpec.describe "Base64" do
  it "encoding and decoding are inverses of each other" do
    property_of {
      Rantly { sized(30) { string } }
    }.check(1000) { |s|
      puts s
      expect(Base64.decode64(Base64.encode64(s))).to eq(s)
    }
  end
end 
```

这基本上创建了随机的 30 个字符的字符串，并生成了 1000 个测试，断言编码和解码确实是彼此相反的。这里有一个例子:

```
...
I.F@5!x}PI({m[8XPw=r1Vep(\*uIi
Cz)ZkAcUUE],xoOI/@g*&;`I):JVn $
J \ Oo " PT`R-8[A3);k*5Li0+v;[e8o=
R{IL2Vz]$.KcOG<uy<gBpPc}T|+j7n
.gsw?:?#"Iy%0O>-V0!]y#K}`6>M！ny
xnkLFCLeim)VR9r | qazoyrnwd 1 Gou
？~ m | 6；；N~w.b) <bef7>_+L-mIx-X？lkb 55 z 7 c " _ Ps
Kb[还是？薇伊。kwk > xB > :s-！d？GxS
！lb%bw > u0V6xHwh9+D6c$/Q0！UkM\Y</bef7>

成功:1000 次测试
。
" `什么

### 2.幂等性

*等幂*——一个让你的朋友印象深刻、让你的同事烦恼的词！这基本上意味着做一次和做多次是一样的。例如，多次调用`Array#uniq`将导致相同的值。排序函数也属于同一类。

让我们试试`Array#uniq`(你可以把这个放在 **spec/uniq.rb** ):

```
require 'rantly'
require 'rantly/rspec_extensions'
require 'rantly/shrinks'

RSpec.describe "Array" do
  it "uniq is idempotent" do
    property_of {
      Rantly { array { Rantly { i = integer; guard i >= 0; i } } }
    }.check { |a|
      expect(a.uniq.uniq).to eq(a.uniq)
    }
  end
end 
```

这里，我们正在生成一个非负整数数组。我们使用*生成器保护*将生成的整数限制为非负整数:

```
Rantly { i = integer; guard i >= 0; i } 
```

### 3.使用现有的实现

假设你发现了一种叫做 QuickerSort 的新排序算法，你知道它比现有的用 Ruby 实现的排序算法要快。现在，您需要做的就是确保 QuickerSort 实现产生与 Ruby 实现完全相同的结果。

使用 QuickCheck，我们可以很容易地表达这样一个属性:

```
RSpec.describe "Array" do
  it "Array#quicker_sort works produces the same result as Array#sort" do
    property_of {
      Rantly { array(range(0, 100)) { integer }}
    }.check { |a|
      expect(a.quicker_sort).to eq(a.sort)
    }
  end
end 
```

这里我们生成一个随机整数数组，它可以是一个空数组，也可以是一个包含 100 个元素的数组。

### 自定义生成器:生成 DNA 序列

让我们学习如何创建一个定制的生成器。当您的输入数据必须符合某些要求时，自定义生成器非常有用。例如，如果一个方法只对二进制数字进行操作，那么使用默认的整数生成器就没有多大用处。

在这个例子中，我们将创建一个 DNA 序列生成器。出于我们的目的，DNA 序列基本上是一个包含`A`、`T`、`G`和`C`组合的数组。

例如`["C", "G", "A", "G", "A", "T", "G"]`。我们的第一站是`Rantly#choose`，它让生成器从指定的选项中选择一个值:

 `choose("A", "T", "G", "C")` 

接下来，我们知道我们需要一个数组。数组生成器接受一个块，调用该块来生成数组的一个元素。这正是我们所需要的:

 `Rantly { array { choose("A", "T", "G", "C") } }` 

为了增加一些变化，我们还可以通过指定一个范围让生成器产生各种长度的数组:

 `Rantly { array(range(0, 20)) { choose("A", "T", "G", "C") } }` 

在控制台上试试这个。你需要做一个`require "rantly"`:

```
> 10.times { p Rantly { array(range(1,20)) { choose("A", "T", "G", "C") } } }
["T", "A", "A", "G", "A", "A", "T", "G", "G", "T", "A", "T", "T", "T"]
["T", "A", "T", "T", "G"]
["T", "T", "C", "G", "T", "T", "C", "A"]
["T", "C", "C"]
["G", "G", "T", "C"]
["A", "A", "A", "A", "C", "G", "T", "G", "G", "T"]
["C", "A", "G"]
["T", "G", "C", "C", "A", "C", "C", "T", "G", "C", "T", "C", "G", "C"]
["G", "C", "T", "T", "T", "A", "C", "A"]
["G", "G", "G", "A", "C", "T", "G", "C"]
 => 10 
```

很酷，是吧？

## 摘要

基于属性的测试提出了另一种思考测试的方式。与其写具体的例子，为什么不写一般的属性，让工具为你生成测试用例呢？

然而，不要放弃你的单元测试！基于属性的测试可能对测试数据结构、无状态函数(也就是函数式编程意义上的函数)和算法最有用。它可能不太适合测试业务逻辑。换句话说，Rantly 是你腰带上的一个新工具，而不是整个腰带。

尝试 Rantly，让我知道它如何进行。测试愉快！

## 分享这篇文章