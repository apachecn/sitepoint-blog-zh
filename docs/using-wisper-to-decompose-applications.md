# 使用 Wisper 分解应用程序

> 原文：<https://www.sitepoint.com/using-wisper-to-decompose-applications/>

从事一个新项目很有趣，而且感觉非常有效率。可以快速添加功能，原型设计思想可以形成一个紧密的反馈环，非常适合敏捷/精益式规划。

然而，随着时间的推移，我发现项目早期的初始势头减弱了，进行 Rails 应用程序的测试优先开发变得困难了。相比之下，当编写库时，测试优先的方法似乎是自然的。

Rails 社区中的主流声音提倡所谓的“Rails 方式”。这是一条黄金法则，毫无疑问，这也是我们许多人最初选择 Rails 的原因。这是 Rails 的美妙之处，也是其著名的生产率提升之处。

但不知何故，这些收益似乎短暂得令人沮丧。

在开发了几个非同小可的 Rails 应用程序之后，我开始认识到这些症状，并且通过倾听他人的意见，我开始拼凑出我沮丧的原因。

## 灵感

我第一次听说六边形轨道是在 2012 年 GoRuGo 大会上马特·韦恩在的一次演讲中。我立即意识到他在我自己的代码中描述的问题，特别是用过程化风格编写的控制器，充斥着`if`语句。Matt 展示了如何使用发布-订阅模式来克服这个问题，这也是我将在本文中演示的内容。

六角形轨道背后的想法是核心应用程序与交付机制的分离，这在主题演讲中得到了呼应，罗伯特·马丁的[“失去的岁月的架构”](http://www.confreaks.com/videos/759-rubymidwest2011-keynote-architecture-the-lost-years)和 Avdi Grimm 的[“轨道上的对象”](http://objectsonrails.com)。

关键是你的应用不是 Rails 应用。Rails 只不过是一个框架，用来用 HTTP 协议包装你的应用。这是一个细节，我们应该关注面向对象的开发，而不是面向 Rails 的开发。

## 又一个易趣的克隆

想象一下，我们正在为一家初创公司工作，这家公司正在为一个利基市场打造一个 ebay 的复制品。在产品页面上，有人可以通过输入金额并按下“立即出价”按钮来出价。

一开始，故事很简单；创建一个新的出价，并让用户知道它是否成功。简单，使用“铁路方式”，交付和运输。敏捷地狱之火。

一段时间后，新的故事被添加到待办事项列表中:

*   给物品的卖家发电邮
*   实时更新项目观察者的用户界面
*   存储统计数据
*   更新该项目的所有订阅者的活动源

这些新故事的代码需要触及几个模型，并触及一个外部 API。无论它走到哪里，(模型、观察者或控制者)它都会引入依赖关系，而这些依赖关系并不是“对一个项目出价”的核心关注点。测试和重构会变得困难，生产率也会降低。

这段代码还能放在哪里？我们已经有了单词:“创建出价”或`CreateBid`。这听起来像是一个很好的新对象的候选对象，它可以模拟出价的创建。

这种类型的对象通常被称为服务、用例或命令。我会用“服务”这个词。

服务通常用于协调跨多个模型的交互和/或与外部系统交互(例如 HTTP 和 SMTP)。

服务对象似乎是一个好主意，但是我们不是已经解决了这个问题吗？

我们的服务难道不会变得像模特一样臃肿吗？是啊！

## 智能服务对象

Wisper 是一个用事件补充 Ruby 的方法和属性的库。

我们将使用它来允许我们的服务对象以分离的方式向其他对象发布事件。

服务对象可以执行其主要操作，然后将结果(一个事件)广播给任何侦听器。侦听器被添加到执行服务的上下文中的服务对象，在这种情况下，上下文就是控制器。让我们看一个例子:

控制器:

```
class BidsController
  def new
    @bid = Bid.new
  end

  def create
    service = CreateBid.new
    service.subscribe(WebsocketListener.new)
    service.subscribe(ActivityListener.new)
    service.subscribe(StatisticsListener.new)
    service.subscribe(IndexingListener.new)
    service.on(:reserve_item_successfull) { |bid| redirect_to item_path(bid.item) }
    service.on(:reserve_item_failed)      { |bid| @bid = Bid.new(bid_params); render :action => 'new' }
    service.execute(current_user, bid_params)
  end

  private

  def bid_params
    params[:bid]
  end
end
```

该服务:

```
class CreateBid
  include Wisper::Publisher  

  def execute(performer, attributes)
    bid = Bid.new(attributes)
    bid.user = performer
    if bid.valid?
      bid.save
      notify_seller_of(bid)
      broadcast(:bid_successful, performer, bid) 
    else 
      broadcast(:bid_failed, performer, bid)
    end
  end

  private

  def notify_seller_of(bid) 
    BidMailer.new_bid(bid).deliver 
  end 
end
```

一些听众:

```
class WebsocketListener
  def bid_successful(performer, bid)
    Pusher.trigger("item_#{bid.item_id}", 'new_bid', :amount => bid.amount)
  end
end

class ActivityListener
  def reserver_item_successful(performer, bid)
   # ...
  end
end
```

你可能注意到的第一件事是控制器没有`if`，可读性很强，并且有一个令人愉快的形状。

我们已经将辅助关注点(websocket 通知、活动提要)转移到单独的对象中。

控制器(上下文)执行以下操作:

*   创建服务对象
*   连接任何需要了解响应的侦听器
*   执行服务，传入从 HTTP 请求中提取的数据

有趣的是，`CreateBid`不是告诉听众做某事，而是告诉他们某事发生了。这是微妙但重要的。`CreateBid`不知道它执行的上下文(可能不是 web 应用程序)或谁在监听。信任而非控制他人的对象只需要根据自己的状态做出选择。这在对象和它们的职责之间创建了一个清晰的边界，并且不会限制我们将来如何使用我们的域对象的选择。

如果我们需要在注册过程中添加新功能，我们有两种选择:

*   向服务对象添加代码。如果特性本质上是服务所表达的用例的一部分，那么这是合适的。在我的例子中，我决定发送电子邮件是注册过程的一部分。
*   向服务对象添加侦听器。如果该特性是服务所表达的用例的辅助，则最合适，不应该在执行服务和/或特定于交付机制的所有情况下发生。

### 发布事件

只需在任何对象中包含`Wisper::Publisher`就可以发布事件。

```
class MyPublisher
 include Wisper::Publisher

  def do_something
    publish(:done_something, 'hello', 'world')
  end
end
```

第一个参数是要广播的事件，后面是应该传递给侦听器的任意数量的参数。

当调用`publish`时，`done_something`将在所有拥有(`respond_to?`)这个方法的监听器上被调用。该方法必须有一个参数签名，该签名可以接受正在发布的参数，因此在上面的示例中，侦听器应该有:

```
def done_something(greeting, location)
  # ...
end
```

`publish`方法也被别名为`broadcast`和`emit`。

### 订阅事件

```
my_publisher = MyPublisher.new
my_publisher.subscribe(MyListener.new)
```

我们还可以选择约束将哪些事件发送给侦听器:

```
create_bid.subscribe(WebsocketListener.new, :on => :bid_successful)
```

如果需要特定的多个事件，还可以将一个数组传递给`on`。

如果监听器有一个方法与正在广播的事件名称不匹配，您可以使用`with`将其映射到一个不同的方法名称。

```
create_bid.subscribe(BidListener.new, on => :bid_successful, :with => :successful)
```

我们也可以订阅一个块，这在控制器中非常有效，允许它们订阅事件并以正确的方式响应。您可以合理地添加控制器作为监听器，例如`create_bid.subscribe(self)`，但是添加一个块会使它看起来更像 Rails。

```
create_bid.on(:reserve_item_successfull) do |reservation|
  redirect_to reservation_path(reservation)
end
```

### 全球听众

Wisper 还允许添加“全局监听器”,自动订阅给每个发布者。

在 Rails 应用程序中，我通常会在初始化器中添加全局侦听器。

```
Wisper::GlobalListeners.add_listener(StatisticsListener.new)
```

这可能很方便，但会造成间接性。执行路径可能不太明显。我建议谨慎使用全局侦听器。像普通的 Rails 观察者一样，您也需要在测试中关闭它们。

## 概括起来

通过将服务对象与 Wisper 相结合，我们获得了以下好处:

*   责任更少的更小的对象
*   对象可以基于上下文以轻量级的方式连接起来
*   核心应用程序不会受到交付机制问题的影响
*   对象告诉听者发生了什么，而不是做什么(信任，而不是控制)
*   隔离测试更容易，因为我们不需要 Rails 测试运行得更快。
*   不同层中的对象之间存在清晰的边界。
*   转移到异步更容易，因为我们可以在不加载 Rails 的情况下从后台执行任务

更容易测试，意味着重构更容易，这意味着寿命增加，从而带来更高的资金价值和更大的利润。

与任何此类考虑一样，一种尺寸并不适合所有人。就我个人而言，我不会在简单的 CRUD 应用中使用 Wisper。但是如果(当！)CRUD 应用程序的开发超出了 Rails 的最佳点，然后我开始使用智能服务对象。

将 Wisper 应用于现有的代码库非常容易。您可以从一个控制器或型号开始。

## 重构成熟的代码库

幸运的是，这个概念很容易分阶段应用到一个成熟的代码库，特别是如果你有良好的测试覆盖率。简单地选择一个开始的地方，比如一个臃肿的模型或者一个疯狂的控制器。

在开发 Wisper 的过程中，我尝试了许多在代码中实现发布-订阅的技术，它仍然包含一些这样的实验。我只在出于其他原因需要触摸有问题的控制器时用 Wisper 替换它们。

一旦模型或服务对象有了 Wisper，添加新的侦听器就非常容易了。例如，缓存可以从模型/观察者/控制器中移出，移到监听器中。

如果您希望移除一些`after_`回调，您甚至可以像对任何其他对象一样容易地将 Wisper 应用于 ActiveRecord 模型。

试着在你的 Rails 应用中找到一个需要 TLC 的地方，然后创建一个分支。看看是否会有更好的结果。

我很乐意听到你的进展，你对 Github 的想法和反馈，在评论或 Twitter 上。

## 分享这篇文章