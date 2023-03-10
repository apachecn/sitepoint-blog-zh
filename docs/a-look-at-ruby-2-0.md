# 看看 Ruby 2.0

> 原文：<https://www.sitepoint.com/a-look-at-ruby-2-0/>

Ruby 2.0 将于 2 月 24 日发布，正好是 Ruby 首次亮相的 20 周年纪念日，所以我决定写这篇文章，向你简要介绍一些最有趣的变化。如果您想在正式发布之前试用这个版本，可以按照本文中的说明来做。

## 安装 RC1

Ruby 2.0 已经弃用 *[syck](http://www.ruby-doc.org/stdlib-1.9.3/libdoc/syck/rdoc/Syck.html)* 而青睐 *[psych](https://github.com/tenderlove/psych)* ，YAML 现在完全依赖 *[libyaml](http://pyyaml.org/wiki/LibYAML)* 。这意味着我们**必须**在安装 Ruby 2.0 之前安装这个库。

在任何基于*nix 的系统上，我们可以通过下载源代码包并手动构建来安装它:

```
$ wget http://pyyaml.org/download/libyaml/yaml-0.1.4.tar.gz
$ tar xzvf yaml-0.1.4.tar.gz
$ cd yaml-0.1.4
$ ./configure
$ make
$ make install
```

或者你在 Mac 上，可以用*家酿*安装:

```
$ brew update
$ brew install libyaml
```

一旦安装了 *libyaml* ，我们就可以继续使用 *[rvm](https://rvm.io/)* 安装 RC1 了:

```
$ rvm install ruby-2.0.0-rc1
```

接下来，我们需要告诉 *rvm* 使用这个新的 Ruby 版本:

```
$ rvm use ruby-2.0.0-rc1
```

太好了，我们现在准备好开始改变了。

## 变化

2.0 中引入的变化[相当广泛](https://github.com/ruby/ruby/blob/v2_0_0_rc1/NEWS)，但在本文中，我将主要关注以下内容:

*   风雅
*   关键字参数
*   模块#前置
*   可枚举#lazy
*   语言变化

### 改进(实验性)

通过提供一种更好、更安全、更隔离的方式来修补代码，优化将取代不安全的猴子修补。传统上，当应用补丁时，它会对对象进行全局修改——无论您喜欢与否。通过改进，您可以将猴子修补限制在特定范围内。

让我们看一个具体的猴子补丁的例子。假设我们想要扩展 String 类并添加一个 *bang* 方法，该方法在给定的字符串后添加一个感叹号。我们会这样做:

```
class String
  def bang
    "#{self}!"
  end
end
```

这种变化现在是全球性的。这意味着任何调用`.bang`的字符串都将如此:

```
> "hello".bang
#=> "hello!"
```

为了防止这种全局范围的变化，提出了*精化*。它利用两种新方法工作:`Module#refine`和`main.using`。第一个是一个块，它允许局部范围的 monkey 修补。后者将细化导入到当前文件或 eval 字符串中，以便可以在其他地方使用。

以我们之前的例子为例，这就是我们如何使用精化来安全地扩展*字符串*类:

```
module StringBang
  refine String do
    def bang
      "#{self}!"
    end
  end
end
```

现在，如果我们试图在任何字符串上调用`.bang`,它都会失败:

```
> "hello".bang
#=> NoMethodError: undefined method `bang' for "":String
```

这是因为对*字符串*类的更改包含在*字符串*模块中。在我们用关键字`using`导入这个精化之后，它会像预期的那样工作:

```
> using StringBang
> "hello".bang
#=> "hello!"
```

**警告:**这个特性仍然是实验性的，在 Ruby 的未来版本中可能会有所改变。JRuby 的 Charles Nutter 对由此带来的挑战有一个[的精彩解释](http://blog.headius.com/2012/11/refining-ruby.html)。

### 关键字参数

这个特性也称为*命名参数*，非常有用，因为它允许声明一个方法来接收关键字参数。这是很多语言都在用的，最后集成到 Ruby 中。

在以前的方法中，如果您想要接受关键字参数，您必须通过在方法中接收散列参数来伪装它:

```
def config(opts={}); end
```

如果您有默认值，那么您应该在默认值的基础上合并用户提供的参数:

```
def config(opts={})
  defaults = {enabled: true, timeout: 300}
  opts = defaults.merge(opts)
end
```

它确实工作，但它是一个黑客，我相信我们都以这样或那样的方式使用它。

忘记这种老方法，向 Ruby 2.0 中的 ***true*** 关键字参数问好。使用与上面相同的例子，下面是我们如何以新的方式定义我们的`config`方法:

```
def config(enabled: true, timeout: 300)
  [enabled, timeout]
end
```

现在，让我们看看调用该方法的不同方式:

```
> config() #no args
#=> [true, 300]

> config(enabled: false) #only enabled
#=> [false, 300]

> config(timeout: 20) #only timeout
#=> [true, 20]

> config(timeout: 10, enabled: false) #inverse order
#=> [false, 10]
```

进一步扩展`config`方法，我们现在将接受两个额外的参数:`value`，一个必需的值；和`other`，一个可选的散列。

```
def config(value, enabled: true, timeout: 300, **other)
  [value, enabled, timeout, **other]
end
```

这是我们可以用这种方法进行交互的不同之处:

```
> config() #no args
#=> ArgumentError: wrong number of arguments (0 for 1)

> config(1) #required value
#=> [1, true, 300, {}]

> config(1, other: false) #required value, optional hash
#=> [1, true, 300, {:other=>false}]

> config(1, timeout: 10) #required value, timeout
#=> [1, true, 10, {}]

> config(1, timeout: 10, other: false) #required value, timeout, optional hash
#=> [1, true, 10, {:other=>false}]

> config(1, other: false, another: true, timeout: 10, enabled: false) #inverse order
#=> [1, false, 10, {:other=>false, :another=>true}]
```

### 模块#前置

这与`Module#include`类似，只是它被设置为前置模块的 *( [源](http://www.ruby-doc.org/core-2.0/Module.html#method-i-prepend) )* “覆盖常量、方法和模块变量”。为了更好地理解这个概念，让我们看看`Module#include`目前是如何工作的:

```
module Foo
  def baz
    'foo-baz'
  end
end

class Bar
  include Foo

  def baz
    'bar-baz'
  end
end
```

这里我们声明一个模块和一个类，每个都包含一个对`baz`方法的声明。当我们调用*栏*类中的`baz`方法时，在 *Foo* 模块中声明的方法被忽略:

```
> Bar.new.baz
#=> "bar-baz"
```

用`Module#prepend`，是逆；模块**中的声明覆盖了**类中的声明。重写上面的例子以使用`Module#prepend`，下面是新的代码:

```
module Foo
  def baz
    'foo-baz'
  end
end

class Bar
  prepend Foo

  def baz
    'bar-baz'
  end
end
```

当调用*栏*类中的`baz`方法时， *Foo* 模块中的方法是实际被调用的方法:

```
> Bar.new.baz
#=> "foo-baz"
```

### 可枚举#lazy

[直接从文档中引用](http://ruby-doc.org/core-2.0/Enumerable.html#method-i-lazy):

> 返回一个惰性枚举器，其方法 map/collect、flat *map/collect* concat、select/find *all、reject、grep、zip、take、#take* while、drop、#drop_while 和 cycle 仅根据需要枚举值。但是，如果给 zip 或 cycle 一个块，那么值会立即被枚举。"

现在我们已经了解了它的概念，让我们看一个例子:

```
> ary = [1,2,3,4,5].select{|n| n > 2}
#=> [3, 4, 5]

> ary = [1,2,3,4,5].lazy.select{|n| n > 2}
#=> #:select>
> ary.force
#=> [3, 4, 5]
```

在第一部分中，没有使用`lazy`方法，一个新的数组在`select`方法完成评估后立即返回。在第二部分中，当使用`lazy`方法时，返回一个惰性枚举器，直到我们调用`force`(或`to_a`)时代码才被求值。

### 语言变化

您现在可以使用 **%i** 和 **%I** 来创建符号列表:

```
> %i{this is a list of symbols}
#=> [:this, :is, :a, :list, :of, :symbols]
```

### 结论

本文介绍了 Ruby 2.0 中一些最受关注的变化，我个人已经迫不及待地想看到正式版本了。

如果你还在使用 Ruby 1.8x，强烈建议尽快升级到 1.9.3，因为它很快就会被弃用，不再维护。至于 Ruby 2.0 和 1.9.3 的兼容性，据说是完全兼容的。

### 资源

*   [rvm](https://rvm.io/)
*   公司自产自用
*   [西克](http://www.ruby-doc.org/stdlib-1.9.3/libdoc/syck/rdoc/Syck.html)
*   [心理](https://github.com/tenderlove/psych)
*   [libyaml](http://pyyaml.org/wiki/LibYAML)
*   [改进](https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/RefinementsSpec)
*   [模块#前置](http://www.ruby-doc.org/core-2.0/Module.html#method-i-prepend)
*   [可枚举#懒惰](http://ruby-doc.org/core-2.0/Enumerable.html#method-i-lazy)

## 分享这篇文章