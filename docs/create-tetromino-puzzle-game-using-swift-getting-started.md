# 使用 Swift 创建一个俄罗斯方块益智游戏——入门

> 原文：<https://www.sitepoint.com/create-tetromino-puzzle-game-using-swift-getting-started/>

## [](#i-introduction)简介

这一系列文章将向您介绍苹果的两项最新技术，Swift 和 SpriteKit。Swift 是一种新的编程语言，被提升为“未来苹果软件开发的语言”。SpriteKit 是苹果第一个游戏开发框架，是其 SDK 的标准。它支持从版本 7 开始的 iOS 和 Mac OS X。

我们不会讨论每个细节，相反，您将通过创建一个简单的游戏来学习 Swift 的基础知识。关于语言的详细讨论，有苹果官方的关于这个主题的书。虽然不是本课程的必备条件，但如果您认真学习 Swift，强烈推荐阅读本书。

至于 SpriteKit，我们不会在游戏中大量使用它。我们只需要它最基本的功能，在屏幕上显示简单的精灵。本课程的重点仍然是 Swift。

您将要创建的游戏的灵感来自于 80 年代非常流行的益智游戏。您很可能熟悉这个游戏，甚至可能玩过一两次，如果您像我一样是这个游戏的粉丝，可能会玩过更多次。下面先睹为快，看看你要做什么:

![Game Preview](img/521107d0fe47a7394a59dc63d7eabc0c.png)

欢迎任何对学习 Swift 感兴趣的人。你不必为了从这个系列中受益而把目标定在游戏开发上。创建一个简单的游戏并不是向某人介绍一项新技术的令人生畏的第一步。当你玩得开心的时候，学习会变得更有效，我希望你在这个系列中玩得开心。

你不必成为一个专业的程序员也能跟上。诸如变量、常数、控制结构和其他基本构造等编程概念的基础知识是非常重要的，它们的目的足以让你轻松地进行讨论。

随着最近发布的 iOS 8.1、OS X Yosemite、Xcode 6.1 以及大量新的苹果设备，现在绝对是开始学习它们并投资苹果软件开发未来的好时机。

## [](#ii-creating-the-project)创建项目

您至少需要 Xcode 6。如果您有旧版本，请从 [Mac App Store](https://itunes.apple.com/us/app/xcode/id497799835?mt=12) 下载最新的 Xcode。让我们创建并设置游戏项目:

1.  运行 Xcode 并创建一个新项目。 
    ![Welcome to Xcode](img/96a1a7a04b756da11094624b76b56eb4.png)

2.  选择 iOS 平台下的游戏模板，点击下一步。

    ![Choose template](img/bb2d2c312db0a00fbb150c6cc0a3addd.png)

3.  在**选项**屏幕中，用项目名称填写**产品名称**字段。你可以将你的项目命名为“砖块”、“T4”或你想到的更酷的名字。为了简单起见，我将把我的命名为“砖块”。

    输入您的**组织名称**，可以是您的公司或您自己的名字。对于**组织标识符**，建议使用您自己以反向 DNS 表示法输入的域名。使用您自己的域可以确保生成的**包标识符**是唯一的。

    为要使用的语言和技术选择 Swift 和 SpriteKit。现在，让我们主要关注作为目标平台的 iPhone。单击“下一步”创建并保存项目。

    ![Choose options](img/020e169d50ed96dc7ef90d977c473085.png)

4.  为项目选择一个合适的位置。我个人更喜欢将正在进行的项目保存在桌面上，以便于访问。在创建项目之前，确保通过将**源代码控制**选项标记为选中来启用版本控制。如果你以前没有使用 Git 的经验，并且有 15 分钟的空闲时间，请查看 [try.github.io](https://try.github.io) 以了解更多信息。

    ![Save project](img/7a186d588145323469f025b3b523ec91.png)

    您现在有了一个包含一些样板代码的通用游戏项目。按下 **⌘ + R** 在模拟器中运行项目。如果你看到一个超大的模拟器，你可以按下 **⌘ + 3** 缩小它。按下 **⌘ + 1** 回到原来的大小。

    玩一玩，感受一下模拟器，然后返回 Xcode。我们将修改一些适合我们游戏的项目设置，并删除不需要的文件。

5.  我们的游戏只能以纵向运行，所以让我们取消选择**部署信息**部分下的两个横向设备方向选项。

    ![Deployment Info](img/e5a206593c88966c28ff6707c9fbe586.png)

    如果您没有看到这个屏幕，请确保您在**项目导航器**选项卡( **⌘ + 1** )中，并且选择了主项目。

    ![Project Navigator](img/a57c8c428ff65d1cbc663a16829ded78.png)

6.  选择并删除 GameScene.sks。确保将文件移至垃圾箱。

    ![Delete GameScene.sks](img/e7befa80e7a4f06b4250e893a298c735.png)

7.  我们也不需要飞船图像，所以也删除它。事实上，我们在这个项目中不需要任何图像。一切都将使用精灵和数组以编程方式绘制。

    ![Delete Spaceship](img/fb9c11c9c97009760a379c23e84f8604.png)

在构建和运行项目之前，首先通过按下 **⇧ + ⌘ + K** (shift + command + K)来执行清理操作，以删除项目的先前构建，这些构建可能仍然包含我们已经删除的资源。

当你运行这个项目时，你会看到一个空白的视图。在下一节中，我们将开始在这个空白屏幕上绘制一些东西。

## [](#iii-drawing-a-block)画一块

快速浏览一下简介中的简短游戏动画。你注意到一种模式了吗？快速分析我们的游戏会告诉我们，游戏中的所有东西都是用方块或积木搭建的。毫不夸张地说，积木是游戏的组成部分！

这些积木被系统地排列起来，形成一组有限的形状，称为四联骨牌。但是在我们尝试绘制这些更复杂的方块形状之前，让我们先关注一下绘制单个方块这个更简单的任务。

### [](#the-gameviewcontroller)游戏视图控制器

继续用下面的代码更新 **GameViewController.swift** 文件中的默认代码。不要复制&粘贴虽然。相反，尝试确定哪些行是新的，哪些行被删除或替换了。手动键入并主动选择要添加的代码将会让您对该语言有更好的感觉，并提高您的学习效果。

```
import SpriteKit

class GameViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()

        // Configure the view.
        let skView = view as SKView
        skView.showsFPS = true
        skView.showsNodeCount = true

        /* Sprite Kit applies additional optimizations to improve rendering performance */
        skView.ignoresSiblingOrder = true

        let scene = GameScene(size: skView.bounds.size)

        /* Set the scale mode to scale to fit the window */
        scene.scaleMode = .AspectFill

        skView.presentScene(scene)
    }

    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
        // Release any cached data, images, etc that aren't in use.
    }

    override func prefersStatusBarHidden() -&gt; Bool {
        return true
    }
}
```

只添加了少量新代码，而删除了几行不需要的代码。切换**版本编辑器**来查看您所做的所有更改的详细视图。你也可以*通过终端或者 Xcode 的内置**源代码控制**命令将这些更改*提交到你的存储库中。如果您还不习惯使用 Git，那么不要担心在代码中执行任何存储库过程。这在本系列中不是必需的，但却是尽早养成的好习惯。

![Version editor](img/40cb8a82895fd55c81a36ea2a6d55c0a.png)

#### [](#semicolons)分号

您首先会注意到的是缺少分号，尤其是当您来自 C、C++或 Java 背景时。另一方面，Python 开发人员会欢迎这种熟悉的风格。

Swift 中的分号不是必需的。意思是你可以，但你不必把它们放在每条语句的末尾，除非你在同一行上组合两个或更多的语句:

```
let skView = view as SKView; skView.showsFPS = true
```

但是为了代码的可读性，避免在一行中组合几个语句。

#### [](#importing-modules)导入模块

不出所料，`import`允许您从外部来源导入或访问代码，并在您自己的项目中使用它。在我们的例子中，我们希望利用 SpriteKit 框架，所以我们在源代码文件的开头声明了这一点。

```
import SpriteKit
```

#### [](#classes)跟班

导入我们需要的框架后，我们声明一个名为`GameViewController`的类，它继承自`UIViewController`。`UIViewController`类为我们的游戏处理视图管理。你可以通过⌥ +点击(option + click)它们的名字来了解这个类和其他框架类的更多信息。

类是一种具有自己的行为和属性的对象。在我们的代码中，`GameViewController`被声明为被称为超类的`UIViewController`的子类。因此，`GameViewController`继承了`UIViewController`的行为和属性。

```
class GameViewController: UIViewController {
```

#### [](#functions)功能

作为子类，`GameViewController`可以改变或扩展其超类的预定义行为。在这种情况下，我们覆盖超类的'`viewDidLoad`'函数来执行我们自己的操作。对于一个类的函数，更准确的说法是*方法*。

Swift 中的函数是使用`func`关键字定义的，后跟函数名、参数、返回类型，然后是用大括号括起来的函数体。至于`viewDidLoad`，我们把它描述成一个不带参数，不返回任何东西的函数。

```
override func viewDidLoad() {}
```

当覆盖父类的方法时，子类仍然能够通过使用`super`前缀来访问父类自己的方法实现。这允许您通过添加自己的过程在原始实现的基础上进行构建。

```
super.viewDidLoad()
```

随着本系列的深入，我们将学习更多关于函数和方法的知识。预计会遇到包含更多元素(如返回类型和参数)的示例。

#### [](#constants-and-variables)常量和变量

Swift 是不变性的主要倡导者，这增加了另一层保护，防止因无意修改值而带来的错误。我们使用`let`关键字来声明不可变的值或常量。

```
let skView = view as SKView
```

另一方面，我们对可变值或变量使用`var`关键字。这是前面写为变量声明的语句。然而，我们只有在确定变量的值会在游戏中的某个点发生变化时才声明变量。

```
var skView = view as SKView
```

#### [](#type-inference)式推断

乍一看，您可能会认为 Swift 是一种松散类型的语言，或者不需要为变量和常量设置类型。但是 Swift 是一种强类型语言，或者更准确地说，是一种类型安全的语言。

Swift 使用*类型推断*在编译期间设置变量和常量的类型。这种机制使编译器能够通过检查赋给变量或常数的值来确定它们的适当类型。

如果你总是为你的常量和变量设置一个初始值，那么你就不必每次都显式地声明它们的类型。

#### [](#accessing-properties)访问属性

可以使用*点语法*访问`skView`的属性。我们在实例名的末尾加了一个点，紧接着是属性名。

这里，我们设置了一些有用的诊断信息来显示在视图中。但是请注意，FPS ( *帧/秒*)信息只有在游戏在实际设备上测试时才是准确的，而不是在模拟器上测试。

```
skView.showsFPS = true
skView.showsNodeCount = true
```

#### [](#comments)评论

注释允许你对你的代码进行注释，以提高可读性和清晰性，这有利于项目中的每一个人。注释还可以用来方便地禁用代码的某些部分，而不用完全删除它们。

写注释有两种方式，多行和单行。Xcode 提供的默认代码包括以多行语法表示的单行注释:

```
/* Sprite Kit applies additional optimizations to improve rendering performance */
skView.ignoresSiblingOrder = true
```

这可以更方便地表达为:

```
// Sprite Kit applies additional optimizations to improve rendering performance
skView.ignoresSiblingOrder = true
```

除非您发现注释对于单行来说太长，并且更喜欢将它分成两行:

```
/* Sprite Kit applies additional optimizations
to improve rendering performance */
skView.ignoresSiblingOrder = true
```

至于这行代码，我们将`skView`的`ignoresSiblingOrder`属性设置为`true`，因为我们不关心渲染顺序，在我们的游戏中不会有重叠的元素。

我故意不在代码中加入任何我自己的注释，以避免重复教程中已经解释过的内容。然而，我们建议你把写合理的评论作为一种习惯，并和版本控制一起成为你工作流程的一部分。

#### [](#instantiation)实例化

我们使用*初始化器语法*创建一个类的新实例。最简单的形式是通过使用空括号不向类名传递任何东西。

```
let scene = GameScene()
```

但是在我们的例子中，我们需要根据视图的边界矩形传入一个大小值。注意*命名参数* `size`的使用，这在初始化一个类的新实例时是必需的。这是从 Objective-C 继承来的一个有用的语言特性，有助于提高可读性。

```
let scene = GameScene(size: skView.bounds.size)
```

#### [](#enumerations)枚举

与类一样，枚举也为一组相关值定义了一个自定义类型。这里，`.AspectFill`是由枚举类型定义的值之一。

```
scene.scaleMode = .AspectFill
```

注意`.AspectFill`没有前缀。这是*式推理*的又一个演示。通过在编辑器中⌥ +单击(option + click)进一步查看`scene`的`scaleMode`属性，我们发现它被声明为类型`SKSceneScaleMode`的变量，类型`SKSceneScaleMode`是包含`.AspectFill`作为其成员之一的枚举。

这意味着我们也可以这样表达:

```
scene.scaleMode = SKSceneScaleMode.AspectFill
```

但是 Swift 并不要求如此冗长。

#### [](#accessing-methods)访问方法

方法访问使用与属性访问相同的*点语法*。调用方法的方式与调用函数的方式相同，在一对括号内传入参数。

```
skView.presentScene(scene)
```

## [](#coming-in-part-2)降临部分 2

在本期讨论了 Swift 的许多基本结构之后，接下来我们将讨论游戏场景的绘制以及更多内容！

## 分享这篇文章