# SOLID Ruby:依赖倒置原则

> 原文：<https://www.sitepoint.com/solid-ruby-dependency-inversion-principle/>

继续我们的系列，让我们来谈谈[依赖倒置原则(DIP)](http://en.wikipedia.org/wiki/Dependency_inversion_principle) 。这个原则表明软件组件应该依赖于抽象而不是实现。这是什么意思？

在像 Java 这样的静态语言环境中，解释起来更容易，因为像接口和抽象类这样的语言结构为程序员提供了提供“抽象依赖”的方法，从而提高了过程中的灵活性和可测试性。

但是露比呢？它是一种动态语言，我们不需要指定依赖项的类型(duck typing 是一种语言所能拥有的最好的特性，IMHO)，所以我们可以免费使用 DIP，对吗？如果你运用一些简单的技巧，是的。让我们谈谈那个。

让我们回到上一篇文章中的例子: