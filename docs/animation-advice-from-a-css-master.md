# CSS 大师的动画建议

> 原文：<https://www.sitepoint.com/animation-advice-from-a-css-master/>

**就在一周前**我们有幸请到了[蒂芙尼·布朗](https://www.sitepoint.com/premium/users/tiffanybrown)加入我们的 [SitePoint 论坛](https://community.sitepoint.com)为她在 CSS 动画上的[现场提问& A](https://community.sitepoint.com/t/live-q-a-tiffany-brown-on-css-animations-transitions-and-transforms-oct-1-at-2pm-pdt/202522) 。

我认为公平地说，超过 2200 的浏览量相当成功！如果你错过了它，我已经从事件中为你的阅读乐趣编辑了集锦。

> 蒂芙尼是洛杉矶的一名自由开发人员。她是 [CSS 大师](https://www.sitepoint.com/premium/books/css-master)的作者，也是 [Jump Start HTML5](https://www.sitepoint.com/premium/books/jump-start-html5) 的合著者。在成为自由开发人员之前，她是 Opera 开发人员关系团队的一员，并在亚特兰大的一些小机构工作过。Tiffany 现在通过她写的书、[博客](http://tiffanybbrown.com/)和文章来教育世界 web 开发。

## 入门指南

### 问:我是一所社区大学的讲师。我被要求为网站开发研究和设计一个网站开发 AAS(两年制学位)。可供选择的东西太多了，以至于我在安排课程时都不知所措。对学生学习 Web 开发有什么建议吗？我想:

*   HTML/CSS
*   JavaScript/jQuery
*   结构化查询语言

### 主要问题是用哪些工具，框架，CMS 等？

Kelle(员工):你好，Suzy，我只能在事情的前端提供建议，但我认为基本的 HTML 和 CSS 是一个很好的起点。对于 JavaScript，我认为最好先学习普通的 JavaScript，然后介绍库，这样学生就能理解语言特性和库功能之间的区别。

同样有帮助的是将他们引向正确的资源，例如， [MDN](https://developer.mozilla.org/en-US/docs/Web) 是一个很好的 API 参考，我希望在我开始的时候就拥有它。

蒂芙尼:我认为这是一条很好的道路。HTML 和 CSS。我会说教 JavaScript 基础知识而不是 jQuery(或者把 jQuery 作为 JavaScript 的一部分来教)。SQL 仍然被广泛使用，所以这是一个很好的补充。我还想添加一种服务器端编程语言:

1.  Ruby 及其 Rails 框架，
2.  Python 和 Django，和/或
3.  PHP+Laravel 框架。

教授基本的服务器配置也很有帮助:Nginx 或 Apache。将版本管理工具引入课程，最好是作为其他课程的一部分。

### **问:**非常感谢。香草 JavaScript 是一个奇妙的想法。你觉得角、烬、脊梁怎么样？

凯勒(员工): [余烬](http://emberjs.com/)和[骨干](http://backbonejs.org/)很棘手，因为在我看来，他们需要相当不错的 JS 背景才能很好地使用它们。

Angular 对于初学者来说非常好，尤其是如果他们已经花时间提前理解了语言的基础。Angular 的一个好处(或者不好，取决于你的观点，但有利于学习)是，它捆绑了一堆有用的东西，可以让人们轻松了解现代前端开发中一些更重要的想法。

在 [SitePoint](https://www.sitepoint.com) 我们几乎只使用 [React](https://facebook.github.io/react/) ，所以我对视图框架颇有偏见；然而，这绝对是一个更陡峭的学习曲线！

### **嘉宾:**奇思妙想。我非常感激。感谢您的回复。有没有版本管理工具推荐？

蒂芙尼:我会推荐[吉特](https://git-scm.com/)或者[水星](https://www.mercurial-scm.org/)。SVN 是另一个选择，但不是一个令人愉快的选择。有些服务提供其中一项或多项，学生有时可以获得免费计划。在 Google/DuckDuckGo 上搜索*托管的 Git* 或*托管的 Mercurial* ，你会找到一堆。

### 问:这是一个你总是想深入研究但却没有时间去做的话题。我总是发现动画有点难学，所以我总是放弃。但是嘿！也许我的印象是错误的，他们毕竟没有那么狡猾。CSS 动画已经有了很大的改进，但我看到的大多数动画仍然包含一些 JS(大多数时候是 jQuery)，所以在我看来，如果你想学习一个，你必须学习另一个。

**蒂芙尼:**你完全可以独立于 JavaScript 编写 CSS 动画或过渡。动画和过渡可以由`:hover`、`:focus`或`:checked`状态触发。然而，在许多情况下，您需要添加或删除一个类名来触发动画/过渡。所以，是的，你至少需要学习一点 JavaScript(但只是一点点)。

## 图书馆推荐

### **问:**有哪些 CSS 动画库推荐？我用的是 [Animate.css](https://github.com/daneden/animate.css/)

蒂芙尼: [Animate.css](https://github.com/daneden/animate.css/) 是最著名的，也是走得最远的，所以这是我的推荐。不过我一直在关注[effect . CSS](http://h5bp.github.io/Effeckt.css/)。这是 HTML5 样板项目的一部分。然而在我的项目中，过渡通常已经足够了。

### **问:**嗨，你推荐用 [Hover.css](http://ianlunn.github.io/Hover/) 吗？或者为什么不呢？

**Tiffany:**[hover . CSS](http://ianlunn.github.io/Hover/)是一个库，我还没有仔细看过它，所以还没有对它发表意见。每一项都自成体系就不错了。它可以让你挑选要使用的部分，而且它还有一些聪明的动画。我既不赞成也不反对。

## 动画表演

### 问:你会推荐在台式电脑或手机上使用动画吗？如果是移动设备，需要注意哪些重大问题？

蒂芙尼:你可以在任何装有支持动画的浏览器的设备上使用动画。如果设备有一个较慢的处理器，你可能会遇到一些问题。但这是(希望)你会在测试中发现的。

现在，如果你的观众包括很多 Opera Mini 的用户，请确保你的动画和过渡使用了后备。Opera Mini 不支持任何类型的过渡或动画，无论是 CSS 还是 JavaScript。

### **问:**当我们使用动画时，你的性能建议/顾虑是什么？什么要慎用，什么要避免？

蒂芙尼:

> 尽可能避免回流

当对象的尺寸或位置发生变化时，就会发生重排。所以不用动画显示顶部、右侧、底部和左侧；使用`transform`和`translate`功能。边距和边框是可能导致重排的其他属性。我没有测试过这个，但是我怀疑制作`outline`动画可能是解决这个问题的好方法。

动画显示对象的宽度和高度属性(包括其中的最小/最大属性)也可以触发重排。不过，有时候你不得不这样做；使用变换:`scaleX()`或变换:`scaleY()` *难道*不是宽度和高度的一个很好的替代品吗？

### 问:我想知道在动画中使用元素是否有实际的限制、大小或数量。

然而，和任何与网络相关的东西一样，你影响的元素越多，你的页面就可能变得越慢。但我不知道有任何基于浏览器的硬性限制，限制一次可以动画显示多少元素。然而，对于癫痫或前庭障碍患者来说，存在一个无障碍问题。你要小心一次激活太多的项目。您还需要注意不要创建占据整个屏幕的动画。WCAG2.0 为癫痫患者提供了一些[指南](https://www.w3.org/TR/UNDERSTANDING-WCAG20/seizure.html)。

### 问:就我目前所见，动画、过渡和变换是用来“增强”的，我还没有注意到如果它们因为任何原因中断，UX 会受到影响。我假设同样的规则适用于“不要使用没有某种形式的替代文本的图像来传达相同的信息”。

### 你知道这方面有什么潜在的问题吗？

**蒂芙尼:**是的！与 CSS 动画相比，转场的工作方式有很大的不同。如果用户的浏览器不支持 CSS 动画，动画将完全失败。您将需要使用 JavaScript 回退。另一方面，转换只是在开始和结束状态之间跳跃。浏览器将忽略与过渡相关的属性。这不是一个漂亮的开关，但 UX 不会打破。

过渡和动画也需要可以被[插值](https://en.wikipedia.org/wiki/Interpolation)的值。你可能遇到过动画到或从高度:自动的情况，并发现它不起作用。这是因为属性的起始值和结束值需要是数字，这样浏览器就可以计算出中间的值。
我还建议你不要将 Ajax 表单提交这样的关键任务动作绑定到`transitionEnd`或`animationEnd`事件上。用户交互*可以*阻止这些事件被触发。

### **问:**什么样的动画用 JavaScript 表现比较好？

### 问:我也有同样的兴趣。接受了“CSS 是为了风格”的观点后，当我第一次看到 CSS 做我通常使用 JavaScript 做的事情时，这似乎是错误的。现在我想知道**新的 CSS 是否更适合“不懂 JavaScript 的人”，或者是否有真正的优势？**

蒂芙尼:这是我知识的上限。我觉得要看浏览器了。据我所知，这是一场平局。无论哪种方式，位置、尺寸或余量操作都相当慢。

> ***JavaScript***的缺点是 JavaScript 动画和你所有其他的 JS 发生在同一个线程上。您可能会影响 UI 的性能和响应能力。

通过使用 CSS，您可以释放 JavaScript 引擎。在某些浏览器中，您将获得 GPU 处理的优势。

现在有了 SVG，您并不总是有选择。你*应该*用 [SMIL](https://www.w3.org/TR/REC-smil/) ，但是 IE 或者 Edge 都不支持 SMIL，Chrome 也不赞成用。所以如果你想制作一条路径的动画，使用 JavaScript 会更容易。

**Paul(嘉宾):**我相信 CSS 动画比 JS 动画流畅多了，尤其是 jQuery 动画很跳跃。当然，在某些情况下，你可能需要一些 JS 来触发 CSS 效果。

### 问:你说 jQuery 动画很跳跃，但是使用像 [velocity.js](http://julian.com/research/velocity/) vs CSS 动画怎么样？velocity.js 上的信息说，它在移动设备上的表现比 CSS 动画更好。

**Paul (Guest):** 是的，我在手机上使用 velocity.js 制作动画菜单，它比 jQuery 流畅得多。我想这取决于你在做什么，但是有时候你在 JS 中需要做的就是给元素添加一个类，让 CSS 给它制作动画，而不是让 JS 也给元素制作动画。(提醒你一下，反正我在 JS 方面也相当垃圾。)

**蒂芙尼:**通过使用 CSS 制作动画和过渡，你将这些任务从 JavaScript 线程转移到图形处理线程。当使用 JavaScript 制作动画时，您会冒其他 JS 操作被延迟的风险，直到动画完成。使用 CSS，页面的 JavaScript 部分仍然可用。如果您想检测动画/过渡何时完成，您可以使用`transitionEnd`或`animationEnd`事件异步完成。

我欣赏使用 CSS 动画的另一点是，你可以将它们与媒体查询结合起来。一个物体可能在一个宽屏幕上水平滑动，但在小屏幕(或其他东西)上翻转。如果用户阻止了 JavaScript，动画仍然可以工作(当然，除了事件处理)。

## 动画提示

### **问:**嗨@webinista！我想知道你是否有任何技巧或规则来制作流畅的表演动画？有什么是我们*应该*远离动画的吗？

**蒂芙尼:**是的！我最近也遇到了这个问题。请注意动画属性会改变页面上对象的尺寸或位置(也称为引起重排)。这可能特别慢，取决于浏览器。

1.  `top`
2.  `left`
3.  `right`
4.  `bottom`
5.  `margin-*`

而是使用`transform`属性和`translate()`、`translateX()`和`translateY()`函数。@PaulOB 很好的回答了这个问题。

我还遇到了关于`filter`属性和`blur()`函数的问题。高斯模糊的计算有点昂贵。所以我建议现在避免动画/过渡。

**保罗(嘉宾):**使用[平移来移动东西](http://www.paulirish.com/2012/why-moving-elements-with-translate-is-better-than-posabs-topleft/)而不是绝对定位，因为这样更有效。

### **问:**关于动画或过渡，你认为最有用或最棘手的技巧是什么？例如我的是关于`animation-delay`的——如果我想让多个动画从页面加载开始时开始，我会给负值，这样我就可以保持它们之间的延迟。谢谢！

蒂芙尼:哦。这个不错。

> 在写这本书的时候，我钻研了一下`cubic-bezier`函数。**它对我来说超级有趣**，因为它涉及到一个我又爱又怕的数学层次**。**

 **我希望我能马上总结出它是如何工作的。但简而言之，当第二个和第四个值大于/小于 1 时，会产生“过冲”或“后退”效应。你可以在这里明白我的意思[。](http://cubic-bezier.com/#.45,.48,.67,1.49)

## 网站上的动画

### **问:**现在，我看到很多人在谈论动画及其可能性，但除了那些看起来令人印象深刻的测试作品，[克里斯·甘农的熔岩灯](http://codepen.io/chrisgannon/post/how-to-make-an-svg-lava-lamp)就是一个例子。我不完全确定它们在网页设计词典中的位置。你预计它们将如何被用在商业网站上(例如),你有任何可以指出的例子吗？

蒂芙尼:我们开始在商业网站上看到动画和过渡。 [MailChimp](http://mailchimp.com/) 有一个我最喜欢的例子。在你安排了一封电子邮件之后，会有一个动画 SVG，显示一只黑猩猩举起“金属手指”鼓励你“继续摇滚”不幸的是，它只对登录的客户可见。

我确实认为你会看到动画——我包括过渡——用来添加那种奇思妙想。你可以做巧妙的加载动画和悬停效果。比方说，通过动画显示`clip-path`属性来显示悬停时的整个对象。麦当劳可能会将他们的拱门标志做成动画。我想你还会看到结合了 SVG 的动画图表。

## 这是一个总结！

智慧之语怎么样？Tiffany 在论坛上花了额外的 1.5 小时和我们讨论 CSS 动画。如果你只是在这里阅读这个讨论，那么我可以肯定你错过了这个活动。然而，没有必要觉得你错过了，因为你仍然可以[阅读整个讨论](https://community.sitepoint.com/t/live-q-a-tiffany-brown-on-css-animations-transitions-and-transforms-oct-1-at-2pm-pdt/202522)并继续参与。更好的是，开始你自己的[论坛讨论](https://community.sitepoint.com/)。

## 分享这篇文章**