# iOS 网络速成班

> 原文：<https://www.sitepoint.com/a-crash-course-on-networking-in-ios/>

大多数应用程序需要网络来连接外部服务和数据源。“联网”意味着通过“http”(超文本传输协议)交换信息，这是最常用的协议之一。每次打开浏览器并检索或发送数据时，您都在使用 HTTP 作为协议。在本文中，您将通过查看以下选项，了解如何在 iOS 中使用网络:

*   超文本传送协议
*   Rest API
*   [nsursession](https://developer.apple.com/library/ios/documentation/Foundation/Reference/NSURLSession_class/):没有第三方库的网络
*   [Alamofire](https://github.com/Alamofire/Alamofire) :与第三方库联网以简化流程
*   其他网络库列表

如果您已经熟悉 HTTP 和 REST APIs，请跳到`NSURLSession`。

本文是我基于一个正在开发的应用程序撰写的一系列 iOS 相关文章中的一部分，该应用程序稍后将被开源。该应用程序使用来自 [Wikimedia Commons](https://commons.wikimedia.org/wiki/Main_Page) 的数据，并使用该网站的 REST API 来访问这些数据。

## 超文本传送协议

HTTP 是最流行的协议之一。如果您正在阅读本文，那么您正在使用 HTTP 从 Sitepoint 服务器检索 HTML 页面。您的客户端(浏览器)向服务器发送请求，服务器返回关于所请求资源的信息。在移动应用程序的上下文中，它将请求数据列表，服务器用请求的数据进行响应。这些被称为`GET`请求。

有时你必须从应用程序向服务器发送一些数据，比如更新。这些被称为`POST`请求。

还有其他类型的请求(或动词)，如:

*   得到
*   邮政
*   头
*   放
*   修补

如果你有兴趣阅读更多关于 HTTP 协议的内容，我推荐这篇维基百科文章。

## REST API

REST API 是一种简单而标准的数据交换方式。您在服务器上有一些数据，并希望在应用程序中检索这些数据。首先，你需要知道如何构建你的 API，或者“应用编程接口”,因为大多数 API 都是数据库的包装。

例如，您有一个包含书籍列表的数据库表，您的应用程序需要检索这些书籍的详细信息。

首先，您需要一个位置来检索这些数据。对于这个例子，域是*[https://books.com/](https://books.com/)*，API 在 */api* 。

您应该如何在 API 上表示您的数据？

通过 API 访问的数据应该组织成集合和实体，在这种情况下，集合是一组书籍，表示为:

*https://example.com/api/books*

这个 url 将返回一系列书籍。还可以对这个集合进行分页和搜索(`https://example.com/api/v2/books?page=2`、`https://example.com/api/v2/books?query='Jump Start PHP Environment'`)。实体是集合中特定的一本书。它可以表示为`https://example.com/api/v2/books/1`，其中`1`是一个 id。

使用 HTTP 中的`GET`、`POST`和`PUT`动词，您可以操作实体和集合。使用`GET`动词，您可以检索一本书的 ID。例如，`https://example.com/api/v2/books/7`。使用`POST`动词，你可以在图书收藏中添加一本新书。您还应该用其他参数指定必填字段。

现在您已经了解了如何在 API 中组织资源，是时候决定如何呈现数据了。我最喜欢的格式是 JSON，因为它帮助两个系统以一种不与语言本身耦合的方式进行通信。

JSON 消息可能如下所示:

```
{
  "id": 7,
  "userId": 20,
  "title": "Jump Start PHP Environment",
  "description": "Many entry level PHP developers want a quick path to glory, a shortcut to “knowing PHP.” Too many books and tutorials go straight into a pre-made, awful environment that just wants you to code, with no regard for security, version control, or other absolutely essential practices. This book is aimed at the absolute beginner who wants to start learning PHP, but aims to set you up with a thorough understanding of what makes for a good, modern, adaptable PHP environment before you start diving into PHP itself."
} 
```

如果你对构建自己的 API 感兴趣，我推荐这篇 [Sitepoint 文章](https://www.sitepoint.com/building-simple-rest-api-mobile-applications/)或者如果你正在使用 WordPress 并且想把它变成一个 API，[这篇文章](https://www.sitepoint.com/wordpress-json-rest-api/)。

## NSURLSession

从 iOS 7+你可以和`NSURLSession`一起联网。

`NSURLSession`是 Swift 中处理远程数据集的“本地”方式。阿拉莫菲尔就是建立在这项技术上的。以下代码是一个如何从 Wikimedia Commons API 检索登录令牌的示例(尚未投入生产)。

```
class WCLoginApi {

  static func nsurlAction(){

      // Create a session configuration
      let session = NSURLSession.sharedSession()

      var stringUrl: String = "https://en.wikipedia.org/w/api.php"
      stringUrl = stringUrl.stringByAppendingString("?action=query")
      stringUrl = stringUrl.stringByAppendingString("&format=json")
      stringUrl = stringUrl.stringByAppendingString("&meta=tokens")
      stringUrl = stringUrl.stringByAppendingString("&type=login")

      let url: NSURL = NSURL(string: stringUrl)!

      let request = NSMutableURLRequest(URL: url)
      request.HTTPMethod = "GET"
      request.cachePolicy = NSURLRequestCachePolicy.ReloadIgnoringCacheData

      let task = session.dataTaskWithRequest(request) {
          (
          let data, let response, let error) in

          guard let _:NSData = data, let _:NSURLResponse = response  where error == nil else {
              print("error")
              return
          }

          let dataString = NSString(data: data!, encoding: NSUTF8StringEncoding)
          print(dataString)
      }

      task.resume()

  }
}

WCLoginApi.nsurlAction() 
```

首先，您需要会话配置，在本例中，您检索共享会话配置。如果您的应用程序将重复使用相同的通用连接配置，这是一个有用的方法，但如果您希望发出不同的请求，这就不合适了。

检索配置后，您需要构建 url 和参数。在这种情况下，基本 url 是 *[、https://en.wikipedia.org/w/api.php](https://en.wikipedia.org/w/api.php)*，参数是`action`、`format`、`meta`和`type`。`GET`用作 HTTP 动词，因为您正在从服务器检索令牌。

要执行的`task`的创建是更复杂的部分。定义后，`resume`方法异步执行它。在定义`task`时，一个回调作为参数传递，而`print(dataString)`打印从服务器检索的数据。

## 阿拉莫菲尔

Alamofire 是一个固执己见的库，旨在简化 iOS 网络。如果您打算使用更多的自定义网络，例如使用不同的协议，这个库可能不适合您。但是如果你打算使用传统的 REST APIs 和简单的网络，那么我推荐你试试 Alamofire。

你可以用 CocoaPods、Carthage 或者手动安装 Alamofire，所以我推荐你阅读[他们的安装指南](https://github.com/Alamofire/Alamofire)来选择你喜欢的方法。

下面是使用 Alamofire 重新编写的早期示例:

```
import Alamofire

class WCLoginApi {
    static func alamofireExample() -> Void
    {
        Alamofire.request(
            .GET,
            "https://en.wikipedia.org/w/api.php",
            parameters: [
                "action": "query",
                "format": "json",
                "meta": "tokens",
                "type": "login"
            ]).responseJSON { response in

                if let JSON = response.result.value {

                    if let loginTokenKey = JSON.objectForKey("query")?.objectForKey("tokens")?.objectForKey("logintoken") {
                        print(loginTokenKey)
                    }else{
                        print("Error to parse JSON")
                    }

                }else{
                    print("Error with response")
                }
        }
    }
}

WCLoginApi.alamofireExample() 
```

要发出请求，您需要调用将 HTTP 动词作为第二个参数的`request`方法(这些是 Alamofire 中的常量，本例中为`.GET`)。第三个参数是没有任何参数的基本 url，作为一个`String`。

接下来是参数列表(同`NSURLSession`)。在这个例子中，你不用像在`NSURLSession`的例子中那样对它们进行编码，Alamofire 会为你构建它们。您可以自己构建 url 和参数字符串，但这是一种更好的方法。

在处理 Rest APIS 时，会遇到不同的格式。Wikimedia Commons API 支持不同的格式，但正如您所知，它将在 JSON 中，您可以使用`responseJSON`方法。对于不同的格式，还有其他的回调方法。

我觉得这比原生 iOS 的方式更简单，更有结构。有更好的方法用原生方法来构建，但是 Alamofire 使它变得更简单。Alamofire 不仅限于检索数据，还可以发布和下载数据。

Alamofire 允许您更改缓存设置、检查标题、链接响应(例如，使用其他响应类型检查`responseJSON`方法)、验证响应数据、验证用户等等。

## 还有什么？

建立关系网是一项常见的任务，因此您简化流程的选择不仅限于 Alamofire。以下是其他一些可以考虑的库:

*   [SwiftHTTP](https://github.com/daltoniam/SwiftHTTP) :围绕`NSURLSession`的简单 Swift 包装器。
*   莫亚:阿拉莫菲尔的快速替代者。
*   [FSNetworking](https://github.com/foursquare/FSNetworking) : Foursquare 的 Objective-C 联网实现。
*   [Reach](https://github.com/Isuru-Nanayakkara/Reach) :一个简单的 Swift 库，检查你是否有网络。
*   [iOS-netdiag](https://github.com/qiniu/iOS-netdiag):Objective-C 网络诊断库。

在本文中，您了解了 iOS 网络和 REST Apis 的基础知识，iOS 处理网络的默认方式，以及 Alamofire 作为替代。

由于联网是每个开发人员的共同任务，我很想听听你的方法以及哪些技术或库对你最有效。

## 分享这篇文章