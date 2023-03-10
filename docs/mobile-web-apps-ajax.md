# 移动网络应用:Ajax

> 原文：<https://www.sitepoint.com/mobile-web-apps-ajax/>

你不会相信这是由[厄尔·卡斯尔丁](http://twitter.com/#!/mrspeaker)、[迈尔斯·埃夫托斯](http://twitter.com/#!/madpilot)和[马克斯·惠勒](http://twitter.com/#!/makenosound)所著的《为智能设备构建移动网站和应用》一书中的第五段摘录。BuildMobile 正在出版这本书的“移动网络应用”一章，这一节叫做“Ajax ”,它变得越来越重要了。

## 5.埃阿斯

我们现在已经学会了如何在页面之间平滑地转换，而不需要重新加载，但是到目前为止，我们只对静态内容这样做。我们需要能够动态加载我们的页面，然后*然后*转换到它们。

好消息是高端移动设备对 Ajax 的支持相对较好——所以 Ajax 的工作在很大程度上仍然和你在桌面上习惯的一样。当然，你必须考虑到平均数据连接会慢一个数量级(也更贵)，所以最好将你的带宽使用保持在绝对最低水平。

有两种方法可以将动态数据加载到我们的应用程序中。我们可以加载原始数据(比如代表最近出现的名人的 JSON 对象列表),并将其合并到应用程序的 HTML 中——创建列表元素并将它们附加到页面上。或者，我们可以直接从服务器加载整个 HTML 内容，并直接将其转储到我们的页面中。后一种方法听起来更简单——所以让我们先试着熟悉一下移动环境中的 Ajax。之后，我们将看看如何处理其他数据格式。

### 5.1.获取 HTML

如果我们想从服务器上检索数据，我们需要的第一件东西是…服务器！如果您试图从一个`file://`协议 URL 获取数据(如果您通过双击一个`index.html`文件来测试页面，就会出现这种情况)，您将会遇到可怕的“Access-Control-Allow-Origin”错误。

### 服务器

因为这是一本关于用 HTML、CSS 和 JavaScript 构建移动 web 应用程序的书，所以很遗憾，涵盖设置服务器来交付数据的细节超出了我们的范围。在本章的剩余部分，我们将看一些可以添加到 Startrackr 中的 Ajax 功能的例子，但是如果您想亲自尝试这些例子，您需要设置一个服务器来传递适当的数据。

假设我们有一个服务器——无论是运行在本地机器上、虚拟机上还是互联网上——我们现在需要一个地方来转储从服务器返回的 HTML 块。为此，我们只需要一个我们正在处理的页面的框架，作为我们要填充的容器。因为我们将像以前一样返回相同的标记，所以我们的原始 CSS 将应用于内容，而不需要修改。这是骨架:

**例 4.52。`ch4/13-ajax.html`(节选)**

```
<div id="pages">
  <div id="page-spots" class="page-spots"></div>
  <div id="page-spot" class="page-spots"></div>
  <div id="page-sightings" class="page-sightings"></div>
  <div id="page-stars" class="page-stars"></div>
  <div id="page-star" class="page-stars"></div>        
</div>
```

每个部分的所有内容都在`spots.html`、`new.html`和`stars.html`文件中——就像一个普通的网站一样，只是现在我们将使用 Ajax 将内容加载到这个空框架中。

有了这个基本的 HTML，下一步就是阻止链接被点击，就像我们之前用`preventDefault()`转换函数一样。然后我们可以执行我们的 Ajax。jQuery `load()`函数非常适合我们的需求:它从 URL 加载 HTML，并提供一种机制来选择返回文档的哪一部分。这很棒，因为我们不需要整个页面，包括 head 和 meta 标签——我们只需要`body`的内容。使用`load()`意味着我们不需要针对 Ajax 的 HTML 页面的特殊版本，任何更新都只需要在一个地方进行。

为了实现这一点，我们使用带有一个字符串参数的`load()`函数，该字符串参数由我们想要的 URL、一个空格、一个 jQuery 选择器字符串组成。选择器匹配的元素的内容将被插入到调用`load()`的元素中。我们要插入的内容包含在。目标页面的包装 div。因此，当点击 Spots 链接时，我们想调用#spots 容器上的`load()`,并传入字符串“spots.html”。包装材料:

**例 4.53。`javascripts/ch4/13-ajax.js`(节选)**

```
$("#tab-spots a").click(function(e){
  e.preventDefault();
  $("#page-spots").load("spots.html .wrapper");
});
```

### 加载 HTML 片段

如果您不熟悉 jQuery，您可能想知道它是如何通过 Ajax 加载到一小部分 HTML 中的。这里没有魔法。它实际上加载了整个页面，并将其转储到 DOM 之外的一个`div`元素中。对该元素进行过滤，并将结果插入正确的位置。非常方便，尽管这当然意味着通过网络传输的数据比你实际使用的要多。对于大多数现实世界的应用程序，您可能希望提取 XML 或 JSON 格式的数据，并在客户端将其插入到 HTML 中。我们很快就会看到如何做到这一点。不过现在，为了简单起见，我们将继续使用`load()`,并重点演示各种 Ajax 方法是如何工作的，以及如何在移动应用程序的上下文中最好地使用它们。

这将把相关的 HTML 加载到正确的容器中，但是还有一些任务需要注意——最值得注意的是，使新内容可见！幸运的是，`load()`允许您指定一个回调函数，这个函数将在 Ajax 调用完成后执行。在这个回调函数中，我们将把页面转换成视图:

**例 4.54。`javascripts/ch4/13-ajax.js`(节选)**

```
$("#tab-spots a").click(function(e){
  e.preventDefault();
  $("#page-spots").load("spots.html .wrapper", function() {
    transition('#page-spots', "fade", false);
  });
});
```

### 5.2.Ajax 化链接

向每个导航项目添加事件是连接我们站点的一种繁琐的方式。我们刚刚为 Spots 页面制作了一个 Ajax 加载器，但是我们必须多次复制这段代码，才能让它为所有链接工作。相反，我们可以利用我们站点的一致结构，根据导航元素的内容，设计一个系统来以编程的方式完成这项工作。这样做给了我们一个渐进的增强级别:我们的链接被设计为正常工作，但是当被点击时我们的系统会拦截它们并通过 Ajax 加载内容。这种方法有时被称为 **Hijax** 。

然后我们将一般化我们编写的 Ajax 代码，以便它可以应用于我们传递给它的任何链接。为此需要两部分数据:要加载的 URL 和要转储的容器的名称。这就是召开会议的重要性。只要我们的页面和类以一致的方式命名，我们就可以轻松地创建针对所有链接的代码:

**例 4.5### 5。`javascripts/ch4/14-hijax.js`(节选)**

```
function loadPage(url, pageName) {
  $("#" + pageName).load(url + " .wrapper", function(){
    console.log(this);
    transition("#" + pageName, "fade", false);
  });
};
```

这个函数与之前的代码几乎相同，只是我们用变量替换了页面名称和 URL。现在我们可以以编程方式加载页面，例如:

loadPage("spots.html "，" spots ")；
事实上，我们需要在应用程序加载时默认加载一个页面，所以我们可以将那行代码放在`document.ready`处理程序中。这将通过 Ajax 加载 Spots 页面作为我们的主页。

### 数据缓存

因为`load()`方法拉入整个 HTML 文件，结果可以被浏览器缓存，这意味着您在被加载的页面中所做的更改可能不会立即反映出来。您可以使用`$.ajaxSetup({ cache: false });`全局禁用缓存(针对所有 Ajax 请求),或者，如果只是针对一个特定的调用，您可以在 URL 后面附加一个时间戳，这样浏览器就会发现每个请求都是不同的。所以，不用装载`url + " #wrapper"`，你可以装载`url + "?" + new Date().getTime() + " #wrapper"`。

最后，每当任何导航项目被点击时，我们希望调用我们的函数。记住，我们必须提取两部分数据传递给我们的函数:URL 和页面名称。URL 很简单——它是链接本身的 href 值。对于页面名称，我们可以采取许多方法:我们可以给容器取与文件相同的名称(去掉`.html`)，或者我们可以在链接本身中添加一些额外的数据。随着 HTML5 中自定义数据属性的增加，后者变得更加容易，这使我们可以用键/值对来注释元素:

**例 4.56。`ch4/14-hijax.html`(节选)**

```
<ul id="tab-bar">
  <li>
    <a data-load="spots" href="spots.html">Spots</a>
  </li>    
  <li>
    <a data-load="sightings" href="new.html">Add a sighting</a>
  </li>
  <li>
    <a data-load="stars" href="stars.html">Stars</a>
  </li>
</ul>
```

数据属性以`data-`开头，后面是您的键名。然后，您可以为它提供您喜欢的任何值。根据规范，应该使用元素的 dataset 属性检索这些值；然而，这还没有得到广泛的支持，所以你最好的选择是使用标准的`getAttribute()`函数(比如，`myElement.getAttribute("data-load")`)，或者，如果你使用 jQuery，使用`attr()`方法:

**例 4.57。`javascripts/ch4/14-hijax.js`(节选)**

```
$("#tab-bar a").click(function(e){
  e.preventDefault();
  var url = e.target.href;
  var pageName = $(this).attr("data-load");
  loadPage(url, pageName);
});
```

*瞧！*`#tab-bar`元素中的任何链接都将触发我们的`loadPage()`函数，我们将从当前页面过渡到新页面。如果您愿意，您可以很容易地扩展这个系统来指定转换类型。

当前的一个问题是，如果页面需要很长时间加载，用户不知道发生了什么(他们可能会变得乐于点击并试图加载另一个页面)。显而易见的解决方案是“装载”指示器；如果你现在就想添加一个，跳到第 6 章，“完善我们的应用程序”

*[ **Ed** :这意味着你可以购买《为智能设备构建移动网站和应用》这本书，也可以等到我们将来发布第 4 章第 6 节《完善我们的应用》的时候。]*

### 构建移动图书

您可以从 Sitepoint 购买《为智能设备构建移动网站和应用程序》一书。阅读完整的[第 4 章。移动网络应用](https://www.sitepoint.com/build-mobile-book/)，独家在 BuildMobile，在下面的部分是免费的。

*   [开店](https://www.sitepoint.com/mobile-web-apps-setting-up-shop/)
*   [事件](https://www.sitepoint.com/mobile-web-apps-events/)
*   [速赢](https://www.sitepoint.com/mobile-web-apps-quick-wins/)
*   [加载页面](https://www.sitepoint.com/mobile-web-apps-loading-pages/)
*   埃阿斯
*   [模板化](https://www.sitepoint.com/mobile-web-apps-templating/)
*   我们有一个应用程序！

## 分享这篇文章