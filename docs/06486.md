# ActiveSupport 帮助 Ruby 开发者的一些方法

> 原文：<https://www.sitepoint.com/ways-activesupport-helps-ruby-developers/>

Ruby 是一种面向对象的解释型语言，由 Yukihiro Matsumoto 于 1993 年发明，主要受 Perl 和 Lisp 的启发。从一开始，Ruby 就被设计成一种“使用的乐趣”——意味着对可读性和优雅性的强烈关注。

Ruby on Rails，通常简称为 Rails，是一个运行在 Ruby 编程语言上的开源 web 应用程序框架。Ruby on Rails 意在强调约定胜于配置(CoC)，以及不重复自己(DRY)的快速开发原则。Rails 是一个强大的框架，可以帮助您变得更加高效和自信。

Rails 使用各种宝石，其中许多本身就是框架，为您提供帮助编写干净、简洁代码的抽象概念。其中一个亮点是 ActiveSupport，它在 Rubygems.org 上将自己定义为:

> 从 Rails 框架中提取的支持库和 Ruby 核心扩展的工具包。对多字节字符串、国际化、时区和测试的丰富支持。

本质上，ActiveSupport 扩展了许多核心 Ruby 类，以提供 Rails 使用的语法糖。让我们看看这些抽象，看看它们如何帮助(和伤害)开发人员。

## Ruby 中的哈希

散列是唯一键及其值的类似字典的集合。也称为关联数组，类似于数组，但是数组使用整数作为索引，散列允许您使用任何对象类型。

哈希的示例:

```
#initialize an empty hash colors
colors = { }

#add a color with key as red
colors[:red] = "Red Color"

#add a color with key as blue
colors[:blue] = "Blue Color"

#display the hash
colors  #=> {:red=>"Red Color", :blue=>"Blue Color"}
```

特定键的值可以通过以下方式从哈希中获取:

```
Hash[key]    
or
Hash.fetch(:key)
```

如果我们想获取键`:red`的值，那么

```
colors[:red]  #=> "Red Color"
```

但是如果我们尝试`colors['red']`，我们将得到值为`nil`。

```
colors['red'] #=> nil
```

这是因为哈希对键中的符号和字符串有不同的处理。如果我们在散列中添加另一个键值，就可以进一步解释这一点。

```
colors['red'] = "Red color, here key is not a symbol"
```

现在，如果我们尝试获取键的值，它将返回我们的新值。

```
colors['red'] #=> "Red color, but key is not a symbol"
colors[:red] #=> "Red Color"
```

考虑一个例子，我们有两个散列，`a = {"red": "Red color"}`和`b = {:red => "Red color"}`。

如果我们合并这两个散列，像这样

```
a.merge! b   #=> {"red" => "Red color, :red => "RED COLOR"}
```

并尝试从 hash 中获取值

```
a['red']  #=> "Red color"
a[:red]   #=> "RED COLOR"
```

至少可以说，结果并不理想。

这有时会有点令人困惑，尤其是如果您使用过 Rails 的话。Rails 允许你访问，例如，`params[:name]`或`params['name']`在一个控制器动作中接收相同的值。这是因为 Rails 使用了`ActiveSupport::HashWithIndifferentAccess`而不是普通的`Hash`。

## hashwithindifferential access

[hashwithindifferential access](https://github.com/rails/rails/blob/4-0-stable/activesupport/lib/active_support/hash_with_indifferent_access.rb, "HashWithIndifferentAccess")是在`ActiveSupport` gem 中定义的一个类，它帮助我们克服了前面提到的问题。这里，键中的符号将被转换为字符串，因此我们可以使用符号和字符串从散列中获取值。

示例:

(如果你没有使用 Rails，那么不要忘记请求 active_support)

```
require 'active_support/core_ext/hash/indifferent_access'
colors = HashWithIndifferentAccess.new
colors[:red] = "Red color"

#Fetch the value using symbol
colors[:red]  #=> "Red color"

#Fetch the value using string
colors["red"]  #=> "Red color"
```

如果我们想将现有的`Hash`转换成`HashWithIndifferentAccess`

```
colors = { blue: 'Blue color', white: 'Red Color' }.with_indifferent_access
```

所有 Rails 开发人员都在使用`HashWithIndifferentAccess`,有时是不为人知的。

## ActiveSupport::影响因素

`Inflectors`是一个随`ActiveSupport`一起提供的模块，包含在您的 Rails 应用程序中。`Inflectors`提供了很多对开发者有帮助的方法。如果我们想在 ruby 脚本中独立使用模块，我们需要将模块包含在 ruby 代码中，如下所示:

```
require 'active_support/inflector'
```

这里解释了一些常用且有益的拐点。

### 骆驼化

它将字符串转换成驼峰式字符串。这些在处理 Ruby 常量时非常有用。

示例:

```
'manu'.camlize #=> 'Manu'
```

### 赋予人性

这种方法使用正则表达式将字符串转换成更容易阅读的字符串。这些可以用在 URL 中，等等。

示例:

```
'author_id'.humanize #=> 'Author'
```

### 钛铅矿

从字符串中删除特殊字符后，这将生成一个漂亮、干净、类似标题的输出

示例:

```
'x-men: the last stand'.titleize #=> "X Men: The Last Stand"
```

更多方法可以在 Rails API [指南](http://api.rubyonrails.org/classes/ActiveSupport/Inflector.html, "Api Rails")中找到，关于代码的细节可以在 [github](https://github.com/rails/rails/blob/4-0-stable/activesupport/lib/active_support/inflector/inflections.rb, "Github") 上找到

## ActiveSupport 扩展

提供扩展 Ruby 的工具。
这些扩展是`ActiveSupport`核心的一部分，可以通过包含`active_support`加载到 Rails 应用中。如果你只想要一个特定的方法，那么通过挑选来包含它，就像这样:

```
require 'active_support/core_ext/object/try.rb'
```

下面描述了一些属于`ActiveSupport::Extensions`的有用方法:

### 尝试法

`try`方法是`ActiveSupport`中包含的最有用的方法之一。您可以使用此方法来代替块，该块在调用对象上的方法之前检查该对象是否为空。

示例:

```
# without try
if @user.first_name
  puts @user.first_name
end    

# with try
@user.try(:first_name)
```

因此`try`将帮助我们避免不必要的条件语句。

## 数字的扩展

### 格式化

使用这种方法，数字可以用多种方式格式化。

#### 电话号码:

示例:

```
5551234.to_s(:phone)
# => 555-1234
1235551234.to_s(:phone)
# => 123-555-1234
1235551234.to_s(:phone, area_code: true)
# => (123) 555-1234
1235551234.to_s(:phone, delimiter: " ")
# => 123 555 1234
1235551234.to_s(:phone, area_code: true, extension: 555)
# => (123) 555-1234 x 555
1235551234.to_s(:phone, country_code: 1)
# => +1-123-555-1234
```

#### 货币

示例:

```
1234567890.50.to_s(:currency)                 # => $1,234,567,890.50
1234567890.506.to_s(:currency)                # => $1,234,567,890.51
1234567890.506.to_s(:currency, precision: 3)  # => $1,234,567,890.506
```

#### 百分率

示例:

```
100.to_s(:percentage)
# => 100.000%
100.to_s(:percentage, precision: 0)
# => 100%
1000.to_s(:percentage, delimiter: '.', separator: ',')
# => 1.000,000%
302.24398923423.to_s(:percentage, precision: 5)
# => 302.24399%
```

#### 人类可读的值

示例:

```
123.to_s(:human)               # => "123"
1234.to_s(:human)              # => "1.23 Thousand"
12345.to_s(:human)             # => "12.3 Thousand"
1234567.to_s(:human)           # => "1.23 Million"
1234567890.to_s(:human)        # => "1.23 Billion"
1234567890123.to_s(:human)     # => "1.23 Trillion"
1234567890123456.to_s(:human)  # => "1.23 Quadrillion"
```

### 转换策略

#### to _ 句子

这个方法将一组单词转换成一个句子。

示例:

```
%w().to_sentence                # => ""
%w(Earth).to_sentence           # => "Earth"
%w(Earth Wind).to_sentence      # => "Earth and Wind"
%w(Earth Wind Fire).to_sentence # => "Earth, Wind, and Fire"
```

更多的方法和详细的解释可以通过浏览 [ActiveSupport](https://github.com/rails/rails/tree/master/activesupport/lib/active_support/core_ext) 本身的源代码找到。

## 结论

Rails 或其包含的模块中的许多方法都没有得到充分利用。探索这些的最佳资源之一是浏览源代码本身。

—

曼努埃尔·阿吉思

[@manusajith](http://twitter.com/manusajith, "@manusajith")

## 分享这篇文章