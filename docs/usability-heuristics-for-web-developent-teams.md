# web 开发团队的可用性启发

> 原文：<https://www.sitepoint.com/usability-heuristics-for-web-developent-teams/>

通常，当客户对可用性测试的预算相对较低，或者进行测试的时间较短时，一个“专家”或者“启发式”评审将由一个有经验的可用性从业者来进行。两者略有不同，专家评审比启发式评审需要更少的正式评估过程。但是考虑到所有的事情，它们在时间上是差不多的……所以我想这也意味着成本上的明智。

进行任何一种审查都有许多好处。正如我上面提到的，在资源有限的地方，它们可能是评估一个地点的有效和高效的方法；与招募、面试和支付测试参与者相关的时间和成本被取消。这些评估方法也可以在网站的整个生命周期中非常容易和一致地进行，提供基准和定期健康检查。

在进行启发式审查时，可用性专家使用一系列的指导方针或检查点来评估一个站点(或应用程序)。然而，在进行专家评审时，这些特定的指导方针可能不会被利用，从业者依靠他们一般可用性原则的专业知识来评审手头的网站。

可以说，最广为人知的可用性启发法是由 Jakob Nielsen 开发的，他概括了十条经验法则。尼尔森的“标准十项”有许多变体，包括由丹尼斯·皮埃罗蒂为施乐公司开发的极其详细的清单[。Pierotti 的试探法使用 Nielsen 的十条规则作为基础，但进一步提供了针对每条规则的单独检查点。这绝对是一个可用性检查点的详细列表，我希望它是为特定环境开发的。](http://www.stcsig.org/usability/topics/articles/he-checklist.html)

事情是这样的…

我相信在这些列表中还有很多事情没有被考虑到。如果我们要采取一个最佳实践，以用户为中心，全面的，愿力量伴随你的方法来进行 web 开发，当然应该有一个更广泛的关注点；并且将尽可能多的前端开发领域合并到启发式审查中。

如果我们考虑信息架构师(IA)、“JavaScripter”和 web 标准开发人员，这些角色中的每一个都可以为创建一个更好、更全面的可用性试探法做出很大贡献。

**IA…**

2004 年，路易斯·罗森菲尔德发表了一份关于智能建筑的启发式列表，他说:

> 每一个信息架构师都应该在他们的后兜里放一套自己喜欢的问题；它们真的很有用。我根据用户最有可能与网站信息架构交互的五个方面将我的信息分成几组:
> 
> 1.  主页
> 2.  搜索界面
> 3.  搜索结果
> 4.  站点范围的导航
> 5.  上下文导航

几周之后，他通过开发一系列针对搜索系统的[试探法对这些进行了扩展。这些非常具体，不一定适用于每个站点或应用程序。但是他们确实提供了一个机会来建立一个更加定制和完善的网络可用性试探法。总之，搜索系统试探法侧重于审查:](http://louisrosenfeld.com/home/bloug_archive/000290.html)

1.  定位搜索
2.  范围搜索
3.  查询条目
4.  检索结果
5.  查询细化？
6.  与其他 IA 组件的交互
7.  正在完成搜索

两篇文章都提供了更多的细节，用一系列相关的问题来定义每个启发。它们对于理解信息架构试探法如何建立在已经酝酿了足够长时间的标准十条基础上是必不可少的。

**JavaScript 程序…**

我还没有找到任何与在网站中使用 JavaScript 相关的真正的“JavaScript 启发式”列表。然而，有一些实现 JavaScript 的方法既能改善整体用户体验，又能提高网站的可访问性，所以我认为开发一些特定的规则会很有用。我为最近的可用性评估创建了一组 JavaScript 试探法，包括:

1.  禁用 JavaScript 后，站点导航还能工作吗？
2.  禁用 JavaScript 时，关键站点功能会失败吗？
3.  该网站是否使用(基于脚本的)弹出窗口？
4.  站点是否使用独立于设备的脚本？

毫无疑问，还有其他的可以包括在这里，但我认为这是一个良好的开端…你能补充这一点吗？

 **网络标准开发者……**

前端编码(CSS 和 HTML)的质量对网站的可用性也有很大的影响，在开发一套可用性试探法时当然应该考虑。“编码试探法”可能包括以下问题:

1.  该网站包含一个网站图标吗？
2.  标题标签中使用的命名约定是什么？
3.  网站是语义编码的吗？

这些启发法中的每一种都提高了网站对所有用户的总体可用性，但是还有其他更具体的编码技术可以提高网站的可用性和可访问性。

《威 WCAG 1.0 版》中列出的一些可访问性指南也提高了非残障用户的网站可用性。除了以上这些，我通常会包括这些作为编码启发:

1.  站点是否实现了字段集和图例？
2.  网站使用框架吗？
3.  颜色对比是否足够？
4.  [访问过的链接是否明显？](https://www.sitepoint.com/where-are-all-the-visited-links/?)
5.  链接目的地是否清晰？
6.  下载的文件类型是否明显，例如 pdf？
7.  是否包含了相对字体大小？
8.  禁用样式表时可以阅读网站吗？
9.  该网站是否有效？

我并不是说这将否定一个完整的可访问性审计的需要。显然，这是一个独立的事件。但是在对网站可用性进行启发式审查时，这些是直接影响所有用户的因素。

这个列表绝不是详尽的，在某些情况下，提炼这些可用性启发法可能是合适的，这样它们就更符合你自己的方法。但是在进行专家或启发式审查时，我们需要比 GUI 更深入地思考。我们需要从“标准十条”向前发展，并结合启发式方法来解决整体开发和以用户为中心的设计。美丽毕竟只是表面的！

[1]
第一手资料来源
让计算机-人们识字。版权 1993。伊莱恩·维斯 ISBN: 0-471-01877-5
二手来源
可用性检查方法。版权 1994。雅各布·尼尔森和罗伯特·麦克 ISBN: 1-55542-622-0

## 分享这篇文章