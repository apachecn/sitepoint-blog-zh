# IDE 鸿沟

> 原文：<https://www.sitepoint.com/the-ide-divide/>

via[Keith](http://keithdevens.com/weblog/archive/2004/Nov/25/IDE):[IDE 划分](http://osteele.com/archives/2004/11/ides)提出了一些关于面向*语言*的开发人员与面向*工具*的开发人员的有趣观点。

虽然这种划分在现实中可能并不那么清晰，正如[这个回复指出的](http://www.bobcongdon.net/blog/2004/11/dividing-developer-world.html)，但这是一种启发性的简化。

就个人而言，我会说我是语言导向型的，经常被不同的观点迷惑。我对统一的 [IDE](https://www.sitepoint.com/what-is-an-ide/) (EMACS / VIM 除外)的抵制主要是基于希望尽可能“原始”地访问源代码，而不是我所认为的使我依赖于工具(和供应商)的“简化”视图。

我也更愿意跟上语言功能的变化和工具特性/界面的变化(在我看来，这种变化更频繁，也更令人沮丧)。

这就是为什么我发现奥利弗·斯蒂尔的观点很有启发性，因为我发现自己同意 T2 的观点。

> 这里有一个来自 apache merlin 网站的例子

 `ReferenceDescriptor reference =
new ReferenceDescriptor( Widget.class.getName() );
ComponentModel model = (ComponentModel) m_model.getModel( reference );
model.commission();`

`Widget widget = (Widget) model.resolve();`

> 注意到你不得不把所有的东西都输入三次。光是阅读就让我生气。

…除了面向工具的开发人员可能不会输入，或者至少不会输入太多，像自动完成/向导/拖放/代码生成等。省了力气。

奥利弗暗示(我的解释)，但没有说明，这是静态和动态语言划分的一个方面。

对我来说，它解释了为什么我们有 Perl / Python / PHP / Ruby(等等)。)一方是程序员(面向语言),另一方是 C++/Java/C#(面向工具),双方都认为对方是疯子。

所以当一个 C++/Java/C#程序员说他们的平台比“X”动态语言更易维护时，也许他们真正指的是更好的工具。他们没有看到的是，动态“X”不需要同等程度的工具，也不需要同等的可维护性(由面向语言的开发人员来维护)。

识别开发人员心态的方法可能是问“你认为编程的未来是什么？”。倾向于“用图片编程/可视化建模/源代码编辑会死”的人在工具阵营，而建议“更具表现力/更强大/更接近自然语言/直观语法”的人在语言阵营。

我无法摆脱我的思维定势:认为古埃及人对象形文字的长寿有一些有用的说法…

## 分享这篇文章