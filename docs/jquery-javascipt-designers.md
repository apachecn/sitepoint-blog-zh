# jQuery:面向设计师的简单 JavaScript

> 原文：<https://www.sitepoint.com/jquery-javascipt-designers/>

如果 JavaScript 的重生是过去两年最大的主题，你可以将围绕这个话题的大部分讨论分成两个主要领域。

在城市的另一端，我们已经看到聪明人利用 JavaScript 用 Ajax 做各种令人惊奇的——有时甚至是荒谬的——事情。

然而，对于像我这样的前端人员来说，大部分脚本的嘶嘶声和气泡都集中在修改您的标记上——也就是说，使用 JavaScript 使您的标记在到达浏览器后工作得更好。Design View 时事通讯的长期读者可能会记得过去几年中我自己的一些实验:

*   在用 DOM 对图像进行样式化时，我们使用 JavaScript 给图像添加圆角。
*   在 DOM 文本阴影中，我们使用 JavaScript 在标题文本上建立一个阴影。
*   在横向规则中！好吧！，我们使用 JavaScript 来修复不可靠的`hr`元素。

尽管这些脚本的用途各不相同，但它们都涉及到向浏览器发送简洁的语义标记，并使用 JavaScript 来修复或扩展那些足够智能的浏览器的功能。在大多数情况下，这涉及到在更多的标记中“包装”部分标记。今天我们来看看一个简单、通用的方法，它可以让我们随时随地做到这一点:jQuery。

##### 那么，jQuery 是什么呢？

jQuery 是又一个 JavaScript 库，加入了已经拥挤的空间，包括 Prototype、Scriptaculous、Rico、Moo。Fx 等十几家。要使用它，只需在页面头部附加`.js`文件:神奇的是，你可以访问许多预建的功能和小发明。

问:为什么你可能想要另一个神秘的 JavaScript 库来处理呢？
**答:**jQuery 的主要吸引力在于它能在你使用它的前 10 分钟内为你提供什么。

不久前，我们花了一些时间来改进 [SitePoint 的市场](https://www.sitepoint.com/marketplace/)的运营方式。在寻找一种优雅的方式让卖家在不离开主拍卖页面的情况下显示大型截图、统计数据、图表和其他图像时，我遇到了[科迪·林德利的 Thickbox](http://codylindley.com/Javascript/257/thickbox-one-box-to-rule-them-all) ，它由[约翰·雷西格的 jQuery JavaScript 库](http://jquery.com/)提供支持。下图显示了 Thickbox 的运行情况。

![Cody's Thickbox in action](img/4274fb1dfe7497fa54ffd71ebf413b80.png)

在仅仅玩了 Thickbox 五分钟之后，您将开始看到它的潜力。在 Marketplace 中，我可以将链接的图片和完整的 HTML 文档拖到 Thickbox 窗口，同时调暗(但不会丢失)启动页面。使用 JavaScript 被禁用或不可用的浏览器的用户会被直接带到项目(即图像或页面)。对于“放大这个缩略图”的问题来说，这是一个非常聪明、有用和容易实现的解决方案。

然而，由于我们已经决定在页面中包含 jQuery 库(它很小，大约 10kB)，我认为找出它还能为我们做什么是个好主意。

一个小时后，我成为了一名 jQuery 转换者。

jQuery 的真正魅力在于它的简单性。单行的 jQuery 代码可以代替十几行普通的 JavaScript 代码，但它仍然非常简单和灵活。让我用一个例子来说明这一点。在我两年前的“水平规则修正器”中，我们使用了以下脚本:

```
function fancyRules() {  

  if (!document.getElementsByTagName) return;  

    var hr = document.getElementsByTagName("hr"); 

  for (var i=0; i<hr.length; i++) {  

    var newhr = hr[i];  

    var wrapdiv = document.createElement('div'); 

    wrapdiv.className = 'line';   

    newhr.parentNode.replaceChild(wrapdiv, newhr);   

    wrapdiv.appendChild(newhr);   

  }  

}  

window.onload = fancyRules;
```

作为这段代码的快速总结，浏览器在遍历 DOM 定位 hr 的每个出现之前等待页面完成加载。每次找到一个，它都创建一个新的`div`，给它一个类名“line”，将它插入到`hr`所在的位置，并将旧的`hr`放入新的`div`中，以实现实现这个特定效果所需的标记。抛开语义上的迂腐不谈，这个脚本的最终结果是我们能够在不改变数百个页面的情况下获得想要的结果。

当时，我认为对于 12 行代码来说，这是一个不错的结果。但是让我们看看如何使用 jQuery 获得相同的结果。

```
$(document).ready(function(){ 

  $("hr").wrap("<div class='line'></div>"); 

});
```

我不骗你。

分解一下(不是有太多东西要分解):

```
$(document).ready(function(){ 

  ... 

});
```

第一行和第三行是 jQuery 的 load 事件，它们从上面替换了旧的`window.onload`。我们希望在页面加载期间完成的任何任务都可以放在这些花括号中。

这是对旧的`onload`方法的一个巨大改进，因为 jQuery 的函数不是等到所有东西都加载完毕，而是监视所有进来的东西，一旦它拥有了所有需要的部分就开始工作。真的很工整。

值得注意的是，第二行甚至更简单:

```
 $("hr").wrap("<div class='line'></div>");
```

“美元对象”“`$("hr")`”是我们需要告诉 jQuery 抓取该页面上的每一条水平线的全部内容，而`wrap`是我们将对那些`hr`元素做的事情。

jQuery 的内置`wrap`函数接收我们给它的任何 HTML(在本例中是`"<div class='line'></div>"`)并将其包装在页面中的每个`hr`周围——不需要循环或测试。

我们在这里使用了一个`div`，但是我们也可以很容易地修改或包装一个`b`、span 或`a`元素。

虽然我们在这里使用了一个非常简单的选择规则(所有的`hr`),但是我们可以很容易地对我们的目标进行更加具体的描述。使用熟悉的旧 CSS 语法，我们可以使用以下任何一种:

*   `$("hr.separate")` —获取类名为`"separate "`的`hr`元素。
*   `$("li:only-child")` —获取单独的列表项目。
*   `$("ul > li")` —仅获取具有无序父列表的列表项目。

虽然我个人认为 wrap 是最有用的 jQuery 函数，但它只是众多函数中的一个，包括 hide、show、`fadeOut("slow")`和`slideUp("fast")`等等。你大概可以猜到这些函数的作用。jQuery 网站上的 [jQuery 入门教程](http://jquery.com/docs/Tutorial/)是一个相当温和的初学者指南，它会带你完成一些最常见的功能。

但是也许 jQuery 最简洁的特性是它将函数“链接”在一起的能力。换句话说，如果出于某种疯狂的原因，我想向我们的`hr`元素添加第二个`div`，我可以简单地在代码末尾添加另一个对 wrap 函数的调用，就像这样:

```
$("hr").wrap("<div></div>").wrap("<div></div>");
```

太简单了，太疯狂了。像狐狸一样疯狂！

Marketplace 的 Sell Your Site 部分为您提供了另一个例子，展示了这一点。

![Thumbnail images popping ou](img/4e36f365dda1e4dd1d943697df3e5ec6.png)

当我们开发这个页面时，我想在每个缩略图的底部添加一个小图标。这需要将每个`img`元素包装在一个容器`div`中，并将另一个显示图标的`div`放置在容器`div`中。

同样，jQuery 代码只有一行(我在这里把它分开，因为我们要处理的列宽有限)。

```
 $("#thumbnails li img") 

.wrap("<div class='wrap'></div>") 

.before("<div class='thumb'></div>");
```

简单地说，这段代码只是要求 jQuery:

*   在`li` lemon 中找到所有在`#thumbnails`中的图像。
*   将这些图像包装在一个名为`"wrap"`的`div`中。
*   在我的`"wrap"` `div`中，挤压另一个`div`(带有图标图形的那个)，就在我的图像之前。

现在我们已经有了结构，CSS 做剩下的事情。

当然，如果关闭了 JavaScript，缩略图会直接链接到原始图像文件，不需要图标。这就是我所说的优雅退化。

像大多数其他 JavaScript 库一样，jQuery 能够实现一些非常高端的功能(我们在上一篇文章中已经介绍了它的 Ajax 特性),但是对我来说最大的吸引力是它能够快速地解决小问题，而且不会引起太大的麻烦。

您可能已经知道，我已经是 jQuery 的忠实粉丝了。我希望你也会觉得有用。

当然，如果您正在扩展您的 JavaScript 视野，不要忘记升级到 Joe Hewitt 的 Firebug 扩展的最新版本，它现在是无可争议的 JavaScript 调试器之王。

*本文最初发表于设计视图#23。*

## 分享这篇文章