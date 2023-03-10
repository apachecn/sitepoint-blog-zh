# 用 Anime.js 制作 DOM 动画

> 原文：<https://www.sitepoint.com/animating-the-dom-with-anime-js/>

![Anime.js Animation Library](img/4eb7e150444a09aac3b9590170cc8e15.png)

如果你正在寻找一个快速和轻量级的动画库，朱利安·卡尼尔的 Anime.js 是你想要考虑的一个选择。

这是关于动态 DOM 动画工具(包括 SVG 图形)的系列文章的第一篇。我不打算包括专门的 canvas-WebGL 动画库或特定于 SVG 的动画库。但是，如果一个库非常擅长在网页上制作 HTML 元素的动画(理想情况下还有 SVG 图形)，我会考虑将它包含在本系列中。

我无意剖析每个库并深入探究其代码。相反，我将从一个主要从事 CSS 工作的人的角度来介绍每个库，但是当涉及到制作 DOM 动画时，我想探索 JavaScript 提供了什么。因此，我将寻找一种工具，它能很好地操纵 DOM，通过 CSS 开发人员熟悉的语法动态地添加和删除 CSS 样式和/或类，以实现动画的目的。

## 网络动画

正如莎拉·德拉斯纳解释的那样，当谈到网页动画时，一个有用的区别是 T2 的用户界面/用户体验动画和 T4 的独立动画。

[研究](https://smartech.gatech.edu/bitstream/handle/1853/3627/93-17.pdf)展示了人类的感知是如何在运动图像的基础上更好地理解世界的。这意味着，当信息以一个移动的场景呈现在我们面前，而不是以一幅图像甚至一系列静态图像的形式呈现时，我们可以更好地理解和吸收信息。

UI/UX 动画用于帮助用户浏览网站、吸收网页上的信息或执行特定任务，例如，注册时事通讯、向购物车添加商品等。

相比之下，独立动画…

> 用于说明页面主体中的概念，可以在旁边，也可以单独说明。
> 
> 莎拉·德拉斯纳— [CSS 会议幻灯片](http://slides.com/sdrasner/cssconf#/6)

这里有一个由莎拉制作的惊人的 [CodePen 演示](http://codepen.io/sdras/full/jEjRwo)来说明这一点。

这种区别很重要，因为当你制作用户界面动画时，你的目标是帮助用户完成那些有助于实现网站目标的任务。相反，当创作一部独立的动画时，你着手**尽可能充分地表达某个概念**。

也就是说，这两种动画都是供人欣赏的。这就是为什么在创作你的动画时，像**性能**和**可访问性**这样的东西会使一部动画成为令人愉快的，或者一部令[用户烦恼甚至伤害](http://alistapart.com/article/designing-safer-web-animation-for-motion-sensitivity)用户的。

## 为什么要用 JavaScript 做动画？

今天，CSS 提供了过渡和动画来增加网页的动感。然而，当谈到网络动画时，JavaScript 仍然是一个大玩家。为什么会这样呢？我们终于可以单独使用 CSS 来运行动画了，为什么还会有人需要 JavaScript 来完成这项工作呢？

为了帮助你在 CSS 和 JavaScript 动画之间做出选择，考虑 Rachel Nabors 的中的[。](https://www.smashingmagazine.com/2014/11/the-state-of-animation-2014/)

动画可以是:

*   **静态**:动画从头到尾，没有决策逻辑。CSS 加载器就是一个很好的例子。光用 CSS 就可以创作出这种动画；
*   有状态的(stateful):点击按钮时打开和关闭的侧边栏是这种类型动画的一个常见例子。您可以使用 CSS 和少量 JavaScript 来添加和删除负责跟踪动画中元素状态的 CSS 类；
*   **动态**:这种动画可以有不同的结果，这取决于各种因素，如用户交互、DOM 事件、同一文档上其他元素的动画状态等。你不能只用 CSS 来创建动态动画——在这种情况下，JavaScript 很可能是你最好的朋友。

除了动态动画，如果你遇到以下任何情况，你可以求助于 JavaScript 来帮助你的动画:

*   您需要在多个元素上链接或交错您的动画。在这种情况下，每个后续动画都需要在前一个动画完成后出现。通过协调每个动画元素上的`delay`属性，您可以单独使用 CSS 来做到这一点。但是，如果您只需要更改一个值，对所有链接的元素进行必要的调整会很快变得难以管理；
*   您正在制作 SVG 图形动画。在这种情况下，缺点是 SVG 上的 [CSS 动画在各种浏览器](https://www.smashingmagazine.com/2016/07/an-svg-animation-case-study/#css-and-velocity-js)中得不到一致的支持；
*   如果您的项目需要支持 CSS 动画不可用的旧浏览器，求助于健壮的 JavaScript 解决方案将使您的动画更广泛可用。

如果你想了解更多关于这个话题的内容，杰克·道尔的《打破神话:CSS 动画与 JavaScript》,[green sock 动画平台](http://greensock.com/gsap)的作者提出了一些很好的观点。

### 一个崭露头角的选择:网络动画 API

W3C 一直致力于制定一个规范，将 CSS、SVG 和 JavaScript 动画集合在一个统一的语言下，不需要任何外部 JavaScript 库。该 API 被称为 [Web 动画](https://www.w3.org/TR/web-animations/)，非常适合 CSS 不太适合的动态动画。你可以在你自己的网站上阅读达德利·斯托里对网络动画 API (AAPI)的有益介绍[。](https://www.sitepoint.com/bringing-pages-to-life-web-animations-api/)

Web Animations API 的工作一直在持续进行，对于不提供 API 原生支持的浏览器来说, [polyfill](https://github.com/web-animations/web-animations-js) 也是可用的。

所有这些听起来都不错，我积极鼓励尝试 WAAPI。然而，在撰写本文时，[浏览器支持](http://caniuse.com/#search=web%20animation)是粗略的，规范在未来仍有可能改变。因此，如果 WAAPI 现在还不适合您的项目，您可以求助于 JavaScript 动画库来完成这项工作。

## 动态 DOM 动画的动画库

有大量的 JavaScript 动画库可供您尝试。在编写[的时候，GreenSock](http://greensock.com/) ，也被称为 GSAP (GreenSock 动画平台)，和 [Velocity.js](http://velocityjs.org/) 都在大玩家之列。

两者都是非常棒的用户友好工具，我将在后续文章中讨论它们。然而，我想以 [Anime.js](http://anime-js.com/) 开始这个系列，这是一个新的 JavaScript 动画库，已经获得了一些重要的关注。

## 动漫的主要特点

*动漫*的名字是[日语为*手绘*或*电脑动画*(日语:アニメ)](https://en.wikipedia.org/wiki/Anime)

![Anime.js JavaScript animation library logo](img/0d7440b9292988fa5c03b0005a875cfb.png)

至于它的特色，Anime.js…

> 是一个灵活而轻量级的 JavaScript 动画库。它可以处理 CSS、单个转换、SVG、DOM 属性和 JS 对象。

Anime.js 支持以下浏览器:

*   铬
*   旅行队
*   歌剧
*   火狐浏览器
*   Internet Explorer 10+

### 有了所有的动漫库，为什么还要动漫？

这个问题的答案最好留给动画作者来回答，他解释了为什么当有像 GSAP 这样强大的东西在身边时，他会想到 Anime.js:

> GSAP 能做的远不止动漫。但它也更重。我对这个库的目标是保持 API 尽可能简单，专注于我真正需要的东西(多个计时、季节、回放控制…)，同时保持代码超级轻量级(9KB 最小化)。
> 
> [黑客新闻](https://news.ycombinator.com/item?id=11986480)

简而言之，对于 HTML 和 SVG 元素的动态动画，不需要 GSAP 和其他大型库提供的所有现成功能，Anime.js 可能是您项目的一个绝佳选择。

## 如何使用 Anime.js

在接下来的内容中，我提供了一些实例来说明 Anime.js 的基本用法。出于演示的目的，大多数动画运行起来会有点慢。

包含 Anime.js 与包含 jQuery 或任何其他熟悉的 JavaScript 库没有区别。

下载。从 GitHub 上的库的[项目的页面压缩文件，提取文件并使用`<script>`标签将`anime.min.js`添加到您的`html`文档中:](https://github.com/juliangarnier/anime)

```
<script src="anime.min.js"></script>
```

或者，您可以使用`npm`或`bower`:

```
npm install animejs
```

```
bower install animejs
```

### 向单个元素添加一个动画:弹跳球

一旦你在你的项目中设置了动画，你就可以开始制作最简单的动画:只有一个元素，一个球，上下跳动。

第一步是调用`anime`,传递一个带有动画细节的对象。这是基本结构:

```
var bouncingBall = anime({

  //code here

});
```

接下来，你需要用一些指令来充实上面的对象，比如动画的内容，动画的种类，持续时间等等。

```
var bouncingBall = anime({
  targets: '.ball',
  translateY: '50vh',
  duration: 300,
  loop: true,
  direction: 'alternate',
  easing: 'easeInCubic'
});
```

属性告诉动画你想要动画哪个元素。这里你可以使用一个 CSS 选择器，就像我在上面的例子中做的那样，或者下面的任何一个:

*   DOM 元素，例如`document.querySelector('.ball')`
*   Nodelist, e.g., `document.querySelectorAll('.ball')`
*   JavaScript 对象，例如`{elementName: 'ball'}`
*   JavaScript 数组，例如`['.ball']`

如果您的`targets`属性有多个值，也就是说，您打算将动画应用于多个元素，您可以使用数组作为您的数据结构，如下所示:

```
var bouncingBall = anime({
  targets: ['.ball', '.kick'],
  //rest of the code
});
```

上面例子中的第二个属性用`translateY`垂直移动球元素，使用 CSS 的人应该很熟悉。[蒂芙尼·布朗](https://www.sitepoint.com/animation-advice-from-a-css-master/)和其他人建议使用`translate`和`scale`来分别移动元素和改变它们的尺寸，而不是定位元素的位置属性或它们的宽度和高度。通过避免[浏览器回流](https://www.sitepoint.com/10-ways-minimize-reflows-improve-performance/)，这有助于动画的性能和质量。

在上述动画片段的属性列表中，下一个是`duration`属性。这告诉动画你希望动画持续多长时间。如果你有多个动画在不同的时间发生，`delay`是另一个有用的属性。

属性告诉动画你想要动画发生多少次。它的默认值是`false`，让动画只运行一次。您可以通过将该值更改为`true`来让动画无限期运行，或者通过将该属性设置为所需的数值来让动画运行精确的次数。

CSS 动画中也有`direction`属性，动画允许你将它设置为 CSS 对应部分的任意值:`normal`、`reverse`和`alternate`。最后一个值在动画的法线方向和它的反方向之间交替，这似乎对弹跳的球很有用。

最后，上面的代码为您的动画指定了一个`easing`属性。变化率是常数吗？也许动画开始很慢，快到结尾时加速了？最后部分有反弹吗？正确的缓动值是完美而有效的动画的重要组成部分。

你可以通过下面这个简单的片段找到所有你可以在动画中使用的缓动功能:

```
console.log(anime.easings);
```

### 向弹跳球添加第二个动画

假设你想让弹跳的球在碰到容器底部时被压扁一点。通过以 JavaScript 对象的形式添加[特定的动画参数](https://github.com/juliangarnier/anime#specific-animation-parameters)，你可以在动画中做到这一点。以弹跳球为例，以下是如何达到预期效果的方法:

```
var bouncingBall = anime({
  targets: '.ball',
  translateY: '50vh',
  duration: 300,
  loop: true,
  direction: 'alternate',
  easing: 'easeInCubic',
  //new code
  scaleX: {
    value: 1.05,
    duration: 150,
    delay: 268
  }
});
```

上面的代码添加了一个新的属性`scaleX`，以增加球的宽度，并以一个[对象文字](http://www.standardista.com/javascript/javascript-object-literals-simplified/)的形式设置它的值，该对象文字带有可以用来控制动画的特定键。

`value`键允许你在水平轴上缩放元素时控制元素的宽度，而`duration`和`delay`键允许你分别设置动画将持续多长时间以及相对于前一个动画的开始时间。

在 [CodePen](http://codepen.io) 上用 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看动漫 JS 的笔[弹跳球。](http://codepen.io/SitePoint/pen/edaxbo/)