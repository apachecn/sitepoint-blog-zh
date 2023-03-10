# 作为语言设计模型的语言

> 原文：<https://www.sitepoint.com/yoda-speak-as-a-model-for-language-design/>

微软软件设计工程师[马特·沃伦在 MSDN](http://blogs.msdn.com/mattwar/archive/2005/10/09/479008.aspx) 的博客中提出“尤达语”是设计更自然编程语言的有用模型。

Matt 是 C#语言发展背后的主要驱动力之一。尽管 C# 2.0 仍在等待它在 Visual Studio 2005 中的正式发布，但 T2 的 C# 3.0 的设计已经完成，Matt 正在展望编程语言设计的未来。

这个即将到来的版本中的一个主要特性是[语言集成查询](http://msdn.microsoft.com/netframework/future/linq/) (LINQ)，它旨在提供通用编程语言中数据库查询的所有功能。这里有一个 LINQ 的例子，来自[微软的 LINQ 概览](http://msdn.microsoft.com/library/en-us/dndotnet/html/linqprojectovw.asp):

```
 string[] names = { "Burke", "Connor", "Frank", 
                   "Everett", "Albert", "George", 
                   "Harris", "David" };

IEnumerable <string> expr = from s in names 
                           where s.Length == 5
                           orderby s
                           select s.ToUpper();

foreach (string item in expr)
  Console.WriteLine(item); 
```

当然，诀窍在于使用类似于 SQL 数据库查询的语法为 expr 赋值。然而，这种语法将被直接内置到 C# 3.0 中。NET 语言)，并且完全受 Visual Studio 下一个主要版本的支持。

SQL 和 LINQ 语法之间一个稍微有争议的区别是“from”出现在“select”之前。在 SQL 中，您可以指定要执行的操作(例如选择)，然后指定要从哪里执行该操作以及如何执行。LINQ 颠倒了这一点，试图用一种对人类来说更自然的方式来描述查询。

Matt 认为，这种转变可以(也将会)延续到语言的其他部分，这将成为下一波编程语言重新设计的创新特征。我觉得，由于他对尤达语的糟糕掌握，他的观点有点迷失了，但是在他的帖子的评论[中提出的一些例子暗示了他的潜力。](http://blogs.msdn.com/mattwar/archive/2005/10/09/479008.aspx#comments)

当然，Perl 几乎从诞生之日起就有了一些这样的东西，并因此获得了不必要的神秘的名声。从 [<cite>Perl 语法文档</cite>](http://perldoc.perl.org/perlsyn.html) :

> Perl 的许多语法元素都是可选的。与其要求您在每个函数调用周围加上括号并声明每个变量，您通常可以不使用这些显式元素，Perl 会明白您的意思。这就是众所周知的做我的意思，缩写为 DWIM。它允许程序员偷懒，以他们觉得舒服的方式编码。

Can 电流。像 C#和 VB.NET 这样的. NET 语言向表面上更自然的“Yoda ordering”过渡，还是像马特建议的那样，微软必须从零开始创造编程语言 Yoda？

## 分享这篇文章