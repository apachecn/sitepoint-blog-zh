# 敏捷的 Java 标准可能吗？

> 原文：<https://www.sitepoint.com/is-an-agile-java-standard-possible/>

在[开源周](https://www.sitepoint.com/blog/)期间，Simon Ritter 写了关于 [Java 社区进程](https://www.sitepoint.com/keeping-community-in-java-community-process-jcp/)的文章，探索了它的历史和优势。在另一篇文章中，[为 Azul 的博客](https://www.azul.com/agile-java-standard-possible/)写的，他跟进了 JCP 在未来可能如何发展——我们在这里发布它，以确保你看到 Java 的发展是如何发展的(是的，meta)。

本周，我们参加了 JCP 执行委员会(EC)每年举行的三次面对面会议中的一次。这只是我第二次参加这种形式的会议；尽管我有很长的 Java 历史，从这个角度来看事情的工作方式还是很有趣的。

由于会议记录尚未公布，我就不直接谈论讨论的内容了。然而，一段时间以来，JCP EC 一直在考虑的一件事是 Java SE 的标准化在未来将如何工作。

首先，让我们看看事情在过去是如何运作的。JCP 始于 1998 年，作为一种正式的机制，允许感兴趣的团体开发 Java 技术的标准技术规范(感谢， [Wikipedia](https://en.wikipedia.org/wiki/Java_Community_Process) )。这是通过标准开发 Java 平台、API 和技术定义的开放过程的开始。在 J2SE 1.4 之前，Sun 在内部开发 Java，并决定增加什么功能和做什么改变。

下表显示了 Java SE 的 Java 规范请求(JSR)的历史，包括专家组(EG)的规模。

| **发布** | **JSR** | **EG 尺寸** | **日期** |
| Java 2 SE 1.4 | Fifty-nine | eight | 2002 年 5 月 |
| Java 2 SE 5.0 | One hundred and seventy-six | Eighteen | 2004 年 9 月 |
| Java SE 6 | Two hundred and seventy | Nineteen | 2006 年 12 月 |
| Java SE 7 | Three hundred and thirty-six | 4 + 2 Oracle | 2011 年 7 月 |
| Java SE 8 | Three hundred and thirty-seven | 3 + 2 甲骨文 | 2014 年 3 月 |
| Java SE 9 | Three hundred and seventy-nine | 3 + 2 甲骨文 | 2017 年初 |

在 900 范围内也有一些奇怪的 JSR，它们是规范的维护版本，特别是 J2SE 1.4。这些是 JSR 915，916，917，918 和 923。这些想法后来似乎都被放弃了。

显然，围绕这一切有很多历史，但看到 EG 大小的变化方式很有启发性。对于第一个 JSR 来说，它很小，大概是因为人们不确定这是否以及如何工作。Java SE 5 和 6 引起了人们的极大兴趣，但这种兴趣后来减弱了。唯一参与 SE 7、8 和 9 的非 Oracle 实体是 Red Hat 和 IBM。看起来，Java 社区的大多数人都乐于看到 Oracle 的 Java 工程小组以及 Mark Reinhold 和 Brian Goetz 这样的人来决定向 Java 中添加哪些主要特性。

这个故事中的另一个重要日期是 2007 年，当时 Sun 开源了他们的 JDK 实现。OpenJDK 项目现在是 Java SE 标准的参考实现。这是一个明智的决定，因为这意味着 Java 社区现在对 Java 平台的开发有了更大的可见性，可以频繁地获得早期版本。

我们还看到，从 JDK 8 开始，在 JDK 9 中变得越来越重要的是使用 [JDK 改进提案(JEPs)](http://openjdk.java.net/jeps/0) 作为定义 JDK 变更的一种方式。其中许多与 Java SE 的定义空间重叠，但也有许多不在此范围内([向 Javadocs](http://openjdk.java.net/jeps/225) 添加搜索功能就是一个很好的例子)。

jep 是对核心 Java SE 平台或包含的 JDK 进行更细粒度变更规范的一种极好的方式。在 Java SE 9 之后，我们的想法是将此作为一种更敏捷(因此也更现代)的方式来交付对 Java 的更改。其概念是，将独立于其他 jep 开发单个 jep，以便当它们准备发货时，可以包含在 JDK 中。不再需要等待两年或两年以上的时间来获得新的特性。太棒了。

理论上，这是一个极好的主意，因为开发者一旦准备好就可以利用这些特性。然而，对于这在实践中如何工作，有一些严肃的考虑。

拥有主要版本在某些方面使开发人员的生活更简单，因为他们知道 Java 源代码是在哪个版本下开发的，所以它将在哪个级别编译，以及它需要哪个版本的运行时。这根本不是一个不可克服的问题，因为所有有效改变的只是发布的粒度。我们没有需要 JDK 8 和 JRE 8 的 Java SE 8 代码和二进制文件，而是有需要相关 JDK/JRE 的 Java SE 9.1.3 代码。

然而，这导致了一些关于如何指定 Java SE 的难题。对于现有的 JSR 系统，Java SE 的每个主要版本都有一个完整的规范，任何人都可以使用它来创建自己的实现。还有一个相关的参考实现(OpenJDK)和一个测试套件(TCK/JCK)。如果我们开始使用细粒度的点发布系统，JSR 的工作方式将需要改变，如果它继续被使用的话(Oracle 软件开发 Java 平台组副总裁 George Saab 已经说过，没有计划停止使用 JSR 来定义 Java SE)。

正如我们所知，JSR 目前使用一个专家组来定义规范，然后发布供公众审查。假设参与的每个人都投票赞成规范被接受，那么实现就被发布了。

在主要版本之间包含较小的功能意味着它们的规范需要被添加到 JSR 中以形成一个集合。拥有一个专家小组和公众评论似乎是一个太过重量级的方法来使这变得可管理(考虑到 90 个 JEPs 正在被包括在 JDK 9 中)。

在我看来，JCP 需要对它所使用的过程进行一些实质性的改变(至少对于 Java SE 是这样，但是对于 Java EE 也可能使用相同的方法)。让我们希望真的有可能制定一个敏捷的 Java 标准。

![](img/db74442c267d37c5977381f9dc0dcd15.png)

西蒙的文章到此结束，但我想修改他最近写的另一篇文章的部分内容，这篇文章用轶事数据跟进了这个想法。

这次演讲的主题[ *编者按:在 [Voxxed Days Bristol](https://voxxeddays.com/bristol/)* ]和我今年早些时候写的一篇博客文章[ *你刚刚读过的那篇* ]一样。在我的演讲中，我讨论了核心 Java 平台的标准在过去的发展方式(主要是通过 [JCP](https://www.jcp.org/en/home/index) )以及这在未来会如何改变。

对我来说，最有趣、最有启发性的是我对观众做了一个快速调查。假设我们要切换到敏捷方法来开发和发布 Java 的新特性，人们希望这样的频率有多高？我开始的想法是每季度发布一次，得到的投票不到几票。大多数观众似乎对目前的发行速度感到满意。显然，这只是一个很小的样本，但是我怀疑它代表了一般的开发者。

后来我与一些人讨论了这一点，普遍的看法是，更快地获得新功能当然是好的，但缺点是平台敏捷开发产生的测试、认证和支持的数量。

在我的旅途中，我肯定会收集更多关于这个问题的数据，因为敏捷方法很可能适合应用程序，但不太适合核心 Java 平台。让我知道你是否有强烈的这种或那种感觉。

## 分享这篇文章