# 在 Swift 3 中存储带有用户默认值的 UIColor

> 原文：<https://www.sitepoint.com/store-uicolor-with-userdefaults-in-swift-3/>

*本文最初发表于 [iOS 极客社区](https://medium.com/ios-geek-community/store-uicolor-with-userdefaults-in-swift-3-e41195c29147#.u3j8egczd)。*

那么，首先`UserDefaults`到底是什么？名字怎么这么难听？我们为什么要使用它？我为什么要写这个？如果你能对这些问题给出至少一个答案，你可以跳到**第二部分**，在那里我会谈到`UIColor`。

***先决条件:*** 从心底里理解类型铸造。换句话说，能够区分`as`、`as!`、`as?`你可以从这个视频开始，我在 [YouTube](https://www.youtube.com/watch?v=A2M5sIXFNbg&t=27s) 上露脸说英语。

## 第一阶段:类比

作为一种传统，让我们从一些时髦而切实的方法开始，在极高的层面上理解`UserDefaults`。其实这个太简单了。我觉得没必要。`UserDefaults`对象**保存用户数据**。因此，当你第一次下载应用程序时，即使电池电量耗尽，你也可以保存背景颜色/图像等偏好设置。它可以保存各种东西。如果你的手机有 254GB 的空闲空间，它可以保存 254GB 的用户数据。但是，有一个大问题。

它在运行时反刍一切。好吧，前面那句话可能有点暧昧。让我们试试这个。它会在你第一次启动一个应用或者加载视图的时候把所有东西都吐出来。嗯，🤔。这是更好的方法。**就像你跑去洗手间，一下子拉了憋了 5 天的便便**。你的身体会怎么样？你会不知所措。它甚至可能不会出来的权利，最重要的是，它会伤害你真正的坏。同样的事情，你想确保你只携带足够的大肠内的粪便，以便你(iPhone)可以像老板一样照顾和处理。

*LOL。对那些新人来说，这是我思考和执行的方式。不好意思。没人能阻止我。*

当然，有另一种方法来解决这个严重的问题，无论是对你还是对手机。我们可以使用 CoreData。你可能以前听过。我将在本周六上午 8 点发表一篇文章。敬请关注。

## 第二阶段:让我们面对现实

我们已经玩够了(至少对我来说)。是时候投入进去了。像往常一样，我们必须像这样创建一个`UserDefaults`的实例/对象

```
let defaults = UserDefaults.standard
```

现在，这个对象能够接受一堆值，确切地说，是一堆“便便”,每个“便便”都有自己的名字。例如，让我们尝试存储一个“Int”值。

```
defaults.set(20, forKey: "myAge")
```

我认为便便的比喻有点太令人不安了，所以我们把比喻改成一只可爱的仓鼠，如下图所示。

![](img/c996fd8d9e153346061b6ad3b0ef8f47.png)

好的，它应该是这样的，

```
class ViewController: UIViewController {
 let defaults = UserDefaults.standard
 override func viewDidLoad() {
  super.viewDidLoad()

  defaults.set(20, forKey: "myAge")
  print(defaults.integer(forKey: "myAge"))
 }
}
```

是的，如果你爸爸想改变值，你可以改变应用程序的指定值`myAge`。

```
defaults.set(47, forKey: "myAge")
```

就是这样。您只编写了两行代码来用`UserDefaults`离线保存数据。你可以保存性别，血型，身高，体重，节目大小，游戏级别。但是，当然，这不是故事的结尾。就像仓鼠/人类一样，他们不能吃各种各样的东西，比如岩石和钻石。你不能给 15 天大的孩子吃米饭。你知道吗？(亲爱的，苹果 UIKit 的工程师们，为什么我们不能……？)

```
// You can only feed these
func set(Bool, forKey: String)
func set(Float, forKey: String)
func set(Int, forKey: String)
func set(Any?, forKey: String)
func set(Double, forKey: String)
func set(URL?, forKey: String)
```

但是，令人惊讶的是，你可以吐槽各种事情。我不知道为什么。我想这和我们身体的工作方式有关。

```
// Catharsis
func array(forKey: String)
func bool(forKey: String)
func data(forKey: String)
func dictionary(forKey: String)
func float(forKey: String)
func integer(forKey: String)
func object(forKey: String)
func stringArray(forKey: String)
func string(forKey: String)
func double(forKey: String)
func url(forKey: String)
func value(forKey: String)
```

另一个问题，存储/检索`Dictionary`怎么样？没有设置字典值的直接方法。但是，当然，有两种方法。

### 第一种方法

```
let name = ["Real": "SangJoon Lee"]
defaults.set(name, forKey: "name") // Store as Any?
if let name = defaults.value(forKey: "name") as? [String: String] {
 print(name) // Downcast from Any? to [String: String]
}
// ["Real": "SangJoon Lee"]
```

### 第二种方法

```
if let name = defaults.dictionary(forKey: "name") as? [String: String] {
 print(name) // Downcast from [String : Any]? to [String: String] }

// ["Real": "SangJoon Lee"]
```

好极了。但是，我爸突然想把背景色存成粉色……**粉色？**你是说`UIColor`？没错。`UIColor`来了😶是时候认真对待了。振作起来。

## 第 2 部分:UIColor

你可能已经注意到没有直接的方法来处理它。我们必须稍微调整一下字体。举个例子，

我们将把`UIColor`转换成`Data`，并保存为`Any?`。听起来不错？我知道这听起来很荒谬。我会边走边解释。

为了从`UIColor`转换到`Data`，我们必须依靠一个特殊的编码器`NSKeyedArchiver`。想象一下这个阶级就像一个用石头和生金属制造剑和盾的铁匠。当然，他/她可以来回转换。

更多信息，请参考苹果的 API:[nskeydarchiver](https://developer.apple.com/reference/foundation/nskeyedarchiver)

> NSKeyedArchiver 是 NSCoder 的一个具体子类，它提供了一种将对象(和标量值)编码成可存储在文件中的与架构无关的格式的方法。—苹果。

所以，代码看起来像这样。

```
extension UserDefaults {
 func setColor(color: UIColor?, forKey key: String) {
 var colorData: NSData?
 if let color = color {
  colorData = NSKeyedArchiver.archivedData(withRootObject: color) as NSData?
 }
 set(colorData, forKey: key)// UserDefault Built-in Method into Any?
 }
}
```

现在，我们已经将`UIColor`转换为`Data`，并将其保存为`Any?`，因为这是唯一可用的选项。只是让你知道，`Any`就像是几乎所有东西的盛大盛大超级类。

好了，是时候吐槽一下我们最近攒的“颜色”了。所以，既然我们`archievedData`进入了人类无法理解的东西，是时候`unarcheive`了。但是，有趣的是，你可以吐槽`outdata?`而不是`Any?`，我们可以从技术上吐槽`Any?`并将其转换为`data`然后是`UIColor?`，但让我们保持简单。只是`data?`到`UIColor?`

```
extension UserDefaults {
 func colorForKey(key: String) -> UIColor? {
  var color: UIColor?
  if let colorData = data(forKey: key) {
   color = NSKeyedUnarchiver.unarchiveObject(with: colorData) as? UIColor
  }
  return color
 }

 func setColor(color: UIColor?, forKey key: String) {
  var colorData: NSData?
   if let color = color {
    colorData = NSKeyedArchiver.archivedData(withRootObject: color) as NSData?
  }
  set(colorData, forKey: key)
 }

}
```

现在，我们可以这样做，

```
defaults.setColor(color: UIColor.red, forKey: "myColor") // set
let myColor = defaults.colorForKey(key: "myColor") // get
```

牛逼？就是这样。

源代码
[Github](https://github.com/bobleesj/Blog_UserDefault_-Save_UIColor_Swift3)

> Andyy Hope 写了一篇很棒的文章，讲述了如何使用协议将用户默认值提升到一个新的水平。我从他身上学到了很多，我保证他比我聪明得多。看看他的文章。Swift: UserDefaults 协议你不会后悔的。

## 分享这篇文章