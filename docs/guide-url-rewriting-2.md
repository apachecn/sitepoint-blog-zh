# URL 重写初学者指南

> 原文：<https://www.sitepoint.com/guide-url-rewriting-2/>

## 什么是 mod_rewrite 解决方案？

但是它到底是干什么的呢？嘿！这篇文章的重点来了！

mod_rewrite 捕获满足特定条件的 URL，并按照要求重写它们。

例如，您可以拥有一个不存在的

```
http://www.mysite.com/anything
```

重写为以下内容的 URL:

```
http://www.mysite.com/deep/stuff/very_complicated_url?text=having_lots_of_extra_characters
```

你期待更多吗？耐心点…

```
<IfModule mod_rewrite.c>  
RewriteEngine on  
RewriteRule ^/shortcut$ /complicated/and/way/too/long/url/here  
</IfModule>
```

当然，这也应该再次放入 httpd.conf 文件中(您甚至可以将其放入 virtualhost 上下文中)。

重启 Apache 后(你很快就会习惯的！)您可以在浏览器中键入以下内容:

```
http://localhost/shortcut
```

如果在你的文档根目录中存在一个目录结构`/complicated/and/way/too/long/url/here`，你将被“重定向”到那里，在那里你将看到这个目录的内容(例如，目录列表，index.html，无论有什么)。

为了更好地理解 mod_rewrite，重要的是要知道这不是真正的重定向。“经典”重定向是通过 HTTP 协议的 Location: header 完成的，它告诉浏览器自己转到另一个 URL。有许多方法可以做到这一点，例如，在 PHP 中，您可以编写:

```
<?  
// this PHP file is located at http://localhost/shortcut/index.php  
header  
("Location: /complicated/and/way/too/long/url/here");  
?>
```

这段代码通过将 HTTP 头发送回浏览器来显示同一个页面。这个标题告诉浏览器立即移动到另一个 URL 位置。但是，mod_rewrite 所做的完全不同:它“欺骗”浏览器，并提供页面，就好像它真的在那里一样——这就是为什么这是一个 URL 重写器，而不是一个简单的重定向器(您甚至可以验证发送和接收的 HTTP 头，以了解区别)。

但是不仅仅是缩短路径让 mod_rewrite 成为“URL 操作的瑞士军刀”…

## 规则

您已经看到了如何指定一个非常简单的重写器。现在让我们仔细看看…

```
RewriteRule Pattern Substitution [Flag(s)]
```

`RewriteRule`是一个简单的指令，告诉 mod_rewrite 做什么。神奇的是，您可以在模式中使用正则表达式，在替换字符串中使用引用。你认为下面的规则怎么样？

```
RewriteRule /products/([0-9]+) /siteengine/products.php?id=$1
```

现在，您可以在 URL 中使用以下语法:

```
http://localhost/products/123
```

重启 Apache 后，您会发现这被翻译成:

```
http://localhost/siteengine/products.php?id=123
```

如果你在你的脚本中只使用‘花哨’的 URL，你的访问者将没有办法找到你的脚本驻留在哪里(`/siteengine`)，它的名字是什么(products.php)，或者要传递的参数的名字(`productid`)是什么！你喜欢吗？我们刚刚完成了两项任务，看！

*   搜索引擎兼容性:URL 中没有花哨的字符，所以搜索引擎会搜索你的整个网站
*   **安全性:** ScriptKiddie(tm)修改的 URL 不会导致任何错误，因为它们首先通过正则表达式验证为一个数字——没有正确语法的 URL 甚至无法到达脚本本身。

当然，您可以创建更复杂的重写规则。例如，下面是我在一个网站上使用的一组规则:

```
 RewriteRule ^/products$ /content.php  
  RewriteRule ^/products/([0-9]+)$ /content.php?id=$1  
  RewriteRule  
    ^/products/([0-9]+),([ad]*),([0-9]{0,3}),([0-9]*),([0-9]*$)   
    /marso/content.php?id=$1&sort=$2&order=$3&start=$4
```

由于这些规则，我可以在应用程序中使用以下链接:

*   显示包含产品类别的打开页面:`http://somesite.hu/products`
*   产品列表，categoryid 为 123，第 1 页(默认)，默认顺序:`http://somesite.hu/products/123
    http://somesite.hu/products/123,,,,`
*   产品列表，类别 id 为 123，第 2 页，按第三个字段降序排列(d 表示降序，3 表示第三个字段):`http://somesite.hu/products/123,d,3,2`

这也是使用多个`RewriteRules`的例子。当有一个`RegExp`匹配时，适当的替换发生，mod_rewrite 停止运行，Apache 用替换的 URL 服务页面。如果没有匹配(在处理完所有规则之后)，就会出现一个通常的 404 页面。当然，你也可以定义一个或多个规则(例如`^.*$`作为最后一个模式)来指定根据错误的 URL 运行哪个(些)脚本。

`RewriteRule`的第三个可选部分是:

```
RewriteRule Pattern Substitution Flag(s)
```

使用标志，您可以在 URL 与模式匹配时向浏览器发送特定的标头，例如:

*   `forbidden`'或`f`'为 403 禁止，
*   `gone`'或`g`'表示 410 走了，
*   您也可以强制重定向，或强制 MIME 类型。

您甚至可以使用:

*   `nocase`或`NC`标志，使模式不区分大小写
*   `next` ' `/N`'循环回到第一条规则(`next round`')——虽然这可能会导致一个无限循环，但要小心！)
*   `skip=N` ' `/'S=N`'跳过以下 N 条规则

…等等。

我希望你能感受到我第一次玩这个模块时的感受！

**Go to page:** [1](/guide-url-rewriting) | [2](/guide-url-rewriting-2/) | [3](/guide-url-rewriting-3/) | [4](/guide-url-rewriting-4/)

## 分享这篇文章