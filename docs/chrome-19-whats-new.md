# Chrome 19 有什么新功能

> 原文：<https://www.sitepoint.com/chrome-19-whats-new/>

我怀疑你注意到了，但 Chrome 19 是本周发布的。我很少提到 Chrome 的更新，因为它们很少值得一提。然而，我在最新的浏览器中发现了一些隐藏的宝石…

## 标签同步

最大的新功能是标签同步。如果你在两台或多台电脑/智能手机上使用 Chrome，你*应该*在新标签页上看到一个*“其他设备”*链接。点击它，你可以打开同步链接。我说*“应该”*是因为我还没能让它工作起来。

标签同步在 Firefox 中已经出现了一段时间，所以我很惊讶它花了这么长时间才出现在 Chrome 中。希望你会比我运气好。

## CSS3 calc()支持

webkit 团队最终实现了我最喜欢的 CSS3 特性之一:鲜为人知的 [calc()函数](https://www.sitepoint.com/css3-calc-function/)。它允许您定义计算尺寸，例如

```
 #myelement { width: calc(50% - 2em + 4px); } 
```

Chrome 支持带有-webkit 前缀的 calc()。火狐用的是-moz，IE9 不用前缀也很开心。为了有效地使用它，您需要回退代码，例如:

```
 #myelement
{
	width: 46%;
	width: -webkit-calc(50% - 2em + 4px);
	width: -moz-calc(50% - 2em + 4px);
	width: -o-calc(50% - 2em + 4px);
	width: calc(50% - 2em + 4px);
} 
```

## 组合设置页面

选择“工具”>“设置”现在会显示一个带有历史记录、扩展、设置和帮助的侧菜单。帮助页面提供了几个链接和通常出现在“关于”对话框*(会很快消失吗？)*

## 新的 JavaScript/ECMAScript 5.1(和谐)特性

许多实验性的 JavaScript 特性已经从 Harmony 规范进入了 Chrome 的 V8 引擎。然而，它们在默认情况下是不可用的——你需要在 [chrome://flags](//flags) 中“启用实验 JavaScript”。像集合和代理这样的语言结构看起来很棒，但是要在所有浏览器中都可用还需要一段时间。

## 安全性和错误修复

Chrome 19 修复了 21 个问题，谷歌向鹰眼安全黑客支付了近 1.5 万美元。

Chrome 一直保持快速稳定。看起来它将在 2012 年夏天把 IE 从浏览器使用排行榜的榜首挤下来。我不相信任何其他厂商能够阻止谷歌对网络和我们用来访问网络的软件的统治。

## 分享这篇文章