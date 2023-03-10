# 不要劫持我的浏览器

> 原文：<https://www.sitepoint.com/dont-hijack-my-browser/>

作为一个残疾人，我发现浏览大多数网站相当简单。我可以使用键盘或鼠标上下滚动来查看内容。仅用空格键，我就可以一次滚动页面一帧。

直到我的浏览器被页面上的元素劫持。然后我必须用我的鼠标来控制体验。

以下是网站劫持我的浏览器的四种令人沮丧的方式。

## 搜索栏

嘿谷歌(现在雅虎！)，我在跟你说话！

我知道你想让人们更容易地进行搜索，但是我的光标真的必须在页面加载后立即跳转到搜索框吗？！

您可以使用`tabindex`将光标移至选项卡上的方框，设置选项卡顺序。

`<input type="text" id="search" tabindex="1">`

我知道谷歌已经将这一点构建到他们的系统中，这是意料之中的行为，但对于像我这样用键盘导航的人来说，这是一个麻烦。

首先，它中断了浏览流程，因为我无法按键盘快捷键(在大多数浏览器上只能按退格键)返回页面。

第二，当我开始按空格键并想滚动页面时，我只是在搜索栏中填充。就是不行。

## 确认警告框

时不时地，我会在网上冲浪，最后到达一个有确认框的网站。有时这个框在到达时弹出，有时当我试图离开页面时弹出。

我对编程的了解足以识别插入恶意软件的机会。所以我从来不点确认。我不知道按下**ok**会关闭标签页还是带我去 russianhotties.xxx。

我的补救办法是关闭我的浏览器，然后重新打开它，并通过历史搜索我打开过的网站。

## 弹出广告

这个让我 ***疯狂*** ，我敢打赌大多数开发者都犯了这个错误。

吸引人的弹出广告不再令人烦恼，而是许多网站商业模式的一部分。我只能看着弹出窗口遮住我的内容。我明白了。公司必须创收。但是广告应该不会挡我的路。

几年前的标准是让弹出窗口超时。一些广告创造性地移动到静态广告的侧边栏。不管技术如何，这都是可用的功能。另一个以前的标准是将键盘快捷键如 **esc** 或 **x** 绑定到弹出菜单的关闭功能上。但是我很少看到一个弹出窗口，我不需要用鼠标点击一个动作项或者一个小的关闭链接。

## 静态导航条

最后，页面顶部有一个静态导航栏。有时这些有三四行高。它们很方便，让用户很容易获得网站信息。

但是回到滚动。

当我在一个典型的网页上点击**空格键**时，窗口向下滚动一帧。这是一种快速浏览页面的便捷方式。正如我上面指出的，劫持我的光标会碍事。但是那些花哨的导航栏也是如此。

*一些*网站解释了 navbar 添加填充以容纳顶部的栏。然而，许多流行的网站却对此视而不见。随着内容的滚动，有几行被便利栏遮住了。

## 我只是个坏脾气的人吗？

作为开发者，我们的生活已经充满了 UX 的需求。

另一个通融现在来自这个家伙，他只是想点击他的空格键滚动，而不是浮动到我的列表的顶部。

我明白了。但是想象一下，你在工作流程中建立的所有小调整是如何节省你的步骤，然后不得不取消它们，因为别人刚刚改变了做事的方式。例如，看看最近 Adobe 和 T2 谷歌关闭服务的决定。

对于有行动障碍的人来说，内容的附加障碍是一个真正的障碍。仅仅是将鼠标光标放在精确的位置上就可能是一件苦差事。

但是深思熟虑的设计和优雅的过渡可以把浏览器还给像我这样的用户，让他们享受我们便捷的网络体验。

## 分享这篇文章