# Xcode 4 的 iOS 开发基础

> 原文：<https://www.sitepoint.com/ios-development-basics-with-xcode4/>

坚持传统，我们将创造一个非常简单的“你好，世界！”应用程序，而不使用图形用户界面生成器；我们只是在编写代码，让你大致了解 iOS 应用程序是如何结合在一起的。

### 先决条件

首先，我们需要一份 Xcode 和 iOS 软件开发工具包(SDK)。

苹果仍然控制着他们的开发者工具，所以如果你想发布你的应用，你最终需要注册苹果开发者计划。一旦你注册了 iOS 开发者项目，Xcode 就可以免费下载，成为会员是在真实的苹果硬件上测试你的应用的唯一方法。

注册 iOS 开发者计划时，您可以使用现有的 iTunes、Apple 在线商店或 MobileMe 帐户。建立帐户后，您可以从 iOS 开发中心下载 Xcode 和 iOS 开发者工具包。

你也可以在 Mac App store 上购买 Xcode 4，价格为 4.99 澳元。这将使你能够在 Mac 上的模拟器(iPad 或 iPhone 的软件模拟)中运行你的 iOS 程序，但你将无法在实际的苹果硬件上运行你的程序，直到你注册了 iOS 开发者计划。

### 我们走吧！

通常在制作 iOS 应用程序时，你会被建议使用苹果的 UI 设计工具(以前被称为“界面生成器”)。这绝对是正确的做法，除非你正在构建非常复杂的动态界面(游戏、数据可视化工具等等)。一旦你的应用开始变得稍微复杂，用代码迷惑你的用户界面就变成了一种真正的痛苦——调整、编译、测试、重复，令人作呕！

不过，知道幕后发生了什么是很好的，所以首先我们将用代码创建一个真正基本的应用程序。本系列的下一篇文章将讨论如何使用界面构建器。

启动 Xcode，你会看到欢迎屏幕；左侧会看起来像*图 1* “欢迎来到 Xcode”。

[![iOS 1 Figure 1](img/f71670b697c1c2ad1db602a3fa848f21.png)](https://www.sitepoint.com/wp-content/uploads/2011/05/ios-1-figure-1.png)

图 1

点击新建项目，会出现 Xcode 的新建项目对话框，如图*图二*所示。你可以在任何时候通过选择文件>新建项目来打开这个对话框。

[![iOS 1 Figure 2](img/3e6bf27e37dc949a49f26acb84ac69d0.png)](https://www.sitepoint.com/wp-content/uploads/2011/05/ios-1-figure-2.png)

图 2

要了解关于不同类型的 iOS 应用程序的更多信息，请参见 [iOS 开发库](http://developer.apple.com/library/ios/navigation/index.html)中的 [iOS 开发快速入门](http://developer.apple.com/library/ios/#documentation/Xcode/Conceptual/iphone_development/100-iOS_Development_Quick_Start/development_quick_start.html)。

我们将从基于窗口的应用程序开始。这是最基本的选择。按照您认为合适的方式命名您的项目，但是请注意，在我们的示例中，我们将使用“HelloWorld”作为标记来表示您选择的任何不同的名称。我们配置其余设置，如*图 3* 所示。

[![iOS 1 Figure 3](img/e51a39ed2774237809a92f36beadcc4e.png)](https://www.sitepoint.com/wp-content/uploads/2011/05/ios-1-figure-3.png)

图 3

因为公司标识符只是一个用来标识应用程序开发者的字符串，所以它可以是任何东西。通常，公司使用反向域名的约定作为他们的标识符，例如 com.buildmobile。这个字符串与产品名称连接在一起，生成包标识符，结果应该是一个唯一的、人类可读的字符串，用于标识您的应用程序。

我们现在要保持简单，因此将设备系列更改为“iPhone”。可以编写在 iPhone 和 iPad 设备上运行的应用程序(不必为你的应用程序制作单独的版本)。我们(还)不会那样做。

出于本练习的目的，确保未选中“使用核心数据”和“包括单元测试”。我们将在后面的教程中再次讨论这些选项。

点击“下一步”,你会被问及你想在哪里保存你的申请。在这个对话框的底部有一个标签为“源代码控制——为这个项目创建本地 git 库”的选项。Xcode 4 中源代码控制的使用超出了本文的范围，但是拥有它是很好的，我们稍后也会再次讨论这个问题。点击“创建”，你就准备好了。

### 欢迎您的申请

iPhone 应用程序都有一个相当常见的结构。每个应用程序(通常)都有一个`main.m`文件(在文件浏览器中的支持文件下),用于实例化 AppController(稍后会详细介绍)。

iOS 应用程序是用一种叫做 Objective-C 的语言编写的，有时这被称为 Cocoa，但严格来说 Cocoa 指的是苹果提供的库和 API。我们在“头文件(*)中声明对象和数据结构。h ),然后在单独的实现文件(*)中编写实现它们的代码。m)。根据许多资料,“m”代表“消息”,这是 Objective-C 语言的核心特性。

支持文件目录(Xcode3 中以前的“资源”)是所有非代码文件应该存在的地方。一旦你开始开发更复杂的应用程序，你会想把图像、文本文件和其他杂集放在这里。*图 4* 显示了到目前为止我们的文件和目录结构。

[![iOS 1 Figure 4](img/1f665505f9d34286fd0b247589c8dd3b.png)](https://www.sitepoint.com/wp-content/uploads/2011/05/ios-1-figure-4.png)

图 4

AppController 是操作系统消息进入应用程序的第一个入口点。有必要浏览一下`HelloWorldAppDelegate.m`,了解一下你的应用程序需要响应的事件。如果您正在创建非常简单的应用程序，这些方法中的一些或多或少是可选的，但是如果您计划基于任何显著复杂的用户输入来存储数据，您将希望实现像`applicationWillResignActive`和`applicationDidBecomeActive`这样的方法。这些方法允许您响应应用程序生命周期中的变化，例如当它即将终止时。

AppDelegate 本身做不了多少事情。我们需要添加一个视图。因为大多数 iOS 应用程序遵循模型-视图-控制器模式，所以我们也需要一个用于该视图的控制器。它们在 iOS 中被称为视图控制器；它们可以让你在编程创建的视图(比如我们在这里使用的)和通过 Xcode 的 GUI 设计的视图(存储在 XIB 文件中)之间切换。注意，强烈建议使用 ViewControllers。

使用“文件”>“新建文件”(或 Command+N)创建新文件。Xcode 给我们的选项之一是一个`UIViewController`子类。整洁！选择那个。出现的第一个屏幕将询问您想要从哪个类中派生出子类。有时，您可能想创建一个自定义 ViewController 类的子类，但是现在，选择`UIViewController`，如图 5 中的*所示。*

[![iOS 1 Figure 5](img/6682b54e41907ce70f677c3e8221d118.png)](https://www.sitepoint.com/wp-content/uploads/2011/05/ios-1-figure-5.png)

图 5

默认情况下，“针对 iPad”和“用户界面使用 XIB”应未选中。如果您发现不同的缺省值，这一次取消选中两个缺省值。对照*图 6* 检查您的选择，满意后点击“下一步”。

[![iOS 1 Figure 6](img/c51fc558770b57380dab1b8f798c5d0f.png)](https://www.sitepoint.com/wp-content/uploads/2011/05/ios-1-figure-6.png)

图 6

您将被要求“另存为”。在“另存为”字段中为您的类命名。我们将使用图 7 中的*中的`HelloWorldViewController`，所以在本教程的剩余部分，如果不同，你需要用你自己的名字替换它。注意 Xcode 会自动为我们生成一个头文件(*。h)，也是。*

[![iOS 1 Figure 7](img/5115f7047e4041aa9d5ac19afc4b8b53.png)](https://www.sitepoint.com/wp-content/uploads/2011/05/ios-1-figure-7.png)

图 7

您应该有两个新文件。这些组件构成了您的 ViewController，它负责接收输入事件并通过操纵视图来响应它们。通常，一个 ViewController 应该负责一个视图。不过，这并不是一个硬性规定；如果您想以不同的方式组织您的应用程序，这取决于您。

[![iOS 1 Figure 8](img/cfc6b838ca083c05656bda6b21b9f730.png)](https://www.sitepoint.com/wp-content/uploads/2011/05/ios-1-figure-8.png)

图 8

如果你看一下你的 ViewController 的实现文件(也就是`HelloWorldViewController.m`文件),你会看到苹果给我们提供了一堆预建的函数，注释掉了，如上面的*图 8* 所示。我们可以实现我们需要的那些，但是哪些是我们需要的呢？我们有一个 ViewController，但是我们需要一个视图。让我们继续在 ViewController 中创建一个视图。取消对`loadView`的注释，如下所示:

```
- (void)loadView {
    UIScreen *screen = [UIScreen mainScreen];
    self.view = [[UIView alloc] initWithFrame:[screen applicationFrame]];
    self.view.backgroundColor = [UIColor greenColor];
}
```

如果你现在点击“运行”……你会失望的。我们已经构建了视图，但是我们仍然需要告诉 AppDelegate 我们创建的 ViewController 的存在，并要求 AppDelegate 实例化视图。切换到`HelloWorldAppDelegate.h`并添加 ViewController 作为 AppDelegate 的属性，如下所示:

```
#import <UIKit/UIKit.h>
#import "HelloWorldViewController.h"

@interface HelloWorldAppDelegate : NSObject  {
    HelloWorldViewController *viewController;
}

@property (nonatomic, retain) IBOutlet UIWindow *window;
@property (nonatomic, retain) HelloWorldViewController *viewController;

@end
```

您会注意到我们在`@interface`定义中添加了 ViewController 作为 AppDelegate 的属性。稍后，我们使用`@property`，这是一点语法上的糖，自动声明该属性的 getters 和 setters，使其成为‘public’(默认情况下，Objective-C 中对象的属性是 private)。

现在切换到您的 AppDelegate 实现文件。那是`HelloWorldAppDelegate.m`文件。您知道吗，有一个很好的快捷键可以在实现(* .m)和标题(*。h)文件，反之亦然:它的命令+Ctrl+Up。

首先，我们需要为 AppDelegate 的`viewController`属性创建 getter 和 setter 方法。我们可以自己声明这些方法，但是有一个更简单的(语法上神奇的)方法，像这样使用`@synthesize`:

```
@implementation HelloWorldAppDelegate

@synthesize window=_window;
@synthesize viewController;
```

我们可以使用`@synthesize`,因为我们在头文件中将`viewController`声明为一个`@property`。`@synthesize`按照惯例，调用应该始终遵循您的`@implementation`声明，并在任何方法定义之前。

我们已经声明希望在 AppDelegate 中使用我们的新类，并创建了访问器方法来在我们的 AppController 上获取和设置它。现在我们需要告诉 AppController 实例化这个类。为此，我们将修改方法`applicationDidFinishLaunchingWithOptions`:

```
- (BOOL)application:(UIApplication *)applicationdidFinishLaunchingWithOptions:(NSDictionary *)launchOptions {

    [self.window makeKeyAndVisible];
    self.viewController = [HelloWorldViewController alloc];
    [self.window addSubview:self.viewController.view];

    return YES;
}
```

我们添加了一行来分配(`alloc`)ViewController，并添加了一行来将 View controller 的视图添加到窗口中。这很关键，因为窗口需要知道它包含的视图，视图需要知道它们包含的任何其他视图或 UI 元素。我们正在创建一个嵌套的视图层次结构。

如果你现在点击“Run ”( Command+R ),你应该会在模拟器里看到一个亮绿色的屏幕。干得好！您已经创建了一个视图。这里很无聊，尽管如此，还是让我们稍微活跃一下气氛吧。你现在可以关闭模拟器，如果你想的话，用 Command+Q 就像一个单独的应用程序。

让我们添加一个标签。这是一个简单的可视元素，包含一些文本。在您的`HelloWorldViewController.m loadView`方法中，添加代码，使其更新为以下内容:

```
- (void)loadView {
    UIScreen *screen = [UIScreen mainScreen];
    self.view = [[UIView alloc] initWithFrame:[screen applicationFrame]];
    self.view.backgroundColor = [UIColor greenColor];

    UILabel *label = [[UILabel alloc] initWithFrame:CGRectMake(50, 200, 200, 80)];
    label.text = @"Hello, World!";
    label.textAlignment = UITextAlignmentCenter;
    label.shadowOffset = CGSizeMake(1,1);
    label.backgroundColor = [UIColor greenColor];
    [self.view addSubview:label];
    [label release];
}
```

我们已经为 UILabel 元素分配了内存。我们已经用 50 个单位的宽度和 200 个单位的高度初始化了它，偏移 x 轴 200(上下)和 y 轴 80(左右)。现在点击“Run”，您应该会看到标签出现在您的视图中，如图 9 所示，“我们亮绿色的“Hello，World！”应用程序。

[![iOS 1 Figure 9](img/3ea47ee308495418e11b490d7a2c6296.png)](https://www.sitepoint.com/wp-content/uploads/2011/05/ios-1-figure-9.png)

图 9

干得好！值得注意的是，默认情况下，应用程序也从 XIB (*)加载一些元素(特别是窗口)。xib 或*。nib)项目中包含的文件。我们将自定义视图覆盖在这些视图之上。在我们的下一篇文章中，我们将学习使用界面构建器和 XIB 文件来创建一个应用程序。

## “欧尼”系列

[安迪·怀特](http://twitter.com/arcwhite)提供了一个在 iOS 平台上开发应用的深度思考。在手边有一杯可口的提神饮料的前提下，使用标签到所有[或任何](https://www.sitepoint.com/mobile/ios/)文章，或者直接从这个索引跳到一篇文章。

*   Xcode 4 的 iOS 开发基础
*   [带有美味用户界面的 iOS 应用](https://www.sitepoint.com/mobile/ios/)
*   [一个互动的 Orny](https://www.sitepoint.com/mobile/ios/)
*   [使用 CoreData 管理信息](https://www.sitepoint.com/managing-information-with-coredata)

## 分享这篇文章