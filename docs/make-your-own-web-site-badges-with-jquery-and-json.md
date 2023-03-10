# 用 jQuery 和 JSON 制作你自己的网站徽章

> 原文：<https://www.sitepoint.com/make-your-own-web-site-badges-with-jquery-and-json/>

![jquery-logo-sm](img/4dfce44c010b1ef3e5ad4109480c89a9.png "jquery-logo-sm")

像 [Flickr](http://www.flickr.com/badge.gne) 、 [Delicious](http://delicious.com/help/tools) 和 [Twitter](http://twitter.com/widgets) 这样的服务都提供 JavaScript 徽章或小部件，你可以添加到你的网站上。但是如果你已经在你的站点上使用了一个 JavaScript 框架，比如 jQuery，为什么还要添加更多的 JavaScript 呢？另外，自己做更好玩。所有这些服务都提供了 JSON 格式的 feed API，滚动自己的小部件很容易。这是我用 jQuery 很快为 Twitter 做的一个。你会惊讶于它只需要这么少的代码。

使用 Twitter JSON API 所需要的只是一个 URL:

`http://twitter.com/status/user_timeline/sitepointdotcom.json?count=5&callback=yourfunction`

将`sitepointdotcom`替换为任何 Twitter 用户名，将`count=5`改为您希望检索的 tweets 的数量，将`yourfunction`改为您的回调函数的名称，您就可以开始了。将该 URL 复制到浏览器的地址栏中，您就可以下载 JSON 数据来看看了:

```
yourfunction([ ... ]);
```

输出是对 JavaScript 函数的调用。一个包含 JSON 对象集合的数组(每个 tweet 对应一个对象)作为单个参数传递。这个想法是，您使用上面的 URL 作为源，向您的 web 页面添加一个脚本元素。这对于绕过大多数浏览器中存在的 Ajax 安全限制是必要的，在这些浏览器中，JavaScript 无法对当前域之外的域进行 Ajax 调用。然而，包含来自另一个域的 JavaScript 源文件是没问题的；这是这类徽章的标准工作方式。

然而，我们将使用另一种方法，因为添加另一个脚本标签并确保有一个回调函数对我来说听起来很混乱。我们将在 jQuery 中使用非常方便的`getJSON`函数。首先，我们需要一些 HTML 来存放小部件数据:

```
<div id="tweet">
  <p>
    <a href="http://twitter.com/sitepointdotcom">
        Follow me on Twitter
    </a>
  </p>
</div>
```

这个想法是所有的 Twitter 更新都将作为单独的段落元素嵌入到那个`div`元素中。简单明了。我也喜欢它，因为如果 JavaScript 由于某种原因无法运行，页面上会留下有意义的内容，而不是，比如说，永远不会结束的加载动画。

所以我希望这个脚本在页面准备好之后就可以运行，在 jQuery 中最好的方法是使用`$(document).ready`:

```
$(document).ready(function(){
  //our code goes here...
});
```

所有的`getJSON`方法需要的是一个 URL。这个函数的好处是，如果您在 URL 的末尾添加查询字符串参数`callback=?`，jQuery 将为您处理回调函数:

```
$(document).ready(function(){
  var tweeturl = "http://twitter.com/status/"
      +"user_timeline/sitepointdotcom.json?count=5"
      +"&callback=?";
  $.getJSON(tweeturl, function(data){
    //read the JSON data here...  
  });
});
```

jQuery 会将 JSON 数据作为变量`data`传递给第二个参数中指定的函数。然后，我们可以使用每种方法遍历所有推文，并将它们插入到页面的 HTML 中:

```
$.getJSON(tweeturl, function(data){
  $.each(data, function(i, item) {
    $('<p></p>')
        .addClass(i%2 ? 'even' : 'odd')
        .html(item.text)
        .prependTo('#tweet');
  });
});
```

如果你去[看看演示](https://www.sitepoint.com/examples/tt237/tt237-eg1.html)，你会看到我们现在有一个小部件拉进 Twitter 数据，将每条推文转换成 HTML 段落，并将它们插入我们页面的 HTML。我们还为 tweets 添加了一个`odd`或`even`的`class`值，这样我们就可以在 CSS 中实现交替的背景颜色。到目前为止还不错，但是还缺什么？我认为任何 Twitter 徽章都必须至少链接@回复、标签和 URLs 添加一些 JavaScript 正则表达式可以很好地完成这项工作:

```
var txt = item.text.replace(
  /(https?://[-a-z0-9._~:/?#@!$&'()*+,;=%]+)/ig,
  '<a href="$1">$1</a>'
).replace(
  /@+([_A-Za-z0-9-]+)/ig, 
  '<a href="http://twitter.com/$1">@$1</a>'
).replace(
  /#+([_A-Za-z0-9-]+)/ig,
  '<a href="http://search.twitter.com/search?q=$1">
      +'#$1</a>'
);
$('<p></p>')
  .addClass(i%2 ? 'even' : 'odd')
  .html(txt)
  .prependTo('#tweet');
```

就是这样！去[看看成品](https://www.sitepoint.com/examples/tt237/tt237-eg2.html)。有多简单？

对[好吃的](http://delicious.com/help/feeds)、 [Flickr](http://www.flickr.com/services/api/response.json.html) 、 [Last.fm](http://www.last.fm/api/rest) ，甚至[雅虎 Pipes](http://pipes.yahoo.com/pipes/) 也可以采取同样的做法。还有很大的改进空间；你可以添加时间信息，链接到 tweet URL，添加头像，等等。SitePoint 的另一位技术编辑 Raena 建议，你甚至可以将所有这些服务的数据按时间倒序合并在一起，形成 lifestream 风格的显示。如果你想尝试自己制作，请留下评论，让我知道。

## 分享这篇文章