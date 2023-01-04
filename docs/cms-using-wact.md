# 使用 WACT 的 CMS

> 原文：<https://www.sitepoint.com/cms-using-wact/>

[Pavel Chtchevaev](http://sourceforge.net/users/pachanga/) 最近在 Sitepoint 的[高级 PHP 论坛](https://www.sitepoint.com/forums/forumdisplay.php?f=147)发布了 [LIMB](http://sourceforge.net/projects/limb/) ，这是一款使用[WACT](http://wact.sourceforge.net)模板引擎的 PHP CMS。

WACT 仍处于进化阶段，因此四肢开发者需要大量的定制，但很高兴看到有人已经利用 WACT，并将结果转化为开源。可以使用 WACT 的某些部分，而忽略其他部分，这是 WACT 设计“哲学”的一部分，是组件的集合，而不是僵化的框架。

在 LIMBs [demo](http://limbdemo.0x00.ru/) 中查看一些模板源代码也很有趣(如果你想检查这些模板，你现在就需要从 SF 下载源代码，尽管它们直到今天才在网上提供)。

我知道有人对 WACT 的模板化方法持某种程度的怀疑态度——讽刺的是，这可能更容易“卖给”有 ASP.NET 或 JSP 标签库经验的开发人员，而不是习惯于自作聪明的 PHP 开发人员。

值得注意的是标记是如何*声明性的*–你*声明*应该出现在模板中的元素，就像 HTML 是声明性的一样–例如你声明一个表格–模板中没有命令性的 if/else 类型逻辑。当您考虑到 LIMB 已经实现的一个更高级的标签(用于编辑内容)时，这种方法的威力就变得显而易见了；

这个标签负责生成在表单中嵌入 [htmlarea](http://www.interactivetools.com/products/htmlarea/) 所需的 HTML 和 Javascript。看一下实现，它在“运行时”做了更多可能是必要的事情——性能可能会通过将一些生成转移到编译时来提高(在 WACT [这里](http://wact.sourceforge.net/index.php/TemplateSyntax)有一些关于运行时与编译时的注释)。无论如何，对我来说，令人鼓舞的是，开发人员似乎发现实现他们自己的 WACT 标签相当容易。

## 分享这篇文章