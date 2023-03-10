# PHP 特质:好还是坏？

> 原文：<https://www.sitepoint.com/php-traits-good-or-bad/>

2012 年 3 月初，PHP 小组宣布发布 PHP 5.4。开发人员急切地期待着这个版本，因为 5.4 将带来许多新特性，其中最受欢迎的是 traits。在发布之前，Shameer C 写了一篇关于在 PHP 中使用 traits[的精彩概述，我强烈推荐在这篇文章之前阅读 Shameer 的文章，因为我假设你已经对 traits 有了基本的了解。](https://www.sitepoint.com/using-traits-in-php-5-4/ "Using Traits in PHP 5.4")

特性已经被 PHP 开发社区普遍接受，主要是因为它是存在于其他编程语言如 Java、C++和 Phython 中的一个特性。此外，traits 的好处被广泛吹捧，开发人员给出了他们自己关于 traits 如何使任何项目受益的意见，特别是作为 OOP 继承的替代。但是特质真的那么好吗？它们是一个有助于提高 PHP 开发水平的特性，还是仅仅是一种时尚？

## PHP 的特点是不好的

从表面上看，PHP 特性得到了强有力的支持，因为使用它们有助于减少整个应用程序中的代码重复。此外，它们可以帮助提高代码的可维护性和整洁性。

特性当然是受欢迎的，但是许多领先的开发人员担心它们可能会被用在意想不到的地方。Anthony Ferrara 就是这样一个开发者，他甚至担心 traits 会成为继 eval 和 constants 之后第二个被滥用的特性。但在安东尼提出他的观点之前，他提出了一个非常有趣的观点:特征实际上是特定混合的集合，本质上没有状态。PHP 的 traits 实现确实允许使用状态，因此 PHP 中的 traits 实际上是 mixins。这个简单的事实质疑了 PHP 特性的真实意图，因为最终它们都挂在一面错误的旗帜下。没有解释为什么特征实际上被处理为混合，而不是它们应该是的全球公认的无状态混合。

Anthony 继续说道，traits 在功能上非常接近于 extensions 允许我们将类耦合在一起。随着 extends 成为一个备受推崇和长期使用的特性，我们有理由问一问，特征是否真的在 PHP 中占有一席之地，或者它们是否试图站在现有特性的肩膀上，试图显得更高？

然后还有接口的问题。许多开发人员对于接口和特征之间的真正区别只有一个模糊的概念；它们被重用的能力非常相似，与它们可能的继承深度也非常相似。特征实际上是 PHP 的新事物吗，或者它们真的只是接口的升级？

## PHP 的特点是好的

不管那些没有回答的问题，traits 对 PHP 来说是很棒的，它允许我们创建多重继承场景(extends 只支持单一继承)。

单一继承已经被使用了很多年，是 PHP 中面向对象编程的重要组成部分，它限制了高级程序员开发复杂的系统，同时保持代码的整洁和最小化。在其他语言中，多重继承可以用来消除这种情况下的重复代码。但是多重继承在 PHP 中是不可能的。相反，接口是作为替代品提供的，而且是不成功的替代品。

接口并不打算以这种方式使用，而是充当契约，强制实现它的任何类交付它的功能。这有助于打开类和方法的耦合，但是在 PHP 中没有提供真正的替代多重继承。

一些开发人员试图为 PHP 中的多重继承创建一些创造性的解决方案，但是很多都很臃肿，使得整体结果变得多余，更像是一个实验而不是实际的解决方案。

有了特征，多重继承就可以用巧妙的系统自然地实现。正如 Shameer 在他对特征的介绍中所展示的，你可以在类中创建多个特征。约翰·斯奎布在他 2011 年的例子中利用多重特征创造了一个多重继承环境[。](http://johnsquibb.like97.com/blog/read/multiple-inheritance-in-php-5-4-using-traits "John Squibb's Programming Blog - Multiple Inheritance in PHP 5.4 using Traits")

许多程序员认为多重继承是邪恶的，引用了[钻石问题](http://en.wikipedia.org/wiki/Multiple_inheritance#The_diamond_problem "Multiple Inheritance - Wikipedia, the free encyclopedia")，并声称单一继承提供了更少的麻烦。PHP 的问题不在于实现多重继承的实用性，而在于实现多重继承的可能性所带来的象征意义。鉴于许多编程公众表示他们不喜欢 PHP，这一点变得越来越重要，如果你在谷歌上搜索“不要使用 PHP”，就会发现这一点。有了多重继承的可能性，PHP 就变成了更有挑战性、更有表现力、更容易被接受的编程语言，而不仅仅是“最常见的 web 语言”。当 Java、Python 或 C++程序员因为 PHP 缺乏对跨语言的标准化编程方法的支持而试图抛弃它时，我发现这非常令人欣慰。

Traits 是一个真实的迹象，表明 PHP 正在慢慢地、肯定地成为一种语言，它将包含越来越多的标准编程技术，有希望赢得更多成熟的程序员和开发人员。

## 结论

Traits 允许 PHP 开发人员创建更干净、更简单、更高效的代码，同时也允许开发和试验更复杂的系统。它们不好；他们太棒了！它们向使用 PHP 的 OOP 程序员开放了另一个层次的开发技术，我相信它们是未来 PHP 版本的标志。

关于本文中提到的主题的更多信息，包括特质本身，请参见以下链接:

*   Ircmaxell 的博客:特质是新的评价吗？
*   [John Squibb 的编程博客:PHP 5.4 中使用特征的多重继承](http://johnsquibb.like97.com/blog/read/multiple-inheritance-in-php-5-4-using-traits "John Squibb's Programming Blog: Multiple Inheritance in PHP 5.4 using Traits")
*   [azon media:PHP 中的多重继承——一种变通方法](http://azonmedia.com/entry/10/Multiple-inheritance-in-PHP---a-workaround "Multiple Inheritance in PHP - a Workaround")
*   [Refulz PHP:PHP 5.4 中的特征——为什么我们需要特征](http://php.refulz.com/traits-in-php-5-4-why-we-need-traits/ "Refulz PHP: Traits in PHP 5.4 – Why we need Traits") 
*   [Christer 的博客 o'Fun:在 PHP 5.4alpha1 中玩特性](http://cogo.wordpress.com/2011/07/01/playing-with-traits-in-php-5-4alpha1/ "Christer's Blog o'Fun: Playing with Traits in PHP 5.4alpha1")
*   PHP 特质，对待还是威胁？
*   [Refulz PHP:PHP 中的特征——多重和嵌套特征](http://php.refulz.com/traits-in-php-multiple-and-nested-traits/ "Refulz PHP: Traits in PHP – Multiple and Nested Traits")

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章