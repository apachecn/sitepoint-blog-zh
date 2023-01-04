# PHP 和 Python 的真正区别

> 原文：<https://www.sitepoint.com/the-real-difference-between-php-and-python/>

在充分意识到这样做的徒劳之后，reddit 上正在发生一些对 PHP 的抨击(像往常一样): [PHP 与 Python——真正的区别](http://programming.reddit.com/info/1arpk/comments)，由这张[略显有趣的图片](http://www.jameslaver.com/php_vs_python.png)带来。虽然我可以接受这些观点——从技术上讲，在 PHP 中统一处理错误实际上要困难得多，而且社区中的计算机科学家不如 Python 丰富——但 reddit 上相应的激烈争论却忽略了不同的一点，这一点最容易用代码来表达。

这两个脚本之间最显著的区别是什么？

```
 <?php
$hits = 0;
printf ("Hits: %sn", $hits);
$hits++; 

```

和一个 [web.py](http://webpy.org) 控制器(绝对没有批评的意思——选择它是因为我喜欢它——讨论几乎适用于非 CGI 的任何东西，事实上，这也不是 Python 特有的)…

```
 #!/usr/bin/env python
import web

urls = ( '/.*', 'counter' )
hits = 0

class counter:        
    def GET(self):
      global hits
      print "hits %s" % hits
      hits += 1

if __name__ == "__main__": web.run(urls, globals()) 

```

取决于您如何部署后者(即，不是作为 CGI)，它将*实际上*计数-即*状态可以在请求*之间持续。虽然这实现了这个特性，但它也是非常痛苦的搬起石头砸自己脚的潜在来源。当你开始怀疑[弱引用](http://en.wikipedia.org/wiki/Weak_reference)是否有助于修复你的[大型代码库](http://www.artima.com/forums/flat.jsp?forum=106&thread=174099)中某处的内存泄漏时，你会有一种沉重的感觉。那份[咖喱](http://en.wikipedia.org/wiki/Currying)可能尝起来不错，但是等到第二天早上……还记得那些 [JScript 内存泄漏](http://javascript.crockford.com/memory/leak.html)吗——你真的想要同样有趣的服务器端吗？

“但是是的*当然*先生。你当然比那聪明。不，一个人不会有什么坏处，对吧？你永远不会犯那个愚蠢的错误。不，从来没有。即使是在你最无聊的日子里”；)

这与错误和[这个图像](http://www.jameslaver.com/php_vs_python.png)有什么关系？因为 PHP 在每次请求后都会“重置”(参见[这里的](https://www.sitepoint.com/lazy-php-part-1/)或者更多细节[这里的](http://devzone.zend.com/node/view/id/1021#Heading3))，所以实际上并不总是需要显式地处理错误——假设你的代码没有什么根本性的“问题”并且是某种运行时错误(例如，数据库关闭)，忽略这个问题并等待系统“自我纠正”就足够了——没有什么会让 PHP 处于无法恢复的状态。Damien Katz 在[错误代码或异常方面做得更好是有道理的？为什么可靠的软件这么难？](http://damienkatz.net/2006/04/error_code_vs_e.html)–阅读–全部阅读。

无论如何——再次向 Python / web.py 道歉——只是按照 reddit 的思路——同样可以是 Rails、 [Catalyst](http://www.catalystframework.org/) 或[否则就是](http://del.icio.us/tag/web+framework)。

## 分享这篇文章