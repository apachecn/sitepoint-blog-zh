# 用 Xamarin 创建 iOS 信用卡读卡器

> 原文：<https://www.sitepoint.com/creating-ios-credit-card-reader-xamarin/>

简而言之， [Xamarin](http://xamarin.com) 使您能够使用一种类型安全的主流语言 C#为三大移动平台开发应用程序。Xamarin 可以让你在 iOS、Android 和 Windows 上“开箱即用”地共享大约 60%的代码，因为它是基于开源构建的。NET 运行时， [Mono](http://www.mono-project.com/Main_Page) 。

如果你使用依赖注入这样的模式和 MVVM 这样的框架，代码共享的比例会更高。使用 Xamarin 的最大好处是您不会失去任何原生用户体验。一款分享的应用。NET 代码看起来和感觉上仍然像是为运行它的操作系统开发的——因为它确实是！

## 轻便

现在它们已经完全成熟，可移植类库([PCL](http://blogs.msdn.com/b/dotnet/archive/2013/11/13/pcl-and-net-nuget-libraries-are-now-enabled-for-xamarin.aspx))允许 Xamarin 进行另一次进化的“代码共享”飞跃。对于那些门外汉，微软许可并发布了。面向多个平台的. NET framework。使用这些子集，现在可以在 <u>**一个**</u> 库中编写代码，这些代码可以针对 Windows Phone 8、Windows Store 应用、Silverlight、Xamarin.iOS 和 Xamarin.Android

使用 [SQLite](http://www.sqlite.org) [ORM](http://components.xamarin.com/view/sqlite-net) 、数据访问存储库、业务领域对象和视图模型绑定类创建一个库。所有这些都在上述平台上编译和运行。没有必要去想象这种可能性，因为这是现实，我稍后会给你看一个例子。

由于你们都是优秀的程序员，我确信你们已经意识到了 PCLs 最有益的部分。*可测性！*所有的代码覆盖都将是平台无关的。此外，由于我们可以在像 [nUnit](http://www.nunit.org) 这样的标准测试运行器中运行测试，模仿和伪造框架很容易获得，这使得集成测试变得轻而易举。这是我在为 Xamarin.iOS 开发时真正错过的事情，因为在 PCLs 之前，所有为 iOS 编写的 C#代码都需要提前编译成字节码( [AOT](http://developer.xamarin.com/guides/ios/advanced_topics/limitations/) )。这意味着。NET 反射命名空间必须被省略，因此强大的模拟框架的帮助是不可能的。

## 版本 3 => Xamarin。形式

去年，我使用 Xamarin 在公共类文件中编写了代码，并通过使用平台特定的库项目来共享它们。这种技术工作得很好，但是仅仅为了共享代码就需要两次(有时是三次)的项目，这就有点麻烦了。今年我使用 PCL 在不同的平台上创建和重用相同的类。

两周前，Xamarin 3 发布了下一个(甚至更多)令人兴奋的代码重用迭代——共享 UI。没错，Xamarin 已经创建了一个通用的用户界面抽象，叫做 [Xamarin。表单](https://xamarin.com/forms)，包装原生 iOS、Android、Windows 界面。这一抽象层是可移植的，可以通过 [nuget](http://www.nuget.org) 包轻松安装。

## 教程–iPhone 信用卡读卡器

有人订购了一个应用程序吗？在本教程中，我们将，

*   使用 Xamarin 的试用版(30 天商业版)
*   使用 Xamarin 制作一个 iOS 应用程序。表单(PCL)
*   使用设备摄像头读取信用卡，使用 SQLite.Net 存储卡号

本教程可以在使用 Visual Studio 的 Windows 上重复，但我选择的平台是使用 Xamarin Studio 的 MacBook。Xamarin Studio IDE 在布局和功能上与 Visual Studio 非常相似。

不要忘记，因为我们将创建一个 iOS 应用程序，你仍然需要安装最新的 Xcode。可以从苹果 AppStore 下载。

访问[此处](http://www.xamarin.com/trial)下载并安装 Xamarin 的试用版。安装程序将下载任何必要的依赖项，然后为 iOS 和 Android 安装 SDK。

## 开始

在这里下载本教程的源代码[，并打开解决方案文件 *CreditCardReader.sln* ，因为我们将会关注它。](https://github.com/sitepoint-examples/Xamarin-Credit-Card-Demo)

最终结果是一个包含三个新项目的解决方案。自从 Xamarin。Forms 是多平台的，它创建了一个 iOS 和 Android 项目，在本教程中，我们将只使用 iOS 项目。右击 **CreditCardReader.iOS** 项目，将其设置为*“启动项目”*。创建的第三个项目是公共库或 PCL。所有公共代码都放在那里，包括公共 UI 代码！

![Projects browser](img/0d2c598a6e3a0597d38a60258b2645f2.png)

## Nuget 包

在我们的应用程序读取信用卡后，我们需要将结果存储在数据库中。我们将使用 Nuget 包来确保 PCL 和 iOS 项目为此做好准备。右键单击 PCL 项目，并从上下文菜单中选择 *Add - > Add Packages* 。

![nuget sqlite component](img/23296a940b893adc0a5fc331c2973ae6.png)

在搜索框中键入`sqlite.net`并将**SQLite.Net PCL**包添加到项目中。
现在对 iOS 项目做同样的事情，只是你需要找到并选择一个指定 **XamarinIOS 平台**的项目。

![nuget in Xamarin](img/fe98d54aa6ea28b8193926e74f37700b.png)

## Card.io

为了实现信用卡读取功能，我们使用了一个 Xamarin 组件。Xamarin 组件是一些库，您可以下载并使用它们来简化工作。大多数是免费的，但也有一些是要花钱的。Xamarin 为开发人员创建了一个“商店”来发布组件，Xamarin 自己也贡献了许多免费组件。用于读取信用卡号码的组件由 Xamarin 发布，它只是 PayPal 开发的库的 C#包装器。要使用该组件，请访问 [Card.io](https://www.card.io) 并注册创建 app-key。注册是免费的。

![Card.io](img/23304d6c6fdd7c8fa777d31be39c60ad.png)

双击 iOS 项目中的 Components 文件夹，您会看到已经添加了 card.io 组件。您可以通过点击*获取更多组件*按钮添加更多组件。

当你安装一个组件时，Xamarin Studio 会打开一个新的标签页，里面有关于该组件的详细信息以及一些代码片段，可以让你快速上手。点击*详细信息*链接，立即访问该片段。

![Card.io Tab](img/189d7d1c264138c704381462412208ca.png)

## 便携式图书馆

### 主视图

在 PCL 内部，打开`App.cs`，你会看到创建主屏幕所需的少量平台无关代码。根据运行该代码的设备，它将创建和显示本机控件。这就是 Xamarin.Forms 的强大之处。

```
var label = new Label { Text = "Card No:" };
var cardEntry = new Entry { Placeholder = "" };
var scanButton = new Button { Text = "Scan", TextColor = Color.White, BackgroundColor = Color.FromHex ("77D065") };

// code omitted

return new ContentPage
{
    Content = new StackLayout {
    Spacing = 20, Padding = 50,
    VerticalOptions = LayoutOptions.Center,
    Children = { label, cardEntry, scanButton }
  }
};
```

### 2.接口

在`Interfaces.cs`文件中，我们声明了几个契约，iOS 项目将使用它们来创建具体的类。并非一切都是平台独立的，这当然适用于 **Card.io** 组件库。所以我们需要创建一个 PCL 可以在运行时引用和调用的接口，而不需要在编译时了解任何关于实现的信息。稍后你会看到这些被使用。

### 3.数据库ˌ资料库

有我们快速简单的代码，用于将收到的信用卡存储在 SQLite 数据库中。**信用卡**类用唯一的 id 保存卡号。我们可以创建**信用卡**对象，并使用 SQLite.Net 内置的 ORM 工具将它们直接保存到数据库中。

```
public class CreditCard
{
    public CreditCard ()
    {
        Id = Guid.NewGuid ().ToString ();
    }

    [PrimaryKey]
    public string Id { get; set; }
    public string Number { get; set; }
}
```

**CardNumberDatabase** 类表示为我们创建一个表并保存**信用卡**对象的 singleton 对象。每次我们启动应用程序并创建数据库对象时， **CreateTable** 方法(在构造函数中)将检查是否需要创建表，或者(如果存在)根据 **CreditCard** 类中的公共属性修改表列。

```
public class CardNumberDatabase : SQLiteConnection
{
    public CardNumberDatabase (string path) : base (DependencyService.Get<ISQLitePlatform>(), path, false, null)
    {
        CreateTable<CreditCard> ();
    }

    public int AddCardNumber (string number)
    {
        return Insert (new CreditCard { Number = number });
    }
}
```

### 4.保存号码

回到`App.cs`,只需点击一个按钮，所有的东西都聚集在一起。

```
var database = new CardNumberDatabase (DBPath);

    // code omitted

            scanButton.Clicked += (sender, e) => {
            var platformCardReader = DependencyService.Get<ICardReader>();
            if (platformCardReader != null)
            {
                platformCardReader.CardFound = delegate (string cardNo) {
                    cardEntry.Text = cardNo;

                    // db save
                    var success = database.AddCardNumber (cardNo) == 1;

                    var alerter = DependencyService.Get<IAlert>();
                    if (alerter != null)
                    {
                        alerter.Show ("Database", success ? "Card Number Saved!" : "Failed!");
                    }
                };

                platformCardReader.ReadCard();
            }
        };
```

使用 Xamarin。Forms **DependencyService** 我们可以找到 **ICardReader** 接口的运行时特定实现，并获取它来使 **Card.io** 组件做它的事情。将使用运行时特定的 **IAlert** 接口实现来报告成功或失败。

注意，当我们进入 **GetMainPage** 方法时，我们创建了一个本地数据库对象。该对象之所以保持活动状态，是因为它正在匿名按钮单击委托中使用。封闭很酷。

## iOS 应用程序

### 1.设置

在`AppDelegate.cs`文件中，我们需要添加一行代码来确保 SQLite 引擎知道在本地设备文件系统中的何处创建数据库文件。在 PCL **App** 类上设置这个静态属性就完成了。

```
App.DBPath = Path.Combine (Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments), "cards.db");
```

### 具体类

iOS 的大部分具体工作是为 PCL 创建具体的类，以便在运行时调用。

这个 **IAlert** 接口(`MyAlert.cs`)非常简单。我们只需要创建并显示一个 iOS UIAlertView 对象。

```
public class MyAlert : IAlert
{
    #region IAlert implementation

    public void Show (string title, string message)
    {
        var alert = new UIAlertView (title, message, null, "OK", null);
        alert.Show ();
    }

    #endregion
}
```

ICardReader ( `MyCardReader.cs`)的实现需要更多的工作，但是不多。这里我们需要使用 **Card.io** 组件来完成工作。还记得当我们安装组件时，我们得到了一个代码片段来帮助我们开始吗？这里使用了这段代码的大部分。

当我们的类方法 **ReadCard** 被(PCL)调用时，我们创建 **Card.io** 特定的委托和控制器对象，然后将它们呈现给用户。

**RootViewController** 是任何 iOS 应用程序的基本控制器/视图。所以在这里，一旦我们获取了它的引用，它就可以完成显示读卡器 UI 的工作。

**OnScanCompleted** 是信用卡“读取”完成后触发的事件。在一些空检查之后，我们调用我们的集体诉讼属性 **CardFound** ，将信用卡号传递回 PCL，并保存在数据库中。

如果愿意，您可以将自己的应用程序令牌添加到该文件中。

```
public class MyCardReader : ICardReader
{
    #region ICardReader implementation

    public Action<string> CardFound { get; set; }

    public void ReadCard ()
    {
        var appcontroller = UIApplication.SharedApplication.KeyWindow.RootViewController;

        var paymentDelegate = new PaymentViewControllerDelegate();
        var paymentViewController = new PaymentViewController(paymentDelegate);

        paymentViewController.CollectCVV = false;
        paymentViewController.CollectExpiry = false;
        paymentViewController.MaskManualEntryDigits = true;
        paymentViewController.AppToken = "e63c673c88c44b179dcbaa9f7a1f76af";

        paymentDelegate.OnScanCompleted += (viewController, cardInfo) => {
            if (cardInfo != null)
            {
                if (CardFound != null)
                {
                    CardFound(cardInfo.CardNumber);
                }
            }

            appcontroller.DismissViewController (true, null);
        };

        appcontroller.PresentViewController(paymentViewController, true, null);
    }

    #endregion
}
```

### 准备…设置…运行

你应该可以让这个应用程序运行起来，但是这里有一个它运行的视频:

<video class="wp-video-shortcode" id="video-84092-1" width="854" height="480" preload="metadata" controls=""><source type="video/mp4" src="https://uploads.sitepoint.com/wp-content/uploads/2014/06/1403737030CreditCardReader.mp4?_=1">[https://uploads.sitepoint.com/wp-content/uploads/2014/06/1403737030CreditCardReader.mp4](https://uploads.sitepoint.com/wp-content/uploads/2014/06/1403737030CreditCardReader.mp4)</video>

这里有一些应用程序的屏幕截图。

![App Main Screen](img/85e8a394ef012f37c1f5cbf3680c007b.png)

![Card reader screenshot](img/3679f945aba21cae029e280f68700758.png)

![Credit Card](img/44057bcd177e4234eef70e9590f44d9c.png)

## 分享这篇文章