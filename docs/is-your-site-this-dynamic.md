# 你的网站是动态的吗？

> 原文：<https://www.sitepoint.com/is-your-site-this-dynamic/>

从非常有趣的[每日 WTF](http://www.thedailywtf.com/) ，一份在野外发现的可怕的糟糕代码的每日摘要，[来了这个动态 web 开发出错的警示故事](http://www.thedailywtf.com/forums/69633/ShowPost.aspx)。

开发人员 Stephan Jennewein 受雇为 Firefox 兼容性更新网站。乍一看，这似乎是一个由 15-20 个静态页面组成的普通网站，结果却是 JavaScript 和 PHP 代码的复杂混合体。每一个看似静态的页面都包含类似这样的代码:

```
<html>
  <head>
    <link href="dynsitebase/styles.php" rel="stylesheet" type="text/css" />
    <script src="dynsitebase/base.php" language="JavaScript"></script>
    <script src="dynsitebase/themes.php" language="JavaScript"></script>
    <script src="dynsitebase/sitedata.php" language="JavaScript"></script>
    <script src="dynsitebase/pagelayout.php" language="JavaScript"></script>
    <script language="JavaScript">
      document.write("<title>" + CurrentPage.Title + "</title>");
    </script>
   </head>
   <body>
    <script language="JavaScript">
      DataInterface.initialize();
      DataInterface.bindToPage(CurrentPage);
      ImageLoader.preload(CurrentPage);
      LinkLoader.bindToContext(CurrentPage);

      PagePrinter.printHeader(CurrentPage);
      PagePrinter.printNavigation(CurrentPage);
      PagePrinter.printTitle(CurrentPage);
      PagePrinter.printSubTitle(CurrentPage);
      PagePrinter.printMainContent(CurrentPage);
      PagePrinter.printFooter(CurrentPage);

      DataInterface.close();
      ImageLoader.close();
      LinkLoader.close();
      PagePrinter.close();
    </script>
   </body>
</html>
```

页面的全部内容是由一系列 JavaScript 对象生成和显示的，而这些对象又是由服务器上的一系列 PHP 脚本生成的！加载该站点的任何页面时，禁用 JavaScript 的浏览器会显示一个空窗口。

如果这还不够荒谬的话，负责生成 JavaScript 代码的 PHP 脚本根据 HTML 页面的地址来确定需要在页面上显示什么，正如不可靠的 HTTP `Referer` header 所报告的:

```
function dynsitebase_PagePrinter_render()
{
  echo "var PagePrinter = new Object()";

  echo "PagePrinter.printHeader = function(pageObj)";
  echo "{";
  echo "  document.write("<div class='$css_headerdivclass'>");";
  echo "  document.write("<h1 class='$css_h1headerdivclass'>");";
  echo "  if (pageObj.header) ";
  echo "  {";
  echo "    document.write(pageObj.header);";
  echo "  }";
  echo "  else";
  echo "  {";
  echo "    document.write("$headerToRender");";
  echo "  }";

  ...

}
```

比不精通 JavaScript 或 PHP 的人编写的代码更糟糕的是，这个怪物的作者显然非常了解这些语言。他或她所缺乏的是对什么使得代码健壮、高效和可维护的理解。

阅读[每日 WTF](http://www.thedailywtf.com/)([RSS feed](http://thedailywtf.com/forums/rss.aspx?ForumID=12&Mode=0))；这会让你感觉更好。如果没有，你可能需要参加计算机科学的补习班。

## 分享这篇文章