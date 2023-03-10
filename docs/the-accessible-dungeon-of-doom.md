# 可进入的末日地牢

> 原文：<https://www.sitepoint.com/the-accessible-dungeon-of-doom/>

今天我们看到了 JavaScript 的艺术&科学[的正式发布，这是我们令人敬畏的新 JavaScript 书，我在其中贡献了一章，讲述了我用 <abbr title="Cascading Style Sheets">CSS</abbr> 和 JavaScript 构建第一人称视角迷宫的技术。游戏](https://www.sitepoint.com/books/jsdesign1/)的[试玩版在网上提供(通过迷宫赢得销售价格的折扣！).这一章本身已经作为一篇](https://www.sitepoint.com/art-science-javascript/)[专题文章](https://www.sitepoint.com/article/art-science-javascript/)在 sitepoint.com 免费再版。

你可能会认为这是我在 2006 年 8 月发表的一个剧本的分支。但这本书的新版本是一个很大的改进，最显著的(就我而言)是因为添加了**为迷宫**生成的文本描述，这使得游戏可以让看不到图像的用户玩。这里有一个例子:

![The corridor stretches 20 meters in front of you, then turns left. On the left wall there's a passage after 4 meters. On the right wall there's a passage after 4 meters.](img/67591dd67498259a9e0608b80188fde9.png)

我实际上在《大白鲨 7》中玩过这个游戏，这是完全可能的——当然更难，因为视觉参考线索不在那里，所以你必须记住你去过哪里，有效地在你的脑海中保留地图。但不管怎样，这是可以做到的，而且我确实做到了！在我看来，游戏是最难实现无障碍的东西之一，因为大多数游戏天生就是可视化的，所以我很高兴能走到这一步。新版本还包括 <abbr title="Application Programming Interface">API</abbr> 钩子，用于向基本迷宫生成引擎添加额外的游戏逻辑。

但是你知道吗，制作这个游戏真的没有那么难。我只是有点失控，它发生了。棘手的部分是**解释它是如何工作的**！有时候当我在开发一些东西的时候，我会有点恍惚，我不完全确定我在做什么，只是尝试看看会发生什么。所以当谈到回顾性地解释我做了什么，我做了什么决定，我使用了什么技巧，这是相当困难的…因为我真的不知道。你怎么解释你自己都不太明白的事情？让它有意义就更难了(但这就是编辑的目的)。

但最终它汇集成一个引人入胜的篇章…即使我自己这么说！这是对书中所有其他迷人而有见地的章节的一个很好的补充，我非常高兴和自豪能成为其中的一部分。

它有许多图片。

## 分享这篇文章