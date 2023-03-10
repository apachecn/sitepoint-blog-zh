# 其他很酷的 ruby 项目

> 原文：<https://www.sitepoint.com/other-cool-ruby-projects/>

考虑到 Rails 制造的所有噪音，你认为它是 Ruby 世界的全部和终结是情有可原的——当然它普及了这种语言，但是还有许多其他很酷的项目在使用 Ruby。以下是一些例子:

## Merb

Merb 是另一个全栈 web 框架，它的工作方式很像 Rails。然而，作者去掉了相当多的赘肉，使框架变得不那么固执己见，并且使其线程安全。Merb 试图尽可能模块化，依赖第三方 [gems](http://www.rubygems.org/) 来实现它的大部分功能，而不是拥有一个庞大的单片内核。

它支持不同的对象关系映射器(ORM——协调应用程序和数据库之间的事务的东西),包括[数据映射器](http://datamapper.org/)、[续集](http://sequel.rubyforge.org/files/sequel/README.html)甚至是 Rails 默认的 ORM: [ActiveRecord](http://ar.rubyonrails.com/) 。

Merb 代表[杂种](http://mongrel.rubyforge.org/) + [ERB](http://www.ruby-doc.org/stdlib/libdoc/erb/rdoc/c) ，所以它们构成框架的重要部分并不奇怪，尽管你可以使用 [HAML](http://haml.hamptoncatlin.com/) 和 SASS 作为你的模板引擎。

## 露营

[露营](http://camping.rubyforge.org/files/README.html)是微框架(只有 4k！)由令人不安的天才设计[为什么幸运僵硬](http://whytheluckystiff.net/)。这可能更像是一个测试，看看你能用很少的代码做多少事情，但是如果你有一个原型或者一次性的站点，这将会很有用。

## 鞋子

另外，为什么， [shoes](http://code.whytheluckystiff.net/shoes/) 根本不是一个 web 框架——它是为了让你轻松编写小型桌面应用程序而设计的。_why 围绕 Cairo 图形库创建了一个可爱的小工具包包装器，它将成为“ [Hackety Hack](http://code.whytheluckystiff.net/hacketyhack/) ”的基础，这是一个初学者编程入门工具包(想想乐高，但有代码)。尽管还处于起步阶段(上次我检查时它不支持 gems ),但时不时地做一些 GUI 编程会很有趣——主要是为了意识到你当初为什么选择 web！

当然还有很多更酷的 Ruby 项目——你为什么不去花几分钟浏览一下[rubyforge.com](http://rubyforge.org/)？

## 分享这篇文章