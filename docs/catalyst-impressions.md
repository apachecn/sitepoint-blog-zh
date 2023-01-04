# 催化剂印象

> 原文：<https://www.sitepoint.com/catalyst-impressions/>

关于 Catalyst 的持续想法，从[到](https://www.sitepoint.com/looking-at-catalyst/)。这并不意味着是一个教程或一本小册子-只是已经出现在脑海中的观察和印象，甚至是真正突出的东西。很有可能我误解了一些事情，或者只是简单地错了，但是我认为在某个地方捕捉这些事情是有用的。也不是试图寻找答案——想看看在没有帮助的情况下我能走多远。

*   [介绍性文档](http://search.cpan.org/~agrundma/Catalyst-5.57/lib/Catalyst/Manual/Intro.pod)很好。给了我们很多可以开始玩的东西。在[食谱](http://search.cpan.org/~agrundma/Catalyst-5.57/lib/Catalyst/Manual/Cookbook.pod) IMO 中还需要更多——尽管 Catalyst 将很多内容留给了外部 CPAN 模块，提供了示例(esp。大量与表单相关的内容)会有所帮助。
*   控制器 API 很容易记住——尽管只做了一些非常基本的东西，各种特殊的方法，如`begin`、`auto`(也许命名为`chain`会更好？)和`end`以及用于 URL 映射的子例程属性(如`Global`、`Local`、`LocalRegex`和`Private`)都存在我的脑子里。这与我尝试过的其他框架形成对比，在其他框架中，需要不断地返回文档。此外，框架行为很容易理解——结合介绍文档，[这张图](http://dev.catalyst.perl.org/wiki/FlowChart)说明了一切。另请参见[快速参考卡](http://refcards.com/refcards/catalyst/draft-catalyst-refcard.pdf) (PDF)。
*   人们赞赏地注意到，日志和测试是 Catalyst 的默认部分。
*   还没有 100%被[框架代码生成器](http://search.cpan.org/~agrundma/Catalyst-5.57/lib/Catalyst/Manual/Tutorial.pod#Setting_up_your_application)说服——虽然拥有这样的被动生成器很好，但这让我感到紧张——会不会出现我想为已经生成的代码重新运行它们的情况？至今想不出一个。
*   默认情况下，Catalyst】似乎没有区分 GET 和 POST 你必须看看`$context->request->method`——文档中没有提到这一点，我想知道这是否会导致人们编写代码来处理通过 POST *和* get 工作的表单。对于将 URL(如参考资料中所示)映射到一个方法，我有一种更普遍的困扰感。最佳总结[为什么 Web 服务器 API 这么烂？](http://www.mnot.net/blog/2003/12/08/http_api)–Catalyst 似乎把问题反过来了。Catalyst 没有将“许多 URIs(以及资源)打包到一个容器中”，而是将 URI 很好地分割开来，然后将所有 HTTP 方法打包到一个 Perl 类方法中。
*   想知道[模板](http://www.phpwact.org/pattern/template_view)——它们足够智能吗？将沿着模板工具包的道路前进…特别是我如何访问我已经嵌入到我的控制器中的伟大的 URL 处理知识？我总是发现错误/后来的破坏的主要来源是模板指向浏览器的地方和控制器期望的地方不匹配。理想情况下，希望模板能够按照[中的](http://www.phpwact.org)[和【WACT】中的](http://cvs.sourceforge.net/viewcvs.py/wact/wact/framework/template/tags/front/)为自己解决一些问题。还有验证之类的东西——再次插入 WACT，它出色地将[验证规则连接到模板输出](http://www.phpwact.org/wact/form_validation),用于错误报告和其他很酷的东西，比如[如何自动将 maxlength 属性添加到适当的表单输入字段。似乎是](http://www.phpwact.org/wact/api/validation/rule/sizerangerule)[的东西](http://cpan.uwinnipeg.ca/dist/Catalyst-Plugin-FormValidator)接近催化剂中的那个——将会看到。

无论如何——更多的时间。

## 分享这篇文章