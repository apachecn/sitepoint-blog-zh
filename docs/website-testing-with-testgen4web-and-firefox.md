# 用 TestGen4Web 和 Firefox 进行网站测试

> 原文：<https://www.sitepoint.com/website-testing-with-testgen4web-and-firefox/>

所以再来一轮。在“离开”期间，更多的毛发脱落了，没能找到有用的工作，成为一名护林员。同时，第二个孩子将于 12 月出生。已经够了。

今天碰到了[test gen 4 web](http://developer.spikesource.com/wiki/index.php/Projects:TestGen4Web)；一个 Firefox 扩展(需要 [1.5 beta](http://www.mozilla.org/products/firefox/beta2.html) )来帮助自动测试 web 应用程序，由 [Spikesource](http://www.spikesource.com/) 开发，这是一家通过测试开源应用程序而出名的公司。

除了有一个漂亮的 UML 图之外，它还为 Firefox 增加了一个工具栏，允许记录和回放你在网上冲浪时的“点击”。完成记录后，您可以将这些步骤保存为 XML 文件(特定于 TestGen4Web 的格式)，以便以后重新运行。

重新运行不仅限于 Firefox 扩展。这个项目的后半部分是一个代码生成器，它解析保存的 XML 并生成代码，以便针对 [HTTPUnit](http://httpunit.sourceforge.net/) (Java)、 [Selenium](http://selenium.thoughtworks.com/index.html) (more Java)或 [SimpleTest](http://www.lastcraft.com/simple_test.php) (PHP)执行。这意味着很容易每天通过 [cron](http://en.wikipedia.org/wiki/Cron) 重新运行测试。

如果你现在感到困惑，前一段时间 [Marcus](http://www.lastcraft.com/about.php) 写了一篇关于 SimpleTest 的“Web 测试”能力的介绍:[编写一个登录框应该不会伤害](http://www.php-mag.net/itr/online_artikel/psecom,id,613,nodeid,114.html)—“自动化 Web 测试如何让你的开发跟上速度”。更多细节可以在[文档](http://www.lastcraft.com/web_tester_documentation.php)中找到。

查看 TestGen4Web 的 XML 格式，它似乎能够捕获大多数与“GET”和“POST”相关的内容(例如，点击链接、填写和提交表单)，并且能够测试对请求的响应是否符合预期，例如，使用 XPath 表达式提取节点的内容，并检查它是否与“recording”中捕获的值相匹配。

对于减少编写 SimpleTest web 测试的工作量肯定是有用的。同时，我以前也看过这种工具，如 [twill](http://www.idyll.org/~t/www-tools/twill.html) 和[maxq](http://maxq.tigris.org/)——我认为如果你不在真正的网络浏览器中运行测试，你能做的就只有这么多了。

我想测试一个“简单”的情况，大概是这样的:

1.  向用户显示“项目”列表
2.  用户点击链接，得到一个弹出窗口，提供单个“项目”的更多信息，并形成一个“详细选择”，该表单包含重要的客户端 Javascript
3.  提交表单时，弹出窗口关闭，原始窗口更新以显示详细的选择

虽然可以使用 SimpleTest 和类似工具测试请求和响应的单个“单元”,但我看不到测试弹出窗口或弹出窗口中包含的 Javascript 的好方法——这意味着没有真正好的方法来模拟完整的用户“事务”并测试它的工作情况。

鉴于 TestGen4Web 与浏览器的密切关系，这可能是 test gen 4 web 可以解决的问题(见[这个问题](http://developer.spikesource.com/forums/viewtopic.php?t=385)),但是您失去了“翻译”功能。据说 HTTPUnit 在 [Rhino](http://www.mozilla.org/rhino/) 的帮助下，似乎有[对这个](http://httpunit.sourceforge.net/doc/faq.html#dialogs)的一些支持——没有试过 HTTPUnit，所以不知道效果如何——鉴于我们对浏览器兼容性的乐趣，我持怀疑态度。

不知何故，基于 wxMozilla 或 T2 的东西似乎更有说服力——这是一个真正的浏览器，但仍然可以从命令行执行。当然，真正解决这个问题的缺点是垃圾邮件发送者会喜欢它…

## 分享这篇文章