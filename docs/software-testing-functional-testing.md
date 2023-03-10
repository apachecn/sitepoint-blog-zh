# 软件测试:功能测试

> 原文：<https://www.sitepoint.com/software-testing-functional-testing/>

## 分类这个

正如软件测试周期可以被分成不同的级别，实际的测试过程本身也可以被分成不同的类别。第一个整体要素涉及功能测试——确保产品正常工作，做正确的事情——验证和确认方法。在执行更高级、更复杂的测试之前，必须成功完成各种类别的功能测试。

## 单元测试

单元测试，或者有时称为模块化测试，通常在开发人员级别执行。这种类型的测试通常由主要开发人员之外的人进行。首选这种方案的原因是让尽可能多的人关注代码。编码人员可能会做出一些假设，这些假设会妨碍他/她对单元的功能保持客观。让其他人测试这个单元可能不能保证一个没有错误的模块，但是它在那个方向上走了很长的路。

软件测试团队可能会也可能不会参与到这个层次中。如果是，那很可能是在一个更非正式的层面上。此时的合作活动鼓励团队合作和凝聚力。这里的目标是始终记住最终的结果:成功的产品交付。

## 集成测试

这是测试团队可以产生重大影响的地方。到目前为止，开发的单元之间的协作情况如何？这里关心的不是检查成品看起来会是什么样子或者它将如何运行；更重要的是，当前发布的模块作为一个新组件的功能如何。这是将构建模块放在一起，看看我们到目前为止已经完成了什么。

正如我在以前的文章中指出的，这种类型的测试应该在它自己的测试环境中进行。测试团队需要能够尝试可能对其他人产生破坏性影响的事情。对于开发人员来说，当他/她试图让一个复杂的算法以预期的方式运行时，没有什么比数据发生意外变化更令人不安的了。

## 系统试验

这是该计划第一次完全作为一个单一的实体，只是等待履行其魔术。此时，测试团队应该记录它发现的每一个谬误。风险分配在这里非常重要。错误的风险可能很低:字体大小或颜色错误。或者它们可能是主要的:按“开始”键会导致电脑关机。大多数缺陷及其相关的风险将介于两者之间。测试团队必须有一个正式的方法来报告每一个失败，以便允许开发人员容易地重现这种情况。

需求确认在这里是最重要的。这是主动评估整个应用程序功能的第一级。最终目标是确保最终的输出是所期望的。

## 系统集成测试

如果不是在客户的系统上，系统集成应该在尽可能接近用户结构的系统上进行。大部分的主要缺陷在这个时候应该已经被发现和纠正了。这里的目标是确保应用程序在安装到最终目的地时能够正常工作。

## 用户接受度测试

顾名思义，用户验收测试(或 UAT)是客户验证即将交付的产品性能的机会。用户可以使用现有的测试用例，或者开发他自己选择的测试用例。无论如何，测试通常由用户群中的个人或专门指定执行该任务的个人来执行。目的是用新的、客观的眼光从不同的角度看待结果。

## 回归测试

回归测试可能在测试过程中的任何时候发生。通常，这意味着重新运行之前已经通过的测试。此处的目标是确保为纠正或丰富产品而添加的增强功能不会引入新的错误。这是将自动化引入测试计划的绝佳时机(但是这是另一个完全不同的主题)。

有几个其他类别的测试涉及到最终用户:Alpha 和 Beta 测试。每一种都有自己独特的环境。

## 阿尔法测试

通常，Alpha 测试是在开发者所在地的受控环境中进行的。外部用户被邀请进来，在一个受控的环境中使用新的应用程序。本地专家可能由最初的开发和测试团队的成员组成，他们随时可以回答可能出现的任何问题。

## Beta 测试

另一方面，Beta 测试可能是在最终用户的生产环境中对新应用程序的测试。通常，它与现有程序并行运行(如果应用程序是替代程序的话),或者可以在“沙箱”环境中运行，让新用户有机会习惯新项目的外观和感觉。

## 未完待续…

我在这里介绍的当然不是一个万能的方法。测试类别，何时以及如何执行，是一个不断变化的过程的一部分。每个项目都有自己的要求和限制。要记住的最重要的一点是，在执行任务时要有一套计划，这些计划可以调整和修改，以提供最完整的方法来获得尽可能成功的最终产品。

## 分享这篇文章