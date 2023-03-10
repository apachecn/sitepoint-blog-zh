# iOS 和 OSX 版 Swift 2.0 简介

> 原文：<https://www.sitepoint.com/introduction-swift-programming-language/>

*本文于 2016 年 2 月更新，以反映 Swift 2.0 的变化*

Swift 是苹果为原生 iOS 应用编程的新语言选项。它是 Objective-C 的补充，这种情况将会持续一段时间，现在是第 2 版，这是学习这种新语言的最佳时机。

Objective-C 开发人员会发现它与其他特性有很多相似之处，比如类型推断、强类型、不依赖头文件、泛型等等。

在本教程中，我将向您展示如何开始使用 Swift 2 开发 iOS 应用程序。我将展示如何设置一个开发环境，研究语言基础，并将语法与 Objective-C、JavaScript 和 C#进行比较。本教程结束时，您将使用 Swift 创建一个简单但完整的 iOS 应用程序。

在很多情况下，Objective-C、C#和 Swift 共享相似的语法，[参考这个文件](https://uploads.sitepoint.com/wp-content/uploads/2014/07/1405348658Swift-Syntax.pdf)以获得完整的参考。

## 设置您的环境

与 Objective-C 一样，Swift 需要最新的 Mac 和 Xcode。在撰写本文时，Xcode 的 7.2 版本是最新的，可以从 Mac App store 下载。

![get Xcode](img/da16ba52177371f56ef4d170f1c622d2.png)

这是一个很大的下载，大约 2.5 GB，所以可能需要一段时间。下载完成后，打开您的*应用程序*目录并启动 Xcode。升级时安装所需的任何附加组件。如果您安装了以前版本的 Xcode，它会更新到最新版本。

您现在可以开始探索和开发 Swift 了。

## 语言基础和语法

让我们看看语言语法，与 Objective-C 的区别，以及它与其他现代语言的比较。

由于不可能在本教程中深入涵盖 Swift 的所有方面，我将使用简单的例子来展示高级概念。

如果你想跟着做，打开 Xcode，选择 *[从游乐场](https://developer.apple.com/library/ios/recipes/Playground_Help/Chapters/AboutPlaygrounds.html)* 开始，然后输入每个例子来看它的运行。

![Welcome to Xcode](img/30b64bcd3002849930541f29ade6b39b.png)

你可以从 [GitHub](https://github.com/sitepoint-editors/IntrotoSwift2) 下载我们将要尝试的代码，以便快速参考。

### 变量和常数

要在 Swift 中声明变量，请使用关键字`var`:

```
var number = 1
```

在其他语言中:

**目标-C**

```
int number = 1;
```

**C#**

```
var number = 1;
```

**Javascript**

```
var number = 1;
```

您可以看到 Swift 的语法是多么的相似，唯一的区别是在声明的末尾缺少一个分号。

与 Objective-C 的主要区别在于，您不必定义变量类型，因为 Swift 使用类型推断，并且能够根据其初始值理解变量是一个数字。

如果愿意，您可以定义类型:

```
var number: Int = 1
```

让我们在 Swift 中声明一个字符串变量:

```
var language = "Swift"
```

在这里，Swift 中的声明看起来更干净，几乎与 C#和 JavaScript 相同。这与 Objective-C 相比如何？

```
NSString *language = @"Swift";
```

使用 Swift，无需使用内存指针(`*`)或在字符串值前加上`@`符号。

如果你正在使用[的基础框架](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/Foundation/ObjC_classic/)，你可以以任何方式使用 Swift 字符串，你可以使用`NSString`并访问整个`NSString` API

Swift 中的常量可以使用`let`关键字声明:

```
let language = "swift"
```

或者

```
let language: String = "Swift"
```

在其他语言中，相同的声明如下所示:

**目标-C**

```
NSString  *const language = @"Swift";
```

**C#**

```
const string language = Swift";
```

Swift 允许在声明常量时进行类型推断。

在 Swift 中使用变量与其他语言没有什么不同，但它有一个简单的方法让您在字符串中添加值:

```
var designers = 4
var developers = 4

var teamSize = "The team has \(designers + developers) members"
```

### 数组

在 Swift 中，您使用方括号`[ ]`创建数组和字典，并通过在方括号中写入索引或关键字来访问它们的元素。

```
var arr = ["first" , "second"]
```

在其他语言中，相同的声明如下所示:

**目标-C**

```
NSArray *arr = @[@"first", @"second"];
```

**C#**

```
var arr = new[] { "first", "second" };
```

**JavaScript**

```
var arr = ["first" , "second"];
```

您可以使用索引值从数组中访问一个项目:

```
var order = arr[0]
```

并使用以下方式设置该值:

```
arr[0] = "zero"
```

Javascript 和 C#中的代码是相同的，但是在语句的末尾有一个分号。

**目标-C**

```
NSString *order = arr[0];
```

您可以使用`for item`循环来枚举数组:

```
for item in arr {
    // do something
}
```

在其他语言中，相同的声明如下所示:

**目标-C**

```
for(NSString *item in arr)
{
    // do something
}
```

**C#**

```
foreach (var item in arr) {
    // do something
}
```

**JavaScript**

```
for (index = 0; index < order.length; ++index) {
     //do something
}
```

要向数组中添加另一个项目，请使用`+=`操作符:

```
arr += ["fourth"]
```

或附加功能:

```
arr.append("fifth")
```

### 字典

通过定义键值对在 Swift 中声明一个字典。当声明一个空字典时，你必须定义键的类型和值。

```
var dict = Dictionary<String, String>()
```

您可以声明和赋值:

```
var dict = ["MEL": "Melbourne", "SYD": "Sydney"]
```

在其他语言中，相同的声明如下所示:

**目标-C**

```
NSDictionary *dict = @{
    @"MEL" : @"Melbourne",
    @"SYD" : @"Sydney"
};
```

**C#**

```
var dict = new Dictionary<string, string>
{
    { "MEL", "Melbourne" },
    { "SYD", "Sydney" }
};
```

要访问字典中的项目，请使用:

```
var entry = dict["MEL"]
```

在其他语言中，相同的声明如下所示:

**目标-C**

```
NSString *entry = dict[@"MEL"];
```

**C#**

```
var entry = dict["MEL"];
```

要在字典中设置或添加项目，请使用:

```
dict["PER"] = "Perth"
```

在其他语言中，相同的声明如下所示:

**目标-C**

```
dict[@"PER"] = @"Perth"
```

**C#**

```
dict["PER"] = "Perth";
```

要迭代字典，请使用:

```
for (cityCode, cityName) in dict {
    print("\(cityCode) : \(cityName)")
}
```

`cityCode`变量将包含键，而`cityName`变量将包含值。

在其他语言中，相同的声明如下所示:

**目标-C**

```
for (id key in dict) {
    NSLog(@"key: %@, value: %@", key, dict[key]);
}
```

**C#**

```
foreach(var item in dict) {
    var cityCode = item.Key;
    var cityName = item.Value;
}
```

### 环

你已经看到了如何创建项目，让我们来看看如何通过他们循环。

首先，`for`循环语法:

```
for var number = 1; number < 5; number++ {
    //do something
}
```

正如所料，您指定一个值并递增，直到满足条件。Objective-C 和 C#的语法几乎相同，JavaScript 也是如此，但它省略了变量类型。

您可以通过使用 Swift `for in`变体获得相同的结果:

```
for number in 1...5{
    //do something
}
```

Swift 创建数字变量，并在迭代指定值时自动赋值。`1…5`是一个封闭的范围，包括从 1 到 5 的数字。

使用前面创建的数组，可以使用以下语法遍历数组中的城市。

```
for city in arr {
  print(city)
}
```

Swift 提供了一个语法几乎与 Objective-C、C#和 JavaScript 相同的`while`和`repeat`循环:

```
while number < 10
{
    print(number)
    number++
}
```

`while`语句后的变量是一个布尔型变量，代码将在计算结果为`true`时执行。

`repeat`循环以同样的方式运行，但是确保在评估条件之前代码至少执行一次。

```
var number  = 9
repeat {
   print(number)
   number++
}
while number<10
```

本示例确保在 while 语句增加和计算数值之前显示数值。

### 条件式

循环控制代码中的重复任务，而`if`和`switch`语句控制流程。

Swift 中的`if`语法可以有括号，但这是可选的，因此您可以使用您喜欢的任何样式。

```
if city == "MEL" {
   print("Melbourne")
}
```

或者

```
if (city == "MEL") {
    print("Melbourne")
}
```

`if`语句后面可以跟一个`else if`或一个`else`。

```
if city == "MEL" {
    print("Melbourne")
} else if city == "SYD" {
    print("Sydney")
} else {
    print("Perth")
}
```

Swift 中的 Switch 语句后面是 case 验证，但不需要 break 语句，因为没有隐含的失败。这意味着一旦一个案例已经评估到`true`并被执行，下一个案例将不会被评估。但是，需要一个默认操作。

```
switch city {
    case "MEL":
        print("Melbourne")
    case "SYD":
        print("Sydney")
    default:
        print("Perth")
}
```

`case`语句可以包含由逗号或范围分隔的多个值。使用 Swift，您可以在 switch 语句中使用`NSString`，这在 Objective-C 中是不可能的。

### 功能

函数是一个很大的主题，但是基础知识是有用的，所以你可以声明和使用它们。

使用关键字`func`在 Swift 中声明一个函数:

```
func sayName() {
  print("Patrick")
}
```

通过指定变量名和类型来传递括号内的参数。

```
func sayName(name: String) {
  print(name)
}
```

您可以传递用逗号分隔的多个参数。

```
func sayName(name: String, lastName: String) {
  print("\(name) \(lastname)")
}
```

通过在参数后添加一个`->`并指定返回类型来声明返回结果的函数。

```
func createName(name: String, lastName: String) -> String {
   return "\(name) \(lastname)"
}
```

## 使用 Swift 创建 iOS 应用程序

学习一门新的编程语言最好通过编写代码和创建应用程序来实现。因此，让我们使用 Swift 创建一个简单的 iOS 应用程序。

该应用程序将连接到 [TheMovieDB API](https://www.themoviedb.org/documentation/api) ，请求即将上映的电影列表，下载 JSON 结果，将它们解析到字典和数组中，然后将这些数据填充到表格视图中。

为了使本教程简单，它将涵盖一些常见的任务，如创建 UI 元素并将其连接到代码，使用多个控制器、代理和协议，并展示如何使用 Swift 执行异步操作，这是该语言擅长的。

开始之前，从[themoviedb.org](https://www.themoviedb.org/)网站注册并请求一个应用 API 密钥。注册如果你还没有账户，打开[你的账户](https://www.themoviedb.org/account)页面，在 *API* 部分生成一个新的密钥。

应用程序的最终代码在 GitHub 上，我已经为每个阶段创建了分支。

### 让我们开始吧。

打开 Xcode，创建一个*新项目*，在 *iOS* 部分选择一个*单视图应用*，点击*下一步*。

![Single View App](img/a64e4a0799562c57896cbe943cd11c9c.png)

我给我的项目取名为*即将上映的电影*，并选择 *Swift* 作为语言。

![New Application](img/501b9bb5835d5db2dfb6ef6985be6149.png)

点击旁边的*，选择保存项目的位置。*

现在，通过在 Xcode 中选择 *Main.storyboard* 文件并从对象库中拖动一个*表格视图对象*，创建一个表格来显示应用程序数据。

![Insert Table](img/2a196569a713f41a613f1c22c7e8841f.png)

要用代码访问表，您需要创建一个委托、一个数据源和一个出口。

选择故事板文件，按住*控件*和*点击，将*从表格视图拖动到视图控制器，在弹出菜单中选择*数据源*。重复该过程，并选择*委托*选项。

要创建 outlet，选择*视图- >助理编辑- >显示助理编辑*菜单项。您将看到一个新的部分，显示来自*view controller 的代码。Swift* 文件。

选择表格，这次按住*控件*，然后*点击*从表格视图拖动到代码窗口，在类声明下释放。

给 outlet 起个名字，我用了 *appTableView* ，点击*连接*。

![Create an Outlet](img/c8ef0b127f1f7a7171341049e15da24e.png)

完成 UI 还有最后一项任务。这是为了创建一个原型单元格，它将包含电影细节文本，并将在表中创建的每个单元格中重复使用。

为此，请选择表格视图，并在属性检查器中，将原型单元格的数量更改为 1。然后点击单元格，将*样式*改为*字幕*，并给它一个标识符 *MovieResultsCell* 。

![Create Prototype](img/ee1bbfb05a38f75b21b4fd9cebda038e.png)

![Change Subtitle](img/3a543ef9052dd7e26c35261a92af7784.png)

好了，是时候和斯威夫特玩玩了。

首先，您必须在故事板中配置前面连接的数据源和数据集。

在 *ViewController.swift* 中，更改该类:

```
class ViewController: UIViewController {
```

对此:

```
class ViewController: UIViewController, UITableViewDataSource, UITableViewDelegate {
```

现在，通过创建所需的方法来实现上面添加的 2 个新协议。

将以下代码行添加到 *ViewController.swift* 文件中。

```
func tableView(tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return 1;
    }

func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell {
        let cellIdentifier: String = "MovieResultsCell"

        let cell: UITableViewCell = UITableViewCell(style: UITableViewCellStyle.Subtitle, reuseIdentifier: cellIdentifier)

        cell.textLabel?.text = "Test"
        cell.detailTextLabel?.text = "Test details"

        return cell
    }
```

第一个方法定义表中的行数，第二个方法显示数据。

您可以删除以下代码，因为它是不必要的。

```
override func didReceiveMemoryWarning() {
    super.didReceiveMemoryWarning()
    // Dispose of any resources that can be recreated.
}
```

*视图控制器。Swift* 代码应该是这样的:

![First phase of code overview](img/90ef79b664c779836dd907db0250429d.png)

这就完成了阶段 1，继续运行项目，您应该会看到下面的结果。

![Example of App created so far](img/f78b7b21f26bc2afe315768dea63a02b.png)

是时候连接到我们的 API 并检索结果了。这段代码应该是通用的和可重用的。目的是将 URL 作为参数传递，获取结果，解析 JSON 并返回一个包含数据的数组。

然后，这些代码可以在后续 API 调用其他 URL 时重用。

通过*在导航窗格中包含 *ViewController.swift* 的文件夹中右键单击*并选择 *New File* 来创建一个新类。

![Create a new file](img/729be096ea54d32d912e33aa7d2ce551.png)

选择*可可触摸类*，点击*下一个*并给它起一个有意义的名字，我用的是 *APIController* 。保留默认选项如下。

![Create API Class](img/bde4fc796bdf57f3588349700bbdd882.png)

打开文件，在`import UIKit`行下面输入下面的代码。

```
protocol APIControllerDelegate {
    func apiSucceededWithResults(results: NSArray)
    func apiFailedWithError(error: String)
}
```

这定义了一个协议，该协议将在*view controller . swift*中实现，并将在 API 调用完成后接收结果。

让我们添加将进行实际调用并处理结果的函数。

在类 decleration 中添加以下代码:

```
var delegate:APIControllerDelegate?

  func getAPIResults(urlString:String) {

      //The Url that will be called.
      let url = NSURL(string: urlString)
      //Create a request.
      let request = NSMutableURLRequest(URL:url!)
      //Sending Asynchronous request using NSURLSession.
      NSURLSession.sharedSession().dataTaskWithRequest(request) { (data, response, error) -> Void in
          do {
              //Check that we have received data
              guard let data = data else {
                  self.delegate?.apiFailedWithError("ERROR: no data")
                  return
              }
              //Call the JSON serialisation method to generate array of results.
              self.generateResults(data)
          }
          }.resume()
  }

func generateResults(apiData: NSData)
    {
        do {
            //Serialise the api data into a json object
            let jsonResult = try NSJSONSerialization.JSONObjectWithData(apiData, options: .AllowFragments)
            //verify we can serialise the json object into a dictionary
            guard let jsonDictionary: NSDictionary = jsonResult as? NSDictionary else {
                self.delegate?.apiFailedWithError("ERROR: conversion from JSON failed")
                return
            }
            //Create an array of results
            let results: NSArray = jsonDictionary["results"] as! NSArray
            //Use the completion handler to pass the results
            self.delegate?.apiSucceededWithResults(results)
        }
        catch {
            self.delegate?.apiFailedWithError("ERROR: conversion from JSON failed")
        }
    }
```

第一个函数获取一个 URL，并请求从 API 接收数据。如果有错误，它将执行传递错误字符串的协议的`apiFailedwithError`函数。如果成功，它将调用`generateResults`函数来解析 API 数据。

`APIController`类现在应该是这样的。

![APIController](img/d02320625f20292b059c4d3b92169294.png)

这就完成了第二阶段，如果你构建并运行这个项目，你会发现这并没有什么不同。

是时候在表中填充数据了。打开 *ViewController。Swift* 并将`APIControler`代表添加到班级:

```
class ViewController: UIViewController, UITableViewDelegate, UITableViewDataSource, APIControllerDelegate {
```

在`appTable`出口下方添加以下两行:

```
var searchResultsData: NSArray = []
  var api: APIController = APIController()
```

第一个变量将包含表数据。第二个变量创建 API 控制器的一个实例，该实例用于调用它的任何方法。

现在，通过在现有的`tableView`函数下添加以下函数来实现 API 控制器协议。**确保用您自己的 API 密钥替换空的 API 密钥。**

```
 // MARK: APIControllerDelegate

  //Make the API call
  func getUpcomingMovies()
  {
      //Construct the API URL that you want to call
      let APIkey: String = "" //Replace with your Api Key"
      let APIBaseUrl: String = "https://api.themoviedb.org/3/movie/upcoming?api_key="
      let urlString:String = "\(APIBaseUrl)" + "\(APIkey)"

      //Call the API by using the delegate and passing the API url
      self.api.delegate = self
      api.getAPIResults(urlString)
  }

  //Handle the Error
  func apiFailedWithError(error: String) {
      let alertController = UIAlertController(title: "Error", message:
          error, preferredStyle: UIAlertControllerStyle.Alert)
      alertController.addAction(UIAlertAction(title: "Dismiss", style: UIAlertActionStyle.Default,handler: nil))

      self.presentViewController(alertController, animated: true, completion: nil)
  }
  //Handle the returned data
  func apiSucceededWithResults(results: NSArray) {
      self.searchResultsData = results
      self.appTableView.reloadData()
  }
```

*ViewController.swift* 现在应该是这样的。

![VCwithDelegates](img/45bd5bbaf1f9f78688e95b4309d05e74.png)

让我们更改`tableView`函数，并将 API 数据映射到行和单元格。

替换以下代码

```
func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell {
    let cellIdentifier: String = "MovieResultsCell"   
      let cell: UITableViewCell = UITableViewCell(style: UITableViewCellStyle.Subtitle, reuseIdentifier: cellIdentifier)

      cell.textLabel?.text = "Test"
      cell.detailTextLabel?.text = "Test details"

      return cell
  }
```

有了这个:

```
func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell {
      let cellIdentifier: String = "MovieResultsCell"

      let cell: UITableViewCell = tableView.dequeueReusableCellWithIdentifier(cellIdentifier)! as UITableViewCell

      //Create a variable that will contain the result data array item for each row
      let cellData: NSDictionary = self.searchResultsData[indexPath.row] as! NSDictionary
      //Assign and display the Title field
      cell.textLabel!.text = cellData["title"] as? String

      // Construct the posterUrl to get an image URL for the movie thumbnail
      let imgURL: NSURL = getPoster(cellData["poster_path"] as? String)
      // Download an NSData representation of the image at the URL
      let imgData: NSData = NSData(contentsOfURL: imgURL)!
      cell.imageView!.image = UIImage(data: imgData)

      // Get the release date string for display in the subtitle
      let releaseDate: String = cellData["release_date"]as! String

      cell.detailTextLabel!.text = releaseDate

      return cell
  }
```

在这里，来自 API 的值被分配给每个单元格，获取电影标题、海报和上映日期。

API 并不总是返回海报的图像。这应该通过 APIController 中的 JSON 序列化来处理，但是这种方法太复杂了，无法在这里讨论。因此，为了这个应用程序的目的，让我们使用以下函数:

```
func getPoster(posterPath: String?) ->NSURL
    {
        guard let posterPath = posterPath,
            let baseUrl: String = "http://image.tmdb.org/t/p/w300",
            let urlString: String = "\(baseUrl)" + "\(posterPath)",
            let imgURL: NSURL = NSURL(string: urlString)
        else {
            let defaultImageUrl: NSURL = NSURL(string: "https://assets.tmdb.oimg/logos/var_8_0_tmdb-logo-2_Bree.png")!
            return defaultImageUrl
        }
        return imgURL
    }
```

这将检查是否从 API 返回了图像路径，以及是否为该表构造了 URL。否则将显示 tmdb 徽标。

替换以下功能

```
func tableView(tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return 1;
    }
```

使用:

```
func tableView(tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return searchResultsData.count
    }
```

这将在表中创建与 API 结果一样多的行。现在表格单元格已经准备好接收数据了，是时候进行 API 调用了。

将下面一行添加到`viewDidLoad`方法中

```
getUpcomingMovies()
```

完整的`ViewController`类现在应该是这样的。

![VC Completed](img/a45ae9345c8f64f85f87c8d3c66df2bc.png)

在应用程序运行之前，您需要启用“非 HTTPS 连接的应用程序传输安全性”,因为 tmdb API 不提供加密数据。

*右击 *info.plist* 文件上的*，选择*开源*。

![enter image description here](img/e7efe4770f20c2606e9dba5636369781.png)

将以下代码添加到文件的底部，在结束 dictionary 标记之前。

```
 <key>NSAppTransportSecurity</key>
  <dict>
      <key>NSAllowsArbitraryLoads</key><true/>
  </dict>
```

![Intro.Plist](img/96d8cfa6c9ecaf92a9c6c10c038b911e.png)

就这样，现在运行应用程序，它应该显示所有即将上映的电影。

![Final App](img/604f4d05b8a4421cb466f8720293082c.png)

我希望你喜欢这次苹果新语言的“快速”之旅，并欢迎你在下面提出意见和问题。如果你渴望了解更多，那么我推荐 SitePoint 的 [Hello World 教程](https://www.sitepoint.com/creating-hello-world-app-swift/)和 [Swift 视频系列](https://www.sitepoint.com/premium/)。

## 分享这篇文章