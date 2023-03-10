# 用 SnappySnippet 捕捉前端 UI 设计技巧

> 原文：<https://www.sitepoint.com/catching-frontend-ui-design-tricks-with-snappysnippet/>

![E.T. Elliot in science class](img/1adff9172bca46b8024ca68c2ab25bbc.png)

外星人:埃利奥特沉思青蛙的生活。

上周一晚上，我又看了一遍电影《外星人》，这是我长大后第一次看。虽然我可能戴着“儿童有色眼镜”，但我认为这很棒。史蒂文·斯皮尔伯格从几个骨瘦如柴的孩子和一个相当笨重的木偶那里得到了一船的故事。

有一个场景让我印象深刻，那就是学校生物课的场景。11 岁的主角埃利奥特(Elliot)和他的同学们每人都得到了一只活青蛙，他们要用氯仿麻醉并解剖它。

埃利奥特决定他不能解剖他的青蛙，并在科学课上启发了一群青蛙越狱。我怀疑 11 岁的自己可能也有同样的感觉。这似乎是对完美青蛙的浪费。

## 用 SnappySnippet 剖析 UI 设计

然而，当谈到网络时，我 ***喜欢*** 把事情扯开，看看它们是如何工作的。要理解前端组件、响应式设计、UI 动画和 SVG 是如何工作的，没有比深入了解更好的方法了。最近我一直在使用一种新工具来帮助我。

SnappySnippet 是一个 Chrome 开发工具扩展，旨在帮助你从网站中提取 UI 元素，并将其设置在你最喜欢的代码游戏中。

安装 SnappySnippet 后，你会在 Chrome Dev 工具面板中找到一个新标签。(见下文)。

### 使用 SnappySnippet

1.当你找到你感兴趣的 UI 部分时，右击它，启动 Chrome Inspector。

![Chrome Devtools - Snappy Snippet](img/2150517a938c1026a1f40a9941840954.png)

Chrome 开发工具–snappy snippet

2.在“源代码视图”面板中单击周围，确保已经选择了所有要提取的 UI 元素。

![snappysnippet](img/3b8c3bb4870123ea890c60423f2d874d.png)

SnappySnippet

3.接下来切换到“SnappySnippet”选项卡，并按下“从被检查的元素创建代码片段”按钮。几秒钟后，您应该看到浅灰色的 HTML 和 CSS 加载到按钮下的面板中。

4.最后，在 Codepen、jsFiddle 和 JS Bin 中选择一个作为代码片段的目的地。

![Codepen loaded](img/00149caf0fa7c0edf7aa7412065e012b.png)

这就是全部了。

#### SnappySnippet 能生成完美的、可投入生产的代码吗？

不，不太可能。SnappySnippet 似乎在 DOM 中寻找相关的连接，并尽最大努力为您选择的 HTML 重新编译新的 CSS 类。它并不完美，也不能编译 JavaScript 中的样式。

然而，我发现它通常能得到大部分重要的东西——当然足以让你开始调查/实验。这是一个很好的起点。

现在，很明显，这个想法不是简单地剽窃别人的作品。除了懒惰之外，复制和粘贴你还不理解的代码通常会给你带来更多的麻烦。

但也可以肯定的是，几乎所有伟大的前端/UI 编码设计者都从“解剖网络青蛙”中学到了很多技巧。

*最初发表于 [SitePoint 设计简讯](https://www.sitepoint.com/newsletter/)* 。

## 分享这篇文章