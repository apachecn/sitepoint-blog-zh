# 本周我们关注的是:代码质量和圣诞节

> 原文：<https://www.sitepoint.com/radar-week-code-quality-christmas-time/>

吼吼吼。欢迎来到圣诞版的《在我们的雷达上》,这是一个来自 web 开发世界的新闻、趋势和其他酷东西的每周综述。

在本周的新闻中，索尼黑客事件的余波仍在继续。尽管开始出现对朝鲜参与的怀疑，但在过去的几天里，这个国家已经在 T2 遭受了两次巨大的 DDoS 攻击，造成了大范围的网络中断。争议的中心是一部名为《采访》的电影，[索尼公司原本打算取消](http://variety.com/2014/film/news/sony-has-no-further-release-plans-for-the-interview-1201382167/)，后来又改变主意，宣布[将在网上上映](http://www.geek.com/news/sony-blesses-everyone-with-the-interview-on-christmas-day-1612367/)。

在朝鲜努力保持在线的同时，韩国也在制造自己的头条新闻，首尔的检察官起诉优步首席执行官特拉维斯·卡兰尼克经营非法出租车服务。从技术上来说，这使他成为一名国际逃犯，T2 可能会对他处以 18000 美元的罚款，或者面临两年的监禁。

在其他地方，一个新的竞争者出现了，这个城市拥有世界上最快的网速。走过苏黎世、东京和纽约——[来到明尼阿波利斯](http://arstechnica.com/information-technology/2014/12/minneapolis-residents-to-get-10-gigabit-fiber-for-400-per-month/)。是的，你没听错！

## 文本编辑器

选择文本编辑器是人们(尤其是程序员)非常热衷的事情——它们是你工作流程中不可或缺的一部分，可以不断调整以提高生产力。

这就是为什么我对 SitePoint 作者 Abder-Rahman Ali 最近的一篇文章非常感兴趣。他采访了 100 名 they 爱好者(包括马茨和 DHH)，问他们更喜欢哪个编辑器。他的发现引发了一些有趣的阅读。

看完调查后，似乎所有酷孩子都在使用 Vim。想变酷吗？好吧，这里有一个 [Vim 新手教程](http://www.dillonhale.com/index.php/blog/linux-tutorials/vim-tutorial-beginners/)可以帮你上路。

你不喜欢 Vim？然后看看这篇描述[一个设计师的崇高文本设置](https://medium.com/design-notes/a-designers-sublime-text-setup-e3963f8d79da)的文章。Sublime text 自称是你会爱上的文本编辑器，我必须承认，它是我目前的首选编辑器。

这里有一个关于在 Sublime Text 3 中设置开发环境的截屏，涵盖了它的许多重要特性，比如包控制和代码完成。

今年早些时候，Github 推出了一款文本编辑器来击败所有的文本编辑器。Atom(这是它的名字)最初在浏览器中运行，但它背后的团队一直在努力工作，上个月发布了用于 Linux 的[官方包，本月发布了用于 Windows](http://blog.atom.io/2014/11/05/linux-packages.html) 的[安装程序。](http://blog.atom.io/2014/12/10/a-windows-installer-and-updater.html)

## 本周流行语:平均堆栈

![MEAN Stack](img/29735c0ddb1a2ebf2c945f897bfba8fd.png)

MEAN 是 MongoDB、ExpressJS、AngularJS 和 Node.js 的首字母缩写——这是一组技术的集合，使开发人员能够仅使用 JavaScript 构建完整的 web 应用程序。它代表了体系结构的重大转变——从关系数据库到 NoSQL，从服务器端 MVC 到客户端单页应用程序。

SitePoint 作者 Adam Bretz 的这篇文章更深入地研究了这些技术，并介绍了他与人合著的关于该主题的新书。

Medium 目前正在撰写[一系列文章，探索平均值堆栈](https://medium.com/@joshuawcomeau/lets-play-mean-part-v-499bdd65385e)。他们目前正在进行第五部分，但是正如文章所说，不要急于求成，从[第一部分](https://medium.com/@joshuawcomeau/feelin-mean-8bd942df37d3)开始。

想要[用 AngularJS，Satellizer，Node.js 和 MongoDB](https://hackhands.com/building-instagram-clone-angularjs-satellizer-nodejs-mongodb/) 构建一个 Instagram 克隆？你当然知道！本文将向您展示如何做到这一点。

[MEANstack Daily](http://paper.li/nottinghamrobin/1412344024) 是一个关于 MongoDB、ExpressJS、AngularJS 和 NodeJS 的文章、视频和新闻的集合。

这听起来是不是有点太费力了？嗯，这里有一个视频详细介绍了[你如何按下按钮](https://www.youtube.com/watch?v=eSVQVauLezA)就可以将 MEAN stack 部署到 Google Cloud。

最后，这里有一个(非常)深入的教程，关于用 MEAN 构建现代 web 应用。

## 代码质量

作为开发人员，我们经常听到的是代码质量，面对现实吧，谁不想写出高质量的代码呢？

有太多的工具致力于可视化我们代码的质量。PhpMetrics 就是这样一个工具，它使用 D3 和一些复杂的分析算法来扫描应用程序的代码，并输出复杂的报告。

这些工具不仅仅局限于服务器端开发。这里告诉你如何使用 ESLint 来保持 JavaScript 的质量，或者使用 HTML Inspector 来帮助你(和你的团队)编写更好的标记。

这是一个开发者的故事，讲述结对编程和指导如何让他写出高质量的代码，这份报告详细介绍了物联网如何提高开源 Java 项目的代码质量。

这个来自 Keep Ruby 怪异会议的视频对此事有不同寻常的看法，声称伟大的作品(比如散文)和编写高质量的代码有很多相似之处。

最后，质量代码由质量测试支持。查看[我对 Rails 4 测试方案的简短评论](https://www.sitepoint.com/want-christmas-rails-4-test-prescriptions/)并为自己赢得一本书。

## 这是圣诞节！

![Christmas card](img/9b45c066335f9893bb49c87bba309074.png)

我们大多数人都将在圣诞节期间好好休息一下，但万一你发现自己渴望一个网页设计，一定要看看 [Boagworld 圣诞特辑](https://boagworld.com/season/10/episode/happy-christmas-boagworld-show/)，其中保罗和马库斯从克里斯·科伊尔、莎拉·帕门特、丹·爱德华兹、布鲁斯·劳森和许多许多人那里听到了过去圣诞节的故事。

《未竟事业》播客还推出了圣诞特辑，恰当地命名为[叮当铃摇滚](http://unfinished.bz/100)。它将安娜·德本汉姆(前联合主持人)作为特邀嘉宾回归，也恰好是第 100 集。

虽然圣诞老人现在已经离去，但这里有 20 个给网页设计师的圣诞礼物创意。亲爱的读者，我很想在评论中听听圣诞老人给你带来了什么网络相关的好东西。

展望 2015 年，在本文[中，50 位知名设计师分享了他们 2015 年](http://foundersgrid.com/design-trends-2015)的三大设计趋势。

回顾 2014 年，以下是[年度最佳和最差网页设计趋势](http://marketblog.envato.com/trends/best-worst-web-design-trends-2014/)。

* * *

这就是这周的全部内容。感谢加入我们。

我将留给你一整年你会看到的最好的灯光展示(T1)(这真的是太棒了)，一份科技下一步想要杀死的八种东西(T3)的清单(不满足于面对面的交谈)和一个找到更符合你口味的啤酒和葡萄酒的应用(T5)的清单(我加入)。

那么哪些链接引起了你的注意呢？你选择的文本编辑器是什么？你如何衡量你的代码质量？圣诞老人给你带来了什么与网络相关的好东西？让我们知道，讨论就可以开始了。

## 分享这篇文章