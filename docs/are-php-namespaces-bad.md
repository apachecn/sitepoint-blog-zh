# PHP 名称空间真的那么差吗？

> 原文：<https://www.sitepoint.com/are-php-namespaces-bad/>

一段时间以来，PHP 开发人员一直需要名称空间(T2)。随着 PHP 应用程序变得越来越大、越来越复杂，名称空间对于防止代码冲突变得至关重要。

我的[最近的教程](https://www.sitepoint.com/php-53-namespaces-basics/)收到了许多抱怨 PHP 中名称空间实现的评论。主要问题是语法和反斜杠字符。在我解决这些问题之前，让我们快速回顾一下 PHP 的历史。

## PHP 一团糟

C#和 Java 等语言是按照严格的语法标准设计的。PHP 从*进化到了*。最初的版本发布于 1995 年，到版本 3 时，它已经是一种流行的过程编程语言。版本 4 引入了基本的面向对象，版本 5 提供了合理的标准 OOP 模型。名称空间现已添加到版本 5.3 中。

PHP 的批评者会认为这种语言一团糟。函数名不一致(例如 strpos、str_split、substr)，对象处理已被标记，并且与其他语言相比，某些语法有所不同(如果不是很奇怪的话)。

然而，PHP 仍然是使用最广泛的服务器端开发语言。其多功能性是其主要优势之一:

*   开发新手可以从简单的过程化编程开始。他们永远不需要接触面向对象的技术，仍然可以生产。
*   10 年前用 PHP 3 编写的代码在今天的 PHP 5.3 中仍然有效。可能需要一些小的调整，但很少需要大的重写。

PHP 代码可能并不总是漂亮、合乎逻辑或优雅的，但是开发是快速的，并且通常比其他选择更容易理解。

## PHP 名称空间实现

与 C#和 Java 不同，PHP 必须保持与非命名空间代码的兼容性。这已经实现了，您可以选择是否使用名称空间。然而，如果您使用 PHP 5.3(或更高版本)，我会推荐它们——即使您只是在整个项目中使用相同的名称。

选择`namespace`和`use`作为名称空间操作符似乎合乎逻辑。一些开发人员可能不同意，但是不管他们的名字是什么，情况都是如此。 [eTor](http://etor.org/ "download movies")

最后，我们来看反斜杠字符。大多数评论家抱怨说，它很难看，难以阅读，在 Mac 上打字也很笨拙。然而，我仍然认为它比最初提议的双冒号更可取。检查以下静态方法调用:

```
 // PHP 5.3 beta static method call
echo ::App::Lib1::MyClass::WhoAmI();

// PHP 5.3 final static method call
echo AppLib1MyClass::WhoAmI(); 
```

第二行打字更快，不容易出错，更容易阅读，也更容易理解。如果你在一个字符串外面看到一个反斜杠，你知道命名空间必须被包含。

当然，如果 PHP 使用“.”就太好了句点用于公共方法、静态方法和命名空间。这将使它与 Java、C#、JavaScript、Python 和许多其他语言保持一致。不幸的是，PHP 的历史和向后兼容性使得这很难实现。

没有一种语言是完美的，PHP 也远非如此！然而，命名空间已经被很好地实现了，尤其是当你考虑到它可能导致的限制和问题的时候。我相信你会喜欢上那个反斜杠的！

相关阅读:

*   [如何使用 PHP 名称空间，第 1 部分:基础知识](https://www.sitepoint.com/php-53-namespaces-basics/)
*   [如何使用 PHP 名称空间，第 2 部分:导入、别名和名称解析](https://www.sitepoint.com/php-namespaces-import-alias-resolution/)
*   [如何使用 PHP 名称空间，第 3 部分:关键字和自动加载](https://www.sitepoint.com/how-to-use-php-namespaces-part-3-keywords-and-autoloading/)

## 分享这篇文章