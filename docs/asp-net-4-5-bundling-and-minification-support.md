# ASP.NET 4.5 捆绑和缩小支持

> 原文：<https://www.sitepoint.com/asp-net-4-5-bundling-and-minification-support/>

如果你对网络开发很认真，你会为一个快速的网站而努力。没有什么比坐下来快速浏览一个伟大的网站更好的了。速度很重要。你的顾客期待它。如果你认为他们不知道，你生活在一个和我不同的世界。随着 2011 年消费者网上购物的趋势越来越强，开发者不得不应对消费者用来浏览你的网站的额外设备。

有许多方法可以提高网站的性能，但是目前为止影响最大的方法是减少 HTTP 请求的数量。每当你引用一个图像、CSS 文件、JavaScript 文件、视频、音频或 flash 文件时，都会增加一个额外的 HTTP 请求。这些时间可以用在其他地方，比如接客户订单！

减少 HTTP 请求数量的一种方法是合并文件。如果您的网页中有三个样式表，那就是三个独立的 HTTP 请求。将它们组合成一个文件意味着只有一个 HTTP 请求。

你可以更进一步，在这个过程中加入缩小。缩小是从 CSS 和 JavaScript 文件中去掉所有空白和注释的过程。

如果你熟悉 ASP.NET，打包和缩小总是你构建过程中的一项工作。随着 Visual Studio 2011 和 ASP.NET 4.5 的出现，微软已经添加了捆绑和缩小功能，在我看来这是早就应该做的。这个过程发生在运行时，可用于 ASP.NET WinForms，MVC 和网页。

### 装置

在开始任何开发之前，你需要安装 ASP.NET 4.5。最简单的方法是通过 Web 平台安装程序。我创作的所有 ASP.NET 4.5 文章都是在 Visual Studio 2011 开发者预览版中开发的。以下是开始使用的链接。

*   [用于 Visual Studio 2010 的 ASP.NET MVC 4](http://www.microsoft.com/web/gallery/install.aspx?appid=MVC4VS2010&prerelease=true)
*   [ASP.NET MVC 4 for Visual Studio 2011 开发者预览版](http://www.microsoft.com/web/gallery/install.aspx?appid=MVC4VS11&prerelease=true)

### 为什么要这么做？

答案很简单；减少客户端和服务器之间的 HTTP 请求数量。结果是一个更快的网站。默认情况下，当您创建一个新的 MVC 4 网站时，以下 JavaScript 文件会被加载到页面中。

```
<script type="text/javascript" src="../../Scripts/jquery-1.6.2.min.js"></script>
<script type="text/javascript" src="../../Scripts/jquery-ui-1.8.11.js"></script>
<script type="text/javascript" src="../../Scripts/modernizr-2.0.6-development-only.js"></script>
<script type="text/javascript" src="../../Scripts/AjaxLogin.js"></script>
```

通过 Chrome 查看，您可以看到 4 个独立的 HTTP 请求。

我们可以做得更好！在您捆绑它之后，您将看到一个请求。

### 魔力在哪里？

神奇的事情发生在 ASP.NET 4.5 的运行时。不用分别引用每个 JavaScript 文件，您可以将它们全部替换为:

```
<script src="scripts/js"></script>
```

您还可以通过添加以下代码来捆绑和缩小您的 CSS 文件:

```
<link href="content/css" rel="stylesheet" />
```

这是假设您的 JavaScript 文件在一个名为 *scripts* 的文件夹中，样式表在一个名为 *content* 的文件夹中。当然，这是可以配置的——您很快就会看到。

在此工作之前，您需要在*应用程序启动*事件中的 *global.asax* 文件中添加一行代码。

```
Bundle.Bundles.EnableDefaultBundles();
```

当 ASP.NET 4.5 发布时，将不再需要这一行代码。当网站运行时，如果 ASP.NET 遇到这些标签中的任何一个，它将自动捆绑并缩小给定文件夹中的每个文件，并发回 JavaScript 文件的单个 HTTP 响应和 CSS 的单个响应。开箱即用，您不需要做任何其他事情。这是一个受欢迎的特性。

默认情况下，当 ASP.NET 捆绑文件时，它们是按字母顺序排列的。如果有已知的库，比如 jQuery、jQuery UI 和 Dojo，那么首先加载它们。对于 CSS 文件，它们也是按照字母顺序打包的。结果可以在下图中看到。

![](img/a349b1cb0fb7006d6db3ee0e245afe89.png)

自定义规则

如果默认捆绑规则不能提供您所需的控制，您可以随时创建自己的捆绑规则。这样做的一个常见原因是对公共库进行分组。需要将整个 JavaScript 或 CSS 文件捆绑到一个文件中的情况并不多见。要创建自定义规则，您需要创建一个新的 [Bundle](http://bit.ly/zt2IAA) 对象。然后，您可以单独添加文件或添加整个目录。

```
var jSBundle = new Bundle("~/CustomJs", typeof(JsMinify));
jSBundle.AddFile("~/Scripts/CustomFunction.js");
jSBundle.AddFile("~/Scripts/jquery-1.4.1-vsdoc.js");
jSBundle.AddFile("~/Scripts/jquery-1.4.1.js");
jSBundle.AddFile("~/Scripts/JSONCreate.js");
```

注意上面的类型 [JsMinify](http://bit.ly/xGMnls) ？这是捆绑和缩小 JavaScript 文件的默认对象。对于 CSS，你使用 [CssMinify](http://bit.ly/x94MaL) ，如下例所示。

```
var cssBundle = new Bundle("~/CustomCss", typeof(CssMinify));
cssBundle.AddFile("~/Content/Collection.css");
cssBundle.AddFile("~/Content/GlobalSupport.css");
cssBundle.AddFile("~/Content/MasterStyle.css");
cssBundle.AddFile("~/Styles/MenuStyle.css");
```

为了引用这些定制规则，您将每个包的名称放在 HTML 中。

```
<script src="CustomJs"></script> or <link href="CustomCss" rel="stylesheet" />
```

### 定制加工

如果您想要完全控制，您可以覆盖默认的 CSS 和 JavaScript 绑定支持，并用一个定制的流程来替换它。一个简单的方法是创建一个实现 [IBundleTransform](http://bit.ly/xN47nV) 接口的类。下面的例子很简单，但它演示了如何做到这一点。该示例将公司版权插入到每个 JavaScript 文件中，并为文件设置默认缓存。

```
public class AddCopyrightToFiles : IBundleTransform
{
    public void Process(BundleResponse bundle)
    {
        StringBuilder sb = new StringBuilder();
        sb.AppendLine("// Copyright your company");
        sb.AppendLine(bundle.Content);
        bundle.Content = sb.ToString();
        bundle.Cacheability = HttpCacheability.ServerAndNoCache;
    }
}
```

为了使用这个定制过程，创建一个新的包并引用定制类。

```
BundleTable.Bundles.EnableDefaultBundles();
Bundle customBundle = new Bundle("~/CustomBundle", typeof(AddCopyrightToFiles));
customBundle.AddFile("~/Scripts/CustomTypes.js");
BundleTable.Bundles.Add(customBundle);
```

在 HTML 中，只需通过名称引用包。

```
<script src="CustomBundle"></script>
```

### 解开 JavaScript 文件

值得注意的是，有一些服务会把你的文件拆开，或者美化它们。我爱用的是[jsbeautiier](http://jsbeautifier.org/ "jsbeautifier")。这会使文件变平。捆绑和缩小的最大缺点之一是可读性。

## 分享这篇文章