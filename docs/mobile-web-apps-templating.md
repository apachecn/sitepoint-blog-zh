# 移动 Web 应用程序:模板化

> 原文：<https://www.sitepoint.com/mobile-web-apps-templating/>

在由 [Earle Castledine](http://twitter.com/#!/mrspeaker) 、 [Myles Eftos](http://twitter.com/#!/madpilot) 和 [Max Wheeler](http://twitter.com/#!/makenosound) 所著的《为智能设备构建移动网站和应用》一书中的第六段摘录中，我们了解了模板化。BuildMobile 正在出版这本书的“移动网络应用”一章，这一节叫做“模板”。

## 6.模板

通过 Ajax 引入一整块现成的 HTML，可以很好很容易地粘贴到页面中，但通常这不是从 web 服务或 API 接收的数据格式。这意味着我们有责任将数据转化为标记。即使您控制着后端，用 XML 或 JSON 而不是 HTML 传输您的站点数据仍然是有意义的，因为这些文件通常会小得多，并且需要的移动用户的宝贵带宽也更少。

那么，这将如何工作呢？我们从接收来自服务器的数据列表开始。对于列表中的每一项，我们希望创建一个 HTML 片段(比如一个列表项)并从数据中注入一些值。最后，我们希望将每个片段添加到页面中的正确位置。有几种常见的方法来处理这种情况:

*   用 JavaScript 自己构建 HTML 片段
*   复制一个“模板”DOM 元素并改变相关标签的内容
*   使用模板引擎

我们将快速浏览每种方法，看看什么在什么环境下最有效。我们将假设我们的数据源是 JSON 格式的，看起来有点像这样:

**例 4.58。`data/spots.json`(节选)**

```
[{
  "id": 4,
  "name": "Planet Bollywood", 
  "distance": "400m", 
  "sightings": 10
}, {
  "id": 7,
  "name": "Soft Rock Café", 
  "distance": "1.1Km", 
  "sightings": 3
}]
```

这是一个简单的数组，每个元素由一个包含位置细节的对象组成。对于我们可能收到的数据来说，这是一种非常常见的(尽管是简化的)格式。要将它提取到我们的应用程序中，我们可以使用 jQuery 的`getJSON()`方法:

**例 4.59。`javascripts/ch4/15-templating.js`(节选)**

```
$.getJSON("../data/spots.json", function(data){
  // Got JSON, now template it!
});
```

口袋里有了一些数据，是时候向世界展示了。我们的三种方法中的第一种是循环遍历`data`数组，构建可以添加到 DOM 中的 HTML 字符串:

**例 4.60。`javascripts/ch4/15-templating.js`(节选)**

```
$.getJSON("../data/spots.js", function(data){
  // Got JSON, now template it!
  var html = "";
  for(var i = 0; i < data.length; i++) {
    html += "<li><a href='#'>";
    html += "<h2>" + data[i].name + "</h2>";
    html += "</a></li>";
  }
  $("#spots-list").append(html);
});
```

这是一种非常老派的方法，虽然它可以用于非常简单的例子，但是它的可伸缩性很差:它容易出错，难以维护，并且很快就会变成一大堆混杂在一起的 HTML 和数据。

数据和表示的分离非常重要，对于 web 应用程序来说尤其如此。使用前面的方法，如果一个设计者想对 HTML 进行修改，他们要么必须知道一些 JavaScript(并有可能破坏某些东西)，要么来烦你。我们希望避免被打扰，所以更好的解决方案是将 HTML 放在它应该在的地方——HTML 页面中:

**例 4.61。`ch4/16-templating-2.html`(节选)**

```
<div id="tmpl-simple" style="display:none;">
  <li>
    <a href="spot.html" data-load="spot">
      <h2></h2>
      <span class="relative-distance"></span>
      <span class="sightings"></span>
    </a>
  </li>
</div>
```

我们构建了一个没有任何数据的通用片段。当我们从服务器接收数据时，我们可以克隆这个模板并用我们的数据填充它。只要有一些钩子可以让我们将数据注入其中，它就可以像需要的那样复杂。当然，您不希望向用户显示列表中的空行，因此需要隐藏模板。在上面的例子中，这是使用`display: none;`完成的。

为了利用这个片段，我们将使用 jQuery 的`clone()`方法克隆它，然后使用`text()`方法将所有数据片段注入到它们正确的元素中:

**例 4.62。`javascripts/ch4/16-templating-2.js`(节选)**

```
$.getJSON("../data/spots.json", function(data){
  // Got JSON, now template it!
  $.each(data, function(){
    var newItem = $("#tmpl-simple").clone();

    // Now fill in the fields with the data
    newItem.find("h2").text(this.name);
    newItem.find(".relative-distance").text(this.distance);
    newItem.find(".sightings").text(this.sightings);

    // And add the new list item to the page
    newItem.children().appendTo("#spots-list")
  });
  transition("#spots", "show");
});
```

这种解决方案效果很好，将所有的 HTML 放在一个文件中，将所有的 JavaScript 放在另一个文件中。这比我们的第一种方法好得多，并且适用于小型静态模板。但是如果 HTML 不断变化，这就不太理想了，对于大型页面，这需要我们编写大量通用 JavaScript 来替换所有字段值。

这是那种我们想自动化处理掉的令人麻木的代码。也许我们可以在 HTML 中添加一些可以从给定的数据对象中自动替换的标记？这是一个很好的想法，事实证明很多开发人员以前都有过:它被称为模板引擎。选择一个模板引擎就像选择一个文本编辑器:有很多，它们有很多不同的特性，大多数都很好，最终你(或你的公司)最喜欢哪一个取决于你。

你会看到一个被反复提及的“大块头”是胡子。它可用于许多编程语言，并且所有语言的模板格式都是相同的——这使得它值得熟悉(尽管这也意味着我们不能使用 JavaScript 的点符号，因为它与其他一些语言冲突)。

然而，鉴于我们已经在使用 jQuery，我们很幸运。在 2008 年，John Resig(jQuery 的创始人)发布了一个新颖的非常小的模板引擎，由大约 20 行 JavaScript 组成。它已经扩展成一个完整的 jQuery 库，被认为是官方认可的 jQuery 模板引擎:[代码可以从 GitHub](https://github.com/jquery/jquery-tmpl) 上的 jQuery 库获得，[文档位于 jQuery 插件站点](http://api.jquery.com/category/plugins/templates/)。

要使用该库，请从 GitHub 下载存档文件，并解压缩最小化的文件。它将被称为`jquery.tmpl.min.js`，大小只有 8KB 左右。将它复制到您的项目中以加载它:

```
<script src="jquery.tmpl.min.js" type="text/javascript"></script>
```

### 6.1.Twitter 与模板的集成

在推出了重要的工具 jQuery 模板引擎之后，我们需要一些更有趣的东西来使用它。客户一直在考虑将一些 Twitter 数据整合到应用程序中。具体来说，他们注意到 Twitter 在名人跟踪方面也做得很好——他们希望在他们的应用程序中利用这种跟踪能力。

该计划是使用公共搜索查询词“名人识别”，并显示任何匹配的推文。一般来说，你不能发出跨域 Ajax 请求(这是一个安全问题——事实上，这个安全问题阻止你向`file://`URL 发出 Ajax 请求)——但是 Twitter 提供了 *JSONP* 格式的数据。JSONP 是一种常见的跨域获取数据的技巧，它将数据加载到一个`<script>`标签中。为此，我们需要将字符串`"callback=?"`追加到 URL 请求中。

我们已经查看了 [Twitter API 文档](http://dev.twitter.com/doc/)，并找到了合适的 URL 来进行搜索:`http://search.twitter.com/search.format?q=query`。我们需要做的就是用那个 URL 和我们的搜索字符串调用`getJSON()`:

**例 4.63。`javascripts/ch4/17-twitter-templating.js`(节选)**

```
var twitQuery = "celeb+spotting",
    twitUrl = "http://search.twitter.com/search.json?q=";

$.getJSON(twitUrl + twitQuery + "&amp;callback=?", function(data){
  // Show some tweets
});
```

现在我们有了一些来自 Twitter 的数据！tweets 作为数组存储在`data`对象的 results 属性中，所以我们可以在模板系统中直接使用它们。

模板引擎中使用的模板的定义方式与我们之前放在一起的简单模板相同:作为 HTML 文件中的元素。jQuery 模板引擎使用了一个聪明的技巧来防止模板成为 DOM 的常规部分:它隐藏在带有`type="text/x-jquery-tmpl"`的`<script>`标签中。浏览器不知道如何处理这种类型的“脚本”，所以它们不会尝试执行它，但我们仍然可以提取内容并将其用作模板:

**例 4.64。`ch4/17-twitter-templating.html`(节选)**

```
<script id="tmpl-tweet" type="text/x-jquery-tmpl">
  <li>
    <a class="avatar" href="#"><img src="${profile_image_url}" alt="${from_user}"></a>
    <a href="http://twitter.com/${from_user}"><h2>${from_user}</h2></a>
    <span class="details">
      ${text}
    </span>
  </li>
</script>
```

### 模板类型

即使您没有使用 jQuery 模板引擎，您也可以在自己的模板中使用`<script>`技巧。你应该使用不同的选项来选择`x-jquery-tmpl`;如果你愿意，你可以自己编一个`type`，但是你会经常看到被使用的`text/html`。

你会注意到在 HTML 里面有一些奇怪的东西。这些是我们的令牌，将被数据取代。花括号内的字符串不是虚构的:它们是从 Twitter 返回给我们的值的名称。为了找出给定 API 调用返回的确切数据，您可以阅读文档，或者使用`console.log(data);`将数据对象记录到控制台。

现在剩下要做的就是调用模板函数。只需通过 id 选择模板项(我们已经调用了我们的`tmpl-tweet`)，并调用`tmpl()`方法，通过 Ajax 传递我们检索到的`data.results`数组。引擎将看到我们的数组有多个项目；对于每一项，它将生成模板的克隆，并用数据数组中的相应值替换所有标记。结果是一个包含构造的 DOM 节点的 jQuery 对象，我们可以像往常一样将它注入页面:

**例 4.65。`javascripts/ch4/17-twitter-templating.js`(节选)**

```
$.getJSON(twitUrl + twitQuery + "&amp;callback=?", function(data){
  $("#tmpl-tweet")
    .tmpl(data.results)
    .appendTo("#spots-list");
    transition("#spots", "show");
});
```

不需要做任何循环或手动插入值。数据丰富的 API 和简单的模板引擎的结合为开发人员提供了强大的能力，使您能够轻松地用各种来源的信息丰富您的应用程序。图 4.5 显示了我们的 Twitter 搜索的一个示例结果，“从 Twitter 获取名人观察”。

**图 4.5。从推特上获取名人信息**

[![fig_4_twitter](img/56302cd17e766c7b2a071620e7cc3227.png)](https://www.sitepoint.com/wp-content/uploads/2011/07/fig_4_twitter.png)

jQuery 模板引擎还包括类似于`{{if}}`和`{{else}}`的条件求值标签，因此您可以根据情况显示模板的不同部分。查看[文档了解可用功能的完整分类](http://api.jquery.com/jquery.tmpl/)。

### 构建移动图书

您可以从 Sitepoint 购买《为智能设备构建移动网站和应用程序》一书。阅读完整的[第 4 章。移动网络应用](https://www.sitepoint.com/build-mobile-book/)，独家在 BuildMobile，在下面的部分是免费的。

*   [开店](https://www.sitepoint.com/mobile-web-apps-setting-up-shop/)
*   [事件](https://www.sitepoint.com/mobile-web-apps-events/)
*   [速赢](https://www.sitepoint.com/mobile-web-apps-quick-wins/)
*   [加载页面](https://www.sitepoint.com/mobile-web-apps-loading-pages/)
*   Ajax
*   模板
*   我们有一个应用程序！

## 分享这篇文章