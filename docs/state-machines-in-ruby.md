# Ruby 中的状态机

> 原文：<https://www.sitepoint.com/state-machines-in-ruby/>

给它们起一个周日的名字，有限状态机(FSM)就在我们周围，如果我们睁开眼睛足够长的时间，当你买一罐汽水，通过电子商务购物车，通过火车站的电子转弯方式时，你就可以看到它们在发挥作用。回想我以前做电子工程师的时候，我们总是用意大利面条式的逻辑门网络来实现有限状态机。在软件世界中，我们确实会将代码与 if 语句混在一起，而这些语句实际上应该使用 State 模式来清理。

## 剖析有限状态机

解释一个 FSM 真的没有什么复杂的。线索真的在名字中，我们有一个系统，模块，类，机器等等。具有已知数量的状态。在软件术语中，它们变得重要的时候是当一些内部状态对一个对象的行为有影响的时候。本质上，有限状态机有三个核心概念:状态、转换和事件。

作为一个例子，让我们看看一个非常简单的汽水机。在我看来，诀窍在于将正确的对象识别为 FSM。乍一看，你可能会选择空闲、硬币投入、饮料分发回到空闲等状态。这不是一个好的状态机，尽管它描述了整体行为，我们所做的是将 soda 机和 soda 机事务的行为混合到一个对象中。

## 实现状态机

从 Ruby 世界中的一个已知问题入手的最佳地方当然是 RubyGems。快速浏览一下 [Ruby Toolbox](https://www.ruby-toolbox.com/categories/state_machines) ，我们会得到一份关于状态机的大杂烩。我个人偏好的是名副其实的[状态机](https://github.com/pluginaweek/state_machine) gem。

我选择它而不是其他任何东西的原因是历史(以前的经验)和它的集成财富的混合。虽然没有直接绑定到 Rails，但是它集成了 ActiveRecord 和 ActiveModel。这些集成意味着我们可以使用一些很好的特性，比如观察器、验证，在 ActiveRecord 世界中，我们可以在转换时获得命名的作用域和数据库事务。它还可以很好地与其他数据库层如 DataMapper、Sequel 和 Mongoid 配合使用。

现在，我们将简单地使用状态机 gems 的能力向普通的旧 Ruby 对象添加 FSM 行为。

如果你能想象，机器将创建一个初始状态为`awaiting_selection`的`SodaTransaction`的新实例。过渡路径将遵循如下路线:`awaiting_selection`、`dispense_soda`、`complete`。

```
require 'rubygems'
require 'state_machine'

class SodaTransaction

  state_machine :state, initial: :awaiting_selection do
  end

end
```

我们本能地知道`awaiting_selection`状态必须对前面板上的按钮按下做出反应。所以让我们继续实施吧。

```
require 'rubygems'
require 'state_machine'

class SodaTransaction

  state_machine :state, initial: :awaiting_selection do

    event :button_press do
      transition :awaiting_selection => :dispense_soda
    end

  end

end
```

此时，我们可以在 irb 中尝试我们的苏打交易。我发现的一个小技巧是从 soda_transaction.rb 文件所在的路径运行命令`irb -I .`。这简单地启动了一个 irb 会话，工作目录设置在当前路径，允许您简单地使用`require 'soda_transaction'`而不需要其他路径。

```
require 'soda_transaction'

sm = SodaTransaction.new
puts sm.state

#=> awaiting_selection

sm.button_press
puts sm.state
#=> dispense_soda
```

万岁。我们实现了我们想要的行为，离解渴越来越近了。碳酸饮料机最让我恼火的一件事是我最喜欢的烈酒脱销了。机器等我投入硬币并按下按钮，然后告诉我只能接受胡椒博士了。我讨厌剥夺任何人同样的愤怒，所以让我们也实现这种行为。

```
class SodaTransaction

  attr_accessor :selection

  state_machine :state, initial: :awaiting_selection do

  event :button_press do
    transition :awaiting_selection => :dispense_soda, if: :in_stock?
  end

  end

  def in_stock?
    stock_levels[@selection] > 0
  end

  def stock_levels
    {
      dr_pepper: 100,
      sprite: 10,
      root_beer: 0,
      cola: 8
    }
  end

end
```

我们在这里所做的只是在从:waiting_selection 到:dispense _ soda 的转换上设置了一个防护。通过使用简单的散列查找，我们检查所需的饮料是否有货。

显然，在实际的实现中，我们会希望处理股票的递减，并将这种检查委托给应用程序中的其他地方。目前，简单的存根哈希查找就可以了。但是你可以从例子中看到选择是一个实例变量，它在哪里？肯定在事件中？

我发现允许这些附加参数传递给事件的最好方法是覆盖事件本身，如下所示:

```
class SodaTransaction

  state_machine :state, initial: :awaiting_selection do

    event :button_press do
      transition :awaiting_selection => :dispense_soda, if: :in_stock?
    end

  end

  def button_press(selection)
    @selection = selection
    super
  end

  def in_stock?
    stock_levels[@selection] > 0
  end

  def stock_levels
    {
      dr_pepper: 100,
      sprite: 10,
      root_beer: 0,
      cola: 8
    }
  end

end
```

因为方法是在状态机中声明的，所以一旦我们完成了任何自定义动作，或者新方法，我们只需调用`super`就可以将调用沿树向上传递回原始事件。

## 继续喝汽水

在稍微进入用户体验的内部之后，我们仍然需要完成汽水的交易。为此，我们需要另一个事件，当汽水罐从托盘中掉落时，这个事件就会触发。很简单，我们将此事件从`dispense_soda`状态设置为“完成:

```
class SodaTransaction

  state_machine :state, initial: :awaiting_selection do

    event :button_press do
      transition :awaiting_selection => :dispense_soda, if: :in_stock?
    end

    event :soda_dropped do
      transition :dispense_soda => :complete
    end

  end

  def button_press(selection)
    @selection = selection
    super
  end

  def in_stock?
    stock_levels[@selection] > 0
  end

  def stock_levels
    {
      dr_pepper: 100,
      sprite: 10,
      root_beer: 0,
      cola: 8
    }
  end

end
```

在 irb 中运行几行代码显示，我们已经成功地为用户提供了一瓶汽水。

```
require 'soda_state_machine'

sm = SodaTransaction.new
puts sm.state

#=> awaiting_selection

sm.button_press(:cola)
puts sm.state

#=> dispense_soda

sm.soda_dropped
puts sm.state

#=> complete
```

现在只剩下一件事困扰着我们，那就是管理我们汽水机的库存。从上面的例子来看，我们现在少了一罐可乐。

## 观察者和回叫

幸运的是，使用状态机 gem 来管理这一点是很简单的。它提供了许多类似于 Rails 过滤器的转换回调。

我们可以像这样在`:soda_dropped`事件上设置一个回调。

```
class SodaTransaction

  state_machine :state, initial: :awaiting_selection do

    after_transition :on => :soda_dropped, :do => :manage_stock

    event :button_press do
      transition :awaiting_selection => :dispense_soda, if: :in_stock?
    end

    event :soda_dropped do
      transition :dispense_soda => :complete
    end

  end

  def button_press(selection)
    @selection = selection
    super
  end

  def manage_stock
    puts &quot;Removing 1 from the #{@selection} count&quot;
  end

  def in_stock?
    stock_levels[@selection] > 0
  end

  def stock_levels
    {
      dr_pepper: 100,
      sprite: 10,
      root_beer: 0,
      cola: 8
    }
  end

end
```

因为我使用了一个简单的 hash 来管理库存，所以我只是打印出了那个`SodaTransaction`对象，它发送了一些消息来从当前库存中删除 1。

虽然从设计的角度来看感觉不太对劲，但`SodaTransaction`现在正在管理汽水机的库存。我的直觉告诉我，我们也许应该换个方式来处理这件事。不要在我们已经实现的简单机器中误解我，我们给出的在我看来很好。

在我们需要更多的情况下，比如记录卖出更多饮料的时间，或者使用观察者的这些回调记录购买漏斗的退出可能更适用。

```
class SodaStockObserver

  def self.manage_stock(transaction, transition)
    puts &quot;Removing 1 from the #{transaction.selection} count&quot;
  end

  def self.after_transition(transaction, transition)
    puts &quot;#{transition.attribute} was: #{transition.from}, #{transition.attribute} is: #{transition.to}&quot;
  end

end

class SodaTransaction

  attr_reader :selection

  state_machine :state, initial: :awaiting_selection do

    after_transition :on => :soda_dropped, :do => SodaStockObserver.method(:manage_stock)
    after_transition SodaStockObserver.method(:after_transition)

    event :button_press do
      transition :awaiting_selection => :dispense_soda, if: :in_stock?
    end

    event :soda_dropped do
      transition :dispense_soda => :complete
    end

  end

  def button_press(selection)
    @selection = selection
    super
  end

  def in_stock?
    stock_levels[@selection] > 0
  end

  def stock_levels
    {
      dr_pepper: 100,
      sprite: 10,
      root_beer: 0,
      cola: 8
    }
  end

end
```

正如你所看到的，在转换回调中使用一个观察者可以不费吹灰之力就变得非常强大。关于观察者代码的一个注意事项是在`after_transition`方法中使用
`transition.attribute`。这只是表示我们给了状态机什么键，在本例中是“state”。

如前所述，状态机 gem 与流行的数据库层(如 ActiveRecord)有一些很好的集成。这些集成的一部分是“自动”回调。例如，如果 SodaTransaction 对象继承自 ActiveRecord，那么简单地定义一个`SodaTransactionObserver`就可以为我们完成连接。

```
class SodaTransaction < ActiveRecord::Base
  ...
end

class SodaTransactionObserver < ActiveRecord::Observer

  def after_soda_dropped(transaction, transition)
    # Remove 1 from selection qty.
  end

  def after_transition(transaction, transition)
    # Do whatever logging we need
  end

end
```

## 实际用法

至于野生状态的机器，有很多。浮现在脑海中的项目有[狂欢](http://spreecommerce.com/)、[restful 认证](https://github.com/technoweenie/restful-authentication)和非红宝石风味 [EmberJS](http://emberjs.com/) 。

Spree 在结账过程中使用 FSM，从记录各种付款细节到完成订单。您甚至可以连接到 FSM 并注入您自己的状态，以进一步定制结帐过程。

RestfulAuthentication(自从[发明](https://github.com/plataformatec/devise)以来，有人使用这个吗？)实现一个状态机( [acts_as_state_machine](https://github.com/aasm/aasm) )。它管理一个用户的帐户，“待定”，“活动”，“暂停”等。

另一方面，Ember 虽然显然不是用 Ruby 编写的，但它使用状态机作为路由原则。当用户浏览站点时，路由器使用 URI 来决定应用程序的状态。

还有一个时间点，一个状态机曾一度被集成到 ActiveRecord 中，[，但在 3.0 版本之前被移除了](http://guides.rubyonrails.org/3_0_release_notes.html)。

以我的经验来看，它们是开发人员手中的王牌，我很惊讶它们实际上很少被使用。也就是说，FSM 可以在错误的情况下实施，这通常会让生活变得痛苦。我想带走的建议是，无论如何在你的对象上使用一个状态数组和一个`state`实例变量。但是，当前者变得过于笨拙时，不要害怕实现更全面的 FSM 解决方案。

## 分享这篇文章