# 用 Hpricot 处理 HTML

> 原文：<https://www.sitepoint.com/processing-html-with-hpricot/>

在这个 Web2.0 混搭和简单的 API 访问的世界里，从第三方站点获取数据并将其重新混搭成新的东西是多么容易，这令人耳目一新。不幸的是，并不是每个人都被这个 bug 咬过，所以我们作为开发人员有时不得不多做一些跑腿的工作来获得我们需要的信息。一种常见的技术叫做屏幕抓取，在这种技术中，您的应用程序就像一个浏览器，解析从第三方服务器返回的 HTML。

虽然这应该足够简单，但是任何尝试过这样做的人都知道在试图找到您需要的标签时使用正则表达式的痛苦。幸运的是，我们的 ruby 爱好者有了 Hpricot 库，这使得解析 HTML 变得不那么困难了。Hpricot 允许开发人员通过 CSS 选择器和 X-Path 访问 html 元素，因此您可以非常容易地定位特定的标签。因为它是用 C 写的，所以速度也很快。

## 装置

Hpricot 是一款精品，因此安装非常简单:

```
gem install hpricot
```

只需要 ruby 文件顶部的库:

```
 require 'hpricot' 

```

## 使用

让我们看看这个 HTML 片段:

```
 <html>
  <head>
    <title>Snippet</title>
  </head>
  <body>
    <div id="container">
      <div id="navigation">
        <ul>
          <li><a href="/">Home</a></li>
          <li><a href="/contact></a></li>
        </ul>
       </div>
       <div id="sub-content">
          <p>This would be some sort of sidebar</p>
       </div>
       <div id="content">
         <p>This is paragraph 1</p>
         <p>This is paragraph 2</p>
       </div>
     </div>
   </body>
</html> 

```

通过这样做，我们可以很容易地提取段落的内容(让我们假设 HTML 已经存储在变量@html 中)

```
 doc = Hpricot(@html)

pars = Array.new
doc.search("div[@id=content]/p").each do |p|
  pars << p.inner_html
end 

```

是的，就是这样。现在，您有了一个数组，其中有两个元素与两个 p 标记中的副本相同。注意，子内容 div 中的 p 标记没有被拉入。

然而，事情并没有就此结束，你还可以操作 HTML——如果你想创建一个快速而肮脏的移动版本，它会派上用场。假设我们想从移动版本中删除子内容 div，我们可以这样做:

```
 doc = Hpricot(@html)

doc.search("div[@id=sub-content]").remove

puts doc 

```

结果 HTML 不再有一个叫做子内容的 div！

向导航用户界面添加一个新类非常简单:

```
 doc = Hpricot(@html)

doc.search("div[@id=navigation]/ul").set("class", "nav") 

```

这只是冰山一角——这个库真的很强大，而且简单易用。去官方页面查看更多(不那么琐碎的)例子。

免责声明:在对网站进行截屏之前，你应该确保你得到了网站所有者的许可。

## 分享这篇文章