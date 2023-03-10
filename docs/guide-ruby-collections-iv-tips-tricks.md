# Ruby 收藏指南 IV:技巧和窍门

> 原文：<https://www.sitepoint.com/guide-ruby-collections-iv-tips-tricks/>

![collections_tips](img/100558ea740e30ad762f3eec1bb2e444.png)

这是 Ruby 收藏指南系列的最后一篇文章。最后，我想提供一些杂七杂八的技巧，希望对您的 Ruby 之旅有所帮助。

### 更详细的阵列创建

在[对数组](https://www.sitepoint.com/guide-ruby-collections-part-arrays/)的介绍中，我向您展示了您可以通过向`Array.new`传递第二个参数来用默认值填充数组

```
>> Array.new(3, 1)
=> [1,1,1]
```

所以如果我们想用随机数初始化一个数组，我们可以直接传入`rand(x)`来代替，对吗？

```
>> Array.new(3, rand(100))
=> [28,28,28]
```

嗯。这不完全是随机的。看起来`rand(100)`只被评估了一次。幸运的是，`Array#new`可以接受一个块，它将为每个元素运行这个块，并将结果赋给元素。

```
>> Array.new(3) { rand(100) }
=> [10,53,27]
```

### 批量分配

与 Python 类似，Ruby 允许在同一个语句中为多个变量或数组中的多个索引赋值。

```
>> letter1, letter2 = ["a", "b"]
=> ["a", "b"]
>> letter1
=> "a"
>> letter2
=> "b"

>> transportation = []
>> transportation[0..1] = ["trains", "planes"]
>> transportation[0]
=> "trains"
>> transportation[1]
=> "planes"
```

### 边界检查

默认情况下，Ruby 数组在收到无效索引时返回 nil。

```
>> [1,2,3][99]
=> nil
```

如果您不喜欢这种行为(也许您的数组中有很多 nil)，您可以在 Array 类中重新定义`#[]`。

```
>> class Array
>>   def [](idx)
>>     self.fetch(idx)
>>   end
>> end

>> [1,2,3][99]
=> IndexError: index 99 outside of array bounds: -3...3
```

**！！警告！！**:摆弄 Ruby 的核心库可能会导致很难追踪的错误。事实上，即使是前面的例子也破坏了 Array 接受多个参数的能力，并且似乎会导致 irb 出错。如果您不想研究如何在不引起问题的情况下重新定义数组方法，一个更安全的替代方法是创建 Array 的子类。

```
>> class BoundsCheckedArray < Array
>>   def [](idx)
>>     self.fetch(idx)
>>   end
>> end

>> arr = BoundsCheckedArray.new([1,2,3])
>> arr[99]
=> IndexError: index 99 outside of array bounds: -3...3
```

### 随机数组元素

我们已经看到了如何在 Ruby 中创建随机数，但是其他的呢？也许你的另一半最近实施了这样一种方法:

```
>> def random_restaurant
>>   "I don't care. Where do you want to go?"
>> end

>> 3.times.map { random_restaurant }
=> ["I don't care. Where do you want to go", "I don't care. Where do you want to go", "I don't care. Where do you want to go"]
```

忍住沮丧的泪水，你可以将附近的餐馆放入一个数组，并使用 [#sample](http://www.ruby-doc.org/core-2.0.0/Array.html#method-i-sample) 方法随机获得一个。

```
>> def random_restaurant
>>   ["Process: The Forkeria", "Shenanigans", "Grease Factory"].sample
>> end

>> 3.times.map { random_restaurant }
=> ["Process: The Forkeria", "Grease Factory", "Process: The Forkeria"]
```

如果你调用两次`#sample`，你就有可能从两次调用中得到相同的元素。如果需要多个唯一的随机元素，可以将元素的数量传递给#sample。

```
>> ["a","b","c"].sample(2)
=> ["c", "a"]
```

### 多维高性能阵列

与它的表亲 Python 不同，Ruby 非常擅长让你以直观的方式声明多维数组。

```
>> two_d = [[1,2,3],[4,5,6],[7,8,9]] 
>> two_d[1][1]
=> 5
```

不幸的是，当执行昂贵的数学运算时，人们通常不会使用 Ruby。在这种情况下，弃船学习数字往往容易得多

然而，一名日本研究人员已经建造了提供快速向量和矩阵的 [NArray](http://narray.rubyforge.org/) gem。不幸的是，速度的代价是你被限制在同质的数字集合中。

```
$ gem install narray --version 0.6.0.8
```

用 NArray 创建 n 维数组真的很容易。NArray 构造函数的每个参数都用作维度的大小。

```
>> require 'narray'
>> NArray.int(2)
=> NArray.int(2): 
   [ 0, 0 ] 

>> NArray.int(2,2)
=> NArray.int(2,2): 
   [ [ 0, 0 ], 
     [ 0, 0 ] ] 

>> NArray.int(2,3)
=> NArray.int(2,3): 
   [ [ 0, 0 ], 
     [ 0, 0 ], 
     [ 0, 0 ] ] 

>> NArray.int(1,2)
=> NArray.int(1,2): 
   [ [ 0 ], 
     [ 0 ] ]
```

NArray 使用数学化的[x，y]符号代替[x][y]符号来指定索引。

```
>> arr = NArray.int(3,3)
>> arr[1,1] = 7
>> arr
=> NArray.int(3,3): 
   [ [ 0, 0, 0 ], 
     [ 0, 7, 0 ], 
     [ 0, 0, 0 ] ]
```

这与数组不兼容。`arr[x][y]`不起作用。

```
>> arr[1][1]
=> 0
>> arr[1,1]
=> 7
```

NArray gem 不限于整数或 NArray 对象。NArray 的英文文档可以在[这里](http://narray.rubyforge.org/SPEC.en)找到

## 标杆管理

有了看似无限多的访问和操作 Ruby 集合的方法，您可能会疑惑如何确定您正在使用的方法是否有效。例如，用 Set 执行成员测试真的比用 Array 快吗？

Ruby 为计时代码提供了基准。

```
>> require "benchmark"
>> require "set"

>> array = ["a", -3.14, 0, []]
>> set = array.to_set

>> Benchmark.bm do |bench|
>>   bench.report("array:") do
>>     1000.times { array.include? -3.14 }
>>   end
>>   bench.report("set:") do
>>     1000.times { set.include? -3.14 }
>>   end
>> end

      user     system      total        real
array:  0.000000   0.000000   0.000000 (  0.000200)
set:    0.000000   0.000000   0.000000 (  0.000212)
```

尽管很难称之为完全的比较，但对于我的 Ruby 实现来说，这种差异似乎微不足道(1.9.3-p448)。

# 结论

好了，我的红宝石收藏系列到此结束。如果您想继续学习，这里有一些额外的资源:

*   [红宝石的私生子之书——收藏](http://ruby.bastardsbook.com/chapters/collections/)
*   [ruby-doc.org–容器块和迭代器](http://www.ruby-doc.org/docs/ProgrammingRuby/html/tut_containers.html)
*   [Ruby 的缺失数据结构](https://www.sitepoint.com/rubys-missing-data-structure/)
*   Ruby 中的 Grep 一个强大的可枚举方法
*   Ruby 的线程安全集合类
*   [了解地图并减少](http://railspikes.com/2008/8/11/understanding-map-and-reduce)

## 分享这篇文章