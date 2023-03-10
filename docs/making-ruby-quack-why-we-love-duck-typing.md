# 让 Ruby 嘎嘎叫——为什么我们喜欢鸭子打字

> 原文：<https://www.sitepoint.com/making-ruby-quack-why-we-love-duck-typing/>

Ruby 最受称赞的特性之一是它对称为 [duck typing](http://en.wikipedia.org/wiki/Duck_typing) 的技术的支持。尽管名字很幽默，但 duck typing 使 Ruby 开发人员能够毫不费力地编写出简洁、干净、可读的代码——所有这些都是 Ruby 最初吸引我们的地方。

## “鸭道奇会怎么做？”

鸭子打字的基本前提很简单。如果一个实体看起来像鸭子，走路像鸭子，呱呱叫也像鸭子，那么无论从哪方面来说，我们都可以假设他是在和一个物种的成员打交道。在实际的 Ruby 术语中，这意味着可以尝试在任何对象上调用任何方法，而不考虑它的类。

为了演示，这里有一个简单的场景:您正在使用一个跟踪船上每个集装箱的货运清单的系统。每个清单的数据很容易通过 RESTful web 服务获得:

```
# Fields:
# * package_count: number of packages in manifest (integer)
# * mass:          total mass of all packages, in kilograms (BigDecimal)
class ShippingManifest
  attr_accessor :package_count, :mass
end
```

每个`ShippingManifest`跟踪包裹的数量，以及所有包裹的总质量。您的任务是获取一组货单(在`Array`中提供),并给出该组中包裹的总质量和总数量的总和。

## "你说尼斯湖水怪住在你的按摩浴缸里？"

这个问题看起来很简单，对吗？我们也许可以用 9 到 10 行代码来完成它:

```
def summarise_manifests
  # Grab the latest set of Manifests (returns an Array)
  manifest_set = ShippingManifest.all

  # Initialize our counters
  total_package_count = 0
  total_mass          = BigDecimal("0")

  manifest_set.each do |m|
    total_package_count += m.package_count
    total_mass += m.mass
  end

  return [total_package_count, total_mass]
end
```

简单，但是冗长。可以将这个方法简化为一行代码:

```
def summarise_manifests
  ShippingManifest.all.sum
end
```

怎么会？鸭打字！

## “万能钥匙，呃？你从哪里学来的？”

我们的单行解决方案利用了 Ruby 最强大的特性之一: [`Enumerable`模块。](http://ruby-doc.org/core/classes/Enumerable.html) `Enumerable`提供了一个 API 来轻松操作对象集合——最常见的是`Array`和`Hash.`

具体来说，我们使用的是在`ActiveSupport` : [`sum`](http://api.rubyonrails.org/classes/Enumerable.html#method-i-sum) 中定义的方法，它在每个 Rails 项目中都可用。顾名思义，它用于计算一组对象的总和。这是通过使用 [`inject`](http://ruby-doc.org/core/classes/Enumerable.html#M001494) 来实现的，这个函数使用一个块来将一组对象减少到一个值。在`sum`的例子中，它通过使用加法运算符来实现这一点。

当我们谈论数字时，这非常有意义:`1 + 1 = 2`是基本算术。很容易看出它是如何将字符串连接在一起的:

```
['foo', 'bar', 'baz'].sum # => 'foobarbaz'
```

但是 Ruby 如何知道如何添加我们的`ShippingManifest`类的两个实例呢？

简单。我们告诉它如何。

## “那只是我的一部分。我其实挺复杂的。”

“一切都是对象”是 Ruby 的核心口号之一。这意味着(几乎)每个操作符实际上都是对一个对象实例的方法调用——这使我们能够为该操作符定义自己的实现。这意味着我们可以这样做:

```
class ShippingManifest < ActiveResource::Base
  # Returns a new instance of ShippingManifest containing the
  # sum of both `mass` and `package_count`
  def +(other)
    self.dup.tap do |neu|
      neu.mass          = @mass + other.mass
      neu.package_count = @package_count + other.package_count
    end
  end
end
```

通过添加几行简单的代码，我们添加了一些功能，使得更广泛的 Ruby 库可以更好地使用潜力`ShippingManifest`。

## "让我再一次提醒你们，伙计们，你们正在听真理或者——啊啊！"

为`+`操作符实现定制行为仅仅触及了 duck 类型化的皮毛。还有其他实现了自定义行为的操作符:`&`、`*`、`-`、 `Even `==`可以有自定义行为。需要对一组记录进行排序？实现比较运算符(`) in your classes and you’ll be able to take full advantage of the sorting methods provided in `Enumerable.

和往常一样，这篇文章的一些示例代码是在我们的 GitHub 帐户上提供的[——请随意使用它并进行实验！首先，尝试实现`-`和`, then experiment with the methods in `Enumerable.`的行为`](https://github.com/RubySource/duck-typing-example)

如果你对 Ruby 的其他方面有任何疑问，欢迎在评论中提问！

