# 漂亮的小图书馆

> 原文：<https://www.sitepoint.com/nice-little-libraries/>

Ruby 有很多可爱的特性。语法简洁明了，使用元编程技术很容易，对象模型很棒……你知道，我可以列出一长串 Ruby 的特性和喜欢它的理由。顺便说一下，程序员应该学习 Ruby 的一个被低估的原因肯定是它的社区。有很多聪明的人使用 Ruby 来编写小而伟大的库，在这篇文章中我将会谈到这一点。

我把这篇文章叫做*漂亮的小图书馆*，我特别想把重点放在*漂亮的小图书馆*部分。这是这些图书馆最有趣的一面。它们是优雅地解决问题的微小代码块。

## 还有，还有

[和](https://github.com/raganwald/andand)库以一种非常好的方式解决了*安全导航方法*的问题。考虑下面这段代码:

```
person.address && person.address.zip_code
```

这是一行简单(也很难看)的代码，下面是 andand 方法:

```
require 'andand' # gem install andand
person.address.andand.zip_code
```

这是处理这种情况的好方法。我也喜欢[的实现](https://github.com/raganwald/andand/blob/master/lib/andand.rb#L23)。请花些时间浏览一下，你会发现很多有趣的东西。您可能知道，由于 ActiveSupport 库，Rails 提供了类似的[方法](http://api.rubyonrails.org/classes/Object.html#method-i-try)。实际上，我们刚刚看到的示例可以使用 ActiveSupport 以如下方式编写:

```
require 'active<em>support/core</em>ext/object/try'
person.address.try(:zip_code)
```

我不得不说我更喜欢 and 和:try 方法不使用点运算符。我不得不把`zip_code`作为一个符号，它给我一种感觉，一些奇特的事情正在发生。有些语言甚至有一个操作符来处理这类问题。例如， [Groovy](http://groovy.codehaus.org/) 提供了一个 [SafeNavigationOperator](http://groovy.codehaus.org/Operators#Operators-SafeNavigationOperator) ，而[coffeescript](http://coffeescript.org/)提供了一个[存在运算符](http://coffeescript.org/#operators)。我不喜欢这种解决方案，但是知道其他语言如何面对这些问题总是很好的。

要求 andand 库有一个很好的副作用:它会给你一个 Object#tap 的增强版本(并为 prior 1.9.x 之前的版本定义了它)。您实际上可以调用 tap 而无需阻塞:

```
[1, 2, 3, 4, 5].tap.pop.map { |n| n * 2 } # => [2, 4, 6, 8]
```

作为最后一个，*甜蜜*，惊喜宝石提供了一个*不要*的方法太:

```
[1, 2, 3, 4, 5].dont.reverse! # => [1, 2, 3, 4, 5]
```

这篇文章的作者( [raganwald](https://github.com/raganwald) )用 Ruby 和 JavaScript 做了很多有趣的工作。他还写了另一个有趣的库:[调用构造工具包](https://github.com/raganwald/ick)，但它不够小，无法在这里涵盖。无论如何，你一定要看看他的 github 简介，你会发现很多有趣的代码和文字。

# 地图方法

[map_by_method](http://drnicutilities.rubyforge.org/map_by_method/) 是一个臭名昭著的小图书馆。我觉得很多人都不熟悉它，但它确实值得一提。gem 为 Array 类提供了一个 map_by_magic 方法。这在使用 IRB(或 Rails 控制台)时特别有用。它也适用于 ActiveRecord 关联。没有什么比一些例子更好地解释这个伟大的图书馆是如何工作的了:

```
[1, 2, 3, 4, 5].map_by_to_s # => ["1", "2", "3", "4", "5"]

%w{foo bar baz}.map_by_reverse => ["oof", "rab", "zab"]
```

好东西。最有趣(也是最有用)的特性是 map_by_method 支持以下功能:

```
Product # => Product(id: integer, name: string, description: text, price: integer, created_at: datetime, updated_at: datetime)
Product.all.map_by_name_and_price # => [["foo", 50], ["bar", 100], ["faz", 150]]
Product.all.map_by_id_and_name # => [[2, "foo"], [3, "bar"], [4, "faz"]]
```

我建议您看一下实现。它将帮助你发现 map_by_method 也支持其他神奇的方法。实际上，你可以这样做:

```
%w{1foo 2foo 2bar 1baz 4baz}.group_by_to_i #  {1=>["1foo", "1baz"], 2=>["2foo", "2bar"], 4=>["4baz"]}
```

非常好。

## 什么方法

另一个在控制台中非常有用的小库是 [what_methods](http://rubygems.org/gems/what_methods) 。它提供了一个对象#什么？那是一个方法查找器。它将帮助您确定可以对给定对象调用哪些方法来返回预期值。你可以用下面的方法调用它:

```
my_obj.what? expected_result
```

可能有几个例子是解释这个小库如何工作的最佳方式:

```
1.9.3-p0 :001 > require 'what_methods'
 => true
1.9.3-p0 :002 > 3.5.what? 3
3.5.to_i == 3
3.5.to_int == 3
3.5.floor == 3
3.5.truncate == 3
 => [:to_i, :to_int, :floor, :truncate]
1.9.3-p0 :003 > 1.what? 2
1.succ == 2
1.next == 2
 => [:succ, :next]
1.9.3-p0 :005 > "Foo".what? "foo"
"Foo".downcase == "foo"
"Foo".downcase! == "foo"
 => [:downcase, :downcase!]
1.9.3-p0 :006 > "Foo".what? "FOO"
"Foo".upcase == "FOO"
"Foo".upcase! == "FOO"
 => [:upcase, :upcase!]
```

信不信由你，`"Foo".what? "FOO"`对我来说是救命稻草。我无法解释为什么我倾向于忘记那个方法的名字，但这是另一个故事了。

到目前为止，我们已经看到了三个不错的小库，但是，以我的拙见，如果没有列出与测试相关的内容，Ruby gems 的集合就不能被认为是完整的。因此，下面我将向您展示几个与测试相关的项目，它们非常有趣。

## 培根

[Bacon](https://github.com/chneukirchen/bacon) 是一个小型的 RSpec 克隆(而且真的很小，查看源代码)。它非常快(见[这个](http://confreaks.com/videos/618)谈真实数据),它有一个非常类似 RSpec 的语法。既然我们有了 MiniTest，我理解关于外部依赖性的反对意见。但是如果你和我一样，不喜欢测试用的 *must* 动词，想要比 RSpec 更简单的，那么你一定要看看这个。

## interactive_rspec

有时候你想在控制台中测试一个匹配器。至少在我熟悉 RSpec 的时候发生了很多。而这里就是: [interactive_spec](https://github.com/amatsuda/interactive_rspec) 。

运行`irspec`命令将打开一个*特殊的* IRB 控制台，你将能够做如下事情:

```
1.9.3-p0 :005 > [].should be_empty
.

Finished in 0.00007 seconds
1 example, 0 failures
 => true
1.9.3-p0 :006 > [].should_not be_empty
RSpec::Expectations::ExpectationNotMetError: expected empty? to return false, got true
```

太棒了！此外，gem 还有许多其他好的特性。例如，它与 Rails 有很好的集成。事实上，您可以打开一个 Rails 控制台，做类似这样的事情:

```
irspec 'spec/requests/users_spec.rb'
```

目前就这些。我希望你喜欢这篇文章。事实上，我正在考虑写关于这个话题的其他文章，所以非常欢迎你的反馈！

## 分享这篇文章