# 微软将推出语义 Web light

> 原文：<https://www.sitepoint.com/microsoft-set-to-launch-semantic-web-light/>

来自微软研究院的有趣消息。他们在即将到来的[开放库 2008](http://or08.ecs.soton.ac.uk/) 会议上首次展示了他们的“研究-输出”库平台。

以下是这篇博客的摘录:

> 我们的目标是抽象底层技术的使用，并提供一个易于使用的开发模型，基于。NET 和 LINQ，在健壮技术的基础上构建存储库。
> …
> 这个平台有一种“语义计算”的味道。“资源”和“关系”的概念是我们平台 API 中的一等公民。
> ……
> 新的实体可以被引入系统(甚至通过编程)，而现有的实体可以通过添加属性来进一步扩展。

上面节选的关键短语是:*“资源”和“关系”的概念是一等公民*。本质上，微软发布的这个新平台将允许。Net 开发人员与 LINQ 一起利用他们的技能来创建[链接数据](http://en.wikipedia.org/wiki/Linked_Data)客户端，这些客户端可以参与即将到来的链接数据 Web。与[雅虎完全相同的链接数据网](http://www.ysearchblog.com/archives/000527.html)已经抛下了他们全力[支持的](https://www.sitepoint.com/preparing-your-sites-for-the-data-web/)在后面。

这就是语义 Web 技术今天所提供的，创建 Web 可伸缩应用程序的能力，这些应用程序可以根据输入的数据进行自我调整。微软正在填补 CouchDB 和 T2 语义网 W3C 规范之间的空白。

Savas Parastatidis 很快指出，尽管他们存储了一个三元组:*主语、谓语、宾语*([RDF](http://en.wikipedia.org/wiki/Resource_Description_Framework)的基础)，他们并没有创建一个语义网数据库；相反，他们在 RDF triplestore 和 SQL Server 2008 之间创建了一种混合方法。

他们还使用了 WinFS 的 Vista“未来文件系统”的技术。这真的很令人兴奋，因为 WinFS 有很多承诺，但它从未实现令人失望(至少可以这么说)。

已经有 ASP.NET 控件提供存储库访问， [WPF 和 Silverlight](http://www.youtube.com/watch?v=0-tJyBek4qU) 控件正在工作中，以提供可视化。

[![Microsoft Research](img/518e72e625c8bcc55ee192c20e897f53.png)](http://www.youtube.com/watch?v=0-tJyBek4qU)

最后，我想把博客中的这些话留给你:

> 这对我们来说至关重要，因为——就像我们团队承担的其他项目一样——我们非常关注互操作性。

猪在飞，牛在回家。微软专注于互操作性和语义网原则。干得好。

## 分享这篇文章