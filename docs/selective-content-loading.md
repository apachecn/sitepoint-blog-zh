# 选择性内容加载

> 原文：<https://www.sitepoint.com/selective-content-loading/>

我们在[快速响应网页设计](https://www.sitepoint.com/books/responsive1/)中谈到的技术之一叫做选择性内容加载(SCL)。当您希望以结构化的方式将小块数据加载到已经加载的页面中时，这种技术非常有用。这个什么时候有用？

*   当您的服务器和最终用户的设备之间存在严重的带宽问题时(例如，移动连接在有很多错误的不良网络上移动，并且必须处理小区切换)。
*   当您的页面在结构上基本相同时，只需重新加载内容就可以节省很多请求。
*   如果您已经很好地分块了您的内容，并且想要简单地加载序列中的下一个片段(例如，Twitter feed 上的无限滚动)

这些问题中的一部分可以通过良好的缓存和使用本地存储来解决，这些肯定应该作为良好的实践来探索。然而，即使使用智能资产缓存，您仍然需要服务器往返来检索文档，所有页面 HTML 仍然必须发送到浏览器，然后浏览器仍然必须呈现页面。

如果你的页面只增加了一些额外的数据(例如，一条推文)或者只改变了少量的内容(例如，一个产品的细节)，那么 SCL 可能是你的一个好选择。这并不是在每种情况下都行得通，而且它还会导致一些可能的问题，比如 URL、浏览器历史记录以及搜索引擎在“页面”上抓取的内容(如果这对你很重要的话)。

概括一下我们从 RWD 起步的方法，这就是我们要从概念上做的事情:

*   在第一个页面请求中，我们将正常加载所有的页面内容，包括站点导航、内容布局、CSS 和 JS 文件。
*   页面加载后，我们将覆盖页面中的所有链接，使它们成为 xhr，而不是标准的文档请求。
*   然后，我们将处理响应(XHR 响应将只是 JSON 中的内部页面内容，而不是整个页面)，并覆盖页面中的内容。
*   然后，我们可以使用`pushState()`来修改我们的浏览器历史(这样 URL 就会更新为可共享的内容，如果需要的话，我们可以后退)。

这里有一个例子可以简单地说明这一点。为了保持简洁，内容被有意截断。

我们将建立一个页面，可以加载关于书籍的内容，而不必重新加载整个页面，只加载相关的内容。我们还将使用历史 API 中的`pushState()`来确保如果用户想要分享或返回 URL，他们将能够这样做。

为了表达简单，我们将使用 jQuery 进行 DOM 操作，并使用一个名为 [Handlebars.js](http://handlebarsjs.com/) 的 JavaScript 模板库。如果您还没有检查 JavaScript 模板选项以及它们能做什么，Handlebars 是一个很好的选择。

我们的解决方案的核心依赖于这样一个事实，即 URL 可以根据它们是 XHR 还是普通的 HTTP 请求而做出不同的响应。如果服务器得到一个正常的请求，那么视图将会传递完整的 HTTP 响应(包含所有的文档，然后是 JS，CSS 等等)。如果服务器得到一个 XHR，它会用 JSON 来响应，JSON 只包含关于所请求的书的数据。

例如，对于“弗兰肯斯坦”页面的标准 HTTP 响应如下所示:

```
<!DOCTYPE html>
<html>
<head>
  <script type="text/javascript" src="https://ajax.googleapis.com/ajax/libs/jquery/1.8.3/jquery.min.js"></script>

var original = null;
var backtostart = true;

  <script type="text/javascript">
      ($(document).ready(function() {
          var source = $("#story-template").html();
          var template = Handlebars.compile(source);

          var story_link_handler = (function(evt) {
              evt.preventDefault();
              $.get(this.href, function(data) {
                  $("#contentarea").html("");
                  $("#contentarea").html(template(data));
                  history.pushState({url:data.location}, data.title, data.location);
              }, "json");
          });

          $("ul#storylist li a").bind("click", story_link_handler);

          $(window).bind("popstate", function(evt) {
              if (event.state) {
                  url = event.state.url;
                  $.get(url, function(data) {
                      $("#contentarea").html("");
                      $("#contentarea").html(template(data));
                  }, "json");
               backtostart = false;
              } else {
               if (! backtostart) {
                  backtostart = true;
                      $("#contentarea").html("");
                      $("#contentarea").html(original);
               } else {
                 original = $("#contentarea").html();
                    backtostart = false;
               }
            }
          });

      }));
  </script>
</head>
<body>
  <ul id="storylist">
      <li><a href="mobydick">Moby Dick</a></li>
      <li><a href="gulliverstravels">Gulliver's Travels</a></li>
      <li><a href="frankenstein">Frankenstein</a></li>
  </ul>
  <div id="contentarea">
      <article id="story">
          <h1>Frankenstein</h1>
              <h2>Mary Shelley</h2>
              <p>Doctor creates artificial life</p>
          </article>
      </div>
<script type="text/javascript" src="handlebars.js"></script>
      <script id="story-template" type="text/x-handlebars-template">
      <article>
          <h1>{{title}}</h1>
          <h2>{{author}}</h2>
          <p>{{synopsis}}</p>
      </article>
      </script>
  </body>
</html>
```

*注意:你可以在本文末尾链接的 zip 文件中下载本文使用的代码*

但是，对于 XHR，等效的 JSON 响应看起来会是这样的:

```
{
  "location": "/frankenstein",
  "title": "Frankenstein",
  "author": "Mary Shelley",
  "synopsis": "Mad doctor creates artificial life"
}
```

在第一个请求中，请求并加载使选择性加载工作所需的所有代码。之后，我们只获取数据，然后将其加载到模板中。让我们看看代码是如何工作的。

```
  <script id="story-template" type="text/x-handlebars-template">
      <article>
          <h1>{{title}}</h1>
          <h2>{{author}}</h2>
          <p>{{synopsis}}</p>
      </article>
  </script>
```

*注意:你可以在本文末尾链接的 zip 文件中下载本文使用的代码*

Handlebars 使用一个脚本元素来创建一个文章外观的模板(浏览器不会呈现该内容，因为它不会对其类型采取行动)。使用`{{variable}}`语法定义变量位置。您可以使用手柄(条件、循环、块执行等)做更多的事情，但是我们在这个实例中没有使用。注意脚本的 ID，我们需要它，这样我们可以将它传递到 Handlebars 模板编译器中。

在我们的文档就绪函数中，我们从上面定义的模板脚本标签中获取 HTML，然后将它编译成一个模板对象，我们可以在以后使用新数据。

```
  var source = $("#story-template").html();
  var template = Handlebars.compile(source);
```

接下来，我们定义一个可以用于 link `onclick`事件处理程序的函数。这里我们只是通过阻止文件的默认行为来停止对文件的实际请求。在这里，我们创建一个 jQuery XHR，将 JSON 返回到链接的`HREF`属性中定义的 URL。

```
  var story_link_handler = (function(evt) {
      evt.preventDefault();
      $.get(this.href, function(data) {
          $("#contentarea").html("");
          $("#contentarea").html(template(data));
          history.pushState({url:data.location}, data.title, data.location);
      }, "json");
  });
```

当响应返回时，我们只需覆盖保存所有图书数据的`div`内容区域。

```
$("#contentarea").html(template(data));
```

我们还使用`pushState()`将我们刚刚请求的 URL 推送到浏览器历史中，这样我们就可以使用 back 按钮后退。

```
history.pushState({url:data.location}, data.title, data.location);
```

然而，这并不是`pushState()`的全部，为了让它为用户“工作”。接下来，我们在窗口上创建一个`popstate`事件处理程序，这样当用户点击 back 按钮时，我们可以用这本书的适当数据更新内容。在这种情况下，我们将使用 XHR 再次获取数据。使用 pushstate，可以将数据存储在一个`state`对象中。在我们的例子中，数据量很小，给用户的浏览器加载额外的数据(特别是在移动设备上)是不好的做法，所以只有在你能保证数据量很小的情况下才这样做。

```
  $(window).bind("popstate", function(evt) {
      if (event.state) {
          url = event.state.url;
          $.get(url, function(data) {
              $("#contentarea").html("");
              $("#contentarea").html(template(data));
          }, "json");
      }
  });
```

对于这种技术，我们需要考虑的一件事是当浏览器回到列表的开始时会发生什么。也就是说，您已经从堆栈中弹出了所有的 xhr，并且回到了开始的地方。

为了补救这一点，我们使用一个标志来确定我们是否回到了起点，并且我们保存了在`#contentarea`中的内容，以便我们可以替换它。您可以使用其他技术，比如简单地隐藏原始内容区域或存储原始文档的 JSON。

然后我们更新`popstate`事件来检查是否没有`event.state`。如果是这样，我们就回复到原来的样子。

```
$(window).bind("popstate", function(evt) {
              if (event.state) {
                  url = event.state.url;
                  $.get(url, function(data) {
                      $("#contentarea").html("");
                      $("#contentarea").html(template(data));
                  }, "json");
               backtostart = false;
              } else {
               if (! backtostart) {
                  // revert the content to the original
                  backtostart = true;
                      $("#contentarea").html("");
                      $("#contentarea").html(original);
               } else {
                 // store original content to retrieve later
                 original = $("#contentarea").html();
                    backtostart = false;
               }
            }
          });
```

最后，我们向所有相关链接添加我们的`click`事件处理程序。在我们的例子中，我们只是使用列表中的链接，但是实际上你可以基于`class`或`HREF`属性对整个范围的链接都这样做。

```
$("ul#storylist li a").bind("click", story_link_handler);
```

页面的其余部分是页面结构和请求的实际内容——在本例中是`/frankenstein` URL。

可以看出，这种方法为我们提供了一个良好的、响应迅速的设置。最初的页面请求稍微重一点(在本例中大约 1Kb ),但是提供了布局页面和提供交互所需的所有框架。后续请求的好处是只需返回非常小的数据片段，然后将这些片段加载到模板中。

使用`pushState()`更新 URL，这意味着用户仍然可以使用意图或复制和粘贴来共享 URL，并且该 URL 将为共享它的任何人正常工作。你还会得到每个文档仍然存在的好处——这意味着搜索引擎仍然可以正确地索引你的站点，如果需要的话。

对于这种技术，我们需要注意的一点是，如果我们的内容存在于许多不同的模板中，那么通过 XHR 仅加载数据片段的好处将会被破坏，因为必须将所有不同的内容模板加载到第一个页面请求中，并在使用之前对用户隐藏它。不要忘记，所有的 HTML 仍然需要被加载，不管它是否被使用。

这种技术特别适用于“无限滚动”场景，比如内容流或很长的文章。内容类型几乎没有变化(或者只是在一个非常明确的范围内)，这种技术对“页面内容”整体上的影响就像惰性加载技术对页面中的图像的影响一样。如果你花时间把你的内容分块，这可能特别有效，因为这意味着你可以避免用户点击“转到第二页”,即使搜索引擎会愉快地跟随。

[下载本文使用的代码文件](https://blogs.sitepointstatic.com/examples/selective-content-loading/scl_files.zip)

在 Andrew 与 Craig Sharkie 合著的新书中深入探究响应式网页设计的世界:[快速启动响应式网页设计](https://www.sitepoint.com/books/responsive1/)。

## 分享这篇文章