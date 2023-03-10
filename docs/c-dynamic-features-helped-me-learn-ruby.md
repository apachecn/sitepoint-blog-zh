# C#的动态特性帮助我学习了 Ruby

> 原文：<https://www.sitepoint.com/c-dynamic-features-helped-me-learn-ruby/>

在 3.0 版本之前，C#一直是一种静态语言。在版本 4.0 中，动态特性被引入到语言中，作为一种尝试来改进 COM 支持和 C#与动态语言(如 JavaScript)之间的互操作性。尽管如此，我最终还是出于不同的原因使用了这些动态特性。

## 向 C#对象添加动态数据

应用程序通常需要动态数据。一个常见的情况是需要跟踪有关用户的不同数据位。例如，应用程序可能提供了跟踪客户名字和姓氏的功能，但是用户可能还希望跟踪“评论”和“家属数量”。由于 C#是一种面向对象的语言，开发人员希望将这些东西视为对象的属性。创建核心客户类别很容易:

```
public class Customer
{
    public string FirstName { get; set; }
    public string LastName { get; set; }
}
```

使用该类也很容易:

```
var customer = new Customer();
var fullName = string.Format("{0} {1}", customer.FirstName, customer.LastName);
```

但是如何处理用户需要动态添加的“属性”呢？开发人员不可能知道这些属性或用户将来可能创建的所有其他属性。

我们将看到一种使用 C#的动态特性来实现这种需求的可能方法。我们首先从测试我们正在寻找的行为的规范开始:

```
[Specification]
public void dynamic_properties()
{
    "Given additional dynamic data for a customer"
            .Context(() => make_data_for_a_customer());
    "When constructing a customer object"
            .Do(() => { _customer = GetCustomer(); });
    "Then the object should expose the additional data as properties"
            .Assert(properties_are_exposed);
    " And getters are available"
            .Assert(getters_are_exposed);
    " And setters are available"
            .Assert(setters_are_exposed);
}
```

如果你不习惯写这种风格的测试，看看 [SubSpec](http://haacked.com/archive/2008/08/24/introducing-subspec.aspx) 。

接下来，准备我们的规范的上下文、动作和断言的方法:

```
void make_data_for_a_customer()
{
    _additionalData.Clear();
    _additionalData.Add("Comments", "Some comments..."); 
    _additionalData.Add("NumberOfDependents", 3);
}

Customer GetCustomer()
{
    return new Customer(_additionalData);
}

void properties_are_exposed()
{
    (_customer as DynamicEntity).RespondTo("Comments").ShouldBeTrue();
    (_customer as DynamicEntity).RespondTo("NumberOfDependents").ShouldBeTrue();
}

void getters_are_exposed()
{
    ((string) _customer.Comments).ShouldEqual("Some comments...");
    ((int) _customer.NumberOfDependents).ShouldEqual(3);
}

void setters_are_exposed()
{
    _customer.Comments = "New comments";
    _customer.NumberOfDependents = 9;

    ((string)_customer.Comments).ShouldEqual("New comments");
    ((int)_customer.NumberOfDependents).ShouldEqual(9);
}
```

但是上面的代码( *_customer)怎么可能。Comments* )编译成功如果 Customer 类在编译时没有显式的 Comments 属性？代码编译是因为*_ 客户*字段在类中被声明为*动态*:

```
dynamic _customer;
```

如果您对 _additionalData 字段有疑问，它只不过是一本字典:

```
Dictionary<string, object> _additionalData = new Dictionary<string, object>();
```

为了支持动态增长的应用程序，我使用并滥用了这些特性，看到 Rails 中的模型类(更确切地说是 ActiveRecord)没有声明任何属性，但在应用程序运行时，它们仍然是完全基于数据库模式填充的，我感到很舒服。以 Ruby 中的这个客户类为例:

```
class Customer < ActiveRecord::Base
end
```

这样的类可以像下面这样实例化和使用:

```
customer = Customer.new
customer.comments = "Some comments..."
customer.number_of_dependents = 3
```

同样，该类根据 Customers 表的列动态获取其属性。

## 哈希，哈希，无处不在…

尽管在 Ruby 中可以实例化一个类并设置它的属性，就像我们在 C#中做的一样，但是大多数 Ruby 爱好者更喜欢使用散列。看起来是这样的:

```
customer = Customer.new(:comments => "Some comments...", 
                              :number_of_dependents => 3)
```

尽管对于 C#开发人员来说，这看起来像是命名参数，但事实并非如此。ActiveRecord 模型上的 *initialize* 方法(当在一个类上调用 *new* 时调用该方法)接受一个散列值，该散列值是我们想要在该对象的属性上设置的值。如果我们重温 C#示例，我们也可以用以下方式实例化 Customer 类:

```
customer = new Customer(new Dictionary<string, object>
                      {
                          { "Comments", "Some comments..." }, 
                          { "NumberOfDependents", 3 }
                      });
```

因为我在 C#中使用了很多类似的字典，所以 Ruby 中的这些东西并不觉得奇怪。

## 添加动态行为怎么样？

到目前为止，我已经谈论了添加到对象中的动态数据；但是添加动态行为呢？这在 C#中以类似的方式实现。让我们以下面的例子为例:假设我们的客户对象需要在这样一个上下文中使用，它必须提供一个全名属性。请记住，属性的 *getter* 只不过是一个返回值的方法，因此，它可能有特定的行为。现在让我们假设全名如何被格式化是由用户通过某种配置提供的，我们永远不知道每个用户到底想怎么做。

我们可以这样测试这种行为:

```
[Specification]
public void dynamic_behavior()
{
    "Given additional dynamic behavior for a customer"
            .Context(() => make_behavior_for_a_customer());
    "When constructing a customer object"
            .Do(() => _customer = GetCustomer());
    "Then the object should expose the additional behavior"
            .Assert(behaviors_are_exposed);
    "And the behavior should be accessible"
            .Assert(behaviors_are_accessible);
        }
```

接下来，我将展示如何在这个示例中指定动态行为:

```
void make_behavior_for_a_customer()
{
    _additionalBehavior.Clear();
    _additionalBehavior.Add("FullName", c => string.Format("{0} {1}", c.FirstName, c.LastName));
}
```

我们的 *_additionalBehavior* 字典比 *_additionalData* 字典稍微复杂一些；我们现在不是将一个*字符串*作为键，将一个*对象*作为值，而是将一个*字符串*作为键，将一个 *lambda* 作为值(由于 lambda 是实现*延迟执行*的一种方式，所以它实际上并不提供“值”，而是“意味着在需要时检索值”)。

该字段的声明也稍微复杂一些:

```
Dictionary<string, Func<dynamic, dynamic>> _additionalBehavior =
            new Dictionary<string, Func<dynamic, dynamic>>();
```

我们的 lambda 由一个`Func<dynamic dynamic=""></dynamic>`代表。换句话说，一个委托接受一个*动态*类型的参数，并返回一个*动态*类型的值。

现在你可能会指出这样一个事实，我提供的 lambda 作为额外的行为仍然被硬编码到应用程序中，你是对的；我这样做只是为了让事情变得简单。在实际应用中，我在编译时创建了这些 lambdas，要么在运行时创建表达式树，要么在运行时生成代码(但这超出了本文的范围)。

这是我们测试的最后一部分:

```
void behaviors_are_exposed()
{
    (_customer as DynamicEntity).RespondTo("FullName").ShouldBeTrue();
}

void behaviors_are_accessible()
{
    ((string)_customer.FullName).ShouldEqual("Jon Jones");
}
```

我们简单地检查我们的对象是否响应发送给它的 *FullName* 消息，以及它是否返回我们期望的结果。

*另一个小提示:你可能会问自己为什么我会选择客户。FullName 转换为字符串，以便访问其值。这是因为 ShouldEqual 方法是一个扩展方法，这种类型的方法目前不能用于动态类型。*

那么如何才能让这个规范通过呢？只需回到 DynamicEntity 类，添加一个字段来存储带有行为的字典，然后从 TryGetMember 方法调用它:

```
public override bool TryGetMember(GetMemberBinder binder, out object result)
{
    if (_additionalData.ContainsKey(binder.Name))
    {
        result = _additionalData[binder.Name];
        return true;
    }
    if (_additionalBehavior.ContainsKey(binder.Name))
    {
        result = _additionalBehavior[binder.Name].Invoke(this);
        return true;
    }
    return base.TryGetMember(binder, out result);
}
```

因为存储在字典中的值是一个委托，所以我们调用它的 Invoke 方法，传入我们的动态对象本身( *this* )以便 lambda 可以使用它，lambda 对它做一些工作。

## 在 Ruby 中会是什么样子呢？

在 Ruby 中实现动态行为有几种方式，我将只介绍其中一种，这种方式可能对 C#开发人员来说更友好。我确信每个经验丰富的 ruby 专家都能想出更干净、更好的方法，但是这些方法对 C#开发者来说可能会很奇怪。

下面是我们在 Ruby 中的测试(使用 [RSpec](http://rspec.info/) 测试框架):

```
require './customer'

describe Customer do

  describe "Dynamic Behaviors" do

    context "Given additional dynamic beheavior for a customer" do

      before(:each) do
        @additional_behavior = { :full_name => 
                 lambda { |c| "#{c.first_name} #{c.last_name}" } }
      end

      describe "When object is constructed" do

        let(:customer) { Customer.new(@additional_behavior) } 

        it "should expose behaviors" do
          customer.should respond_to(:full_name)
        end

        it "should make behaviors accessible" do
          customer.first_name = "Joe"
          customer.last_name = "Jones"
          customer.full_name.should == "Joe Jones"
        end
      end
    end

  end
end
```

即使您还不熟悉 Ruby，您也应该能够理解下面测试中的代码，首先是因为我以类似于我之前展示的 C#测试的方式编写了它，其次是因为 RSpec 提供了一种非常有表现力的编写测试的方式。

这是客户类别:

```
require 'dynamic_entity'

class Customer < DynamicEntity
    attr_accessor :first_name, :last_name
end
```

在这个示例中，我声明了 first_name 和 last_name 属性，这样我们就不会依赖于数据库、ActiveRecord 等。最后但同样重要的是，动态性类:

```
class DynamicEntity

  attr_reader :additional_behavior

  def initialize(additional_behavior)
    @additional_behavior = additional_behavior
  end

  def respond_to?(message)
    self.additional_behavior.include?(message)
  end

  def method_missing(message, *args, &block)
    if self.additional_behavior.include?(message)
      self.additional_behavior[message].call(self)
    else
      super
    end
  end
end
```

通过使用`method_missing`(类似于 C#的`TryGetMember`)，将 lambdas 存储在一个散列中，并在适当的时候调用它们，我已经从 C#移植了相同类型的实现。我再说一遍:在 Ruby 中实现类似这样的东西，还有其他几种方法，每种方法都有自己的优缺点。我只给你看一个你应该看起来最熟悉的。一旦你已经使用 Ruby on Rails 一段时间了，我强烈推荐你阅读[元编程 Ruby](http://pragprog.com/book/ppmetr/metaprogramming-ruby) ，因为它解释了你将在 Rails、ActiveRecord 和其他宝石中发现的许多“魔力”。

## 那些测试是怎么回事？

如果你是一个不习惯为你的代码编写测试的. NET 开发人员，你可能想知道这篇文章中的那些测试是怎么回事。我确保将它们包括在内，因为它们在我学习 Ruby 的过程中也发挥了巨大的作用。当我开始编写利用动态特性的 C#代码时，我立即意识到我只需要*和*为所有代码编写测试，因为编译器不会在整个过程中帮助我。这没什么大不了的，因为我已经在为静态类型的代码编写测试了。

进入 Ruby，坚持编写测试才有意义，因为在那里*一切*都是动态的。此外，作为一个 Ruby noob，我知道我并不总是写最好的代码，所以我尽我所能来通过我的测试。大多数时候，我的 Ruby 代码看起来像 C#代码(*“我说的是带有 C#口音的 Ruby”*，或者我会这么说)。这很好，因为一旦一两个月过去了，我回头看那些代码，我可能会知道一个更好的方法，所以我可以重构我的代码，并让测试来支持我，以防我搞砸了什么。

## 摘要

动态编程是可以帮助. NET 开发人员学习“Ruby 方式”的事情之一。一旦你看到人们用它创造出了令人惊奇的东西，回到 C#中，看看这些知识是如何被转移的。一个很好的例子可以在 Rob Connery 的 [Massive](https://github.com/robconery/massive) 项目中看到，他使用 C# dynamic 特性创建了一个数据访问工具，该工具借鉴了他在 Ruby 和 ActiveRecord 中所学的思想。

这篇文章的完整代码可以在这里找到。

## 分享这篇文章