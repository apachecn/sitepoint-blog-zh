# Ruby 2.2 中的新方法

> 原文：<https://www.sitepoint.com/new-methods-ruby-2-2/>

![vector illustration of dark red shield with ruby programming lan](img/5c69c35cce159f152638046ee11de7b1.png)

围绕 Ruby 2.2 的大部分宣传都集中在垃圾收集(GC)升级上。GC 现在将清理符号，并有一个新的增量算法来减少暂停时间。这些变化非常令人兴奋，但也添加了一些新方法。来看看 2.2 还送了什么新玩具。

### 绑定#局部变量

如果你曾经想知道在一个作用域中定义的局部变量，我有一个方法给你。将它贴在任何地方，并找出正在使用的变量:

```
def addition(x, y)
  puts binding.local_variables.inspect
  z = x + y
  puts binding.local_variables.inspect
  z
end

> addition(2, 3)
[:x, :y, :z]
[:x, :y, :z]
5
```

### 绑定#接收器

也许你不关心当地的 var。您想知道哪个对象正在接收方法调用吗？

```
class Cat
  def self.type
    binding.receiver
  end
end

> Cat.type
Cat

class Tiger < Cat
end

> Tiger.type
Tiger
```

我们大多数人都不直接处理绑定，但是如果你这样做，生活就会变得简单一些。

### Dir#fileno

获取一个`IO`对象的文件描述符并不是什么新鲜事。现在您也可以为目录获取它。文件描述符是引用特定的打开文件、目录等的一种方式。你可能以前使用过它们，甚至没有意识到。在 POSIX 系统上，0、1 和 2 分别为标准输入、标准输出和标准误差保留。
你曾经在命令行中使用过类似`2>&1`的东西吗？你提到了文件描述符。

```
> $stdout.fileno
1
> Dir.new('.').fileno
8
```

注意，这种新方法只在 POSIX 系统上可用。如果你试图在 Windows 上调用它，它会抛出一个`NotImplementedError`。

### 可枚举的#slice_after

该方法是对现有`slice_before`方法的补充。我没有在野外遇到过`slice_before`，所以我必须看看它是如何工作的。

顾名思义，`slice_before`是用来对可枚举数切片切块的。给定一种方法来匹配可枚举中的元素，它将找到一个匹配项，并在匹配之前将其分割开来:

```
> [1, 'a', 2, 'b', 'c', 3, 'd', 'e', 'f'].slice_before { |e| e.is_a?(Integer) }.to_a
[[1, "a"], [2, "b", "c"], [3, "d", "e", "f"]]
```

在这种情况下，它在整数`1`、`2`和`3`之前进行切割。

可以传递一个参数来代替一个块。当发生这种情况时，使用`===`检查参数(即大小写相等)。我们可以通过传递`Integer`得到与上面相同的结果:

```
> [1, 'a', 2, 'b', 'c', 3, 'd', 'e', 'f'].slice_before(Integer).to_a
[[1, "a"], [2, "b", "c"], [3, "d", "e", "f"]]
```

想猜猜`slice_after`是做什么的吗？它不是在匹配前切片，而是在匹配后切片:

```
> [1, 'a', 2, 'b', 'c', 3, 'd', 'e', 'f'].slice_after(Integer).to_a
[[1], ["a", 2], ["b", "c", 3], ["d", "e", "f"]]
```

### 可枚举的#slice_when

一个特别好玩的附加是`slice_when`。不像`slice_after`，这个方法只接受一个块。它遍历一个可枚举的，将元素对传递给块。当该块返回`true`时，可枚举数在一对元素之间切片:

```
> [1, 3, 4, 5, 7, 8, 9, 10, 12].slice_when { |a, b| a + 1 != b }.to_a
[[1], [3, 4, 5], [7, 8, 9, 10], [12]]
```

这里，当第二个数字不是第一个数字加 1 时，我们通过切片找到了连续数字的游程。

给定一组数字，你可以很容易地找出每个数字有多少个:

```
> Array.new(10) { rand(3) + 1 }.sort.slice_when(&:!=).map { |x| [x.first, x.size] }
[[1, 4], [2, 4], [3, 2]]
```

在这次运行中，我们得到了四次`1`，四次`2`，两次`3`。

### 浮点#next_float，浮点#prev_float

这些函数返回下一个或上一个可表示的浮点数。注意那个句子中的单词“**可表示**”，并不是所有的浮点都能被表示。

```
> 1.0.next_float
1.0000000000000002
```

注意它是如何跳过`1.0000000000000001`的？它不能用`Float`来表示:

```
> 1.0000000000000001
1.0
```

同样值得注意的是，两步之间的距离并不总是相同的:

```
> 2.0.prev_float
1.9999999999999998

> 2.0.next_float
2.0000000000000004
```

显然，像这样的方法对于寻找 [ULP](https://en.wikipedia.org/wiki/Unit_in_the_last_place) 值和做其他你很可能永远不会做的事情很有用。它们可能不是全球最有用的方法，但如果你需要它们，你会很高兴它们就在那里。

### File.birthtime，File#birthtime，File::Stat#birthtime

我们有`atime`、`ctime`和`mtime`来检查与文件相关的各种访问和修改时间。在 2.2 之前，我们还没有一个文件的诞生时间(即创建时间)。您会注意到，它和其他的一样，有类和实例两种风格:

```
> File.new('test', 'w').birthtime
2015-01-06 19:24:44 -0600

> File.birthtime('test')
2015-01-06 19:24:44 -0600
```

也可用于`File::Stat`:

```
> File::Stat.new('test').birthtime
2015-01-06 19:24:44 -0600
```

### 内核#本身

Ruby 走出去给自己找了一个身份方法。对于那些不熟悉的人，identity 方法返回它所调用的对象:

```
> 1.itself
1
```

此时，您可能想知道这在哪里有用。最常见的例子之一是分组:

```
> [2, 3, 3, 1, 2, 3, 3, 1, 1, 2].group_by(&:itself)
{2=>[2, 2, 2], 3=>[3, 3, 3, 3], 1=>[1, 1, 1]}
```

它也可以用作取消像`map`或`select`这样的方法的方法。在函数式编程中，这可能是避免尴尬条件的好方法。

### 方法#咖喱

你可能没有意识到 Ruby 能够 curry 和部分应用。以前只能在一个`Proc`上调用`curry`。同样的力量现在在`Method`上你也可以得到。

```
def sum(*args)
  args.reduce(:+)
end

> inc = method(:sum).curry(2).(1)
#<Proc:0x007ff68ac96728 (lambda)>
> inc.(3)
4
```

### 方法#超级方法

前面我们发现对象用`binding.receiver`接收一个方法。我们还可以找到该方法的父方法。调用`super_method`会返回你调用`super`时得到的方法。如果该方法没有父方法，则返回`nil`。

```
class Cat
  def speak
    'meow'
  end
end

class Tiger < Cat
  def speak
    'roar'
  end
end

> Tiger.new.method('speak')
#<Method: Tiger#speak>
> Tiger.new.method('speak').super_method
#<Method: Cat#speak>
> Cat.new.method('speak').super_method
nil
```

### String#unicode_normalize，String#unicode_normalize！，字符串#unicode_normalized？

您知道吗，在 Unicode 中，有些字符可以用多种方式表示。举个例子，一个带有重音符的 e。您可以使用单个代码点通过规范化形式规范合成(NFC)来表示它:

```
> nfc = "\u{e9}"
"é"
```

您也可以通过组合两个代码点，用规范化形式规范分解(NFD)来表示它:

```
> nfd = "\u{65}\u{301}"
"é"
```

在这种形式中，一个普通的“e”和“◌́”组合在一起，就形成了你上面看到的“é”。虽然这两者可能会产生相似的输出，但它们并不相同:

```
> nfc == nfd
false
```

`unicode_normalize`所做的是将字符串从一种形式转换成另一种形式。默认情况下，它会转换为 NFC:

```
> nfc == nfd.unicode_normalize
true
```

您还可以传递一个符号来指示要转换到的格式:

```
> nfc.unicode_normalize(:nfd) == nfd
true
```

这些形式之所以存在，是因为它们在不同的情况下有价值。NFC 代码更短，但如果你想去掉一个词的重音符号，比如说为了搜索的目的，NFD 表单会更容易处理。虽然 NFC 和 NFD 是最常见的，但如果需要，您也可以通过`:nfkc`和`:nfkd`。

bang 版本做了您所期望的事情，并修改了接收消息的字符串。

调用`unicode_normalized?`检查字符串是否匹配您想要的形式。它采用与`unicode_normalize`相同的参数:

```
> nfc.unicode_normalized?
true
> nfc.unicode_normalized?(:nfd)
false
```

### 是时候改进了

这些方法中有很多并不是日常使用的工具。它们是支撑这种语言的渐进式改进。Ruby 在过去几年经历了很多变化。1.8、1.9 和 2.0 版本之间的变化是巨大的。现在，这种语言专注于提炼由这个过程产生的一些粗糙的边缘。事实上，2.2 允许您在散列中创建带冒号的引用符号键:

```
{ 'programming-language': :ruby }
```

由于一个连字符的 HTML 属性，不再有符号键哈希和杂散哈希火箭。我认为这是我们都可以支持的。

## 分享这篇文章