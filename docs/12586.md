# 技术宝藏#2: Twitter Buzz Widget

> 原文：<https://www.sitepoint.com/techy-treasure-2-twitter-buzz-widget/>

就我个人而言，我不是推特的狂热粉丝，但它是目前最流行的，甚至像我这样的愤世嫉俗者也能看出它是与你的客户联系的一个有价值的工具。事实上，关于[的发布有太多的讨论，你所知道的关于 CSS 的一切都是错误的！](https://www.sitepoint.com/books/csswrong1/)我们在销售页面上添加了一个简单的“Twitter Buzz”小部件。

在构建我的小部件时，我找不到任何与我想要的东西非常相似的东西，所以我用 PHP 和 T2 jQuery T3 做了一些东西，现在我想与你分享。

## 服务器端 Twitter 代理

这个非常简单。看看这个:

```
<?php
header("Content-Type: text/xml");
// replace Foobar with your URL-encoded search term
echo(file_get_contents(
    "http://search.twitter.com/search.atom?q=Foobar"));
?>
```

在这里，我们简单地从`http://search.twitter.com/search.atom?q=Foobar`获取搜索结果，并将其直接返回给客户端。我们还将响应的内容类型设置为`text/xml`；没有这一点，jQuery 就不知道如何将响应作为 XML DOM 文档来处理。

## 客户端 Twitter Buzz 小部件

有了服务器端代理和 jQuery，我们可以开始将响应注入到文档中。在文档中的某个地方放置一个带有`id` `twitter-buzz`的隐藏`div`，然后包含以下 JavaScript:

```
$(function() {
    $.get("twitter-proxy.php", function(data, status) {
      // check for success
      if(status == "success") {
        // check for entries
        if($("entry", data).size() > 0) {   
          // create the list               
          var list = $("<ul>").get(0);
          // iterate through entries
          $("entry", data).each(function(index, entry) {
            // parse out the details of the tweet
            var authorElement = $("author", entry).get(0);
            var authorName = $("name", authorElement).text();
            var authorUri = $("uri", authorElement).text();
            var authorImage = $("link[rel='image']", entry).attr("href");
            var text = $("title", entry).text();
            // add the tweet to our list
            $(list).append("<li><a href="" + authorUri + "">" +
                "<img src="" + authorImage + "" alt="" + authorName + "" />" +
                "</a><span>" + text + "</span>");
          });
          // add the list to the document
          $("#twitter-buzz").append(list);
          // reveal the area
          $("#twitter-buzz").show("slow");
        }
      }
    });
});
```

这只是从我们之前创建的 Twitter 代理中获取 Atom 响应，创建一个无序的 tweets 列表，然后将它们添加到文档中。即使出错，或者搜索返回零条 tweets，页面也根本不会损坏；我们将在页面中拥有一个隐藏的空的`div`。

当然，这个小部件还可以做一些改进，比如添加定时自动刷新和自动添加@replies 链接，但是我会让你自己添加这些特性。

## 分享这篇文章