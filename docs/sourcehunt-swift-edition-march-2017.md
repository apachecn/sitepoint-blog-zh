# source hunt–Swift 版–2017 年 3 月

> 原文：<https://www.sitepoint.com/sourcehunt-swift-edition-march-2017/>

在 Swift Month 的延续中，这个 Sourcehunt 充满了 Swift 的美好。我们将向您展示用于操作字符串、创建可重用样式、显示用户头像以及将 FontAwesome 的强大功能引入 iOS 的包。我们还得到一个像老板一样解析 JSON 的包，以及下一次(或第一次)的用户认证！)Vapor app。我有没有提到这一切都在 Swift 中！

## [artisantsev/guitar](https://github.com/ArtSabintsev/Guitar)【350】

受 JavaScript 的 Voca 库的启发，这个包使得处理字符串变得轻而易举。它向`String`类添加了一些有用的方法，让您可以做任何事情，从改变大小写到添加填充。

假设我有一个应用程序，它接受用户输入的文件名。我想确保文件名中没有任何空格或大写单词。多亏了吉他，我可以很容易地做到这一点。

```
var filename = "Deathstar Plans.pdf"

if !filename.isAlphanumeric() {
    filename = filename.snakeCased().lowercased()
}

print(filename) // -> deathstar_plans.pdf 
```

这个库还可以做更多的事情，而且还有一些特性需要实现。所以，无论你是需要做一些弦乐魔术，还是只想帮忙，看看吉他就知道了！

* * *

## [psharanda/Atributika](https://github.com/psharanda/Atributika) [70 ★]

暂且不谈这些，这是 Atributika。假设你正在编写一个社交应用程序，你的用户可以在他们的帖子的任何地方添加标签。您可能希望设计这些标签的样式，使它们从文本中脱颖而出。Atributika 让这变得简单。它允许您为字符串定义样式规则。下面我为标签创建了一个样式规则:

```
let myString = "Vader told me he's my dad today! I'm totally freaking out! #confused #shouldastayedondagoba"

let formattedText = myString.styleHashtags(                       
    Style.foregroundColor(Color.blue).font(.systemFont(ofSize: 15))
).attributedString 
```

产生了这个:

![Atributika hashtag text](img/1615be6127427ced25d67391d86b1bb7.png)

Atributika 还可以解析定制的 HTML 标记，允许您轻松地设置文本样式，并在整个应用程序中重用样式。下面是一个用 Atributika 定义一些自定义样式的例子:

```
let e = Style.font(.italicSystemFont(ofSize: 14))
let hl = Style.backgroundColor(Color.green) 
```

在这里，我可以使用我的风格产生一个充满智慧的奇特属性字符串:

```
let myString = "<e>Do</e>, or <e>do not</e>. <hl>There is no try.</hl>".style(tags: e, hl).attributedString 
```

看起来像这样:

![Atributika styled text](img/71fbc263cd684b0733545c8b3e3b215f.png)

更好地利用这个库的一个方法是在一个服务对象中定义所有的样式。然后，这个对象可以用作属性化的字符串工厂，您可以通过它传递字符串。这允许您将所有的样式保存在一个地方，并在整个应用程序中使用它们。

* * *

## [ayushn 21/头像图像视图](https://github.com/ayushn21/AvatarImageView)【173】

这个包在处理社交应用程序时特别有用。AvatarImageView 提供了一个特殊的`UIImageView`用于显示用户个人资料图片。如果用户没有照片，`AvatarImageView`会显示用户的姓名首字母。您所需要的只是一个符合`AvatarImageViewDataSource`协议的数据源，就像这样:

```
struct MyUser: AvatarImageViewDataSource {
    var name = "Han Solo"
} 
```

这个对象可以指定几个选项，如图像、特定的首字母、背景颜色等…

可选地，你也可以包括像头像这样的配置:

```
struct AvatarConfig: AvatarImageViewConfiguration {
    var shape: Shape = .square
} 
```

然后你可以像这样输入一个`AvatarImageView`你的数据源和配置:

```
myAvatar?.configuration = AvatarConfig()
myAvatar?.dataSource = MyUser() 
```

就这样，你有了一个虚拟形象！

![AvatarImageView in action](img/8a178057de0a6ab598922ff2c3d7d8f7.png)

这可能看起来不多，但这个软件包消除了担心从丢失的图像回落，获得首字母缩写，重塑图像等的需要…

* * *

## 查尔斯-奥德/冯塔韦瑟斯威夫特

这个了不起的库将 FontAwesome 的了不起带到了您的下一个了不起的 swift 项目中！(我说的够牛逼了吗？)如果你对 FontAwesome 不熟悉，我会推荐你去看看。它是一组矢量图形和 CSS，允许你显示和定制图标，就像它们是文本一样。虽然最初的 FontAwesome 主要用于网络，但这个包为 iOS、WatchOS、MacOS 等带来了同样的简单性和定制化……告别在 Photoshop 或 GIMP 中编辑图标。

API 很简单:创建一个`FASIcon`对象，对其进行样式化，并将其放在您想要的位置。你也可以把你的`FASIcon`转换成`UIImage`或者`NSAttributedString`。这里有几个例子。

首先，我将从 FontAwesome 图标制作一个大的 UIImage:

```
myImageView?.image = FASFontAwesome().userCircleIcon(size: 32).color(color: UIColor.blue).image 
```

接下来，我将创建一个属性化的字符串，其中混合了一些图标以供娱乐

```
let home: NSAttributedString = FASFontAwesome().homeIcon(size: 17).attributedString
let wifi: NSAttributedString = FASFontAwesome().wifiIcon(size: 17).color(color: UIColor.green).attributedString

let myString: NSMutableAttributedString = NSMutableAttributedString()
myString.append(home)
myString.append(NSAttributedString(string: " is where the ", attributes: nil))
myString.append(wifi)
myString.append(NSAttributedString(string: " connects automatically.", attributes: nil))

myLabel?.attributedText = myString 
```

这就是最终的结果！

![FontAwesomeSwift in Action](img/f511f560bf0f0a1d2e8640d93fd3cb04.png)

尽管它的名字，这个包实际上支持几个不同的图标包，如材料，离子，等等。你甚至可以用你自己的图标来扩展它！

* * *

## [nodes-ios/Serpent](https://github.com/nodes-ios/Serpent) [70 ★]

接下来是一个非常完美和高效的 JSON 解析器，叫做 Serpent。这个包真的很酷。它不仅速度极快(他们有数据可以证明)，而且创作者们也努力让它非常容易使用。

假设我有一个形状像这样的`struct`:

```
struct StormTrooper {
    var name = "FN93847"
    var favoriteColor = "white"
    var shootingRangeScore = "2/457"
} 
```

我想从一个 API 中提取一些`StormTroopers`。我必须做的第一件事是教 Serpent 我的数据是什么样的，以及我希望它如何被解析或序列化。为此，我的`struct`必须实现与 Serpent 捆绑在一起的`Serializable`协议。该协议要求以下数据映射方法:

```
extension StormTrooper: Serializable {
    init(dictionary: NSDictionary?) {
        name               <== (self, dictionary, "name")
        favoriteColor      <== (self, dictionary, "favorite_color")
        shootingRangeScore <== (self, dictionary, "shooting_range_score")
    }

    func encodableRepresentation() -> NSCoding {
        let dict = NSMutableDictionary()
        (dict, "name")                 <== name
        (dict, "favorite_color")       <== favoriteColor
        (dict, "shooting_range_score") <== shootingRangeScore
        return dict
    }
} 
```

现在，这只是针对三个属性。为更大的结构编写所有这些代码将是乏味和耗时的。谢天谢地，Serpent 背后的团队编写了一个名为 Model Boiler 的小型 Mac 应用程序，可以为您生成这些代码。要使用它，把你的`struct`复制到系统剪贴板，然后点击 *cmd+option+6* 。这将使用为那个`struct`生成的所有代码填充您的剪贴板。然后，您可以将它粘贴到您想要的位置，并在必要时进行任何调整。

* * *

## 风暴之路/十字转门 [95 ★]

最后，如果你是一个使用 Vapor 的服务器端迅捷者，你会发现 Turnstile 特别有趣。Stormpath 的人编写了它来管理 Vapor 应用程序中的用户验证。它使用几个对象来跟踪应用程序中的当前用户，并确保他们已经过身份验证。它还提供了用于在内存中存储用户凭据的对象。如果您想将凭证存储在其他地方，您可以编写名为`Realms`的自定义对象来实现这一目的。

Turnstile 也有助于通过脸书和谷歌等第三方登录。这是一个由了解身份认证的人编写的健壮的库。

* * *

这些伟大的软件包中的每一个都非常有用，所以出去给他们一些爱吧。更好的是，深入研究他们的源代码并提交一些 PRs。无论你是在寻找新工具还是新项目，这里都有一个很好的列表。如果你发现了其他你想分享的优秀库，请使用#sourcehunt 和#swift 标签告诉我们！

## 分享这篇文章