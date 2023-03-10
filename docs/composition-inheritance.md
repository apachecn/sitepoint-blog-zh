# 构成和继承

> 原文：<https://www.sitepoint.com/composition-inheritance/>

![](img/9b40618c7ff69602661381c769d4a434.png "comp")“偏爱组合胜过继承”，通常被认为是 1994 年的一本书，[设计模式](http://en.wikipedia.org/wiki/Design_Patterns)，作者是一群被称为“四人帮”的非常聪明的人，经常被作为一个可靠的软件开发建议给出。在这篇文章中，我将尝试解释为什么以及如何更喜欢组合而不是继承——特别是，如何使它适合您的 Ruby 代码。

## 那么遗传有什么问题呢

出于本文的目的，我将假设你们都和我一样是糟糕的程序员。我还假设你会犯类似的错误，当一个项目从“这太棒了”的绿地开发变成“哦，不，为什么我做了那件事”的维护模式时，你会发现自己处于类似的困惑中。所以，如果这读起来有点像忏悔，那是因为我犯了所有这些错误，所以你不必(再次)这样做。

### 模拟真实世界

我在开发中遇到的许多令人头痛的问题都来自于对什么是继承以及为什么应该使用继承的误解(事实上，我确定我在这里提供了一个不正确的定义)。一个初学编程的人——不管是 Ruby、Java 还是其他语言——看到继承时会想“好极了，我让`Foo`继承了`Bar`,这样我就可以免费获得`Bar`的所有特性了。”而且是真的，`Foo < Bar`就会这么做。这并没有完全捕捉到的是，在除了最小的程序之外的任何程序中，说 Foo 是一个酒吧’只在有限的时间内有效。

最终，Foo 和 Bar 会有很大的不同，正确的说法更像是“Foo 共享 Bar 的一些特性”。当然，现在已经太晚了，项目已经进行了 3 个月，并且被两个紧密耦合的类所困，这两个类很难测试，也很难分离。

通常，继承问题会在项目进行到一半的时候或者需求发生变化的时候显现出来。这是因为看起来只能被建模为“是-是”关系的关系可以被表达为“有-是”关系，而“有-是”关系要灵活得多。

### 一个例子

让我们看一个例子。假设我们正在尝试建立发展世界的模型。我们有`Developers`和`Testers`。你可以合理地期望任何初学者的文本框为类结构提供类似这样的东西。