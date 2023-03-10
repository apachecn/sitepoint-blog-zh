# 为 iOS 8 构建 Today 扩展

> 原文：<https://www.sitepoint.com/building-today-extension-ios-8/>

iOS 8 中引入了许多受欢迎的增强功能，其中最强大的新功能之一是扩展的概念。扩展提供了多种方式来从您自己的应用程序与 iOS 或其他应用程序共享功能和数据。

iOS 中的扩展以多种不同的形式存在。每一个都处理系统的一个特定部分。这里我们将关注 Today 扩展，也称为小部件。小部件出现在“今日”视图中的通知中心。在我们创建扩展之前，理解它们背后的概念是很重要的。

## 什么是扩展？

扩展可让您将应用程序中的自定义功能或内容共享到 iOS 的其他区域。所有的扩展都是为一个中心目的而创建的，它们通常没有各种各样的组件，而是为了方便某个特定的任务而创建的。

操作系统的许多领域都支持小部件。这些被称为**扩展点**。您想要提供的功能将决定您的目标扩展点。对于 iOS 8，有七个扩展点:

*   **今日(iOS&OS X)**–在今日视图中查看快速更新。
*   **分享(iOS&OS X)**–与他人分享内容或将内容发布到网站上。
*   **操作(iOS&OS X)**–编辑或查看主机应用程序提供的内容。
*   **照片编辑(iOS)**–使用照片库应用程序编辑照片或视频。
*   **文档提供者(iOS)**–访问和编辑文件存储库。
*   **定制键盘(iOS)**–提供定制的系统键盘。
*   **Finder 同步(OS X)**–显示 Finder 中文件同步状态的信息。

在 Xcode 中，每个扩展模板都是为上面列出的一个扩展点定制的。不存在可以与多个扩展点一起工作的“通用”扩展的概念。

每个扩展都作为独立的二进制文件存在于容器应用程序中。它们将作为新目标添加到现有项目中。这意味着你不能单独发布扩展。相反，它们与容器应用一起发布。一个应用程序可以包含几个不同的扩展。

现在我们已经很好地理解了什么是扩展，让我们把注意力集中在我们将要创建的 Today 扩展上。

## 小部件设计注意事项

创建 widget 时，您应该遵循一些规则。小部件需要专注、反应灵敏，并包含快速浏览信息。这意味着如果你正在为一个体育应用程序创建一个小部件，它可能会显示某场比赛的比分。它将 ***而不是*** 包括一个步骤密集的过程，如登录，浏览多个运动的所有分数等。

除了用户体验，还有技术限制。例如，小部件不能显示键盘。“今日”视图也使用模糊视觉效果，所以保持背景清晰是个好主意。用户在今日视图中的主要手势是上下滑动，所以也不推荐使用滚动视图。

## 我们的小部件会做什么？

我们将要创建的小部件将使用由 [Telize](http://www.telize.com) 提供的 API 基于您的 IP 地址显示地理位置信息。当 iOS 试图更新 widget 时，如果还没有检索到数据，就会向 API 发送请求。然后 JSON 将被存储在一个`NSDictionary`中。

当数据被解析时，将显示您所在位置的随机值，如纬度、经度、国家代码等。还会有一个按钮来更新标签以显示一个新的随机值。

Apple 建议 widgets 使用自动布局。最近，iOS 8 推出了自适应设计，鼓励开发人员创建自己的视图，以便可以在任何设备上正确呈现。这确保您的 widget 能够提供卓越的用户体验，无论是在 iPad、iPhone 4 还是 iPhone 6 Plus 上。 [SitePoint 最近发表了一篇关于汽车布局的文章](https://www.sitepoint.com/building-adaptive-user-interfaces-ios-8/)，如果你想了解更多，不妨一读。

你可以在 [GitHub](https://github.com/sitepoint-examples/GeoLocation) 上找到我们正在创建的最终项目。

## 创建新目标

首先打开 Xcode，使用单视图应用程序模板创建一个新项目。如果您已经有了一个应用程序，还想添加这个小部件，可以跳过这一步。通过选择*文件>新建>目标*添加一个新目标，然后选择 *iOS* 扩展下*应用扩展*部分下面列出的*今日扩展*。

![Create a Today Extension](img/2cdfcbed3167a67aa661eef586e9f9e1.png)

填写目标的产品名称和组织标识符的选项。如果您想按照本教程的代码进行操作，一定要选择 swift 作为编程语言。回想一下，扩展包含在应用程序中。这就是为什么包标识符是基于项目已经存在的标识符的原因。单击“完成”添加扩展。

## 用户界面

请确保在继续之前打开 Widget 文件夹。默认情况下，小部件模板会添加一个视图控制器、故事板和 plist 文件。如果你打开 *MainInterface.storyboard* ，你会注意到为用户界面提供了一个标签。对于我们的小部件，我们还需要添加一个按钮。

首先双击标签，键入“正在加载…”并按回车键。然后，打开*尺寸检查器*。将*宽度*设置为 254。标签将变为橙色，表示框架已更改，当前约束无效。我们稍后会解决这个问题。

![Size Inspector](img/41f882422c699b6bbf42e801d96ce83c.png)

接下来，从*对象库*中拖动一个按钮，并将其放在标签的右侧。打开*属性检查器*，设置*类型*为信息灯，取消*显示触摸高亮*。在*尺寸检查器*中，将按钮 *X* 设置为 266，将 *Y* 设置为 8。

## 创建 IBOutlets 和 IBAction

点击 Xcode 右上角的*助理编辑*按钮。Xcode 的右侧应该会显示`TodayViewController`代码。这将使视图控制器内的插座更容易连接。

![Assistant Editor](img/db8bb7a3a74ba857f18e868c89e07c2e.png)

*Ctrl+拖动*从标签到`TodayViewController`的顶部，然后放开。在提示符下，键入`lblGeoData`作为*名称*。单击连接。

![Name Label](img/96b282ed5dbb80886d3712ef4b1eaf4a.png)

对按钮做同样的操作，但对*的名字*称之为`btnRefresh`。再重复一次这个过程，但是向视图控制器的底部释放鼠标按钮。将*连接*类型设置为*动作*，将*名称*设置为`btnRefreshData`。

![Add Action](img/6bd1a3abfacb4c9f7b915f0e45ac1846.png)

选择标签后，在故事板画布的底部点击“解决自动布局问题”按钮，并点击*所有视图*部分下的*清除约束*。

![Resolve Layout Issues](img/8b5a83279ca93acd06e10eac7264248c.png)

确保标签仍处于选中状态。使用故事板画布底部的*大头针*按钮，添加下图所示的约束，并点击*添加 5 个约束*。

![Pin Label](img/7b742864c5c96da208d7b688f015a65a.png)

我们需要编辑刚刚添加的约束中的一些优先级。在*尺寸检查器*中，你会看到应用于标签的所有约束。

![Edit Constraint](img/3acfeb4303efbd001e3392c3b186772f.png)

打开宽度约束，将*关系*改为*大于或等于*。对于*顶部*和*底部*约束，降低它们的*优先级*至`999`。

选择我们添加到标签旁边的按钮，再次打开 *Pin* 弹出窗口。添加以下约束并点击*添加 4 个约束*:

![Pin Buttons](img/ddff29325e28c68d24c551a85fb32740.png)

按下助理编辑右边的按钮，切换回*标准编辑器*视图。

![Standard Editor Button](img/bb9558a2f839a2c1f3e9e7c95c445bd4.png)

## ncwidget 提供协议

现在用户界面已经设置好了，打开`TodayViewController.swift`文件。就其核心而言，小部件只不过是一个视图控制器。这里提供的符合一个重要的协议，`NCWidgetProviding`。该协议提供了定制小部件的外观和行为的功能。

`NCWidgetProviding`有两个委托功能。第一个是需要实现的`widgetPerformUpdateWithCompletionhandler:`。不需要手动调用这个函数。如果我们指出其中有新数据，那么将缓存一个快照，稍后用于更新显示。

协议中定义的另一个委托功能是`widgetMarginInsetsForProposedMarginInsets:`。与最后一个函数不同，这是可选实现的。默认情况下，创建的小部件带有左边距插图。如果你在“今日”视图中查看苹果提供的小工具，比如日历，你会注意到左边的空间。如果你想让你的部件居中，你可以在这里返回适当的`UIEdgeInsets`。

## 检索数据

我们需要编写代码来使用 API 来显示小部件上的数据。首先，创建一个属性来维护小部件的更新状态。在现有`IBOutlet` s 的下方`TodayViewController.swift`增加以下物业作为我们的标签:

```
var updateResult:NCUpdateResult = NCUpdateResult.NoData
```

我们将使用这个属性来告诉系统，一旦从 API 中检索到新数据，我们的用户界面是否应该更新。

创建类型为`NSDictionary`的另一个名为`geoInfoDictionary`的属性。这将缓存数据，因此不需要向 API 发出新的请求来在标签中显示新信息。

在上面的`viewDidLoad:`中，您的代码现在应该是这样的:

```
@IBOutlet var btnRefresh: UIButton!
@IBOutlet var lblGeoData: UILabel!
var updateResult:NCUpdateResult = NCUpdateResult.NoData
var geoInfoDictionary:NSDictionary?
```

此时，是时候调用 API 并获取关于您当前 IP 地址的地理位置数据了。将这段代码添加到视图控制器中(不要担心`setLabelText()`，我们将在接下来添加):

```
//MARK: Update Logic - Data Retrieval 
func updateWidget()
{
    if self.geoInfoDictionary == nil
    {
        self.lblGeoData.text = "Refreshing..."

        let urlPath: String = "http://www.telize.com/geoip?"
        let url: NSURL = NSURL(string: urlPath)!
        let request: NSURLRequest = NSURLRequest(URL: url)
        let queue:NSOperationQueue = NSOperationQueue()

        NSURLConnection.sendAsynchronousRequest(request, queue: queue, completionHandler:{ (response: NSURLResponse!, data: NSData!, error: NSError!) -&gt; Void in

            if(error != nil)
            {
                self.updateResult = NCUpdateResult.Failed
                self.lblGeoData.text = "An error occurred while retrieving data"
            }
            else
            {
                let jsonResult: NSDictionary = NSJSONSerialization.JSONObjectWithData(data, options: NSJSONReadingOptions.MutableContainers, error: nil) as NSDictionary
                self.geoInfoDictionary = jsonResult
            }

            self.setLabelText()
        })

    }
    else
    {
        self.setLabelText()
    }

}
```

在函数声明之上，我们有一个看起来像注释的东西。这类似于 Objective-C 中的 pragma 标记，使用`//MARK:`有助于将代码分解成可以在跳转栏中查看的逻辑部分。

![jumpBarMarks](img/cee4fccc1668e3b82c351fa5ab5627ab.png)

在`updateWidget()`函数中，我们使用 API。首先进行验证，看请求是否成功。如果不是，那么`NSError`对象将被初始化。如果发生这种情况，`updateResult`被设置为*失败*。

如果错误为 *nil* ，则从响应中接收到数据。`geoInfoDictionary`由地理位置数据填充。

接下来，在`updateWidget():`的正下方创建这个函数

```
func setLabelText()
{
    if let geoDictionary = self.geoInfoDictionary
    {
        let randomKeyIndex = Int(arc4random_uniform(UInt32(geoDictionary.count)))
        let randomKey = geoDictionary.allKeys[randomKeyIndex] as String
        let keyValue = geoDictionary[randomKey] as String
        let lblText = "\(randomKey) - \(keyValue)"

        if(self.lblGeoData.text != lblText)
        {
            self.updateResult = NCUpdateResult.NewData
            self.lblGeoData.text = lblText
        }
    }
    else
    {
        self.updateResult = NCUpdateResult.NoData
    }

}
```

这个函数将在按下按钮刷新标签或者第一次返回 JSON 时更新标签。从地理位置字典中选择一个随机值，如果它与已经显示的信息不同，则显示在标签上。

我们之前添加的按钮将用另一个值更新标签。现在完成`btnRefreshData`功能:

```
@IBAction func btnRefreshData(sender: UIButton)
{
    self.geoInfoDictionary == nil ? self.updateWidget() : self.setLabelText()
}
```

当按钮被按下时，它会检查我们是否已经有了来自 API 的响应。如果我们做到了，就没有理由再去攻击服务器了。所有地理位置数据都保存在属性`geoInfoDictionary`中。不管怎样，标签会更新一些新的东西。

为了完成任务，像这样实现`viewDidAppear:`:

```
override func viewDidAppear(animated: Bool)
{
    super.viewDidAppear(animated)
    self.updateWidget()
}
```

## 调用完成处理程序

当它离开屏幕时，小部件需要更新。这使它保持一致的更新。我们有责任告诉系统这是否应该发生。更改协议功能`widgetPerformUpdateWithCompletionHandler(completionHandler:`以尝试显示新数据，然后执行完成处理程序:

```
//MARK: NCWidgetProviding Functions
func widgetPerformUpdateWithCompletionHandler(completionHandler: ((NCUpdateResult) -&gt; Void)!)
{
    self.updateWidget()
    completionHandler(self.updateResult)
}
```

如果您正在创建一个类似的小部件，使用`NSUserDefaults`来缓存报价将是明智的。如果您想要在微件之外共享报价，可以为微件和包含微件的应用程序创建应用程序组权利。这将允许`NSUserDefaults`使用`initWithSuiteName:`初始化器在两个目标之间同步。

## 设置边距插入

如果您现在构建并运行这个小部件，那么利润率似乎会有点低。要解决这个问题，请在`widgetMarginInsetsForProposedMarginInsets:`中设置一些自定义的插入值。

```
func widgetMarginInsetsForProposedMarginInsets(defaultMarginInsets: UIEdgeInsets) -&gt; UIEdgeInsets
{
    var newMargins = defaultMarginInsets
    newMargins.right = 10
    newMargins.bottom = 5
    return newMargins
}
```

根据您构建的小部件的类型，您甚至可能不需要实现这一点。记住，这是`NCWidgetProviding`协议中的一个可选函数。

现在您已经添加了这段代码，您的整个视图控制器应该如下所示:

```
import UIKit
import NotificationCenter

class TodayViewController: UIViewController, NCWidgetProviding
{

  //MARK: Properties
  @IBOutlet var btnRefresh: UIButton!
  @IBOutlet var lblGeoData: UILabel!
  var updateResult:NCUpdateResult = NCUpdateResult.NoData
  var geoInfoDictionary:NSDictionary?

  //MARK: View Lifecycle
  override func viewDidLoad()
  {
      super.viewDidLoad()
      // Do any additional setup after loading the view from its nib.
  }

  override func viewDidAppear(animated: Bool)
  {
      super.viewDidAppear(animated)
      self.updateWidget()
  }

  //MARK: NCWidget Providing
  func widgetPerformUpdateWithCompletionHandler(completionHandler: ((NCUpdateResult) -&gt; Void)!)
  {
      self.updateWidget()
      completionHandler(self.updateResult)
  }

  func widgetMarginInsetsForProposedMarginInsets(defaultMarginInsets: UIEdgeInsets) -&gt; UIEdgeInsets
  {
      var newMargins = defaultMarginInsets
      newMargins.right = 10
      newMargins.bottom = 5
      return newMargins
  }

  //MARK: Update Logic - Data Retrieval
  func updateWidget()
  {
      if self.geoInfoDictionary == nil
      {
          self.lblGeoData.text = "Refreshing..."

          let urlPath: String = "http://www.telize.com/geoip?"
          let url: NSURL = NSURL(string: urlPath)!
          let request: NSURLRequest = NSURLRequest(URL: url)
          let queue:NSOperationQueue = NSOperationQueue()

          NSURLConnection.sendAsynchronousRequest(request, queue: queue, completionHandler:{ (response: NSURLResponse!, data: NSData!, error: NSError!) -&gt; Void in

              if(error != nil)
              {
                  self.updateResult = NCUpdateResult.Failed
                  self.lblGeoData.text = "An error occurred while retrieving data"
              }
              else
              {
                  let jsonResult: NSDictionary = NSJSONSerialization.JSONObjectWithData(data, options: NSJSONReadingOptions.MutableContainers, error: nil) as NSDictionary
                  self.geoInfoDictionary = jsonResult
              }

              self.setLabelText()
          })

      }
      else
      {
          self.setLabelText()
      }

  }

  func setLabelText()
  {
      if let geoDictionary = self.geoInfoDictionary
      {
          let randomKeyIndex = Int(arc4random_uniform(UInt32(geoDictionary.count)))
          let randomKey = geoDictionary.allKeys[randomKeyIndex] as String
          let keyValue = geoDictionary[randomKey] as String
          let lblText = "\(randomKey) - \(keyValue)"

          if(self.lblGeoData.text != lblText)
          {
              self.updateResult = NCUpdateResult.NewData
              self.lblGeoData.text = lblText
          }
      }
      else
      {
          self.updateResult = NCUpdateResult.NoData
      }
  }

  @IBAction func btnRefreshData(sender: AnyObject)
  {
      self.geoInfoDictionary == nil ? self.updateWidget() : self.setLabelText()
  }
}
```

## 运行小部件

现在通过按下 *command + R* 来构建并运行小部件。因为我们使用了自动布局，所以你选择哪个模拟器并不重要。系统会提示您选择要运行的应用程序。今天选择*。当模拟器运行时，您会看到今天屏幕出现。如果小工具没有出现，请按屏幕底部的“编辑”并添加它。*

 *![Add the Widget](img/4a9f71666a4506f4b7ff54b7cb090fcd.png)

5-10 秒后，来自 API 的响应返回，并显示一些随机的地理位置信息。按几次按钮，查看它循环显示的一些数据。

![Completed Today Extension](img/ea7b47a2f98070ec15bb559d4aa7f387.png)

## 结论

扩展为开发人员打开了许多激动人心的机会。事实证明，小工具是最受用户欢迎的 iOS 新功能之一。如果你在 app store 上发布了现有的应用程序，问问自己它们是否能从一个小部件中受益。一旦你理解了`NCWidgetProviding`的概念，让一个小部件启动并运行起来并不需要太多代码。

记住要让他们直接、专注，并把绩效放在心上。简短的信息浏览最适合今天的屏幕。要特别注意苹果的小工具是如何工作的。有了本教程中介绍的基础知识，您就拥有了开始开发自己的小部件所需的一切。

## 分享这篇文章*