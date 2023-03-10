# SitePoint 的幕后:代码周

> 原文：<https://www.sitepoint.com/code-week/>

你可能没有意识到，但是在我们 sitepoint.com 的秘密巢穴中，一个开发者团队正在疯狂地建设下一代的 T2 市场和 T4 竞赛。我们的任务相当简单:利用当前的系统，将其构建成一个坚实的架构平台，我们可以迅速发展成为真正美好的东西。最初我们计划建造一个 SitePoint 品牌的星际火箭，但我们勉强承认这超出了我们的范围。

作为快速缩短时间表的一个实验(咬牙切齿和从论坛上揪头发并没有被忽视)，我们决定将团队运送到拉伊的一个海滨别墅，进行为期 10 天的无节制生产。理论上。这就是结果的故事。

[![Makeshift Boardroom](img/4e101f1ffd9481d51202764b4512a344.png) ](http://www.flickr.com/photos/andrew_k/635686037/) [ ![Sunlight is not the enemy](img/a1aba8fa75b9fa8ac82fa8b80e996759.png)](http://www.flickr.com/photos/andrew_k/645141576/)

五人小组由我本人([拉克伦·唐纳德](http://www.lachlandonald.com))、[保罗·安斯利](http://paul.annesley.cc)、[安德鲁·克雷斯班尼斯](http://www.leftjustified.net/)、[卢卡斯·陈](http://www.lucaschan.com)和[詹姆斯·爱德华兹](http://www.brothercake.com)(我们可接触到的前端摇滚明星)。总的来说，这是一个相当令人敬畏的阵容，我对我们所能做的抱有很高的期望，特别是如果我们能够建造星际火箭的话。

我们重新开发 [Marketplace](https://www.sitepoint.com/marketplace/) 和[竞赛](https://www.sitepoint.com)的目标是让代码库进入一个我们可以为所有未来开发打下基础的状态。这项任务需要几个要点:

*   源管理(通过 [SVN](http://subversion.tigris.org/) 和[跟踪](http://trac.edgewall.org/)
*   测试驱动(使用 [SimpleTest](http://simpletest.sourceforge.net/) 和我们自己的 Javascript 测试 UI，Testr)
*   与 vBulletin 分离
*   基于 PHP5

大部分工作是在代码周之前完成的，还有大部分的[域对象模型](http://www.martinfowler.com/eaaCatalog/domainModel.html)，包括[映射器和查找器](http://www.martinfowler.com/eaaCatalog/dataMapper.html)以及我们内部框架为数据持久性提供的其他好的抽象。剩下的大部分工作是实现当前系统提供的面向用户的特性，以及一些不错的新特性。我们的核心目标是用大致相同的特性集来改进当前的代码平台，但是有些事情不需要一字不差地重新实现。

作为一个小小的预览，即将发布的版本中将包含一些特性:

*   使用 Zend 框架中的 [Lucene 实现来实现搜索。它提供了许多丰富的功能，如字段搜索(允许您按作者、标题等进行搜索)和高级的类似 Google 的搜索操作符。我真的对这一进展感到兴奋，我希望我们能够及时将类似的技术应用于 sitepoint.com](http://framework.zend.com/manual/en/zend.search.lucene.html)的其余部分。
*   创建新的列表已经过重新组织和简化。我们让 brother 在标记和结构上放松，结果相当壮观。我们有所见即所得的描述编辑，简化的日期选择和改进的验证。
*   Marketplace 线程现在被一个 HTML 列表所取代，它不依赖于 vBulletin。这允许附件、评论以及与市场系统的其他部分更好的集成。

总而言之，代码周取得了巨大的成功，产生了令人难以置信的大量输出，将代码库从 60，000 行应用程序代码增加到 120，000 行(不包括库，见下图)。我们设法喝了相当多的咖啡——我相信 5 个人喝了将近 1.8 公斤。这相当于每个杯子有大约 666 行代码，这本身就令人不安。

![Lines of Code in Marketplace/Contests](img/789da2e4b0e5dc3e9873b732aa4e7cac.png)

请务必查看[开发论坛](https://www.sitepoint.com/forums/forumdisplay.php?f=249)以了解关于新版本的进一步公告，并在接下来的几周内查看一些更详细描述我们实现的 PHP 方面的博客帖子。

## 分享这篇文章