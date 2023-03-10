# 我喜欢 ModalPopup

> 原文：<https://www.sitepoint.com/i-heart-the-modalpopup/>

如果你错过了， [Atlas 控制工具包](http://codeplex.com/Wiki/View.aspx?ProjectName=AtlasControlToolkit)的[九月发布](http://codeplex.com/Release/ProjectReleases.aspx?ProjectName=AtlasControlToolkit)。现在，[的新特性是动画支持](http://weblogs.asp.net/scottgu/archive/2006/09/19/September-Atlas-Control-Toolkit-Released-_2800_Now-with-Animation-Support_2900_.aspx)。我不得不说这很酷。不过话说回来，如果我想要动画，我会用 flash。但是工具箱中还有另一个我喜欢的控件:ModalPopup。

你问我为什么这么喜欢？主要是因为它允许开发人员创建复杂的确认对话框。或者制作一个没有实际弹出窗口的弹出添加项目表单。所有这些都不需要编写任何 JavaScript 代码。我不擅长 javascript。

然而，让 ModalPopup 回发到服务器端有一个很大的技巧，这是已发布的示例没有说明的——如何允许弹出面板触发回发。经过半天的努力，我发现答案实际上简单得令人不安:只要简单地忽略 OnOkScript 属性，确保您希望回发的任何按钮都不会被 ModalPopupExtender 处理。

开发人员需要注意的另一个问题是当 ModalPopup 出现时验证控件的行为:

*   如果验证程序在基础表单上，客户端验证程序将被忽略。该页的 IsValid 属性设置正确。这意味着，作为开发人员，您必须优雅地处理来自 ModalPopup 的提交的潜在无效性。我怀疑使用客户端脚本的人比我更狡猾，如果表单无效，他们可以找到阻止弹出窗口的方法。
*   如果验证器在弹出的控件上，那么客户端验证工作得很好。但是，没有必需的客户端脚本的 CustomValidators 将允许回发表单，并且回发后 ModalPopup 不可见。

尽情享受吧。

## 分享这篇文章