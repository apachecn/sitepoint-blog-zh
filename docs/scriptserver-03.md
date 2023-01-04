# ScriptServer 0.3

> 原文：<https://www.sitepoint.com/scriptserver-03/>

[ScriptServer 0.3](https://sourceforge.net/project/showfiles.php?group_id=96178) 出来了。

又没有令人兴奋的变化了——主要是清理错误的处理方式。

现在网上有几个或更多有趣的例子；

[服务器端自动完成](http://xmlrpccom.sourceforge.net/scriptserver/scriptserver/examples/autocomplete.php)–用于“自动完成”表单域的国家列表存在于服务器上，由 PHP 读取。该示例还展示了如何使用 HTTP 基本身份验证来提供简单的访问控制。连接 sourceforge 的延迟显示了这种方法的问题；也许自动完成是一个不好的例子。

Sourceforge 项目搜索——这是自动完成的一个变种。PHP 扫描 Sourceforge 项目目录，通过 Unix 名称识别项目，然后允许你链接到项目页面。没有关于项目的信息，这是一种幸运的尝试——理论上也可以解析[项目摘要提要](http://sourceforge.net/export/rss2_projsummary.php?group_id=96178)并显示一些有用的信息，不仅仅是名称，但实际上 SF 安全策略阻止了这种可能性。这个例子实际上演示的是如何将数据和行为发送给客户端——服务器响应是一个项目列表以及 Javascript 函数，该函数将数据嵌入到任何合适的 HTML 标签的 innerHTML 属性中。这是否是一个好主意是另一个问题(违反分离等)。)但是这个例子展示了如何用您自己的扩展 ScriptServer 的 PHP 到 JS 序列化。

否则[杰森](http://blog.casey-sweat.us/)会好心地在同一个句子中使用单词[岩石和脚本服务器](http://blog.casey-sweat.us/index.php?p=27)。Jason 解释了它帮助他解决的问题，如果你想知道这是怎么回事，这可能会有所启发。

此外，如果你有兴趣参与(或者只是有问题)，我已经建立了一个[邮件列表](http://lists.sourceforge.net/lists/listinfo/xmlrpccom-scriptserver)。有人想试试用 ScriptServer 连接[小维基](http://www.tiddlywiki.com/)吗？

## 分享这篇文章