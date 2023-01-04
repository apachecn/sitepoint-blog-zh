# 重构测试:无情的绿色

> 原文：<https://www.sitepoint.com/refactoring-workout-relentlessly-green/>

[![Image courtesy of Steven Depolo (Flickr)](img/6d2d05523d773e2c25ad9ec5834ac57a.png)](https://www.sitepoint.com/wp-content/uploads/2014/04/13979477587161973100_d97a4b4def_z.jpg)

图片由史蒂文·德普罗(Flickr)提供

我们不怎么练习——不像运动员和音乐家那样。

当然，他们比我们有优势，因为他们有数以千计的练习和进步，可以用来提高他们技能的任何方面。

另一方面，我们在前进的过程中不断弥补。

> 重构是以这样一种方式改变软件系统的过程，它不改变代码的外部行为，但改进其内部结构。—马丁·福勒

如何更好地进行重构？

嗯，你读了重构的书，做了更多的重构。基本上。

这还不如说*“用节拍器练习这个独立性练习，直到你能准确无误地弹奏 120 秒。从每分钟 30 次(BPM)开始，以每分钟 5 次的增量练习，直到你能一直保持每分钟 180 次。”*。

注意这里的单词 **drill** 。操练是一种非常集中的练习形式。它将一项技术定位到扭曲它的程度，使得一遍又一遍地重复这项技术的某个特定方面成为可能，如果你做错了，它会立即通知你。

即时反馈在训练中至关重要，因为如果你练习的不正确，重复会使缺陷固化，使得以后更难正确执行。

> 熟能生巧，熟能生巧。—拉里·格尔维

### 重构不是重新构建

重构时，我们经常采取跳跃，而不是步骤。然而，当我们开始着手做一些我们只知道(最终)会成功的事情时，测试却失败了。

也就是说，如果我们运行它们的话。

> 重构的挑战之一是继承——如何将重构工作分成安全的步骤，以及如何对这些步骤进行排序。—肯特·贝克

安全步骤不会导致测试失败。

这个重构练习集中在继承上，一遍又一遍地重复选择一个微小的、安全的步骤，自动化测试提供连续和即时的反馈。

### 绿色下的重构

这个练习的起点是一个简单的测试，一个平凡的实现通过了。目标是以微小、安全的步骤重构代码，直到它成为一个通用的解决方案。

一个步骤的大小受限于您的编辑器通过一次*撤销*可以恢复的内容。

有两条规则:

*   每次更改后都要运行测试。
*   如果测试失败，应该只需要一次*撤销*就可以回到绿色。

测试套件是最小的，断言一个`Checkerboard`实例的默认字符串表示返回一个交替的黑色(B)和白色(W)方块的网格。问题中的电路板尽可能小，同时仍然定义一个方格图案。

```
gem 'minitest', '~> 5.3'
require 'minitest/autorun'
require_relative 'checkerboard'

class CheckerboardTest < Minitest::Test
  def test_small_board
    expected = <<-BOARD
B W
W B
BOARD
    assert_equal expected, Checkerboard.new(2).to_s
  end
end
```

该实现对 2×2 网格的字符串表示进行了硬编码。

```
class Checkerboard
  def initialize(_)
  end

  def to_s
    "B W\nW B\n"
  end
end
```

当重构完成时，应该可以调用任意大小的`Checkerboard.new`并得到一个正确格式化的棋盘。

在这一点上，很容易添加另一个失败的测试，也许是针对 3×3 板，以便三角测量更好的设计。但是这不是测试驱动开发中的一个练习。

在这次演练中，设计将由对下一个安全步骤的有意识选择来驱动，而不是由失败的测试来驱动。如果这一步不安全，点击*撤销*一次，你应该回到绿色。

### 从哪里开始？

当前的算法存在一些问题。

```
def to_s
  "B W\nW B\n"
end
```

首先，它无法扩展。此外，它混合了数据和表示。

理想情况下，网格将被单独构建，而`to_s`方法将操纵网格来提供一个字符串表示。换行符清楚地表明这里有两个东西。两排。闻起来像数组。

如何从一个字符串到一个数组，而不在途中的某个地方失败呢？

如何在不杀死几个病人的情况下切断医院与电网的连接？

冗余。

准备一些可以让您安全进行故障转移的东西。

#### 裁员策略

写一个全新的实现，并把它放在旧的实现之前。

```
def to_s
  ["B W\n", "W B\n"].join
  "B W\nW B\n"
end
```

运行测试。

新的实现还没有通过审核，但是已经执行了，这提供了语法检查。

删除原始实现，然后再次运行测试。

```
def to_s
  ["B W\n", "W B\n"].join
end
```

如果失败了，一次撤销就可以把工作代码放回去。

使用相同的技术将换行符移出硬编码的行。

```
def to_s
  ["B W", "W B"].map {|row| row + "\n"}.join
end
```

我们太习惯于将复制视为敌人。然而，在重构中，复制是一种短期的、低成本的投资，可以带来丰厚的回报。

### 设置和交换策略

很难找到单个细胞。如果它们在自己的数组中，那就更容易了。设置和交换策略添加了所有必要的代码，以便能够在一个小步骤中进行最终更改。

```
def to_s
  rows = []
  rows << "B W"
  rows << "W B"
  ["B W", "W B"].map {|row| row + "\n"}.join
end
```

这增加了三行代码，它们对测试套件的状态没有任何影响，但是它们使得用新的`rows`变量替换硬编码的数组变得非常容易。

```
def to_s
  rows = []
  rows << "B W"
  rows << "W B"
  rows.map {|row| row + "\n"}.join
end
```

每行本身仍然是硬编码的，但是可以使用另一个设置和交换来独立转换。

```
row = ["B", "W"].join(" ")
rows << "B W"

row = ["B", "W"].join(" ")
rows << row

def to_s
  rows = []
  row = ["B", "W"].join(" ")
  rows << row
  row = ["W", "B"].join(" ")
  rows << row
  rows.map {|row| row + "\n"}.join
end
```

这就把空格从单个单元格中移走了，但是我们可以做得更好。`join`属于循环。

这尤其棘手，因为我们有两个连接需要折叠成一个点。如果我们移动一个，测试就会失败。如果我们都删除，测试将失败。如果我们首先在循环中的`row`上粘贴一个`join`，测试将会失败。

我们可以使用冗余策略，引入一个`rows2`变量，但是有一个更简单的方法。

### 空方法策略

没有理由说`String`不能有一个`join`方法！

```
class String
  def join(_)
    self
  end
end

class Checkerboard
  # ...
end
```

这使得在不破坏任何东西的情况下进行更改变得微不足道。

```
def to_s
  rows = []
  row = ["B", "W"].join(" ")
  rows << row
  row = ["W", "B"].join(" ")
  rows << row
  rows.map {|row| row.join(" ") + "\n"}.join
end
```

现在循环中的`row`可以处理数组和字符串，我们可以删除原来的`join`和 null 方法。

```
def to_s
  rows = []
  row = ["B", "W"]
  rows << row
  row = ["W", "B"]
  rows << row
  rows.map {|row| row.join(" ") + "\n"}.join
end
```

两个`row`任务相似，但不完全相同。使用冗余策略将不同的部分与保持不变的部分分开。

```
def to_s
  rows = []

  row = []
  row << "B"
  row << "W"
  rows << row

  row = []
  row << "W"
  row << "B"
  rows << row

  rows.map {|row| row.join(" ") + "\n"}.join
end
```

只有我们引入一个循环，这个解决方案才能扩展。实际上是两个。

#### 放松策略

有两个组块具有相同的设置和相同的光洁度。称这些*为 A 块*和*B 块*。

```
# chunk a

# chunk b
```

wind-unwind 策略使用一个循环来卷起数据块，并使用一个条件将它们展开到原来的位置。

```
2.times do
  if condition A
    # chunk a
  else
    # chunk b
  end
end
```

那么普通代码可以移出条件:

```
2.times do
  # common code
  if condition A
    # variation a
  else
    # variation b
  end
  # common code
end
```

将冗余策略与缠绕展开策略结合起来，以安全地引入循环。

```
def to_s
  rows = []
  2.times {|y|
    row = []
    if y == 0
      row << "B"
      row << "W"
    else
      row << "W"
      row << "B"
    end
    rows << row
  }
  rows.map {|row| row.join(" ") + "\n"}.join
end
```

将单元格铲入行中仍有许多相似之处。唯一的区别是顺序。再次运用缠绕策略。

```
def to_s
  rows = []
  2.times {|y|
    row = []
    2.times {|x|
      if y == 0
        if x == 0
          row << "B"
        else
          row << "W"
        end
      else
        if x == 0
          row << "W"
        else
          row << "B"
        end
      end
    }
    rows << row
  }

  rows.map {|row| row.join(" ") + "\n"}.join
end
```

诚然，这很糟糕，但是测试通过了，嵌套的条件可以用冗余策略来修复。

```
def to_s
  rows = []
  2.times {|y|
    row = []
    2.times {|x|
      if x == y
        row << "B"
      else
        row << "W"
      end
    }
    rows << row
  }
  rows.map {|row| row.join(" ") + "\n"}.join
end
```

`x == y`只对 2×2 棋盘有效。在较大的棋盘中，这会产生对角线条纹。

有几种有效的方法。首先:

```
if (x.even? && y.even?) || (x.odd? && y.odd?)
  # it's black
else
  # it's white
end
```

另一个更简洁:

```
if (x+y).even?
  # it's black
else
  # it's white
end
```

只要我们循环的次数足够多，这个算法对任何大小的棋盘都有效。我们一直在向新的`Checkerboard`实例传递我们需要的参数。使用设置和交换策略使该数据对实例的其余部分可用，然后用对`size`的调用替换幻数。

```
attr_reader :size
def initialize(size)
  @size = size
end

def to_s
  rows = []
  size.times {|y|
    row = []
    size.times {|x|
      if (x+y).even?
        row << "B"
      else
        row << "W"
      end
    }
    rows << row
  }
  rows.map {|row| row.join(" ") + "\n"}.join
end
```

通过添加第二个测试来证明解决方案有效，从而对解决方案进行健全性测试。

```
def test_chess_board
    expected = <<-BOARD
B W B W B W B W
W B W B W B W B
B W B W B W B W
W B W B W B W B
B W B W B W B W
W B W B W B W B
B W B W B W B W
W B W B W B W B
BOARD
    assert_equal expected, Checkerboard.new(8).to_s
  end
```

这不是最终的解决方案。方法受到许多代码气味的困扰。也许应该有一个`Cell`对象，它有一个`to_s`方法来决定*黑色*或*白色*的字符串表示是什么样子。所有这些改变都可以通过安全的步骤来实现。

### 结束语

> 等等。真的吗？你会草草了事，这样你就能一步到位而不是两步就做出改变？

呃，不。不是真的。那太荒谬了。

这些策略是高度集中的夸张。它们的变体在现实世界中可能是有用的，但是进行几次这种训练的最大结果是视角的转变。

突然，你会发现自己找到了偷偷摸摸的、创造性的方法来做出改变，而不必关闭网站或处理长期存在的分支。

你可能还会发现你的尺度感得到了调整。小步往往比我们想象的要小。

最后，你可能会发现你对怪异代码的容忍度增加了。

毕竟，你总是可以重构的。

## 分享这篇文章