# 灯一代的教训——tilllate.com

> 原文：<https://www.sitepoint.com/lessons-from-the-lamp-generation-tilllatecom/>

[昨晚](http://webtuesday.ch/meetings/20061114)我们接受了[西尔万](http://webtuesday.ch/members/silvan)和[斯蒂芬斯](http://webtuesday.ch/members/rofus)tilllate.com[旋风史](http://tilllate.com)的款待，并于周二交付给一个挤满人的[网络——感谢](http://webtuesday.ch/) [search.ch](http://search.ch) 的主持，并向那些被困在走廊里的人道歉——寻找更大的会议空间的工作正在进行中。

这里描述的演讲[大致翻译为“tilllate.com:从 0.1 到 30 台服务器”；](http://webtuesday.ch/meetings/20061114#agenda)

> tilllate.com 每月有 1 亿的浏览量和 100 万的访客，是瑞士最大的网络平台之一。该网站目前包含 60，000 行代码和 430 个数据库表，由 30 多台服务器提供服务。软件和基础设施由 5 名开发人员和工程师组成的团队负责。Stefan(系统工程师)和[Silvan](首席技术官)展示了他们从零开始构建 tilllate 的技术经验。

这本书既有洞察力又有娱乐性，以忏悔和过去灾难的故事为支撑。蒂尔莱特的故事可能对大多数“灯一代”来说是真实的。

在 redux:tilllate.com 开始是一个“只是为了好玩”的项目，而创始人是学生。自学，从“View > Source”开始，逐步升级到 PHP，Y2K 发布的第一个版本是共享主机上的大约 40 个脚本。你知道那种——违反每一个软件“最佳实践”,例如[关注点分离](http://en.wikipedia.org/wiki/Separation_of_concerns)—嵌入 HTML 的 SQL，成熟的注入，XSS 等等。但是它起作用了，网站越来越受欢迎…

…直到六年后的今天，他们雇用了 30 多名全职员工，存储着大约 1.5*TB*的用户上传图像，忙于将 tilllate 网络扩展到欧洲其他地区，维护着一个接近最佳实践的代码库，并且似乎完全乐在其中。这几乎是一个“从贫穷到富有”的故事，由 LAMP 和一个聪明的团队驱动，他们在发现问题时就采取行动。

但是最后一个第 22 条军规出现在西尔万的最后一张幻灯片上——“回顾过去，我们还能做得更好吗？”，其中一点大意是“采用更好的开发实践，OOP 等”。对此 [Lukas Smith](http://pooteeweet.org/) (他现在是苏黎世的公民)回答道——“如果你这样做了，你可能已经失败了——几个月来开发一个类似 J2EE 的东西，当它发布时，对用户来说是平淡无奇的，而且太死板了，不能采纳他们的反馈”。

从另一个角度来看，PHP 肤浅的“上网”学习曲线和“黑客能力”允许 tilllate 的创始人在他们对网站主题有热情的时候推出一个互动网站。如果他们等着成为计算机科学专业的学生，在 BigCorp 公司做几年隔间开发，tilllate.com 很可能永远不会发生。

不管怎样，tilllate.com 目前最大的问题似乎是寻找(体面的)PHP 开发人员。我不知道其他地方的情况是否有所不同，但我听说苏黎世有不少中小企业面临同样的问题。PHP 开发人员在哪里？

## 分享这篇文章