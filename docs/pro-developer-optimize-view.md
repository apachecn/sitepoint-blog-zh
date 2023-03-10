# 专业开发人员–优化您的视图

> 原文：<https://www.sitepoint.com/pro-developer-optimize-view/>

对于任何花大量时间编写用户界面代码的人来说，优化视图会立即让人联想到窗口、智能 UI 小工具、可用性问题，以及与兴奋过度的程序员举行的一长串设计会议。事实上，这些小聚会很可能涉及到空中白板擦以很高的速度和目的旅行，这是我们对如何向客户展示我们的软件的热情重要性的另一个证明。

事实上，用户窥视我们代码深处的门户似乎常常定义了软件本身，至少在旁观者看来是这样。因此，我们承认用户界面问题不仅仅是一个漂亮脸蛋的问题，而是事实上定义了用户操作的边界。把一个笨拙的观点放在一个好的软件上，你最终会得到一个笨拙的软件。

然而，那些已经启动了程序员编辑器，打电话订购意大利香肠比萨饼，期待着又一次令人兴奋的编码会议的人，可能有点超前了。我们不是在这里谈论编码的比特和字节。我们在这里谈论一些更重要的事情——你作为一名专业软件开发人员的未来。所以，你暂时不需要那个程序员编辑器。不过，披萨可能还是个好主意。有些传统永远不应该改变。

##### 设定优先级

如果我们不打算谈论功能和物体，以及难以理解的弯弯曲曲的小行文字，那么我们在这里到底是为了什么？对于开发人员来说，还有什么比编写代码的细节更重要的呢？答案显而易见，但又难以捉摸。对任何地方的程序员来说，最重要的不是写代码，而是交付高质量的软件。

你是不是停下来，摇摇头，把最后一句话再读一遍？你并不孤单。几乎普遍地，优秀的程序员本能地将编写代码等同于交付软件，好像它们是一回事。他们不是。事实上，正是这种观点在很大程度上造成了世界范围灾难。

如果你不知道什么是释放灾难，你已经过了一个迷人的生活。从零用现金中拿出两个甜甜圈，然后回到你正在做的事情上。我们其余的人都太熟悉好的发布变坏的细节，包括任意的截止日期、无休止的加班、高压力水平、暴躁的脾气和从五楼窗户飞出的显示器，所有这些都是为了一个由于市场压力而在准备好之前就发货的产品。当你的软件在街上遇到比廉价汽车旅馆更多的错误，比一个一周没睡觉的维护程序员更不稳定时，编码的技术细节真的是有罪的一方吗？不太可能。我可以向你保证，我见过的一些最糟糕的软件发布是绝对聪明的技术头脑的产物。让我再次强调这个想法。质量差的软件发布几乎从来不是因为缺乏技术技能。

所以，如果你正在做的项目被用户社区用两个不朽的词来问候，这两个词是任何地方的程序员都害怕的(“糟透了！”)，那么如果不是代码，谁是罪魁祸首呢？当然是程序员。嘿，你不会以为你会这么容易脱身吧？

##### 通常的嫌疑人

我开始看到房间后面有些呆滞的眼神。如果糟糕的发布不是由糟糕的技术技能引起的，那怎么会是程序员的错呢？好吧，事实上，我们要在这里调用一点椒盐卷饼逻辑。不是直的，火柴棍大小的那种也很适合搅拌你的饮料，而是那些扭曲的，缠绕的椒盐卷饼，需要转很多圈，但最终总是回到他们开始的地方。我们将从围捕所有常见的嫌疑人开始，但相信我，最终这一切都将再次回家，在我们自己的笔记本电脑上休息。椒盐卷饼就是这样。

事实上，这里的许多退伍军人已经列出了一份名单，列出了要对搞砸一个完美项目负责的各方，甚至就革命到来时他们应该靠着哪堵墙提出了建议。你们中一些更有经验的人甚至建议律师们必须等待轮到他们，或者使用另一堵墙。

这些恶棍是谁，这些人强大到足以凌驾于最优秀的程序员的能力？营销和管理肯定是第一个想到的，当程序员深夜在卡布奇诺咖啡机旁闲逛时，他们通常被称为黄鼠狼和西装。当对一个毫无戒心的软件开发团队放任自流时，结果不可避免地包括模糊的和变化的需求，在没有技术现实概念的情况下任意宣布的截止日期，范围蔓延，危机驱动的管理，完全缺乏专业的测试部门，最后，软件在它应该发布之前很久就发布了。

##### 这不是我的工作

你说什么？我刚刚清楚地证明了这不是你的错？不错的尝试。递给我一块椒盐卷饼，好吗？项目失败的原因非常简单。极其聪明或者才华横溢的程序员一次又一次地做出天真的假设，如果与代码无关，那就不是他们的工作。在现代空对空战斗中，一名喷气式战斗机飞行员发现自己离对手足够近，可以用机枪一决胜负，但他已经错过了在安全距离内用远程导弹解决问题的关键机会。程序员也是如此。如果你发现自己在加班城，保证发布的灾难就在眼前，你在那之前很久就搞砸了，因为在它控制你之前没有控制你的情况。哎哟。我可以这么说吗？或许我至少应该先给你一个椒盐卷饼。

你对软件开发过程的看法决定了你在工作周中做什么，不做什么。如果你认为编码之外的一切都“不是我的工作”，那么你和你的项目毫无疑问会成为席卷整个企业界的强大而不合逻辑的力量的牺牲品。然而，精明的程序员可以将营销和管理的愚蠢行为最小化。对于这些火箭科学家扔给我们的每一个骨头脑袋的东西，都有一个计数器。在游戏中尽早处理问题，你的发布灾难反而变成了发布派对。他们甚至可能会掏钱买披萨。

##### 前方的路

你不一定要有 MBA 学位或者是一个销售黄鼠狼才能有效地管理这些问题。您只需要扩展您对软件开发过程的看法，以包括任何可能影响您的发布的东西，并做必要的事情来保护您辛辛苦苦创建的代码。当然，这并不总是像用当今最酷的语言写一个华而不实的 UI 那样性感，但是当凌晨 4 点的时候，你已经 3 天没睡觉了，糟糕的管理决策几乎保证了明天你的产品不会完美，没有什么是性感的。

是时候让程序员重新控制软件开发过程了。学习偷偷摸摸和不那么偷偷摸摸的技巧来处理所有威胁我们的程序、我们的自由时间和我们的理智的非编码问题是我们在未来几个月要做的事情。一步一步，一个问题接一个问题，我们将研究如何在灾难来临之前发现它，并采取先发制人的措施。最终的结果将是有更多的时间做你真正喜欢的事情——编写酷代码，成为下一个黑仔应用。这难道不是你选择这个职业的初衷吗？

## 分享这篇文章