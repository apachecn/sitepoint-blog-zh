# 网站开发新手常犯的 10 个错误

> 原文：<https://www.sitepoint.com/top-10-web-development-mistakes/>

我对我最近的文章[新手网页设计师犯的 10 个常见错误](https://www.sitepoint.com/top-10-web-design-mistakes/)的反应感到惊讶。我预料会有一群愤怒的暴徒拿着干草叉和燃烧的火把聚集在 SitePoint 总部外面。我以为至少会有一些煽动性的评论。

是时候重新平衡并给 web 开发者一些批评了。他们会同样开明吗？他们愿意接受一点批评吗？显然，这篇文章不是针对*你* …你是一个已经使用了最佳实践技术的 SitePoint 读者。它列出了新的 web 开发人员所犯的典型错误——但我们都曾经是新手。有些观点可能也适用于那些敌对的合作开发者，他们拒绝接受任何认为他们的代码不完美的观点…

**1。忽略 web 标准**
Web 标准的发明是有原因的:它们帮助你创建独立于设备的网站和应用程序。很少有人想学习它们，不是每个人都喜欢它们，大多数开发人员不同意某些方面——但是忽略它们，后果自负！

开发新手会犯一些典型的错误，比如:

*   忘记或使用不适当的文档类型。我还是不明白为什么那么多开发者用一个过渡的 DOCTYPE——他们真的要加`font`标签和背景属性吗？
*   使用老式的 HTML，比如表格布局和`center`元素
*   不理解内联或块元素的微妙之处，例如将`h2`标题放在`span`中
*   没有验证他们的代码。或者更糟，使用一个验证器，然后忽略结果，认为验证器有内在的缺陷。

**2。可视化视觉效果**
你依赖所见即所得设计软件吗？你有没有避免学习 HTML？抱歉，您不是 web 开发人员。

所见即所得软件迫使你进行视觉思考——你将关注页面的外观而不是内容结构。使用文本编辑器，并在稍后阶段考虑表示层。

**3。语义模式**
下面是一个网络开发新手如何编写网页的主标题:

```
 <h1>Main Heading</h1> 
```

不幸的是，许多开发人员随后疯狂地使用他们新发现的 HTML 功能，并以这种畸形结束:

```
 <div class="mainheading" style="color:black;"><strong>Main Heading</strong></div> 
```

他们一开始就是对的。HTML 提供了您需要的大多数内容标签(尤其是如果您已经迁移到 HTML5)。适当地使用它们。

您还应该注意 divitis 和 classitis:开发新手对浏览器问题的解决方案。当你的 HTML 看起来像这样的时候，是时候停下来重新思考你的代码了…

```
 <div id="content">
	<div id="main-content" class="main">
		<div class="first-item">
			<div class="para1">
				<p id="top-para" class="para">Hello World!</p>
			</div>
		</div>
	</div>
</div> 
```

**4。练习描述性的类名**
描述性的类名是另一个大罪，例如

```
 <div id="blue-left-column-96px">...</div> 
```

这最初似乎是合乎逻辑的——它是自文档化的代码。这很好，直到您的设计发生变化，块变成 150 像素右对齐的红色列。

**5。懒惰浏览器测试**
*我要让你知道一个小秘密。*你知道开发者如何抱怨 IE6 吗？任何网站或应用程序都可以在微软古老的浏览器中运行。当然，这需要额外的时间，浏览器也有缺陷，但它们都有据可查。为 IE6 修复一个站点通常比 IE7 更容易。

然而，将 IE6 测试留到项目结束会导致无尽的悲伤。修复浏览器问题一点也不好玩，这才是你的开发者真正抱怨的。

当然，不仅仅是 IE6——所有的浏览器都有缺陷、怪癖和问题。如果您尽早测试并经常测试，大多数问题都可以在开发阶段得到纠正。不幸的是，懒惰的新手开发者会:

*   在整个开发过程中，在一个浏览器中进行测试
*   花更多的时间说服人们其他浏览器是垃圾，而不是修复他们的应用程序
*   而且，如果他们没有其他选择，他们会使用浏览器嗅探和黑客来实现可怕的快速修复。

**6。不注意可移植性**
开发新手会使用固定的文件路径、硬编码的数据库连接字符串，并对环境做出假设。如果应用程序没有放在运行 PHP 并启用 register_globals 的 IIS web 服务器的根目录下名为“MyApplication1”的文件夹中，它将会失败。哦，是的，它连接到一个运行在他们电脑上的数据库。

理想的 web 应用程序应该是免维护的:

*   它应该在文件所在的任何地方运行
*   配置参数应该包含在一个易于编辑的文件中
*   如果合适的话，它应该可以在不同的操作系统和 web 服务器组合上工作。

顺便说一下，要小心会话。当您的流行应用程序迁移到多服务器托管平台时，他们会发现您的问题。

**7。刷掉带宽**
在本地 PC 服务器上测试的问题是，带宽问题通常不明显，你的 4MB 背景图像会立即出现。包括 27 个不同的 JavaScript 库——万一你需要它们——也不会导致任何延迟。

开发新手不关心带宽。事实上，我有点担心我们最近的 SitePoint 民意调查显示 23%的开发者不关心带宽。我敢肯定他们一定是内部网开发人员…

**8。糟糕的可访问性**
对于开发新手来说，可访问性==图片 alt 属性。这就像假设你会开车，因为你成功地调好了汽车音响。

可访问性是指支持具有不同技术能力的多种设备，例如移动电话、屏幕阅读器、没有 JavaScript 的浏览器、缺少 Flash 插件或没有鼠标的系统。这并不总是优先考虑的事情——没有人会在屏幕阅读器中使用在线视频编辑器——但是对于基于内容的网站来说几乎没有借口。例如，如果您的页面小部件、表单或链接依赖于 JavaScript，您只是阻挡了一部分受众。

**9。鄙视 SEO**
我把这部分归咎于搜索引擎优化行业。SEO 是精神分析、技术复杂性和神秘的黑艺术的混合体。企业为 SEO 巫术支付了高昂的费用——他们不知道他们支付了什么，做了什么，或者有任何成功的保证。

因此，开发新手认为 SEO 是一种空洞的软技能，最好在网站上线后留给营销部门。那太晚了——SEO 应该从一开始就计划好，就像项目的其他方面一样。

我现在要揭示另一个行业秘密:*大多数 SEO 技术都非常明显*。(那是一千个搜索引擎优化销售顾问倒下的声音吗？)

如果你在卖绿色的杜莓，一定要提到它——尤其是在标题和网址中。坚持标准，做一些关键词研究，制作像样的内容，添加一个网站地图，将网站提交给搜索引擎，你就完成了 90%。忘记关键字填充和其他技巧:他们不会工作。

10。无用的升级
为什么网站和服务会因为*【基本维护】*而消失几个小时？当硬件出现故障或者您成为拒绝服务攻击的受害者时，我可以理解，但是正常的维护任务不应该需要超过几分钟的停机时间。所有文件和数据都可以在瞬间上传、测试和转换。

此外，如果你有相同的内容，你的用户应该永远不会看到 404 页面。URL 可能会改变，但很容易将旧地址重定向到新位置。

**11。奖励失误**
这里有一组不在我主要名单上的言论:

*   很少了解客户机-服务器方法。当桌面开发人员开始为 web 编码时，这一点尤其明显——我认识一个人，他编写了一个一次只能被一个网站访问者使用的组件！
*   引用统计数据作为在一些浏览器中忽略或不测试的理由
*   重要的时候不使用源代码管理
*   不断重写和重构相同的代码
*   花几个小时争论为什么 X 技术比 Y 技术好，而不是继续完成任务
*   开发人员总是无法解决真正的问题，因此他们可以专注于更有趣的琐碎问题
*   可悲的是低估了开发时间
*   在同一 HTML 页面上使用重复的 ID 名称
*   使用表单重置按钮——什么时候有人需要过？
*   依靠臃肿的库，而几行 JavaScript 就足够了
*   不验证用户输入或假设只有客户端验证就足够了
*   使用 JavaScript 来应用 CSS 能更好处理的效果
*   与其他 JavaScript 代码不兼容
*   Ajax 的过度使用或不当使用
*   为每个浏览器创建单独的 CSS 文件(不仅仅是 IE6/7 条件注释)
*   到处添加内联样式
*   闪光灯使用不当
*   当 HTML 文本和 CSS 效果可以达到同样的效果时，使用图像
*   忘记为`body`添加背景颜色
*   用含糊的技术术语欺骗客户和同事。

我错过了你最讨厌的网络开发新手的错误了吗？你不同意任何观点吗？让反弹开始吧！…

## 分享这篇文章