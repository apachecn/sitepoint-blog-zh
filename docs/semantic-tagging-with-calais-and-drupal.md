# 用 Calais 和 Drupal 进行语义标记

> 原文：<https://www.sitepoint.com/semantic-tagging-with-calais-and-drupal/>

天啊。没有比这更简单的了。自动和(相当)智能地标记你的内容是一件非常强大的事情。尤其是当这些标签是结构化标签而不仅仅是文本字符串时。这曾经是一个相当痛苦的命题，手动标记——我是使用大众分类法还是创建有意义的分类法。这个会扩展吗？？？别再烦恼了。让别人(或其他东西)为你做这件事。

在过去的一个月里，我一直在使用 Drupal [Calais 标签模块](http://drupal.org/project/opencalais),并对它的有效性感到惊喜。我从网上扔过电子邮件、博客帖子、随机文本。这一切都至少有一个像样的标签(这是一个非常模糊的测试样本)。

今天，汤森路透发布了他们的加莱网络服务的 2.0 版本。这在很多方面都很酷:

*   他们现在标记更多的非新闻项目(娱乐活动、医疗状况、电影、音乐、音乐组、出版媒体、体育赛事、体育游戏和电视节目)。以前他们关注的是新闻事件，所以大多数非新闻类的帖子很少有标签。
*   允许为雅虎嵌入元数据的工具！搜索猴子
*   标签插件
*   前面提到的 Drupal [模块](http://drupal.org/project/opencalais)

安装模块很简单，只需要几个步骤。首先，从上面的链接下载。我使用的是 Drupal 6 的版本，Drupal 5 的安装过程略有不同。

第二:如果你使用的是 Drupal 6，你必须下载令人惊叹的 [Arto](http://drupal.org/user/26089) 的 [RDF 模块](http://drupal.org/project/rdf)。这必须在 Calais 模块之前安装。如果你使用的是 Drupal 5，你必须将 [ARC2 库](http://arc.semsol.org/download)安装到 opencalais/arc_rdf/arc2 目录下。

这真的不难。只需下载两个文件，将它们解压缩到您的站点/所有/模块目录中，然后激活它们。

最后，在你的帖子被语义标记和你的生活被改变之前，还有最后一步。选择您希望 Calais 在哪些内容类型上施展魔法。

![calais drupal module](img/48b1e7e33057ed9e198b67ca78f2bc01.png)

这里有三个选项:

1.  不由 Calais 处理(默认)
2.  建议关键字(手动术语关联)
3.  自动应用关键词***我的最爱***

我把我的每一种类型都设置为第三种，因为这是最简单的(总是好的),我不需要记得做任何额外的事情。设置好了就算了。一旦你保存了你的文章，标记过程就开始了。虽然标记速度很快，但它确实为您的内容添加了一两秒钟的保存时间。

这个新版本的 Calais 真的给你带来了强大的功能，而且几乎不需要任何成本——嗯，安装它只需要大约 8 分钟。得到它并至少尝试一下是显而易见的。为 [SearchMonkey](http://developer.yahoo.com/searchmonkey/) 添加的[嵌入式元数据](https://www.sitepoint.com/preparing-your-sites-for-the-data-web/)确实将这个版本推向了高潮。

现在，为了增加一点趣味。下载 [Tagadelic 模块](http://drupal.org/project/tagadelic)并基于您的所有努力获得超级简单的标签云:)

![calais drupal tagadelic](img/8312cc93e294ef0c3df32856effb642e.png)

为了帮助你把所有的东西都放在一起，并且可以浏览，让分类 VTN 怎么样？

![calais drupal taxonomy](img/2517fac50bf4824f8e45bc2b06fd0fa5.png)

这会让你的客户大吃一惊，你可以告诉他们这一切有多难…

## 分享这篇文章