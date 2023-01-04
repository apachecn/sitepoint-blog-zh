# Ruby 元编程:第二部分

> 原文：<https://www.sitepoint.com/ruby-metaprogramming-part-ii/>

欢迎回到 Ruby 元编程！在第一部分中，我们看了什么是元编程以及它是如何工作的；我们深入探究了 Ruby 的方法查找系统的内部，并介绍了创建单例类如何适应这种机制。现在是好的部分:全部应用。

### 模拟测试对象

Ruby 元编程的一些最有用的特性已经在大量可用的测试框架中展示了无数次。虽然很多这些框架使用各种形式的元编程来创建它们的 API，但是我们将探索 Ruby 测试框架中使用的元编程的一个最重要的用途:用于模仿和存根的元编程。

根据您现在对元编程的了解，您或许可以开始得出结论，这可能是如何工作的。让我们从一个简单的好例子开始:

```
class Book
end

class Borrower
end
```

我们将假设我们正在管理一个图书馆一天，我们需要记录哪些书被借出以及被谁借出。我们还需要知道试图借书的人是否达到了他们的借出图书的限制。为此，我们创建了两个类:`Book`类和`Borrower`类。

```
class Borrower
  attr_accessor :books

  def initialize
    @books = []
  end
end
```

不言而喻，`Borrower`被允许借很多书，所以上面我们给了`Borrower`的每个实例一个类变量`@books`来保存它们当前借出的`Books`。我们添加了一个`attr_accessor`，所以我们可以在`borrower.books`访问它，并使用`borrower.books=`分配给它。知道`Borrower`已经出版了多少本书也是有用的，所以让我们继续添加一个方法来轻松地做到这一点:

```
class Borrower
  # ...

  def books_on_loan
    @books.length
  end

  # ...
end
```

最后，我们需要知道是否允许 a `Borrower`借出更多的书(这个图书馆有点严格，所以我们一次只允许人们借出 5 本书)；一个简单的方法可以做到这一点:

```
class Borrower
  # ...

  def can_loan?
    @books.length < 5
  end

  # ...
end
```

因为你是一个优秀的程序员，在现实世界中，你可能在开始编写这些方法之前已经编写了一些测试。在这样做的时候，你可能开始意识到的一件事是，为了测试`:can_loan?`方法，你必须有一个分配给`Borrower`的`Books`集合。您可以通过在测试实际方法之前向您的`Borrower`中添加一些`Books`来实现这一点，但是这种测试方式变得非常冗长，并且如果在创建这些实例时`Book`类有任何问题，这种测试方式就会失败。这里是元编程的救星:

```
describe Borrower do
  before :each do
    @borrower = Borrower.new
  end

  describe "can_loan? performs correctly" do
    it "returns false if equal to or over the limit" do
      @borrower.books.instance_eval do
        def length
          5
        end
      end
      @borrower.can_loan?.should == false
    end

    it "returns true if under the limit" do
      @borrower.books.instance_eval do
        def length
          1
        end
      end
      @borrower.can_loan?.should == true
    end
  end
end
```

我在这里使用了 RSpec，但是这些原则同样适用于您选择的任何测试框架。通过元编程的力量，您不再需要创建一个`Books`的集合并让您的测试受制于外部依赖；相反，您可以重写您的类方法用来强制它们返回您正在测试的值的方法。当您意识到您可以存根日期和时间、IO 活动、数据库记录、外部 API 调用时，您就可以开始想象这种方法的威力了；这样的例子不胜枚举。

因此，下次当您发现自己在测试中创建了大量的外部依赖项或依赖于额外的类时，可以考虑在组合中加入一些元编程。

在进入一个更有趣的例子之前，需要注意的一点是，对象的模仿和存根化是一个被大量神奇的宝石广泛封装的过程。你会发现像 [FlexMock](http://flexmock.rubyforge.org/) 和[RSpec mock](http://relishapp.com/rspec/rspec-mocks)这样的项目对于保持你的元程序测试干燥是非常宝贵的。

### 动态方法

像 ActiveRecord 这样的库因其对元编程的有趣使用而闻名，允许它们创建基于用户数据动态生成的方法。让我们看看是否可以实现类似的东西。

通过在类中定义`method_missing`方法，任何对该类或其继承链中的方法的不成功调用都将自动调用您定义的`method_missing`方法。结合`instance_eval`和`class_eval`的力量，你就有了一个难以置信的 DSL 和灵活的 API 的配方。这种组合不仅可以用于允许动态方法，还可以避免必须编写重复的方法。

例如，我们将尝试创建一个响应方法`is_<countryname>?`的`Country`类，其中`<countryname>`是您正在测试的国家的名称。更进一步，我们还会让它响应`is_<countryname>(_or_<countryname>...)?`，其中`_or...`可以无限重复，以匹配一个可能的国家列表。

让我们从创建我们的`Country`类开始:

```
class Country
  attr_accessor :name

  def initialize(name)
    @name = name
  end
end
```

Country 有一个`name`属性，在创建类的实例时必须设置这个属性。我们也有一个非常简单的初始化方法，给它一个初始值。现在，让我们实现我们的`method_missing`方法:

```
class Country
  ...

  COUNTRY_QUERY_REGEX = /^is_((?:_or_)?[a-z]+?)+?$/i

  def method_missing(meth, *args, &block)
    if COUNTRY_QUERY_REGEX.match meth.to_s
      self.class.class_eval <<-end_eval
        def #{meth}
          self.__send__ :check_country, "#{meth}"
        end
      end_eval
      self.__send__(meth, *args, &block)
    else
      super
    end
  end
end
```

这看起来远比实际复杂，所以让我们一行一行地来看。在第 5 行，我们通过检查被调用的方法是否匹配正则表达式来开始我们的`method_missing`函数的代码；不要太担心`Regexp`本身，因为重要的是它匹配`is_something<_or_somethingelse...>?`形式的东西(例如`is_italy?`和`is_italy_or_ukraine?`)。

一旦我们知道我们试图调用的方法与我们设置的模式相匹配，我们就可以在第 6 行执行我们现在熟悉的`class_eval`语句。我们使用 heredoc 语法有效地创建了一个格式良好的多行字符串，但是您也可以将整个代码块放在字符串中的一行上。在代码块内部，我们定义了一个与被调用方法同名的方法，并让它调用将在第 8 行完成繁重工作的方法(我们将在稍后定义)。我们创建了这个处理程序中缺少的方法，这样在后续调用中会更快，但是如果你想保持简单，你可以调用`check_country`方法而不创建方法。一旦我们定义了它，我们就调用我们刚刚创建的方法。

如果调用`method_missing`时模式不匹配，我们在第 13 行调用`super`;这一点很重要，如果你不在这里调用`super`，那么继承链中的其他类就不会被询问是否能回答这个缺失的方法。

现在来定义我们的`check_country`方法的内容:

```
class Country
  ...

private
  def check_country(query)
    countries = query[3..-2].split("_or_")
    countries.any? { |s| s == @name }
  end
end
```

在这个方法中，我们将查询分割成一个`Array`，用中间的*或*位将它们分开；如果不包含任何“*或*”，这将使用`query`的整个值。一旦它是一个`Array`，我们使用`Enumerable`方法`any?`来检查`Array`中的任何条目是否匹配我们的类实例所代表的国家的名称。由于这是该方法的最后一次调用，它的返回值也是函数的返回值。

现在让我们试一试:

```
italy = Country.new("italy")
italy.is_ukraine? # => false
italy.is_italy? # => true
italy.is_ukraine_or_italy? # => true
italy.is_ukraine_or_australia_or_portugal_or_italy? # => true
```

现在你可能已经意识到我们所创造的力量。手工制作这些方法要么极其费力，要么难以维护，或者根本不可能。通过将元编程技术与我们的`method_missing`回调相结合，我们已经创建了一个极具表现力和美丽的 API，它是干巴巴的并且易于维护。

### 结论

尽管它看起来很复杂，但我们在本文中所实现的是一些通常很难或很难正常编码的东西，这就是元编程的美妙之处。看起来很难甚至不可能的事情通常可以用简单的元编程技术来编码。

您已经了解了 Ruby 中的 Singleton 类，以及它如何通过向每个方法调用添加新的查找级别来改变语言的结构，我们已经应用了这种强大的技术来生成代码，这些代码可以将日常解决方案转化为优雅的可重用模式。

### 进一步阅读

*   [通过上下文开发中的彼得·琼斯进一步了解 Ruby singleton】。](http://www.contextualdevelopment.com/articles/2008/ruby-singleton)
*   [获取元编程的鹤嘴锄，《元编程 Ruby》](http://pragprog.com/book/ppmetr/metaprogramming-ruby)，作者 Paolo Perrotta。
*   迪夫·托马斯在 PragProg 上有一个精彩的视频系列，叫做[Ruby 对象模型和元编程](http://pragprog.com/screencasts/v-dtrubyom/the-ruby-object-model-and-metaprogramming)。

## 分享这篇文章