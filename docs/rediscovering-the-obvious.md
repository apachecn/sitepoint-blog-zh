# 重新发现显而易见的东西

> 原文：<https://www.sitepoint.com/rediscovering-the-obvious/>

(大声警告:你已经被警告了)

我一直在钻研 Unix 编程的艺术，这本书总体来说非常优秀，应该是任何开发软件的人的必读之作。

去年底，乔尔·斯波尔斯基以[双文化主义](http://www.joelonsoftware.com/articles/Biculturalism.html)为题写了一篇很好的评论，他同意 Eric S. Raymond 似乎偶尔(但不总是)对 Windows 和 GUI 的概念有不合理的偏见。

一个特别的例子出现在他对[文档](http://www.catb.org/~esr/writings/taoup/html/ch18s02.html)的讨论中，我觉得他低估了 WYSIWYG 编辑器的价值，即使是在用于编辑结构化标记(相对于表示)时，它也是一种代码生成器。使用过 Epic Editor(凯文[在此](https://www.sitepoint.com/article/review-epic-editor-4-0)评论)，这是一个商业 docbook 编辑器，发现它工作得很好——比手工编码好很多倍，即使你使用的是 emacs。举例来说，我认为 docbook 缺乏像样的所见即所得编辑器是为什么 [PEAR](http://pear.php.net/manual/) 在很大程度上是[未记录的](http://paul-m-jones.com/blog/index.php?p=14)。

尽管如此(看到他哪里“错了”，这本身就是一个有趣的见解)，这是一本优秀的书。

让我印象深刻的可能是*整本书的*备注是[这个](http://www.catb.org/~esr/writings/taoup/html/ch19s01.html)；

> 回想起来，令人吃惊的是，我们都没有意识到自己行为的含义。

他在这里谈论的是开源开发和[大教堂和集市](http://www.catb.org/~esr/writings/cathedral-bazaar/)的背景，但我认为这句话可以更广泛地应用于整个软件开发——我们太忽视人的因素了。这就是像[极限编程](http://www.extremeprogramming.org/)这样的开发方法派上用场的地方。如果你理解了围绕 XP 的天花乱坠的宣传和行话(如果你希望你的想法被认真对待，这是意料之中的事)，它实际上是关于采用一种非常适合普通人的方法，记住我们的局限性。

回到雷蒙德，他继续说；

> 这一经历应该让任何有思想和谦逊的人想知道还有哪些重要的统一概念仍然隐含在我们的行为中，潜伏在我们的集体眼皮底下，不是因为它们的复杂性，而是因为它们的简单性。

最初我学习了物理学(也许是系统分析的最初形式，除了哲学)，我的基本观点是任何系统都可以(并且应该)被任何人理解。一旦理解，系统就变得可预测。当然，系统越复杂，就越难确定管理它的规则，而经验在这一过程中发挥着作用，而个人的条件反射会导致模糊规则的偏见。也就是说，一个“天真”的观察者的第一印象往往包含真正的洞察力，应该受到重视。

相反的情况是，聪明的个人牺牲了他们的“直觉”,而接受了最新的“正确的方式”来做事，就像当时的专家所禁止的那样。

软件开发之外的一个例子(但与所有 PHP 之父相关——祝贺乔治最近成为 PHP 之父)是围绕娱乐中的暴力及其对儿童的影响的辩论。回到 80 年代，随着好莱坞提高电影中的暴力程度，人们就观看阿尼赶走另一个小演员军团是否对儿童有益展开了大讨论。娱乐界设法通过问“你的证据在哪里？”来让批评者闭嘴(或者:你的专家呢？).几年后，[关于娱乐暴力对儿童影响的联合声明](http://www.aap.org/advocacy/releases/jstmtevc.htm)，在进行了无数次研究后，证实了屏幕暴力*可能对儿童有害。哇——有个惊喜！谁会想到呢？*

回到软件开发，让我恼火的是看到专家们(那种永远不会忘记“Hello World！”，如果他们走得够远的话)阐述软件开发的未来(以及软件公司的发展趋势)。

《时代》杂志的经典人物大概要数[m .埃里克·约翰逊教授](http://oracle-www.dartmouth.edu/dart/groucho/tuck_faculty_and_research.faculty_profile?p_id=Q1X3CS)(**运营**管理教授，笑容需要擦擦)了，他在 2003 年 12 月接受 [NYT](http://www.nytimes.com/2003/12/07/business/yourmoney/07out.html) 关于外包的采访时说(需要注册)；

> 在旧金山湾区，有很多人喜欢围绕他们的 it 工作创造一点保护主义，但我们最好放弃很多这样的工作。像编码这样的低技能工作正在转移到海外，留在他们位置上的是更高级的项目管理工作(T2)。

努夫说。(这里还有进一步的讨论和[这里](http://slashdot.org/article.pl?sid=03/12/07/1938241&tid=187&tid=98))。

与此同时，进入[软件工厂](http://msdn.microsoft.com/architecture/overview/softwarefactories/)，下一次革命的[。我真诚地说*不是*指的是 MS bashing——这个想法的核心似乎与 Eric S. Raymond 提供的建议相同——利用](http://radio.weblogs.com/0126569/2004/08/31.html#a220)[代码生成](http://www.catb.org/~esr/writings/taoup/html/generationchapter.html)和[领域特定语言](http://www.catb.org/~esr/writings/taoup/html/minilanguageschapter.html)(你可能会认为 PHP 就是一个例子)。我对“软件工厂”的问题在于使用的语气——更多的是“跟随我们，我们更了解，为革命牺牲你的理性”。软件开发人员可能不会买账，但历史告诉我们，那些从事“高级项目管理工作”的人很可能会买账。

似乎又一次失去了人的因素。相比之下， [ActiveState](http://www.activestate.com) 在 2004 年 7 月发布了一份白皮书:[动态语言——准备迎接下一个挑战，通过设计](http://www.activestate.com/Company/NewsRoom/whitepapers_ADL.plex)。如果有什么可以称之为“革命”(以一种讽刺的方式)，我认为是像 Perl、Python、Ruby 和 PHP 这样的语言，它们成功地缩小了人类意图和机器指令之间的差距，对软件开发工作和成本产生了真正的*影响。*

遗憾的是，没有一个有营销资金的人真正推动他们，尽管谢天谢地，微软有人在这个案子上，雇佣了吉姆·胡古宁，他是《T2》Jython T3 和《T4》Iron Python T5 的作者。网)。

无论如何——不完全确定我在这里想说什么，但我得到的总体感觉是那些为软件和软件开发付费的人越来越对大承诺和糟糕的交付感到沮丧。然而，更多的宣传和“抽象实践”会对已经很少了解的“行业”造成更大的损害，给那些认为软件开发是低技能工作的人火上浇油。[吻](http://www.catb.org/~esr/writings/taoup/html/ch01s07.html)。

## 分享这篇文章