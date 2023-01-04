# Ruby 函数式编程技术:第一部分

> 原文：<https://www.sitepoint.com/functional-programming-techniques-with-ruby-part-i/>

Ruby 是一种有趣的语言，因为它支持多种范例的使用。其中之一是“功能范式”。

## 函数式语言的特征

以函数式风格使用语言意味着您可以使用一些关键特性:

*   **不可变值**:一旦“变量”被设定，就不能更改。在 Ruby 中，这意味着你必须像对待常量一样对待变量。
*   **无副作用**:当传递给一个给定值时，函数必须总是返回相同的结果。这与拥有不可变的值密切相关；一个函数永远不能接受一个值并改变它，因为这会导致一个与返回结果无关的副作用。
*   **高阶函数**:这些函数允许函数作为参数，或者使用函数作为返回值。可以说，这是任何函数式语言最重要的特性之一。
*   **Currying** :由高阶函数启用，Currying 是将一个接受多个参数的函数转换成一个接受一个参数的函数。这与部分函数应用程序密切相关，部分函数应用程序将一个多参数函数转换成一个比原来使用的参数更少的函数。
*   **递归**:从函数内部调用函数进行循环。当你不能访问可变数据时，递归被用来建立和链接数据结构。这是因为循环不是一个函数概念，因为它需要传递变量来存储给定时间的循环状态。
*   **延迟求值，或延迟求值**:将值的处理延迟到实际需要的时候。例如，如果您有一些代码生成了启用了延迟求值的斐波那契数列，那么实际上不会处理和计算它，直到结果中的一个值被另一个函数(比如`puts`)所需要。

## 使用 Ruby 作为函数式语言

在本系列中，我们将逐一介绍这些特性，并看看如何使用它们来生成更干净、更高效、结构更好的 Ruby 代码。

我应该提前指出一个警告:不推荐使用我们将在产品代码中涉及的一些技术。我会尽可能清楚地说明这些是什么特性，为什么不好。然而，事实是，Ruby 本质上是一种面向对象世界的面向对象语言。用 Ruby 可以很容易地编写函数式代码，但是由于没有把重点放在范型上，它最容易受到性能问题的困扰。

稍后会有更多的介绍。现在，让我们谈谈手头的话题:不变性。

## 不变性和无副作用的代码

首先，让我们探索一下不可变性和无副作用的代码。纯函数式语言不允许可变数据，因此，函数除了接受参数并使用这些参数生成结果之外，什么也做不了。

## 什么是无副作用代码，为什么它对我有好处？

函数式编程是一种范式，主要基于编程应该遵循数学规则的原则。让我们采用以下有效的命令式编程范例表达式:

```
x = x + 1
```

如果我们将数学规则应用于此，我们应该能够重新平衡等式:

```
x - x = 1
∴ 0 = 1 (!?)
```

哇，那里发生了什么？！显然，这是行不通的。命令式编程风格与函数式编程的数学基础不匹配。数学函数是建立在不变性的概念上的；也就是说，不能像我们在上面的命令式语句中所做的那样重新定义值。我们可以通过使用一个新的常数来表示更新后的值来解决这个问题:

```
x = 1
y = x + 1
```

这在两种范式中都完全成立:

```
y = x + 1
∴ x = y - 1
```

函数式编程范式的基础是数学，其核心就是使用这一基本思想。当使用 Ruby 进行函数式编程时，如果你想避免副作用，你应该努力不要重定义变量；也就是说，把变量当成常量。这种方法有许多好处，其中一些重要的好处是:

*   函数或方法变得易于单元测试。这是因为，如果它们从不改变数据，而只是提供副本或新对象作为返回值，那么相同的输入就一定会产生相同的输出。因此，该代码可以说是“无副作用的”。
*   你的代码变得非常简洁易读。由于您必须为您需要做的每一个变异创建一个新的变量，乍一看，特定方法的可伸缩性和不足之处是显而易见的。纯函数式语言经常宣扬一种简单的功能，即推理给定代码块的性能。
*   链接变得非常容易，主要是因为每个函数返回的都是新的、不变的值，可以放心地传递给下一个函数，也可以放心地丢弃。
*   你的函数将是自由域的；您可以用无数种方式应用它们，因为它们内部没有隐藏状态特定的逻辑。

## 编写无副作用的代码

### 版本号解析

2010 年，Yahuda Katz 在 Twitter 上发布了一个简单而有趣的问题:

> 问题:在 Ruby 中从“X::Y::Z”中获取[“X::Y::Z”、“X::Y”、“X”]最简单的方法是什么？

当然，这个问题有几十种解决方案，但是用函数式的方式解决这个问题对 Ruby Quicktips Tumblr <sup>2</sup> 来说是一个无法抗拒的诱惑:

```
def module_split(module_path, separator = "::")
  modules = module_path.split(separator)
  modules.length.downto(1).map { |n| modules.first(n).join(separator) }
end

module_split("W::X::Y::Z")
```

以没有副作用的函数式风格编写这个方法有很多好处:

*   易于用单元测试来测试。
*   使用尽可能多的核心 Ruby 方法，减少错误蔓延和重复发明的可能性。
*   相当快。尽可能使用内存化，并在其他地方优化 Ruby 核心方法。
*   干净、易读、易懂；它实际上读起来像简单的英语。

### 使用可链接方法的流畅 DSL

使用函数式风格的一个主要好处是它通常使链接变得很容易。在这个例子中，我们将尝试构建一个 DSL 来构建一个 CSS 代码块；我们将构建一个选择器，以及用来设置样式的属性和值。

因此，我们需要一个类，有两个方法:一个用于添加属性，一个用于设置选择器。

```
class CssBlock
  # We'll add some methods in here.
end
```

首先，我们将在初始化器中将选择器设置为强制属性。我们还将设置 properties 属性为空的`Hash`:

```
class CssBlock
  attr_reader :selector, :properties

  def initialize(selector, properties = {})
    @selector = selector.dup.freeze
    @properties = properties.dup.freeze
  end
end
```

注意，我们对两个属性都使用了一个`attr_reader`,我们复制并冻结了值；现在我们可以保证传入的值不能被修改。

接下来，我们创建代码向块中添加一些属性:

```
class CssBlock

  # ...

  def set(key, value = nil)
    new_properties = if key.is_a?(Hash)
      key
    elsif !value.nil?
      {
        key => value
      }
    else
      raise "Either provide a Hash of values, or a key and value."
    end

    self.class.new(self.selector, self.properties.merge(new_properties))
  end

end
```

这段代码的关键要点是:

*   我们接受一个属性的`Hash`来合并，或者一个键/值对来添加到已经存在的属性中。
*   我们返回一个`CssBlock`类的新实例，将属性设置为现有属性和新属性的合并结果。
*   如果他们没有给我们正确的项目格式，我们会提出一个错误。
*   我们不添加、编辑或修改我们正在处理的对象的副本上存储的任何状态。一旦对象被实例化，就这样了；它像以前一样固定。

现在，让我们添加一个方法来序列化这个类的内容，这基本上就是我们定义了`#to_s`方法:

```
class CssBlock

  # ...

  def to_s
    serialised_properties = self.properties.inject([]) do |acc, (k, v)|
      acc + ["#{k}: #{v}"]
    end

    "#{self.selector} { #{serialised_properties.join("; ") } }"
  end
end
```

我们使用`#inject`来构建一个字符串化属性的数组。这里一个绝对重要的小细节是，我们不修改块中的`acc`变量。作为大多数函数式编程语言中的`fold/foldr`函数的 Ruby 等价物，`#inject`是以函数方式构建对象的主要方法，如图所示。

现在来测试一下:

```
CssBlock.new("#some_id .class a").set("color", "#FFF").set({ "color" => "#000", "text-decoration" => "underline"}) # => "#some_id .class a { color: #000; text-decoration: underline }"
```

万岁，成功了！不仅如此，它超级简单，易于测试，并且应该证明非常容易使用或根据需要进行扩展。

为了方便起见，下面是完整的源代码:

```
class CssBlock
  attr_reader :selector, :properties

  def initialize(selector, properties = {})
    @selector = selector.dup.freeze
    @properties = properties.dup.freeze
  end

  def set(key, value = nil)
    new_properties = if key.is_a?(Hash)
        key
      elsif !value.nil?
        {
          key => value
        }
      else
        raise "Either provide a Hash of values, or a key and value."
      end

    self.class.new(self.selector, self.properties.merge(new_properties))
  end

  def to_s
    serialised_properties = self.properties.inject([]) do |acc, (k, v)|
      acc + ["#{k}: #{v}"]
    end

    "#{self.selector} { #{serialised_properties.join("; ") } }"
  end
end
```

## Ruby 中无副作用代码的注意事项

试图用 Ruby 编写无副作用的代码的现实是，在这个阶段，除了实验之外，它实际上没什么用处。事实的真相是，Ruby 在处理完全不可变的数据时有几个主要问题:

*   冻结对象不能自动化。您可以通过对类上的`#new_method`打补丁来调用原始`#new method`的返回值上的 freeze，但是当某些核心类型被实例化时(Hash、String、Array 等),该方法不会被调用。).这意味着如果你想让对象变成不可变的，你必须手动冻结它们；如果你不这样做，你就不能保证你真的在编写没有副作用的代码。
*   在 Ruby 中创建对象的高昂成本意味着使用函数式编程风格对于任何与性能相关的目标来说都是代价高昂的。希望这个问题会随着 Ruby 实现对更快的解释器的需求而逐渐消失。
*   函数式编程的一个主要优点，即更简单的并发性，在 Ruby 中很难实现，因为存在 GIL 和一个幼稚的线程实现。在 JRuby 和 Rubinius 等替代的 Ruby 实现中，这不是一个大问题。尽管 Ruby 1.9 已经在使用纤程等更好的线程模型方面取得了很大的进步，但它仍然比在专门构建的函数式语言中更难充分探索这一领域的好处，因为在 CRuby 中仍然没有办法拥有适当的并行线程。

虽然这些技术在生产代码中不一定有用，但是在设计健壮的对象模型时，记住它们是非常有用的。

## 下次…

我们将讨论高阶函数，以及它们如何支持 currying，这是一种非常有趣且非常有用的编程方法。

<sup>1</sup>:[http://twitter.com/wycats/status/9042964562](http://twitter.com/wycats/status/9042964562)
2:[http://rubyquicktips . com/post/1018776470/arminging-functional-programming](http://rubyquicktips.com/post/1018776470/embracing-functional-programming)

## 分享这篇文章