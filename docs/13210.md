# Firefox 渲染错误在创纪录的时间内得到修复

> 原文：<https://www.sitepoint.com/firefox-rendering-bug-fixed-in-record-time/>

上周一，当 Mozilla 发布火狐 2.0.0.10 时，[发布说明](http://en-us.www.mozilla.com/en-US/firefox/2.0.0.10/releasenotes/)称这是一个相对较小的更新，像往常一样，修正了少量的安全问题。然而，事实证明，对于那些网站依赖于`Canvas.drawImage`的开发者来说，这个版本包含了一个令人讨厌的惊喜，这是最近浏览器的一个 JavaScript 功能，允许开发者显示带有旋转和阴影等效果的图像。

火狐 2.0.0.10 完全打破了这一功能，导致依赖它的网站上的图片消失。像 [instant.js](http://www.netzgesta.de/instant/) 这样的 JavaScript 效果库突然停止工作，开发者没有办法解决这个问题，因为，就 Firefox 而言，一切都运行得很完美。

一份[错误报告](https://bugzilla.mozilla.org/show_bug.cgi?id=405584)很快被归档，无助的开发人员开始报告。Klaus Reimer 写道:“顾客们在抱怨，因为他们的 Firefox 自动更新到了 2.0.0.10，现在他们再也不能在我们的商店订购照片了。”他强调了这种错误在现实世界中可能有多严重。

Mozilla 开发人员行动迅速，在最初的错误报告后 16 个小时就能生产出浏览器的修复版本。随后，发布团队接管并以创纪录的时间推出了火狐 2.0.0.11。“这将是迄今为止 Firefox 版本之间最快的转变，”[在新版本发布前写道](https://bugzilla.mozilla.org/show_bug.cgi?id=405584#c27) Firefox 开发者 Nick Thomas。

随着火狐 2.0.0.11 现在普遍可用，Mozilla 正在审查这个 bug 被允许公开发布的情况。自动化回归测试已经到位，以防止这个*特有的*错误再次出现，当然，其他步骤也正在采取。Mozilla 开发人员 Marcia Knous 回应了 web 开发人员收到即将发布的产品的早期通知的请求，宣布了一个新的 [Betatesters 邮件列表](https://mail.mozilla.org/listinfo/betatesters)，供有兴趣在 Firefox 和 Thunderbird 新版本上线之前测试它们的开发人员使用。

总结这一集，GMP Worldwide 的故事片《VFX》的工具设计师 Jonathan Flack 发布了他的想法:

> [……]在我们的书中，对此的反应绝对是一流的。作为开发人员，我们认识到有时你肯定会引入这样的错误。任何声称他们公司在程序上不受这种事情影响的人都完全是妄想。
> 
> 在我们的书中，这是一个很好的例子，说明了为什么这种开源开发是有效的。我从来没有想到过一个封闭源代码的供应商会在+/- 48 小时内发布一个重要的补丁。

## 分享这篇文章