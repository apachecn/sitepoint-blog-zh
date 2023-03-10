# 使用 Apple tvOS SDK 为大屏幕编码

> 原文：<https://www.sitepoint.com/coding-for-the-big-screen-with-the-apple-tvos-sdk/>

> 电视的未来是应用程序！

这是蒂姆·库克在最近 9 月的苹果活动上所说的，随着越来越多的电视内容通过移动设备和应用程序流动，这种未来已经在我们面前。

新发布的 tvOS SDK 让 iOS 开发者感到兴奋。我们将首次能够为新的苹果电视创建应用程序，并将其发布到应用程序商店。这是一个通过应用程序向每个人的客厅大屏幕提供内容和出色用户体验的绝佳机会。

让我们深入了解一下 tvOS SDK。

我们将涵盖每个新框架的基础知识，确定它们的用途，并描述制作应用程序的要求。利用我们新发现的知识，我们将一步一步地创建一个简单的定制 tvOS 应用程序。

## SDK 概述

tvOS 有两种类型的应用。

### 自定义应用程序

这些应用程序使用最新的 iOS 框架，类似于 iPhone 和 iPad 的传统 iOS 应用程序。您可以使用故事板或代码来创建用户界面。

### 客户端服务器应用程序

这些应用程序使用一套新的 tvOS 特有的框架。如果您是 web 开发人员，您会发现这些框架很有趣，因为它们提供了使用 JavaScript 和标记语言创建应用程序的能力。是的，你没看错，这是苹果第一次提供 JavaScript 框架来创建应用程序。

以下是所有新框架的列表:

#### TVML

TVML 是苹果公司的定制标记语言，用于创建界面。它有 XML 风格，如果你懂 HTML，你会觉得很熟悉。

Apple 提供了一套可重复使用的 TVML 模板来帮助您开始使用。你可以在这里找到这些模板[的详细描述。](https://developer.apple.com/library/prerelease/tvos/documentation/LanguagesUtilities/Conceptual/ATV_Template_Guide/TextboxTemplate.html#//apple_ref/doc/uid/TP40015064-CH2-SW8)

#### tvbs

顾名思义，TVJS 是用于加载 TVML 页面的 JavaScript APIs 的集合。它包含与 Apple TV 硬件交互的必要功能。在我看来，这是没有任何特殊语法的标准 JavaScript。

更多信息可在 [TVJS 框架参考](https://developer.apple.com/library/prerelease/tvos/documentation/TVMLJS/Reference/TVJSFrameworkReference/index.html#//apple_ref/doc/uid/TP40016076)文档中找到。

#### TVMLKit

这是核心框架，所有的东西都聚集在这里。TVMLKit 提供了一种将 TVJS、JavaScript 和 TVML 元素整合到你的应用中的方法。可以把这个框架想象成一个容器和桥梁，把代码和标记作为苹果电视的原生应用程序。

更多详情参见 [TVMLKit 框架参考](https://developer.apple.com/library/prerelease/tvos/documentation/TVMLKit/Reference/TVMLKit_Collection/index.html#//apple_ref/doc/uid/TP40016429)。

#### 电视服务

TVServices 是一个框架，允许你添加一个顶层扩展到你的应用程序。顶层是 Apple TV 主屏幕的一个区域，专门用于显示位于第一行的应用程序的内容。当焦点在应用程序图标上时，它提供了向用户显示内容和信息的机会，而无需启动应用程序。

![TopSelf](img/f98633d427c6f0387cdd5afb70a46350.png)

参见 [TVServices 框架参考](https://developer.apple.com/library/prerelease/tvos/documentation/TVServices/Reference/TVServices_Ref/index.html#//apple_ref/doc/uid/TP40016412)。

#### 客户端服务器应用程序的架构

以上所有的框架构成了应用程序客户端的一部分。为了让应用程序正常工作，您还需要一个服务器端。服务器将包含您的 TVML 标记、JavaScript 代码和您的数据。打包的 tvOS 应用程序中的 TVMLKit 将处理数据的检索和显示。

我喜欢把这种架构想象成典型的网站，其中所有的 HTML 和 JS 文件都驻留在 web 服务器上，负责呈现它们的应用程序类似于用户的浏览器。

## 发展考虑

在使用 Xcode 创建应用程序之前，考虑以下限制很重要:
–应用程序大小限制在 200MB 以内。
–您不能将数据保存到设备中，如果您需要保存用户设置，您需要熟悉 CloudKit。

重要的是要了解苹果电视有一个新的用户界面，苹果已经定义了一套专门针对电视操作系统的[人机界面指南](https://developer.apple.com/tvos/human-interface-guidelines/)。

你需要注意的是，与 iOS 设备不同，用户的触摸能力有限(滑动、点击和轻敲)，这影响了他们与你的应用程序的交互方式。这就是聚焦对于苹果电视的重要性，鉴于这是一个新概念，我强烈建议你花些时间学习如何在你的应用程序中支持聚焦。

## 创建您的第一个 tvOS 应用程序

是时候享受 tvOS SDK 的乐趣了。

我们将创建一个应用程序，它调用电视数据库 API，检索受欢迎的电视节目列表，并在我们的电视应用程序上显示它们。

当我们完成本教程时，应用程序将如下所示:

![image](img/d47fd34de215c97b08f3b00e8b6d417d.png)

### 设置您的开发环境

Apple TV 的开发需要仍处于测试阶段的 Xcode 7.1。与任何正在开发的东西一样，您可能会面临意外的崩溃和缓慢的性能。

从[这里](https://developer.apple.com/xcode/download/)下载 Xcode 7.1

我们将使用[themoviedb.org API](https://www.themoviedb.org/documentation/api)，特别是下面的 API 请求，该请求返回包含流行电视节目列表的 json 数据。你可以在这里看到[的请求信息](http://docs.themoviedb.apiary.io/#reference/tv/tvpopular/get)

API 需要一个密钥，你可以免费申请，但是你必须先注册。

### 开发应用程序

打开 Xcode，在 tvOS 部分下选择一个新的*单视图应用*。

![image](img/e33c72e2b66a1fa1757ce1c79d53c816.png)

给应用程序一个名字，我命名为 mine*populatvshows*，为文件选择一个位置并点击 *create* 。

![image](img/33a0e05d7ff42930f8cd23b49bad915e.png)

#### 设计用户界面

选择主故事板，你会注意到这是一个非常大的故事板区域。这是因为我们现在需要在电视上创建全高清用户界面。

选择默认视图，你会注意到默认情况下它的分辨率是 1920 x 1080。

![image](img/f3d7da9ca55f817bc07f4c09f3bb886d.png)

除了这种差异，其他一切都应该是熟悉的。

您可能想要缩小以便能够看到完整的故事板。

首先将一个*集合视图*拖到故事板，将其放置在左上角，并在所有 4 个边上添加零边距约束。

![image](img/9e23986178114a43d711e36e219a67d8.png)

![image](img/a144a4e281b8f49df4afbc0d5c57cd3c.png)

点击*更新帧*。

![image](img/6a1a1f796c27c3ef02b3799b06594a96.png)

集合视图现在将占据故事板上的所有空间。

在大多数 iOS 设备上，当有更多数据可以适合屏幕时，滚动通常是垂直实现的。对于 Apple TV，推荐的滚动方向是水平的。在集合视图中设置此项。

![image](img/daf346413621aae8e07eb3c7b9931c80.png)

接下来选择*集合视图单元格*，并给它一个 260 x430 的自定义大小。

![image](img/a31e39f98033843a8c8383d3496e5bcc.png)

这个单元格将包含电视节目海报和节目名称，我们给它一个相对较小的尺寸，以允许在屏幕上显示多个节目。

在属性检查器中分配一个标识符 *ShowCell* 。

![image](img/b1a21b50d30ebff07dd727e2b825d853.png)

在单元格中添加一个图像视图，并将其命名为 *ShowImg*

![image](img/ebb56552f001913589f39483de8709df.png)

添加以下约束:上边距 0，宽度 225，高度 354，并在容器中水平对齐。

![image](img/b31217ea2aa762c821758c2b86363b1e.png)

![image](img/64dd217e948bdc5eeac91753135bf8fa.png)

在图像视图下拖动一个标签，并给它以下约束:上边距 20，宽度 225，高度 35

![image](img/31b98039f79c60f1304a37a5b9b270b7.png)

![image](img/987d1ee979c5b664e53f1a24ce3d5f1c.png)

在容器中水平对齐。

![image](img/64dd217e948bdc5eeac91753135bf8fa.png)

将文本设置为居中对齐，并将其更改为任何你喜欢的电视节目，我将我的设置为神秘博士。

![image](img/5e43c14ac87844e46094af20524a5c67.png)

将图像视图设置为使用您选择的海报或我在[这里](http://image.tmdb.org/t/p/w500/igDhbYQTvact1SbNDbzoeiFBGda.jpg)使用的海报

下载图像并将其拖到项目的*资产*目录中。

![image](img/9844cc938fb318f074ee78b57a5d3ff1.png)

给它起个合适的名字，我给我的*海报*背景 _ 命名。

返回到故事板，选择图像视图，并在属性检查器中将其设置为背景。

![image](img/aead3ac3fd18bc470dc37d826843039b.png)

这就是我们的 UI 所需要的全部内容，如果你还没有完成任何步骤，请从 UI 分支[这里](https://github.com/sitepoint-editors/IntroToTvOS/tree/UI)下载一份代码

#### 获取数据

是时候发出 API 请求并在应用程序中获取数据了。

由于可选值的原因，用 Swift 序列化和反序列化 json 会变得很耗时。我喜欢用一个叫做 [SwiftyJSON](https://github.com/SwiftyJSON/SwiftyJSON/tree/xcode7) 的框架。它使得使用 json 的过程变得更加简单，并且提供了一个包装器，消除了大量的代码管理。

下载该框架的副本，并确保您在 Xcode 7 分支上。

![image](img/cd57dba94359e76a42833c9a60e74118.png)

将库集成到项目中最简单的方法是将 *SwiftyJSON.swif* 文件拖到项目中，选择项目目标。

![image](img/358171a38d0911faf2e0cd6d3c08b2df.png)

![image](img/0a6a747e761cb9649dc17537c4ed63ab.png)

太好了！现在我们准备添加一些代码。

向项目添加一个新的 Swift 文件，并将其命名为 *ApiService* 。

```
class ApiService {

    static let sharedInstance = ApiService()

    func apiGetRequest(path: String, onCompletion: (JSON, NSError?) -> Void) {
        let request = NSMutableURLRequest(URL: NSURL(string: path)!)

        let session = NSURLSession.sharedSession()

        let task = session.dataTaskWithRequest(request, completionHandler: {data, response, error -> Void in
            let json:JSON = JSON(data: data!)
            onCompletion(json, error)
        })
        task.resume()
    }
}
```

该代码有一个创建带有请求的网络任务的函数，它将 url 作为参数，如果成功则返回数据，否则返回错误。

这个函数是通用的，可以用于应用程序发出的每个 API 请求。

我们来看看 API。为了访问数据，我们向以下 URL 发出 GET 请求:'http://api.themoviedb.org/3/tv/popular？api_key= '包括结尾的密钥。

在类的顶部添加下面一行代码。

```
let API_TV_POPULAR_URL = "http://api.themoviedb.org/3/tv/popular?api_key=YourKeyHere"
```

确保用您的 api 密钥替换`YourKeyHere`。

很好，现在让我们添加另一个函数，它接受这个 url 并将其传递给我们之前创建的通用函数，然后它应该返回请求的数据或错误。

```
func getPopularTVShows(onCompletion: (JSON, NSError?) -> Void) {
    apiGetRequest(API_TV_POPULAR_URL, onCompletion: { json, err in
        onCompletion(json as JSON, err as NSError?)
    })
}
```

这就是我们这节课所需要的。它现在应该是这样的:

```
import Foundation

let API_TV_POPULAR_URL = "http://api.themoviedb.org/3/tv/popular?api_key=yourKeyHere"

class ApiService {

    static let sharedInstance = ApiService()

    func apiGetRequest(path: String, onCompletion: (JSON, NSError?) -> Void) {
        let request = NSMutableURLRequest(URL: NSURL(string: path)!)

        let session = NSURLSession.sharedSession()

        let task = session.dataTaskWithRequest(request, completionHandler: {data, response, error -> Void in
            let json:JSON = JSON(data: data!)
            onCompletion(json, error)
        })
        task.resume()
    }

    func getPopularTVShows(onCompletion: (JSON, NSError?) -> Void) {
        apiGetRequest(API_TV_POPULAR_URL, onCompletion: { json, err in
            onCompletion(json as JSON, err as NSError?)
        })
    }

}
```

让我们创建我们的数据对象，这些类将包含从 json 接收的数据。我们将使用这些对象来填充应用程序的 UI 元素。

如果您打开浏览器并转到上面代码中添加的 URL，您将收到我们将使用的 json 数据。应该是这样的。

```
{
    "page": 1,
    "results": [
        {
            "backdrop_path": "/aKz3lXU71wqdslC1IYRC3yHD6yw.jpg",
            "first_air_date": "2011-04-17",
            "genre_ids": [
                10765,
                18
            ],
            "id": 1399,
            "original_language": "en",
            "original_name": "Game of Thrones",
            "overview": "Seven noble families fight for control of the mythical land of Westeros. Friction between the houses leads to full-scale war. All while a very ancient evil awakens in the farthest north. Amidst the war, a neglected military order of misfits, the Night's Watch, is all that stands between the realms of men and icy horrors beyond.\n\n",
            "origin_country": [
                "US"
            ],
            "poster_path": "/jIhL6mlT7AblhbHJgEoiBIOUVl1.jpg",
            "popularity": 36.072708,
            "name": "Game of Thrones",
            "vote_average": 9.1,
            "vote_count": 273
        },
        {
            "backdrop_path": "/kohPYEYHuQLWX3gjchmrWWOEycD.jpg",
            "first_air_date": "2015-06-12",
            "genre_ids": [
                878
            ],
            "id": 62425,
            "original_language": "en",
            "original_name": "Dark Matter",
            "overview": "The six-person crew of a derelict spaceship awakens from stasis in the farthest reaches of space. Their memories wiped clean, they have no recollection of who they are or how they got on board. The only clue to their identities is a cargo bay full of weaponry and a destination: a remote mining colony that is about to become a war zone. With no idea whose side they are on, they face a deadly decision. Will these amnesiacs turn their backs on history, or will their pasts catch up with them?",
            "origin_country": [
                "CA"
            ],
            "poster_path": "/iDSXueb3hjerXMq5w92rBP16LWY.jpg",
            "popularity": 27.373853,
            "name": "Dark Matter",
            "vote_average": 6.4,
            "vote_count": 4
        }
    ],
    "total_pages": 3089,
    "total_results": 61761
}
```

仔细观察，您会发现我们在顶层有一个包含以下内容的结构:
–一个页码。
–一组结果。
–总页数。
–总结果。

让我们创建一个表示这个数据结构的类。添加一个新的 Swift 文件并将其命名为 *ApiResults* 。

添加以下代码

```
class ApiResults {

    var page : Int!
    var results : [ApiTVResult]!
    var totalPages : Int!
    var totalResults : Int!

    /**
    * Instantiate the instance using the passed json values to set the properties values
    */
    init(fromJson json: JSON!){
        if json == nil{
            return
        }
        page = json["page"].intValue
        results = [ApiTVResult]()
        let resultsArray = json["results"].arrayValue
        for resultsJson in resultsArray{
            let value = ApiTVResult(fromJson: resultsJson)
            results.append(value)
        }
        totalPages = json["total_pages"].intValue
        totalResults = json["total_results"].intValue
    }

}
```

这将创建整数形式的`page`、`totalPages`和`totalResults`变量，因为它们将保存 json 数值。它还创建了一个结果数组。

初始化代码获取 json 数据，并将值赋给适当的变量。

您可能注意到我们声明了一个`ApiTVResult`对象，并将其映射到`results`数组。这将是我们的第二个对象，它将包含每个结果的数据，在本例中是电视节目细节。

添加一个新的 Swift 文件，并将其命名为 *ApiTVResult* 。

添加以下代码

```
let imagesBasePath = "http://image.tmdb.org/t/p/w500"

class ApiTVResult {

    var backdropPath : String!
    var firstAirDate : String!
    var genreIds : [Int]!
    var id : Int!
    var originalLanguage : String!
    var originalName : String!
    var overview : String!
    var originCountry : [Int]!
    var posterPath : String!
    var popularity : Float!
    var name : String!
    var voteAverage : Float!
    var voteCount : Int!

    /**
    * Instantiate the instance using the passed json values to set the properties values
    */
    init(fromJson json: JSON!){
        if json == nil{
            return
        }

        let apiBackDropPath = json["backdrop_path"].stringValue
        backdropPath = "\(imagesBasePath)\(apiBackDropPath)"
        firstAirDate = json["first_air_date"].stringValue
        genreIds = [Int]()
        let genreIdsArray = json["genre_ids"].arrayValue
        for genreIdsJson in genreIdsArray {
            genreIds.append(genreIdsJson.intValue)
        }
        id = json["id"].intValue
        originalLanguage = json["original_language"].stringValue
        originalName = json["original_name"].stringValue
        overview = json["overview"].stringValue
        originCountry = [Int]()
        let originCountryArray = json["origin_country"].arrayValue
        for originCountryJson in originCountryArray {
            originCountry.append(originCountryJson.intValue)
        }
        let apiPosterPath = json["poster_path"].stringValue
        posterPath = "\(imagesBasePath)\(apiPosterPath)"
        popularity = json["popularity"].floatValue
        name = json["name"].stringValue
        voteAverage = json["vote_average"].floatValue
        voteCount = json["vote_count"].intValue
    }

}
```

这里的方法和以前一样，我们创建了匹配 json 数据的变量，并添加了初始化代码来填充对象。查看接收到的数据，`backdropPath`和`posterPath`只包含图像的名称。要构建完整的图像 URL，我们需要将文件名和图像路径结合起来，如下所示:

```
let apiBackDropPath = json["backdrop_path"].stringValue
backdropPath = "\(imagesBasePath)\(apiBackDropPath)"
```

只要我们有一个像`var genreIds : [Int]!`这样的值数组，我们就遍历每个值，并将其添加到数组中，如下所示:

```
let genreIdsArray = json["genre_ids"].arrayValue
            for genreIdsJson in genreIdsArray {
                genreIds.append(genreIdsJson.intValue)
            }
```

这就是我们需要为我们的数据模型所做的一切。

上面的代码可以在模型分支[这里](https://github.com/sitepoint-editors/IntroToTvOS/tree/Model)找到

#### 将这一切结合在一起

让我们将 UI 连接到我们的代码，并调用 API 来获取数据。

首先，我们将创建一个允许我们填充集合单元格的类。创建一个新的 Swift 文件并将其命名为 *ShowCell* 。

添加以下代码

```
import UIKit

class ShowCell: UICollectionViewCell {

    @IBOutlet weak var showImg: UIImageView!
    @IBOutlet weak var showLbl: UILabel!

    func configureCell(tvShow: ApiTVResult) {

        if let title = tvShow.name {
            showLbl.text = title
        }

        if let path = tvShow.posterPath {
            let url = NSURL(string: path)!

            dispatch_async (dispatch_get_global_queue (DISPATCH_QUEUE_PRIORITY_DEFAULT, 0)) {
                let data = NSData(contentsOfURL: url)!

                dispatch_async(dispatch_get_main_queue()) {
                    let img = UIImage(data: data)
                    self.showImg.image = img
                }

            }
        }
    }
}
```

这段代码为电视节目海报创建了一个出口，为电视节目标题创建了另一个出口，分别命名为`showImg`和`showLbl`。

它包含一个函数，该函数接受`ApiTVResult`对象，将标题分配给标签，并使用海报 URL 异步下载图像并填充`imageView`。

我们现在需要将插座连接到代码。打开故事板，选择`showCell`并将类设置为我们刚刚创建的类

![image](img/f0c3b19999d72b46de07ef44be408959.png)

右键点击`ShowCell`，选择插座名称旁边的 *+* ，拖动到海报`imageView`，连接`showImg`插座。重复上述步骤，将`showlbl`插座连接到标题标签上。

![image](img/bf3ab9dff1368a3df47f2e414d462390.png)

这就是`ShowCell`所需要的全部。

现在让我们将`CollectionView`连接到`ViewController`。

转到视图控制器，在类的顶部添加以下代码行:

```
@IBOutlet weak var collectionView: UICollectionView!
```

返回到故事板，*右键单击集合上的*，选择新引用出口旁边的 *+* ，拖动到黄色的视图控制器图标。选择*视图集合*。

这完成了所有需要的链接，所以选择`ViewController`类并替换下面的代码

```
class ViewController: UIViewController {
```

与这个

```
class ViewController: UIViewController, UICollectionViewDelegate, UICollectionViewDataSource, UICollectionViewDelegateFlowLayout    {
```

我们刚刚向`CollectionView`添加了一些协议，这些是添加数据和控制视图布局所需的最低实现。

让我们实施它们。首先删除下面的代码，因为我们不会对它执行任何操作。

```
override func didReceiveMemoryWarning() {
    super.didReceiveMemoryWarning()
    // Dispose of any resources that can be recreated.
}
```

替换以下代码:

```
 override func viewDidLoad() {
    super.viewDidLoad()
    // Do any additional setup after loading the view, typically from a nib.
}
```

使用:

```
 override func viewDidLoad() {
    super.viewDidLoad()

    collectionView.delegate = self
    collectionView.dataSource = self
}
```

我们说过，`ViewController`将作为数据源和`CollectionView`的代理。

在我们实现我们的协议之前，我们应该创建一个函数来调用 API 并填充我们的对象，这样我们就可以将适当的数据分配给`CollectionView`。

我们首先需要的是一个包含数据的变量，通过在`IBOutlet`下面添加以下代码来创建它

```
var tvShows = [ApiTVResult]()
```

这个变量将包含一个电视节目数组。

创建一个方法，使用前面创建的 ApiService 进行 API 调用。

```
func downloadData () {
    ApiService.sharedInstance.getPopularTVShows {JSON, NSError in
        if NSError != nil {
            print(NSError!.debugDescription)
        }
        else {
            let apiResults = ApiResults(fromJson: JSON)
            self.tvShows = apiResults.results

            dispatch_async(dispatch_get_main_queue()) {
                self.collectionView.reloadData()
            }
        }
    }
}
```

在上面的代码中，一旦 API 调用成功并且我们有了 json 数据，我们就填充`ApiResuts`对象。然后，我们将结果中包含的数据分配给`viewController`变量。

在这里，您可以看到 SwiftyJSON 库如何帮助序列化和填充对象。

在`viewDidLoad`函数中调用这个方法。您的代码应该如下所示:

```
 override func viewDidLoad() {
    super.viewDidLoad()

    collectionView.delegate = self
    collectionView.dataSource = self

    downloadData()
}

func downloadData () {
    ApiService.sharedInstance.getPopularTVShows {JSON, NSError in
        if NSError != nil {
            print(NSError!.debugDescription)
        }
        else {
            let apiResults = ApiResults(fromJson: JSON)
            self.tvShows = apiResults.results

            dispatch_async(dispatch_get_main_queue()) {
                self.collectionView.reloadData()
            }
        }
    }
}
```

现在我们可以实现所需的`UICollectionView`协议了。将以下代码添加到您的文件中:

```
func collectionView(collectionView: UICollectionView, cellForItemAtIndexPath indexPath: NSIndexPath) -> UICollectionViewCell {

    if let cell = collectionView.dequeueReusableCellWithReuseIdentifier("ShowCell", forIndexPath: indexPath) as? ShowCell {

        let tvShow = self.tvShows[indexPath.row]
        cell.configureCell(tvShow)

        return cell
    }
    else {
        return ShowCell()
    }
}

func numberOfSectionsInCollectionView(collectionView: UICollectionView) -> Int {
    return 1
}

func collectionView(collectionView: UICollectionView, numberOfItemsInSection section: Int) -> Int {

    return tvShows.count
}

func collectionView(collectionView: UICollectionView, layout collectionViewLayout: UICollectionViewLayout, sizeForItemAtIndexPath indexPath: NSIndexPath) -> CGSize {

    return CGSizeMake(260, 430)
}
```

这里的代码是协议实现的标准。

在`numberOfSectionsInCollectionView`中，我们返回 1，因为我们在 UI 中只有一个类别。

在`numberOfItemsInSection`中，我们返回数据中`TvShows`的计数。基本上我们说我们想要显示尽可能多的从 API 返回的`TVShows`条目。

在布局中，我们将项目的大小设置为故事板中`showcell`的大小。

最后，在看起来是`cellForItemAtIndexPath`中最复杂的一段代码中，我们基于之前创建的`ShowCell`类创建了一个单元格。我们根据索引从`tvShows`数组中选择`TVShow`，并对其进行配置以显示所需的数据。

现在是激动人心的部分…让我们运行应用程序！

**哎呀……**

![image](img/90de06133ada2e64efaf8efc428f9c8e.png)

iOS 9 现在要求在应用程序中传输数据时明确使用 HTTPS 协议。因为我们的 API 调用目前是 HTTP，所以我们需要在应用程序设置中启用它。

打开 *Info.plist* 文件，点击最后一项的 *+* 按钮。选择 *App 运输安全设置*条目

![image](img/386bce954df4efeea5f4cd181f9d3469.png)

将*允许任意负载*设置为*是*。

![image](img/a8ca9b6ebb4e82fb855baebf237374ac.png)

这将允许应用程序的所有类型的连接。

让我们再跑一次…

如果你设法跟上，你现在应该看到成功了！该应用程序显示电视节目及其各自的海报和标题。

但是仍然缺少一些东西。

在 Apple TV 上，焦点很重要，在应用程序的元素之间导航也需要它。

让我们实现最后一个有助于此的函数。

在`ViewController`的顶部(在`var TVShows`的下面)添加以下代码

```
let originalCellSize = CGSizeMake(225, 354)
let focusCellSize = CGSizeMake(240, 380)
```

这将创建一个变量，其中包含单元格的原始大小和一个稍大的值。添加一个函数，当聚焦时改变单元格的大小，使用户可以看到选择。

```
override func didUpdateFocusInContext(context: UIFocusUpdateContext, withAnimationCoordinator coordinator: UIFocusAnimationCoordinator) {
    if let previousItem = context.previouslyFocusedView as? ShowCell {
        UIView.animateWithDuration(0.2, animations: { () -> Void in
            previousItem.showImg.frame.size = self.originalCellSize
        })
    }
    if let nextItem = context.nextFocusedView as? ShowCell {
        UIView.animateWithDuration(0.2, animations: { () -> Void in
            nextItem.showImg.frame.size = self.focusCellSize
        })
    }

}
```

再次运行应用程序并在电视遥控器上滑动，您应该能够看到现在选择了哪个项目。这就完成了我们的申请。

代码的完整副本可以在[这里](https://github.com/sitepoint-editors/IntroToTvOS)找到

## 最后的想法

有经验的 iOS 开发者会发现为 tvOS 创建应用程序很熟悉。有一个小的学习曲线，特别是围绕 UI 设计和用户交互，但这是值得努力的。对于那些对提供媒体和大型内容感兴趣的人来说，TVML 应用程序提供了一种简单的方法，同时使用熟悉的 web 技术。

对于那些刚接触 iOS 或移动开发的人来说，现在是最好的时机。你将获得的开发定制 tvOS 应用程序的技能将在以后转移到 iOS。

Apple TV 将为定制应用程序提供一个新的平台，它很可能会有自己独立的应用程序商店。像这样的机会并不是每天都会出现在开发者面前。

我已经列出了我想在电视大屏幕上使用的应用程序，我期待着在一只手拿着 Siri 遥控器，另一只手拿着爆米花的同时，享受您将提供的内容和体验。

## 分享这篇文章