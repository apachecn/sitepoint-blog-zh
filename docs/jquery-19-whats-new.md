# jQuery 1.9 的新特性

> 原文：<https://www.sitepoint.com/jquery-19-whats-new/>

[jQuery 1.9](http://jquery.com/) 于 2013 年 1 月 15 日发布。这个版本标志着 jQuery 发展过程中的一个重要里程碑:

*   在为 2.0 版做准备时，已经删除了几个不推荐使用的功能，以提供一个更精简、更干净的库
*   它将是最后一个支持 IE6，7 和 8 的(下面会有更多关于它的内容)。

不要急着下载它——有许多迁移问题需要考虑。1.9 变化的长列表意味着很少有网站能够毫发无损。

## jQuery 迁移插件

jQuery 迁移插件应该可以缓解你的升级困难。该插件提供了两个基本功能:

1.  它重新启用了不推荐使用的特性，因此您的 v1.8 兼容代码将再次工作，并且
2.  当使用不推荐使用的功能时，它会向开发人员控制台记录警告。您应该会发现解决问题更容易。

迁移插件应该在 jQuery 之后立即加载，例如

```
<script src="https://code.jquery.com/jquery-1.9.0.js"></script>
<script src="https://code.jquery.com/jquery-migrate-1.0.0.js"></script> 
```

也可以用在(如果？)你升级到 2.0 版本。

## 移除的功能

这不应该被认为是一个明确的删除列表，但是它们最有可能导致兼容性问题。一如既往，全面测试是您的最佳选择…

*   **jQuery.browser()** —已移除
*   **。live()** 事件—使用[。【T3 上()】相反](http://api.jquery.com/on/)
*   **。die()** —使用[。](http://api.jquery.com/off/)改为 off()
*   **。andSelf()** —使用[。addBack()](http://api.jquery.com/addBack/) 相反
*   **。add()** —节点现在按文档顺序返回，未连接的节点(不在文档中的节点)在最后。所有包含断开节点的集合都遵循这种行为
*   **。在()，，之后。之前()，。replaceWith()** —现在返回未修改的 jQuery 集
*   **。appendTo，。插入之前，。插入后，。replaceAll** —如果目标选择器没有选择任何元素，例如`$(elements).appendTo("#not_found"))`，则结果集现在为空
*   **Ajax 事件必须附加到文档**——不是 DOM 节点，即`$(document).ajaxStart(...);`而不是`$("#node").ajaxStart(...);`
*   **单选/复选框点击事件** —现在返回选中状态，而不是之前的状态。未调用 preventDefault()
*   **焦点事件的顺序** —前一个元素上的模糊事件现在在新元素上的焦点事件之前触发
*   **jQuery(htmlString)** —如果 htmlString 以“<”字符开头，则它仅被视为 HTML(而不是选择器)
*   **。attr()** —你通常应该使用[。道具()](http://api.jquery.com/prop/)
*   **“悬停”伪事件** —“悬停”不再作为“鼠标输入鼠标离开”的同义词
*   **jQuery.ajax 返回一个空的 JSON 结果**——这现在被认为是格式错误的 JSON 并抛出一个错误

## 新功能

除了精简和错误修复，新功能相对较少…

**。css()多属性 getter**
现在可以向。css()方法。它返回一个带有当前值的对象，例如

```
var dims = $("#box").css([ "width", "height", "backgroundColor" ]);
// { width: "10px", height: "20px", backgroundColor: "#D00DAD" } 
```

**CSS3 选择器支持**
Sizzle 选择器引擎在所有浏览器中支持以下 CSS3 选择器:`:nth-last-child`、`:nth-of-type`、`:nth-last-of-type`、`:first-of-type`、`:last-of-type`、`:only-of-type`、`:target`、`:root`和`:lang`。

**。**完成()方法
了。finish()方法停止所有排队的动画并将元素置于其最终状态。在以前的版本中，可以使用。停止()和。clearQueue()，但是。finish()更容易使用。

**源地图支持**
源地图允许你调试使用简化脚本或 CSS 的生产站点。本质上，浏览器的调试器将压缩文件中的行映射到未压缩的源文件，这样更容易查看代码、设置断点、更改值等。*请关注 SitePoint 上的更多文章……*

## 放弃遗留 ie

“老掉牙”的决定有[种不同意见](https://www.sitepoint.com/jquery-2-support-ie6-ie7-ie8/)；一些开发者认为这为时过早，而其他人认为任何有助于 IE6、7 和 8 消亡的想法都是好事。我有一些担心:

*   StatCounter 估计 IE6/7/8 的使用率约为 13% ， [NetMarketShare 估计为 32%](http://www.netmarketshare.com/browser-market-share.aspx?qprid=2&qpcustomd=0&qptimeframe=M) 。无论您相信什么，遗留 ie 并没有死亡，也不太可能在 jQuery 2.0 到来时被埋葬。
*   jQuery 团队推荐使用条件注释来加载 jQuery 1.9 或 2.0，这取决于用户的设备。这是浏览器嗅探——一种不应该在 20 世纪 90 年代末出现的做法！
*   这会引起混乱。不管发出多少警告，一些开发人员会在不了解向后兼容风险的情况下安装 jQuery 2.0。
*   jQuery 2.0 将拥有与 jQuery 1.9 相同的 API(去掉了旧代码)。分叉代码库将不可避免地带来后果——尤其是随着 v2.0 系列的发展。你将需要更彻底的浏览器测试，你将如何处理一个版本出现的问题，而另一个版本没有？
*   尽管仍有工作要做，jQuery 2.0 beta 比 1.9 小 10%。让我们假设团队节省了两倍的钱；最初的 jQuery 下载将减少 6Kb。这是否会显著加快浏览器的响应速度？我表示怀疑。
*   jQuery 团队会发现 v2.0 开发更容易，但是对 jQuery 用户有什么好处呢？这个库不一定会执行得更快——它已经运行了可用的本地浏览器 API。IE6/7/8 中不能实现哪些核心特性？
*   浏览器兼容性是 jQuery 最大的优势之一。如果没有 IE6/7/8 的支持，就没有理由使用这个库。原始 JavaScript 速度更快，并且在其他浏览器中基本一致。
*   任何 JavaScript 库的主要目标都是推动 web 前进吗？或者应该是为了帮助当前浏览器的开发，而不考虑人们的看法？

jQuery 必须发展，这包括支持新的浏览器和放弃不使用的浏览器。放弃 IE6 和 IE7 是有道理的，但 IE8 是 XP 支持的最新版本，很明显许多 Windows 用户对升级无动于衷。就个人而言，我建议在任何需要 IE6/7/8 支持的网站上坚持使用 jQuery 1.9。两种代码库的风险大于好处。

## 是时候升级了？

那些更容易紧张的人可能更喜欢等待几个星期来解决任何问题。您还应该注意到，jQuery 1.9 不太可能是简单的文件替换，旧代码可能需要调整。也就是说，如果您遇到问题，Migrate 插件提供了一个很好的临时解决方案。

尽管对其未来有一些保留，jQuery 仍然是我和大多数 web 开发人员的首选 JavaScript 库。

## 分享这篇文章