# 在 Swift 3 中通过 NSNotification 在 ViewControllers 之间传递数据

> 原文：<https://www.sitepoint.com/pass-data-with-nsnotification-between-viewcontrollers-in-swift-3/>

*本文最初发表于 [iOS 极客社区](https://medium.com/ios-geek-community/pass-data-with-nsnotification-in-swift-3-73743723c84b)。*

今天，我们来谈谈如何以及为什么使用`NSNotification`在类和结构之间传递数据和通信/通知。对了，`NSNotification`和名字不一样，和推送通知——菜鸟错误 101(我)没有关系。

是的，Swift 中传递数据的方式有很多种:`Delegate`、`Key-Value Observing`、`Segue`，还有`NSNotification`、`Target-Action`、`Callbacks`。但是，各有利弊。它们就像餐具一样。例如，我不会用筷子——尽管它相当不错——喝汤。

因为有很多，我将只涵盖一次一个，我会提到为什么你应该勺子，而不是筷子。在我还没用过筷子和刀子之前就说它们的区别是没有意义的。

## 我认为你会学到什么

在本教程中，您将学习两件事情。首先，您将了解如何“通知”其他视图控制器或在单个视图控制器中。第二，你会掌握`NSNotification`的力量和它的弱点。

### UI 组件

有两个视图控制器。`FirstVC`和`SecondVC`。我假设你已经知道如何嵌入`UINavigationController`和连接`IBOutlets`和`IBActions`等等。

`SecondVC`会通知`FirstVC`。当我说“通知”的时候，就像戳。它没有发送任何数据，但当然，我们可以。我将在本文稍后解释如何发送数据。下面的例子类似于用户在脸书或 Instagram 上更新个人资料。我没有使用`UITableView`,因为这对于解释这个概念来说太过了。

![enter image description here](img/9d36c7e6f4523d03c524afa1a2f01171.png)

在我们开始之前，让我们想象一下我们将如何在一个非常高的层次上实现它。想象一下，两个 viewcontrollers 就像一对美丽的夫妇。他们都有智能手机(NSNotification 对象)来相互交谈。第二，每部智能手机都有两个特点:**接收和发送数据**。最后，为了定位彼此的设备，他们有一个**公共密钥**。然而，这取决于对方是否想接电话或干脆忽略。

既然我们已经大致了解了它们是如何相互通信的，那就让我们深入了解一下 Swift ☝️吧

首先，我们要存储密钥。您可以创建一个单独的 Swift 文件，或者像这样在任何视图控制器之外创建一个。

```
import UIKit
let myNotificationKey = "com.bobthedeveloper.notificationKey"
class SecondVC: UIViewController {} 
```

将用于将这些智能手机连接在一起。当然，就像其他一些夫妇一样，你可以出于任何目的拥有一把以上的钥匙。🙃

现在，是时候连接智能手机了。姑且称之为观察者吧。观察器将有四个参数。它将询问`Observer`哪个将是`self`，因为你正在将智能手机连接到`SecondVC`。第二，`Selector`，是通知时运行的函数。第三，名字是指秘密代码。最后，`object`我将在后面讨论 FirstVC 时解释。暂时就放`nil`吧。

```
class SecondVC: UIViewController {
  @IBOutlet weak var secondVCLabel: UILabel!

  override func viewDidLoad() {
    super.viewDidLoad()
    NotificationCenter.default.addObserver(self,
                                           selector: #selector(doThisWhenNotify),
                                           name: NSNotification.Name(rawValue: myNotificationKey),
                                           object: nil)
  }

  func doThisWhenNotify() { print("I've sent a spark!") }
} 
```

我不明白`default`类型属性的含义，因为在 API 指南中没有描述。上面写着*

> “无可用概览”——[苹果](https://developer.apple.com/reference/foundation/nsnotificationcenter/1855943-defaultcenter?language=objc)

无论如何，`SecondVC`有一个智能手机/观察者，当按钮被点击时，是时候发送/通知了

```
@IBAction func tabToNotifyBack(_ sender: UIButton) {
  NotificationCenter.default.post(name: Notification.Name(rawValue: myNotificationKey), object: self)

  secondVCLabel.text = "Notification Completed!😜"
} 
```

在这个上下文中，`object`指的是发送者。因为`SecondVC`正在通知自己，所以它是`self`。

由于`FirstVC`还没有注册观察员，火花/戳戳不会影响。我之前提到过，伴侣有权利接电话或者直接无视。在 iOS 中，我们称之为“松耦合”。不像使用委托/协议在视图控制器之间发送数据，没有疯狂的绑定。我知道你们有些人很困惑。我计划将来写一篇关于如何使用 delegate 传递数据的文章。另外，我将讨论`delegate vs NSNotification`

**资源**
在 Swift 3 中的 ViewControllers 之间传递数据，没有 Segue ( [YouTube](https://www.youtube.com/watch?v=jy_fHmuJIJg)

## 接收时间

很简单。它将添加一个智能手机，并听取火花，如果它有相同的密钥。

```
import UIKit

 class FirstVC: UIViewController {
   @IBOutlet weak var FirstVCLabel: UILabel!

   override func viewDidLoad() {
     super.viewDidLoad()
     NotificationCenter.default.addObserver(self,
                                            selector: #selector(doAfterNotified),
                                            name: NSNotification.Name(rawValue: myNotificationKey),
                                            object: nil) }

   func doAfterNotified() {
     print("I've been notified")
     FirstVCLabel.text = "Damn, I feel your spark"
   }
} 
```

现在，我们来谈谈`object`，这是我之前跳过的参数之一。如果是`nil`，只要你有秘钥，你就不在乎智能手机是从哪个/哪里发送数据的。除了`nil`之外，我从来没有用过任何东西
，所以也许那些以前用过的人可以帮我一下。*换句话说，我不知道如何实现*

现在，它应该看起来像这样。

![](img/6a8ae93b2e3e280e593b41a5b2583bcf.png)

顺便想象一下`SecondVC`就像脸书现场直播一样。只要有许多其他视图控制器包含监听密钥的观察者，它就可以通知很多人。然而，这是同步完成的**。对于那些不明白这意味着什么的人，任务一次发生一个，同时阻止任何其他活动，直到任务完成。因此，如果有太多的视图控制器，就会降低设备的速度。(不确定多少才算多)。**

 ****源代码**
[Github](https://github.com/bobleesj/Blog_NSNotification_Send_Spark)

## 传递数据

现在你已经学会了如何*通知*。让我们快速学习如何在通知的同时发送数据。这是合法的。这是真正的奇迹发生的地方。

在`SecondVC`中，没有使用好的老方法，

```
// Pass Spark
NotificationCenter.default.post(name: NSNotification.Name(rawValue: myNotificationKey), object: nil) 
```

现在，你可以发送一个包含字典的火花

```
// Pass Data
NotificationCenter.default.post(name: NSNotification.Name(rawValue: myNotificationKey), object: nil, userInfo: ["name":"Bob"]) 
```

在`FirstVC`中，在`viewDidLoad`下，您将插入这个

```
NotificationCenter.default.addObserver(forName: NSNotification.Name(rawValue: myNotificationKey),
                                       object: nil,
                                       queue: nil,
                                       using:catchNotification) 
```

我就不说`queue`了。如果你放`nil`，接收任务同步发生。换句话说，如果不是`nil`，`FirstVC`可以使用`Grand Central Dispatch`接收数据。如果你不懂 GCD，也不用担心。我为你写了两篇文章。也许我应该写如何使用 GCD 异步传递数据！那会很有趣。

**资源**
Swift 3 中的大中央调度介绍([中](https://medium.com/ios-geek-community/intro-to-grand-central-dispatch-in-swift-3-with-bob-lee-1d4b56f731b3#.m8s1uisl5)

UI &像 Swift 3 ( [Medium](https://medium.com/ios-geek-community/ui-networking-like-a-boss-in-swift-3-b4607dadfcea) )中的老板一样联网

你已经注意到了一些不同的东西。没错`catchNotification`这个函数会消耗包含`userInfo!`的火花`catchNotification`看起来像什么

```
func catchNotification(notification:Notification) -> Void {
  guard let name = notification.userInfo!["name"] else { return }

  FirstVCLabel.text = "My name, \(name) has been passed! 😄"
} 
```

一旦按下`SecondVC`按钮，`catchNotification`自动运行，包含从`SecondVC`传来的`userInfo`。如果你不明白如何使用 guard 语句解开选项，请随意查看我下面的视频。

**资源**
守护声明( [YouTube](https://www.youtube.com/watch?v=oeUYGNLqqqg)

所以，最后它应该看起来像这样

![](img/bd31247f76c88cb70694cb92639d9bda.png)

**资源**
[源代码](https://github.com/bobleesj/Blog_NSNotification_Send_Data_ViewControllers)

## 移除观察者/智能手机

如果您想在视图关闭时移除任何观察者，只需在`FirstVC`或任何其他视图控制器中插入下面的代码。

```
override func viewDidDisappear(_ animated: Bool) {
  super.viewDidDisappear(true)
  NotificationCenter.default.removeObserver(self)
} 
```

## 最后的话

这篇文章花的时间比我预计的要长一点。但是，和很多人一起写作和交流感觉真好。谢谢大家一路走到最后。非常感谢。👍

*推荐文章*:

[随意查看推荐文章:](#)

[iOS 开发者的十大基本规则](#)

[鲍勃介绍大调度中心](#)

## 分享这篇文章**