# 为什么我在我们的项目中引入 Scala

> 原文：<https://www.sitepoint.com/why-i-introduced-scala-in-our-project/>

这篇文章最初发表在 [Bozho 的科技博客](http://techblog.bozho.net/why-i-introduced-scala-in-our-project/)上。我们在这里重新发布它，因为批判性的话语和真实的生活经验帮助开发者做出无炒作的决定。专题图片由[费德里科](https://www.flickr.com/photos/dekkar76/ "Author on Flickr")在 [CC-BY 2.0](https://creativecommons.org/licenses/by/2.0/ "Link to Licence") 下[发布](https://www.flickr.com/photos/dekkar76/15215808737 "Source on Flickr")。

[我不喜欢 Scala](http://techblog.bozho.net/i-dont-like-scala/) 。我认为它有一些[不好的和非常丑陋的方面](http://www.slideshare.net/Bozho/scala-the-good-the-bad-and-the-very-ugly)，使它成为主流开发的一个糟糕的选择。

但是最近我还是在我们的项目中引入了。不仅如此，团队对 Scala 毫无经验。我会试着解释为什么这不是个坏主意。

*   首先，也是最重要的一点——我遵循了自己的建议，只在一个小的辅助模块中介绍了它。我们没有验收测试，我们迫切需要一些，所以 JBehave 测试模块是 scala 项目的一个很好的候选。
*   测试代码与“常规”代码有些不同——不太一致、更“粗略”和有漏洞是可以的。另一方面，它可以受益于 scala 的表现力和更少的冗长，因为测试通常很难阅读，尤其是在设置阶段。因此，Scala 似乎是一个不错的选择——你可以快速编写简洁的测试代码，而不用太担心从长远来看你会搬起石头砸自己的脚，或者多个团队成员以不同的方式做事(Scala 慷慨地允许)。此外，在测试中你不必面对一大堆框架，因此——所有的语言概念(如隐含、部分函数、case 对象、currying 等)。)
*   我没有选择脚本语言(或者特别是 groovy)，因为我对静态类型语言有强烈的偏好(是的，我知道 groovy 几年前就有这个选项了)。它应该是一种 JVM 语言，因为我们想要重用我们的一些公共库。
*   偶尔学习新的编程语言对人们来说是有好处的，因为这会拓宽他们的视野。即使这并没有改变他们选择的语言。
*   我认为学习 Scala 可以更好地理解和使用 Java 8 lambdas，因为在 Scala 中你可以“在它们的自然栖息地”体验它们。

(作为旁注——IntelliJ Scala 支持现在比我上次使用它时更好了(不像基于 Eclipse 的 Scala IDE，它仍然是坏的))

如果用 Scala 编写验收测试代码像我想象的那么容易，那么这意味着我们将有更多更好的验收测试，这是实际的目标。这意味着我们在工作中使用了正确的工具，而不是锤子。

## 分享这篇文章