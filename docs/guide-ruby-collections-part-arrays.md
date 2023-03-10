# Ruby 集合指南，第一部分:数组

> 原文：<https://www.sitepoint.com/guide-ruby-collections-part-arrays/>

![collections_arrays](img/56546a3f678848fe4fc1947657009364.png)

编程主要包括排序和搜索。在像 C 这样的老语言中，你可能需要为这些任务编写自己的数据结构和算法。然而，在 Ruby 中，这些结构已经被抽象掉，以便能够专注于手头的任务。

下面是这些抽象的指南。它并不完全全面——可以写一整本书的 Ruby 集合——但是我撒了一张大网，涵盖了我认为作为 Ruby 程序员经常会遇到的内容。它分为 4 个部分:

1.  数组和迭代
2.  散列、集合和范围
3.  可枚举和枚举器
4.  技巧和提示

这是一个高度示例驱动的指南。我认为学习这些东西的最好方法是打开一个 irb shell 并跟着做，在这个过程中创建你自己的聪明变体。

## 数组

数组是 Ruby 集合的主力。大多数对集合进行操作的方法都会返回一个数组，即使原始集合不是数组。事实上，它们根本不是真正的数组，而是一种通用的数据结构。您可以使它们表现得像集合、堆栈或队列。它们在功能上相当于 Python 列表。

### 创造

Ruby 数组的创建类似于其他动态语言中的数组。

```
>> numbers = [1, 0, 7]
>> numbers[2]
=> 7
>> numbers.size
=> 3
```

数组不需要由相同的数据类型组成。它们可以是异质的。

```
>> data = ["bob", 3, 0.931, true]
```

由于 Ruby 是完全面向对象的，数组被表示为对象，而不仅仅是特殊的解释器规则。这意味着您可以像构造其他对象一样构造它们。

```
>> numbers = Array.new([1,2,3]) 
=> [1, 2, 3]
```

可以向数组构造函数传递一个起始大小，但它可能不会像您预期的那样工作。因为 Ruby 数组是动态的，所以没有必要为它们预先分配空间。当您将一个数字单独传递给 Array#new 时，就会创建一个包含这么多 nil 对象的数组。

```
>> numbers = Array.new(3)
=> [nil, nil, nil]
```

尽管 nil 是一个单例对象，但它像其他对象一样在集合中占据一个位置。所以当你向一个 nil 对象的数组中添加一个元素时，它会被附加在末尾。

```
>> numbers << 3
=> [nil, nil, nil, 3]
>> numbers.size
=> 4
```

如果向 Array#new 传递第二个参数，它将变成填充值而不是 nil。

```
>> Array.new(3, 0)
=> [0, 0, 0] 

>> Array.new(3, :DEFAULT)
=> [:DEFAULT, :DEFAULT, :DEFAULT]
```

除了标准文字，Ruby 还通过[%符号](http://en.wikibooks.org/wiki/Ruby_Programming/Syntax/Literals#The_.25_Notation)提供了一些其他的语法快捷方式。

```
>> ORD = "ord"
>> %W{This is an interpolated array of w#{ORD}s}
=> ["This", "is", "an", "interpolated", "array", "of", "words"] 

>> %w{This is a non-interpolated array of w#{ORD}s}
=> ["This", "is", "a", "non-interpolated", "array", "of", "w\#{ORD}s"]
```

### 数组索引

如果试图访问尚不存在的数组索引，大多数语言都会抛出异常。如果试图读取一个不存在的索引，Ruby 返回 nil。

```
>> spanish_days = []
>> spanish_days[3]
=> nil
```

如果你写一个不存在的索引，Ruby 会在数组中插入 nil 直到那个索引。

```
>> spanish_days[3] = "jueves"  
=> [nil, nil, nil, "jueves"] 
>> spanish_days[6] = "domingo" 
=> [nil, nil, nil, "jueves", nil, nil, "domingo"]
```

如果试图访问负数组索引，大多数语言也会出错。Ruby 认为负索引从数组的末尾开始，随着它们的增加，返回到数组的开头。

```
>> ["a","b","c"][-1]
=> "c"
>> ["a","b","c"][-3]
=> "a"
```

如果您提供一个不存在的负数组索引，结果与一个不存在的正数组索引相同–nil

```
>> ["a","b","c"][-4]
=> nil
```

### 数组范围

Ruby 数组的另一个有用特性是访问元素范围的能力。然而，考虑到在 Ruby 中有许多不同的方式来指定一系列元素，它们可能会很棘手。

```
>> letters = %w{a b c d e f}
=> ["a", "b", "c", "d", "e", "f"]
>> letters[0..1]
=> ["a", "b"]
>> letters[0, 2]
=> ["a", "b"]
>> letters[0...2]
=> ["a", "b"]
>> letters[0..-5]
=> ["a", "b"]
>> letters[-6, 2]
=> ["a", "b"]
```

以下是这些例子背后的原因:

1.  字母[0..1]–给我从 0 到 1 的元素
2.  字母[0，2]–从索引 0 开始，给我 2 个元素
3.  字母[0…2]–给我从 0 到 2 的元素
4.  字母[0..-5]–给我元素 0 到-5
5.  字母[-6，2]–从元素-6 开始，给我 2 个元素

如果您是 Ruby 新手，您可能会想这怎么可能。原来数组访问无非就是对#[]方法的调用。

```
>> letters.[](0..1)
=> ["a", "b"]
```

此外，0..1 只不过是一个伪装的范围对象。您可以使用#class 方法来验证这一点。

```
>> (0..1).class
=> Range
```

所以真正发生的是一个表示元素目标范围的 Range 对象被传递给 Array#[]。

```
>> letters.[](Range.new(0,1))
=> ["a", "b"]
```

Ruby 面向对象的特性使我们能够做一些非常疯狂的事情，如果我们愿意的话。如果我们想以英语单词的形式使用数字会怎么样？

numerouno gem 可以用来解析英文数字。

```
$ gem install numerouno

>> require 'numerouno'
>> "one-hundred sixty three".as_number
=> 163
```

使用 numerouno，你现在可以创建一个接受英文索引的数组类。

```
class EnglishArray < Array
  def [](idx)     
    if String === idx
      self.at(idx.as_number)  
    end
  end
end 

>> arr = EnglishArray.new(["a","b","c"])
>> arr["one"]
=> "b"
```

### 转换

还记得我说过 Ruby 的数组是一种通用的数据结构吗？下面是一些可以在 Array 上执行的操作类型的示例。

**添加元素**

```
>> [1,2,3] << "a" 
=> [1,2,3,"a"]

>> [1,2,3].push("a")
=> [1,2,3,"a"]

>> [1,2,3].unshift("a")
=> ["a",1,2,3]

>> [1,2,3] << [4,5,6]
=> [1,2,3,[4,5,6]]
```

**移除元素**

```
>> arr = [1,2,3]
>> arr.pop
=> 3
>> arr
=> [1,2]

>> arr = ["a",1,2,3]
>> arr.shift
=> "a"
>> arr
=> [1,2,3]

>> arr = [:a, :b, :c]
>> arr.delete(:b)
=> :b
>> arr
=> [:a, :c]
>> arr.delete_at(1)
=> :c
>> arr
=> [:a]
```

**组合数组**

```
>> [1,2,3] + [4,5,6]
=> [1,2,3,4,5,6]

>> [1,2,3].concat([4,5,6])
=> [1,2,3,4,5,6]

>> ["a",1,2,3,"b"] - [2,"a","b"]
=> [1,3]
```

**布尔运算**

```
>> [1,2,3] & [2,3,4]
=> [2,3]

>> [1,2,3] | [2,3,4]
=> [1,2,3,4]

>> arr1 = [1,2,3]
>> arr2 = [2,3,4]
>> xor = arr1 + arr2 - (arr1 & arr2)
=> [1,4]
```

**移动元素**

```
>> [1,2,3].reverse
=> [3,2,1]

>> [1,2,3].rotate
=> [2,3,1]

>> [1,2,3].rotate(-1)
=> [3,1,2]
```

**安全保护**

```
>> arr = [1,2,3]
>> arr.freeze
>> arr << 4  
=> RuntimeError: can't modify frozen Array
```

**将元素组合成一个字符串**

```
>> words = ["every","good","boy","does","fine"]
>> words.join
=> "everygoodboydoesfine"

>> words.join(" ")
=> "every good boy does fine"
```

**移除嵌套**

```
>> [1,[2,3],[4,["a", nil]]].flatten
=> [1,2,3,4,"a",nil]

>> [1,[2,3],[4,["a", nil]]].flatten(1)
=> [1,2,3,4,["a", nil]]
```

**删除重复**

```
>> [4,1,2,1,5,4].uniq
=> [4,1,2,5]
```

**切片**

```
>> arr = [1,2,3,4,5]
>> arr.first(3)
  => [1,2,3]

>> arr.last(3)
=> [3,4,5]
```

**查询**

```
>> ["a","b","c"].include? "d"
=> false

>> ["a", "a", "b"].count "a"
=> 2

>> ["a", "a", "b"].count "b"
=> 1

>> [1,2,[3,4]].size
=> 3
```

### 循环

迭代是 Ruby 真正闪光的地方。在许多语言中，迭代被笨拙地附加上去。然而，在 Ruby 中，你永远不会觉得有必要写一个经典的 for 循环。

Ruby 迭代中的核心构造是#each 方法。

```
>> ["first", "middle", "last"].each { |i| puts i.capitalize }
First
Middle
Last
```

虽然#each 是 Ruby 中的核心迭代器，但是还有许多其他的迭代器。例如，您可以使用#reverse_each 向后遍历集合。

```
>> ["first", "middle", "last"].reverse_each { |i| puts i.upcase }
LAST
MIDDLE
FIRST
```

另一个方便的方法是#each_with_index，它将当前索引作为第二个参数传递给块。

```
>> ["a", "b", "c"].each_with_index do |letter, index| 
>>   puts "#{letter.upcase}: #{index}"
>> end
A: 0
B: 1
C: 2
```

这个#each 方法到底是如何工作的？最好的理解方式就是自己做#each。

```
class Colors
  def each
    yield "red"
    yield "green"
    yield "blue"
  end
end

>> c = Colors.new
>> c.each { |i| puts i }
red
green
blue
```

#yield 方法调用您传递给#each 的块。对于许多 ruby 新手来说，这可能很难理解。把#yield 想象成调用一段匿名代码，这段代码是你提供给#yield 所在的方法的。在前面的示例中，yield 被调用了三次，因此“{ |i| puts i }”运行了三次。

### 部分迭代

for 循环的好处之一是可以指定开始点和结束点。#但是，each 总是遍历整个集合。

如果您只需要遍历集合的一部分，至少有两种方法可以做到:

对集合进行切片，然后遍历切片

```
>> [1,2,3,4,5][0..2].each { |i| puts i }
1
2
3
```

使用范围来生成指数

```
>> arr = [1,2,3,4,5]
>> (0..2).each { |i| puts arr[i] }
1
2
3
```

虽然有点难看，但我敢打赌，随着数组元素变大，复制时间变长，选项 2 会更有效。

### #每个与#地图/#收集

除了#each，您可能还会经常遇到#map。#map 类似于#each，只是它从每个块调用的结果中构建一个数组。这很有用，因为#each 只返回调用者。

```
>> [1,2,3].each { |i| i + 1 }
=> [1,2,3]

>> [1,2,3].map { |i| i + 1 }
=> [2,3,4]
```

如果您只是在每个元素上调用一个方法，您可以使用一个方便的快捷方式。

```
>> [1,2,3].map(&:to_f)
=> [1.0, 2.0, 3.0]
```

这与以下内容相同:

```
>> [1,2,3].map { |i| i.to_f }
=> [1.0, 2.0, 3.0]
```

注意#map 不会改变原始集合。它只是根据每个块调用的结果返回一个数组。如果您希望原始集合反映这些更改，请使用#map！相反。

```
>> numbers = [1,2,3]
>> numbers.map(&:to_f)
=> [1.0, 2.0, 3.0]
>> numbers
=> [1, 2, 3]
>> numbers.map!(&:to_f)
=> [1.0, 2.0, 3.0]
>> numbers
=> [1.0, 2.0, 3.0]
```

您可能也注意到了 Ruby 代码中的#collect。它与#map 相同，所以两者可以互换。

```
>> letters = ["a", "b", "c"]
>> letters.map(&:capitalize)
=> ["A", "B", "C"]
>> letters.collect(&:capitalize)
=> ["A", "B", "C"]
```

### 经典迭代

鲁比提供了经典的习语。虽然它看起来更整洁，对于来自其他语言的新手来说也更熟悉，但是它不是惯用的，因为它不是面向对象的，并且不接受块。

```
>> animals = ["cat", "dog", "bird", "chuck testa"]
>> for animal in animals
>>   puts animal.upcase
>> end
CAT
DOG
BIRD
CHUCK TESTA
```

如果“不地道”还不足以吓住你，下面是 Nikals B. on stackoverflow 展示的一个不直观的结果[:](http://stackoverflow.com/questions/10396475/ruby-for-loop-a-trap)

```
>> results = []
>> (1..3).each { |i| results << lambda { i } }
>> results.map(&:call)
=> [1, 2, 3]

>> results = []
>> for i in 1..3
>>   results << lambda { i }
>> end
>> result.map(&:call)
=> [3, 3, 3]
```

此外，在 for 循环中创建的“临时”变量根本不是临时的。在上面的动物循环中使用的动物变量现在不见了，对吗？

没有。

```
>> animal
=> "chuck testa"
```

结论:Ruby for loops——最适合用来引起会议观众的眼球转动或关注表情。

## 分享这篇文章