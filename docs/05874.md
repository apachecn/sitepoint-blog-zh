# 利用游戏机制构建引人入胜的网络应用

> 原文：<https://www.sitepoint.com/building-engaging-web-apps-game-mechanics/>

当我们谈到“游戏”时，我们会想到体育活动，或者视频和棋盘游戏。请放心，我不是在谈论开发下一个大片 FPS，第三人称恐怖片，MMORPG，或 2D 平台。这是关于超越 JavaScript 动画和 CSS 转换的简单视觉吸引力，进入游戏设计和机制，以创建引人入胜的网站和网络应用程序。

## 什么是游戏化？

“游戏化”是在非游戏环境中使用和应用游戏设计技术和游戏机制，以吸引目标受众改变行为、学习新技能或实现创新。游戏设计几乎可以应用于商业的所有方面，从客户参与、员工表现、培训和教育、创新管理、个人发展、可持续性和健康。Gartner 预测，到 2015 年，超过 50%的管理创新流程的组织会将这些流程游戏化。

#### 行为改变

游戏化最常见的用途是吸引特定的受众，鼓励他们改变目标行为。例如，耐克的 [Nike+](https://secure-nikeplus.nike.com/plus/) 移动应用和 FuelBand 可穿戴技术将健身(期望的行为改变)变成了一种游戏，以吸引和鼓励人们采用和保持新的习惯(例如跑步)。

俄罗斯零售初创公司 Enter.ru 成立于 2011 年，采用有趣且创新的游戏化方法来帮助塑造他们的组织文化。奥林匹亚达！(字面奥林匹克是的！)，是一款内部激励游戏，使用 [Jive 的](https://www.jivesoftware.com)游戏化平台开发，通过他们的内部网提供。奥林匹克的主要区别！它认可线上和线下的成就和行为。在奥林匹克好好表现的动力！主要是内在的，这可能是一个比外在的财务奖励更强大的文化变革工具。一些相当[另类的游戏活动](https://twohives.wordpress.com/2012/10/08/using-intranet-gamification-to-shape-organisational-culture/)会获得积分，在年底，前 30 名积分领先者会获得一个惊喜的海外假期。

#### 技能发展

游戏化越来越多地被用于正规教育和企业培训项目中，以使学生获得更身临其境的学习体验。通过结合模拟和动画，将课程变成游戏，学生们沉浸在一个提供即时反馈的环境中。这使他们能够在安全的虚拟环境中获得和实践新技能。

像[汗学院](https://khanacademy.com)和[代码学院](https://codecademy.com)这样的教育网站通过承认学生在课程中进步的各个方面来迫使学生继续学习。关键的成就会被授予徽章，学生们被鼓励在社交网络上分享他们的成就。这种方法不仅能让学生保持参与，还能提升网站本身。

#### 支持创新

创新游戏的结构不同于行为改变和技能发展游戏。这些通常利用[紧急游戏结构](https://en.wikipedia.org/wiki/Emergent_gameplay)来定义目标、规则、工具和游戏空间，玩家需要在其中探索、实验、合作和解决问题。创新游戏通常使用游戏机制来创建更具吸引力的体验，但关键是让大量玩家参与(众包)来解决非常具体的问题。一个例子是 [Fold.it](https://fold.it) ，以及[它如何帮助创造了艾滋病研究的突破](https://techland.time.com/2011/09/19/foldit-gamers-solve-aids-puzzle-that-baffled-scientists-for-decade/)。

## PBL–积分+徽章+排行榜

点数、徽章、排行榜(PBL)是视频游戏行业的支柱。实际上，今天所有的视频游戏都有某种形式的 PBL 奖励系统，以 XP、升级和成就解锁的形式。

这种游戏通常使用玩家之间的竞争和/或合作(co-op 游戏),并涉及行动点数、成就徽章和排行榜来推动竞争。LevelEleven 是 PBL 机械如何通过竞争、劳动力激励和平台参与来帮助推动销售业绩和 CRM 采用的一个例子。

像 [Badgeville](https://badgeville.com) 、 [Bunchball](https://bunchball.com) 、 [JiveSoftware](https://www.jivesoftware.com) 和 [Gigya](https://gigya.com) 这样的公司提供各种产品和服务，旨在将观众参与和成就系统整合到网站中，以实现内部和面向客户的游戏化。

如果你可以原谅基本的管理界面，Arturs Sosin 的 [Gamify 类](https://www.phpclasses.org/package/7045-PHP-Manage-user-points-to-implement-site-game-logic.html)提供了一个简单的架构，用于在现有的 PHP/MySQL 驱动的网站中实现功能性 PBL 机制:

```
<?php
require_once './gamify.php';
// 
$g = new gamify($dbhost, $dbuser, $dbpass, $dbname);

// give John Doe 1000XP for doing something 
$g->add_experience('john_doe', 1000); 

// add 50 points towards achievement #1 for doing something specific
$g->action('john_doe', 1, 50);  

// add 25 points towards achievement #2 for something else
$g->action('john_doe', 2, 25);
```

请注意，Gamify 类并不完全是即插即用的——肯定需要一些黑客技术——但它提供了一个基本的管理界面来管理用户、XP 等级和成就解锁。还有一个非常基本的排行榜(top.php)，以及一个示例脚本(actions.php)来演示 XP 均衡和基于时间的成就解锁交互。

## 超越 PBL

虽然 PBL 机制是游戏化的传统标志，但请记住，它们只是实现潜在参与模型的工具。专注于明显的 PBL 驱动的游戏机制往往会偏离游戏化最初打算的潜在参与模式。在最好的情况下，这可能导致“玩家”只是为了升级而增加积分(即“研磨”)，或者积累无意义的活动徽章，或者在最糟糕的情况下，脱离你的观众。

据 Gartner 称，目前 80%的游戏化应用将无法满足业务目标，主要原因是糟糕的设计。高德纳集团(Gartner Group)研究副总裁布莱恩伯克(Brian Burke)建议，企业应该转而关注游戏设计中更重要、更微妙的元素——比如平衡竞争与合作，或者定义一个有意义的游戏经济。

作为 web 开发人员，将目标受众(即客户、员工或公众)视为“玩家”——有真实需求和欲望的人，他们会对刺激做出反应，这一点很重要。真正的挑战是设计以玩家为中心的应用程序，关注潜在的动机和奖励，更充分地吸引玩家。

游戏化的先驱 Yukai Chou 描述了这种“以人为中心的设计”而不是“以功能为中心的设计”，并围绕游戏化的“8 个核心驱动力”开发了一个[框架](https://www.yukaichou.com/gamification-examples/octalysis-complete-gamification-framework/#.UuMThjc_4WM):
1。史诗意义&召唤
2。发展&成就
3。赋予创造力&反馈
4。所有权&占有权
5。社会影响力&关联度
6。稀缺&急躁
7。好奇心&不可预测性
8。损失&规避

![Otalysis Framework](img/83bf7f05eb8684d932c58bc08e2a8907.png)

你可以在 Yukai 的[网站](https://www.yukaichou.com/gamification-examples/octalysis-complete-gamification-framework/#.UuMThjc_4WM)上阅读这些核心驱动是什么，它们与左脑/右脑活动的关系，以及它们如何用于白帽/黑帽游戏化。理想情况下，你会希望专注于这 8 个核心游戏驱动的特定组合，这将取决于你的目标受众，你希望鼓励什么样的特定行为，作为参与该平台所提供的激励奖励的回报。

## 结论

我们已经简要了解了什么是游戏化，以及如何在各种非游戏环境中应用游戏化来吸引观众、影响行为、开发或学习新技能，或者促进协作和创新。我们已经看到了不同的公司是如何在他们的产品和组织中实现游戏化的，以及你可以立即用来游戏化你自己的网络项目的工具。

希望这能激发你把乐趣带回到有趣的 web 开发中。请在下面留下评论，告诉我们你是如何在自己的网络项目中使用或计划使用游戏化的。

## 分享这篇文章