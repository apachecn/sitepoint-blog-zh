# 使用 Google Feed API 的无压力 RSS

> 原文：<https://www.sitepoint.com/no-stress-rss-with-the-google-feed-api/>

在 Craig 最近关于 Ajax 数据格式的系列文章中，我们[了解了 JSON，](https://www.sitepoint.com/ajax-data-formats-json-jsonp/)一种用于处理 Ajax 页面中数据的简洁高效的格式。但是，如果您处理的是博客、新闻源或通用内容管理系统，RSS 仍然是事实上的标准，JSON 可能不可用。您可以破解您的源代码，在 JSON 中提供您需要的数据，或者您可以编写一个中间件来为您转换 RSS，但有时这是不可能的或不切实际的。

如果是这样的话，为什么不看看谷歌的 Feed API 呢？这是一种获取 RSS 提要并立即将它们用作 JSON 对象的快速而简单的方法:您无需进行任何混乱、忙乱或奇怪的解析工作！你正在检索谷歌的 RSS 缓存副本，所以它是快速和可靠的；此外，它还会减少对提供提要的站点的影响。为了向您展示这有多简单，我们将使用来自 SitePoint 的 RSS 提要的最新新闻构建一个快速的 JavaScript headlines 小部件。

首先，你需要[获取一个谷歌 API 密匙。](http://code.google.com/apis/loader/signup.html)这是可选的，但是快速简单，谷歌强烈建议你这么做。

要开始在我们的页面中使用 Feed API，我们需要在文档的`head`中包含 Google loader。将 API 键作为变量添加到脚本的`src` URL:

```
<script type="text/javascript" src="https://www.google.com/jsapi?key=YOURKEY"></script> 
```

接下来，在我们自己的脚本中，我们将像这样加载 Google Feed API:

```
 google.load("feeds", "1"); 
```

这一行指定了我们想要的模块(`feeds`)和版本，在这个阶段仍然是版本 1。这个`google.load`东西看着眼熟吗？如果您以前见过这种情况，可能是因为您在应用程序中使用过谷歌地图，或者您喜欢使用谷歌托管的 JavaScript 库或 Web 字体。有很多不同的模块可用，所以如果您使用多个模块，您可以将它们一起加载，如下所示:

```
 google.load("feeds", "1");
    google.load("jquery", "1.5.1");
    google.load("maps", "2"); 
```

接下来，我们需要指定一个在文档完全加载后执行的函数——我们使用`google.setOnLoadCallback`来完成。对于一些不寻常的事情，我将使用一个名为`showFeed`的函数来显示我的提要:

```
google.setOnLoadCallback(showFeed); 
```

所有的活动都将在这里进行，在这里我们可以看到 Google Feed API 可以为我们提供的一些选项。我们的第一个程序是建立一个新的对象，名为`feed`。它只需要一个参数，一个 URL:

```
function showFeed() {
  var feed = new google.feeds.Feed("https://www.sitepoint.com/feed/");
} 
```

默认情况下，API 将只提供提要中的四个项目。使用`setNumEntries`方法，我们会要求更多——比如说十个:

```
function showFeed() {
  var feed = new google.feeds.Feed("https://www.sitepoint.com/feed/");
  feed.setNumEntries(10);
} 
```

有一个`setResultFormat`方法允许我们指定一种格式:XML、JSON 或两者的组合结果。因为 API 默认提供 JSON，所以实际上没有必要这样指定它；如果你很好奇，它看起来会是这样的:

```
feed.setResultFormat(google.feeds.Feed.JSON_FORMAT); 
```

如果你想挖掘旧的 RSS 条目，Google 可以返回以前条目的存储副本。这是一个巧妙的诡计！SitePoint 的提要包含足够多的条目供我们使用，所以我们可以在脚本中跳过它，但是您可以像这样使用它:

```
feed.includeHistoricalEntries(); 
```

我们已经准备好了获取素材所需的一切，现在让我们开始加载吧。我们希望每个 RSS 条目在 ID 为`headlines`的列表中显示为列表项，显示每个条目的标题、链接和作者。[API 可以提供所有这些，以及更多的](http://code.google.com/apis/feed/v1/reference.html#resultJson) —内容、类别、发布日期和媒体。值得注意的是能够自动为我们创建每个条目的内容片段，这是一个很好的接触。让我们把那个也扔进去！

`.load`方法请求提要并传递一个结果。结果，我们会收到一个漂亮的胖 JSON 对象，或者如果失败，会收到一个错误代码和消息。回调对结果起作用:如果成功，我们将遍历 JSON 对象中的每个项目，并为每个项目插入一个列表项目。否则，我们将礼貌地失败，并显示一个指向 SitePoint 的链接:

```
feed.load(function(result) {
  if (!result.error) {
    var container = document.getElementById("headlines");
    for (var i = 0; i < result.feed.entries.length; i++) {
      var entry = result.feed.entries[i];
      var li = document.createElement("li");
      li.innerHTML = '<h3><a href="' + entry.link + '">' + entry.title + '</a>' + <cite>by ' + entry.author + '</cite></h3>';
      li.innerHTML += '<p>' + entry.contentSnippet + '</p>';
      container.appendChild(li);
    }
  } else {
    var container = document.getElementById("headlines");
    container.innerHTML = '<li><a href="http://www.sitepoint.com">Get your geek news fix at SitePoint</a></li>';
  }
}); 
```

就是这样！您可以在我们的[演示中看到这个脚本的完整示例。](http://s3.sitepoint.com.s3.amazonaws.com/examples/rss/feed.html)总的来说，只花了几分钟就完成了——比在这篇文章中解释它所花的时间要短得多！

我们已经看到了 Feed API 所能提供的一部分，但是在 [Feed API 文档中还有更多。](http://code.google.com/apis/feed/v1/devguide.html)为无压力的提要解析干杯！

**note:**Want more?

如果你想阅读更多来自 Raena 的文章，请订阅我们每周的科技极客时事通讯， *[《科技时报》](https://www.sitepoint.com/newsletter/)* 。

## 分享这篇文章