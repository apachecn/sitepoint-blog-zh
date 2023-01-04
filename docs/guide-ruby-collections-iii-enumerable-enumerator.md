# Ruby 集合指南 III:可枚举和枚举器

> 原文：<https://www.sitepoint.com/guide-ruby-collections-iii-enumerable-enumerator/>

![collections_enum](img/4e9616bae370b7e659da1133f1c3e60a.png)

在本文中，我将介绍可枚举模块和枚举器类。为了最大限度地利用 Ruby 的集合，你需要理解它们是如何工作的，以及它们能给你带来什么。特别是，Enumerable 本身对 Ruby 的简洁和灵活性有很大的贡献。事实上，许多新的 ruby 爱好者甚至在不知情的情况下使用它。

## 可列举的

由于“混合”的概念，可枚举在 Ruby 中是可能的。在大多数语言中，代码共享只能通过继承来实现，而继承通常仅限于一个父类。除了类，Ruby 还有模块，它们是方法和常量的容器。如果你想写一次代码，然后在一堆不相关的类之间共享，你可以把它放在模块中，和你的类“混合”在一起。请注意，这不同于共享接口，比如在 Java 中，只共享方法签名。

可以通过`Object#included_modules`找出哪些类使用了 Enumerable。

```
>> Array.included_modules
=> [Enumerable, PP::ObjectMixin, Kernel] 
>> String.included_modules
=> [Comparable, PP::ObjectMixin, Kernel]
```

为了让一个类使用 Enumerable，它必须定义一个`#each`方法。此方法应该将集合中的每一项都生成一个块。还记得我们之前上的颜色课吗？让我们给它添加 Enumerable 以赋予它神奇的迭代能力。

```
>> class Colors
>>   include Enumerable

>>   def each
>>     yield "red"
>>     yield "green"
>>     yield "blue"
>>   end
>> end

>> c = Colors.new
>> c.map { |i| i.reverse }
=> ["der", "neerg", "eulb"]
```

通过检查`#instance_methods`方法的输出，可以看出 Enumerable 提供了哪些方法。

```
>> Enumerable.instance_methods.sort
=> [:all?, :any?, :chunk, :collect, :collect_concat, :count, :cycle,
    :detect, :drop, :drop_while, :each_cons, :each_entry, :each_slice,
    :each_with_index, :each_with_object, :entries, :find, :find_all, 
    :find_index, :first, :flat_map, :grep, :group_by, :include?, :inject, 
    :map, :max, :max_by, :member?, :min, :min_by, :minmax, :minmax_by, 
    :none?, :one?, :partition, :reduce, :reject, :reverse_each, :select, 
    :slice_before, :sort, :sort_by, :take, :take_while, :to_a, :zip]
```

### 搜索

Enumerable 提供了几个过滤集合的函数。您可能经常看到的是“ect”系列方法。

```
>> [1,2,3,4,5].select { |i| i > 3 }
=> [4,5]

>> [1,2,3,4,5].detect { |i| i > 3 }
=> 4

>> [1,2,3,4,5].reject { |i| i > 3 }
=> [1,2,3]
```

`#find_all`和`#find`方法执行相同的操作。

`Enumerable#grep`提供执行一般搜索的能力。这是`#select`和`===`操作符的快捷方式。

Threequals ( `===`)是一个奇怪但相当有用的运算符。它不是用来建立绝对意义上的平等，而是一种更普遍的意义。

```
>> (1..3) === 2
=> true

>> (1..3) === 4
=> false

>> ('a'..'c') === 'b'
=> true

>> /el/ === "Hello World"
=> true

>> Object === Array
=> true
```

使用`===`时，更一般的对象(即 Range、Regexp)放在运算符的左边，具体对象放在右边。反之则不行，因为 threequals 通常是单向覆盖的。`Range#===`知道如何处理 Fixnums，但是`Fixnum#===`不知道如何处理范围。

```
>> 2 === (1..3)
=> false
```

如果你想用`select`做一个非常全面的搜索，你可以使用 threequals。

```
>> [:hello_world, "Jello World", 3].select { |i| /.ello..orld/ === i }
=> [:hello_world, "Jello World"]
```

`Enumerable#grep`是这个的包装器。

```
>> [6, 14, 28, 47, 12].grep(5..15)
=> [6, 14, 12]

>> [0.3, "three", :three, "thirty-three"].grep /three/
=> ["three", :three, "thirty-three"]
```

看看如果在异构阵列上尝试`#map`操作会发生什么。

```
>> ['a', 1, 2, 'b'].map(&:upcase)
=> NoMethodError: undefined method `upcase' for 1:Fixnum
```

我们可以使用`#grep`来过滤集合中接受我们方法的元素。

```
>> ['a', 1, 2, 'b'].grep(String, &:upcase)
=> ["A", "B"]
```

### 整理

假设你有一个由整数和字符串组成的数组。如果你试图使用`#sort`，它将会失败。

```
>> [1, "5", 2, "7", "3"].sort
ArgumentError: comparison of Fixnum with String failed
```

因为字符串不能保证是带引号的数字，所以 Ruby 在尝试排序之前不会尝试隐式地将字符串转换成合适的数字。我们可以通过告诉如何排序元素来解决这个问题。

```
>> [1, "5", 2, "7", "3"].sort_by { |a| a.to_i }
=> [1, 2, "3", "5", "7"]
```

也可以用稍微短一点的版本。

```
>> [1, "5", 2, "7", "3"].sort_by(&:to_i)
=> [1, 2, "3", "5", "7"]
```

现在，如果你排序的东西不是数字或者不容易转换成数字呢？这个方法并不神奇。它检查组合比较运算符方法的结果(`#<=>`)。

`#<=>`方法是这样工作的:

```
>> 1 <=> 2
=> -1

>> 2 <=> 1
=> 1

>> 1 <=> 1
=> 0
```

如果你想让一个任意的类可排序，你只需要定义它。继续创建一个新类，看看当你尝试排序时会发生什么。

```
>> class McGuffin
>>   attr_accessor :absurdity 
>>   def initialize(a)
>>     @absurdity = a
>>   end
>> end

>> m1 = McGuffin.new(0.1)
>> m2 = McGuffin.new(2.3)
>> [m1, m2].sort
=> ArgumentError: comparison of McGuffin with McGuffin failed
```

它会产生一个错误，因为 Ruby 不知道如何比较这个新类的成员。这很容易通过定义`#<=>`来解决。

```
>> class McGuffin
>>   def <=>(other)
>>     @absurdity <=> other.absurdity
>>   end
>> end

>> [m1, m2].sort
=> [#>McGuffin:0x00000000de0238 @absurdity=0.1>, 
    #>McGuffin:0x00000000de50f8 @absurdity=2.3>]
```

注意`#<=>`不是其他比较操作符的替代品。如果需要，您需要单独定义它们。

```
>> m1 > m2
=> NoMethodError: undefined method `>'...
```

### 有吗？所有的呢？

对于集合中的任何元素，如果其块为 true，则返回 true。`Enumerable#all?`如果它的块对于每个元素都为真，则返回真。

```
>> [2,4,6,8].all?(&:even?)
=> true 

>> [2,4,5,8].any? { |i| i % 2 == 0 }
=> true

>> [2,4,5,8].all? { |i| i % 2 == 0 }
=> false
```

### 谁需要 Excel？

获取一个集合，并将其简化为一个元素。它对每个元素应用一个操作，维护一个运行的“总计”

例如，`#reduce`可以用来对集合求和。

```
>> [1,2,3].reduce(:+)
=> 6
```

Ruby 没有附带阶乘方法。然而，多亏了`#reduce`,很容易拼凑出一个漂亮的黑客。

```
>> class Integer
>>   def factorial
>>     (1..self).reduce(:*) || 1
>>   end
>> end

>> 6.factorial
=> 720
>> 0.factorial
=> 1
```

众所周知难以理解。到目前为止，我通过让`#reduce`的蓄电池在看不见的地方工作来保持事情的简单。但是现在让我们把它公开。

```
>> [1,2,3].reduce(0) { |sum, element| sum + element }
=> 6
```

喔，喔，喔。我刚才加的那些东西是什么？传递给`#reduce`的参数是什么意思？

对比这三个`#reduce`调用。

```
[1,2,3].reduce do |accumulator, current|
  puts "Accumulator: #{accumulator}, Current: #{current}"
  accumulator + current
end

Accumulator: 1, Current: 2
Accumulator: 3, Current: 3

=> 6

[1,2,3].reduce(0) do |accumulator, current|
  puts "Accumulator: #{accumulator}, Current: #{current}"
  accumulator + current
end

Accumulator: 0, Current: 1
Accumulator: 1, Current: 2
Accumulator: 3, Current: 3

=> 6

[1,2,3].reduce(1) do |accumulator, current|
  puts "Accumulator: #{accumulator}, Current: #{current}"
  accumulator + current
end

Accumulator: 1, Current: 1
Accumulator: 2, Current: 2
Accumulator: 4, Current: 3

=> 7
```

我认为在这种情况下最令人困惑的是传递 0 和什么都没有的区别。

*   `#reduce`–电流开始作为第二个元素。累加器作为第一个元素开始。

*   `#reduce(x)`–电流作为第一个元素开始。累加器以 x 开始。

这有什么用？ruby-doc.org 使用的例子是寻找集合中最长的单词。

```
>> words = %w{cool bobsled Jamaican}
>> longest = words.reduce do |memo, word|
>>   memo.length > word.length ? memo : word
>> end

=> "Jamaican"
```

最初，`#reduce`被称为`#inject`，在现代 Ruby 中你可以使用其中任何一个。然而，我个人更喜欢`#reduce`，因为我发现将一个系列“注入”到单一元素的想法令人困惑。

## 无限迭代

对集合进行任意次甚至无限次的迭代通常是很有用的。一种简单的方法是跟踪一个计数器索引，并在每次达到集合的大小时重置它——1(当集合像 Ruby 中一样是零索引时)。

更好的解决方案是使用 incrementer 和 mod ( `%`)集合的大小来获取每个索引。假设您有 3 个产品 id，并且您需要执行 10 个迭代步骤。

```
>> arr = ["first", "middle", "last"]
>> 10.times { |i| puts arr[i % arr.size] }
first
middle
last
first
middle
last
first
middle
last
first
```

Ruby 用`Enumerable#cycle`提供了一种稍微干净的方式。

```
>> arr.cycle(2) { |i| puts i }
first
middle
last
first
middle
last
```

将一个参数传递给 cycle 将遍历集合那么多次。如果没有传递参数，它将无限迭代(产生一个无限循环)。

以这种方式骑自行车有几个问题:

*   `#cycle`的参数指定循环的次数，而不是循环的元素数
*   如果你想无限循环，你所有的相关逻辑必须进入`#cycle`的块，因为代码永远不会离开

幸运的是，这两个问题都可以通过使用枚举器对象来解决。

## 人口普查员

如果没有向`#cycle`传递一个块，它将返回一个枚举数。

```
>> cycle_enum = arr.cycle
=> #>Enumerator: ["first", "middle", "last"]:cycle>
```

现在，`Enumerator#next`可以根据需要多次检索下一个元素。

```
>> cycle_enum.next
=> "first" 
>> cycle_enum.next
=> "middle" 
>> cycle_enum.next
=> "last" 
>> cycle_enum.next
=> "first"
```

这是可行的，因为枚举器专门来自`#cycle`。观察当使用常规的`#each`枚举器时会发生什么。

```
>> each_enum = arr.each
>> each_enum.next
=> "first"
>> each_enum.next
=> "middle"
>> each_enum.next
=> "last"
>> each_enum.next
=> StopIteration: iteration reached an end
```

注意:不能只把`#next`用在`#cycle`和`#each`上。

```
>> arr.cycle.next
=> "first"
>> arr.cycle.next
=> "first"
>> arr.cycle.next
=> "first"
```

这是因为迭代器方法每次都返回一个新的枚举数。

```
>> arr.cycle.object_id == arr.cycle.object_id
=> false
```

描述枚举器对象的一个好方法是，它们包含有关如何循环访问集合的信息。例如，`#cycle`枚举器知道如何遍历集合一次或多次，而`#reverse_each`枚举器知道如何向后遍历集合。

这有什么用？

好吧，假设你想在一个集合中向后循环。你会用`#reverse_cycle`，对吗？

```
>> [:first, :middle, :last].reverse_cycle(2) { |i| puts i }
NoMethodError: undefined method `reverse_cycle'...
```

废话！可枚举中没有`#reverse_cycle`！我们告诉女老板，今天下午我们会倒着骑自行车。随着经济和所有…

但是等等。也许并不是所有的希望都破灭了。拿`#reverse_each`…然后在那上面打`#cycle`怎么样？

```
>> [:first, :middle, :last].reverse_each.cycle(2) { |i| puts i } 
last
middle
first
last
middle
first
```

链接:这就是你可以用枚举器做的事情。想要在一个集合中向后循环，并将结果放入一个数组中吗？只需将`#map`添加到链中:

```
>> [:first, :middle, :last].reverse_each.cycle(2).map { |i| i }
=> [:last, :middle, :first, :last, :middle, :first]
```

## 结论

这包括枚举器和可枚举的。在我的收藏系列的下一期(也是最后一期)中，我将介绍一些我最喜欢的技巧和诀窍。

## 分享这篇文章