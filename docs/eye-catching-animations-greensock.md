# 醒目的绿色动画

> 原文：<https://www.sitepoint.com/eye-catching-animations-greensock/>

引人注目的网站动画确实可以吸引注意力，有助于提高转化率。不幸的是，动画从来都不容易创作。网站开发人员花了很多时间设计、审查和修改他们的动画，然后才把它们发布到世界上。虽然许多开发人员使用 Adobe Flash 平台来简化在时间轴上补间对象的艰巨任务，但移动设备上缺乏 Flash 支持使得许多开发人员转向 JavaScript。

为了支持对 JavaScript 动画日益增长的需求，软件供应商创造了一些令人惊叹的开发工具。其中一家供应商 GreenSock 提供了一些非常有用和成熟的工具，用于补间对象并将它们放在时间轴上。这些工具是成熟的，因为 GreenSock 对这个行业并不陌生；他们已经创建并继续创建 Flash ActionScript 的动画工具，并移植这些工具以支持 JavaScript。

在本文中，我将简要描述用于补间和时间轴构建的 GreenSock 工具。

## 时间轴上的补间简介

动画开发人员使用“补间”来填充两个时间点之间的动画帧。(“补间”一词来源于“between”)例如，当开发人员希望制作一个从 A 点滚到 B 点的球的动画时，她只需要在每个端点绘制一个球的图片，并让计算机填充其间的帧。

动画开发人员使用时间线来移动他们的动画。例如，开发人员精确地指定球何时从 A 点释放，何时到达 b 点。此外，开发人员使用“缓动”功能来控制球如何沿其路径加速和减速。放松使球看起来符合我们熟悉的物理定律。

时间轴上的补间会变得非常复杂，尤其是当有几个对象在屏幕上移动时，每个对象都有自己的路径、速度和时间轴。当需要在设计评审后进行修改时，情况会变得更加复杂。事实证明，以编程方式修改补间和时间轴比以图形方式修改要容易得多。换句话说，你只需改变一些数值变量，而不是费力地修改重绘帧。

## 绿石工具

GreenSock 有四个主要的动画库:TimelineLite、TimelineMax、TweenLite 和 TweenMax。“Max”版本以面向对象的方式扩展了“Lite”版本，以稍大的文件大小为代价提供了更多的功能。你通常可以免费使用这些库；然而，我建议购买一个许可层，并成为“绿石俱乐部”的一员这为你提供了最新的测试版本以及一些很酷的扩展。此外，您的许可同时适用于 JavaScript 和 ActionScript 版本。

GreenSock 库的名称是不言自明的:“补间”库允许您对屏幕上的可见元素进行补间,“时间轴”库允许您在对象组上创建独立的时间轴。请注意，一个对象可以是多个时间轴的一部分。

让我们来看一个简单的例子，看看时间轴上的补间如何与绿色背景工具一起工作。

## 大量信件

我们将设置一个简单的动画，其中短语“时间轴上的补间”中的每个字母独立地进入视图，反弹，然后固定到位。首先，我们用下面的 HTML 文件设置舞台。

```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>Tweening on a Timeline</title>
    <script src="http://cdnjs.cloudflare.com/ajax/libs/gsap/latest/TweenMax.min.js"></script>
    <script src="//ajax.googleapis.com/ajax/libs/jquery/2.0.3/jquery.min.js"></script>
    <script src="js/raining.js"></script>
    <link href="css/style.css" rel="stylesheet" type="text/css" />
  </head>
  <body>
    <div id="stage">
      <div id="raining-text">Tweening on a Timeline</div>
    </div>
  </body>
</html>
```

注意第 6 行包含了 GreenSock 工具 TweenMax。这个外部引用的文件包含大多数主要的 GreenSock 库，包括时间轴文件和一些辅助库。在您自己的应用程序中，您应该从本地目录加载这些库，并且只加载您需要的库。为了简单起见，本例中加载了所有内容。

还要注意，我们在第 7 行加载了 jQuery 库。GreenSock 工具根本不需要 jQuery。但是，如果您碰巧加载了 jQuery，GreenSock 可以很好地使用它。

第 8 行显示了本地`raining.js`文件的加载。这是将控制动画的文件，我们稍后将更仔细地研究它。

最后，注意主体包含了`stage`和`raining-text`元素。`stage`是动画发生的窗口，而`raining-text`元素包含将要被动画化的短语。

接下来，我们用`style.css`文件来设计一切:

```
@charset "UTF-8";
#stage {
  background-color: #000000;
  width: 600px;
  height: 250px;
  position: relative;
  margin-top: 20px;
  overflow: hidden;
}

#raining-text {
  color: #ffffff;
  font-family: Arial, sans-serif;
  font-size:24px;
  position: absolute;
  bottom: 5px;
  width: 100%;
  text-align: center;
}
```

在这个 CSS 文件中有一些事情需要注意。首先，`stage`采用相对定位。这允许更容易地定位包含在其中的绝对定位的元素。其次，隐藏`stage`溢出；这可以防止动画干扰舞台外部的元素。最后，下雨的文本位置由`bottom`属性设置。该属性将在动画中被操纵。

现在，让我们看一下`raining.js`文件，所有的动作都发生在这里。

```
// Sample script for Tweening on a Timeline
$(function() {
  var rainingPhrase = $("#raining-text");
  var tl = new TimelineLite();

  tl.add(TweenLite.set(rainingPhrase, {bottom: 250}));
  tl.add(TweenLite.to(rainingPhrase, 2, {bottom:5, ease: Bounce.easeOut}));
});
```

在第 3 行，我们使用 jQuery 为下雨的文本创建一个选择器`rainingPhrase`。如前所述，GreenSock 不依赖于 jQuery，但是如果 jQuery 可用的话，它可以使用 jQuery。

在第 5 行，我们创建了一个时间线并用变量`tl`引用它。绿色时间线用于按顺序对单个行动进行分组。在本例中，我们创建了两个动作:1)将短语设置在舞台上方，它将被`overflow`属性隐藏，2)将文本放到舞台底部。

让我们看看第 6 行的第一个动作。我们通过实例化一个新的 TweenLite 对象，然后在该对象上使用`set`类函数来创建补间。对象本身是第一个参数，第二个参数中的对象指定操作。在本例中，我们设置了`rainingPhrase`对象的`bottom`属性，这样它就被移动到舞台上方。`set`功能本质上是即时操纵一个对象，没有运动。

如果我们只是即时移动某些东西，补间的目的是什么？嗯，如果加载或运行 GreenSock 库时出现问题，原始的 raining 文本将不会从其原始位置移动。基本上，如果由于某种原因，动画无法运行，我们将文本放在舞台中有意义的位置。

可见的动作发生在第 7 行。因为此行中的补间动画是在第 6 行的补间动画之后添加到时间轴中的，所以在前面的补间动画完成之前，它不会执行。请注意，情况不一定总是如此；我们可以在这个补间中使用一个“负延迟”属性，让它在前面的补间完成动作之前作用于目标对象。然而，在这种情况下，我们希望前一个补间运行完成。

第 7 行的补间被实例化，并立即运行`to`类函数。这个函数本质上是说，“将一个对象从其当前状态操纵到一个目标状态。”对象本身被指定为第一个参数，动作的持续时间(秒)在第二个参数中指定，第三个参数指定操作的类型。注意，这个操作实际上可以包含任何 CSS 属性，比如`scale`、`rotation`、`color`等。在这种情况下，操作指定对象将被移动到舞台的底部。

缓动功能不是以线性方式将对象滑动到舞台底部，而是给动画增添了一些乐趣。通过将缓动函数指定为`Bounce.easeOut`，我们告诉系统在实现动画时模拟一个弹跳的球。

如果您运行该程序，您会看到当页面加载时，整个短语最初出现在舞台的顶部，然后加速向底部移动。当它触底时，会弹跳几下才停下来。很酷，是吗？

然而，有一个问题。我还没有兑现我的承诺，让每个角色独立地下落和反弹到位。相反，整个乐句作为一个整体上下跳动。这是一部有趣的动画，但我们需要加点料。

问题是我们需要把短语分解成单个的字符。这可能需要大量的工作。如果你要用困难的方式来做，你必须将短语分割成独立的非阻塞的`div`元素，将每个`div`加载到时间线中，并希望它落入适当的位置，这样它看起来就像原始短语。想象一下，做了所有这些编码，却发现营销部门想把这个短语改成别的，比如“补间动画时间轴”

幸运的是，有一个简单的解决方案。它叫做 SplitText。如果你是绿石俱乐部的成员，你可以访问一个叫做`SplitText.min.js`的插件文件。SplitText 获取对文本字符串的引用，将其拆分为单个字符，然后将它们加载到一个数组中。然后，您可以使用带有 TimelineLite `stagger`函数的数组，该函数将自动为每个 SplitText 对象构建一个独立的时间轴。

让我们看看这是如何工作的。

首先，将`SplitText.min.js`文件放入 JavaScript 目录，然后在 HTML 文件中引用它，如下面第 7 行所示。

```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>Tweening on a Timeline</title>
    <script src="http://cdnjs.cloudflare.com/ajax/libs/gsap/latest/TweenMax.min.js"></script>
    <script src="js/SplitText.min.js"></script>
    <script src="//ajax.googleapis.com/ajax/libs/jquery/2.0.3/jquery.min.js"></script>
    <script src="js/raining.js"></script>
    <link href="css/style.css" rel="stylesheet" type="text/css" />
  </head>
  <body>
    <div id="stage">
      <div id="raining-text">Tweening on a Timeline</div>
    </div>
  </body>
</html>
```

请注意，SplitText 会自动查找文本元素，而不需要 jQuery 的帮助，因此您可以在第 8 行删除对它的引用。唯一的问题是您必须使用不同的“文档就绪”功能，而不是在`raining.js`中使用的`$(function(){})`功能。

现在让我们看看真正酷的部分。我们实际上可以拆分文本，将其预设在舞台上方，并将单个字符放到舞台上，只需四行 JavaScript 代码！打开`raining.js`，如下图所示进行更改。

```
// Sample script for Tweening on a Timeline
$(function() {
  var splitPhrase = new SplitText("#raining-text", {type: "chars"});
  var tl = new TimelineLite();

  tl.staggerTo(splitPhrase.chars, 0, {bottom: 250}, 0);
  tl.staggerTo(splitPhrase.chars, 2, {bottom: 5, ease: Bounce.easeOut}, 0.02);
});
```

在这段代码中，我们在第 3 行创建了一个对拆分文本的引用。SplitText 对象接受两个参数:对文本的引用和对该文本执行的拆分类型。在这种情况下，我们在字符上分割，尽管我们也可以在行和单词上分割，或者三者的组合。`splitPhrase`变量现在包含了一个字符数组，我们很快就会引用它。

我们在第 4 行构建时间线，就像我们在本文前面所做的一样。

第 5 行使用了一个名为`staggerTo`的 TimelineLite 函数。该函数将一个对象数组作为其第一个参数，并为每个对象构建单独的时间轴和补间动画。第二个参数提供了一个持续时间值——在本例中，我们想要即时动作，所以我们将其持续时间设置为零。(请记住，我们将角色预先放置在舞台上方。)第三个参数包含要在数组中的每个对象上执行的“到”操作，第四个参数指定错开每个对象的时间轴的时间量。

第 6 行是所有动作发生的地方。它看起来和第 5 行完全一样，除了参数被改变了。在这种情况下，我们在两秒钟的时间内将所有角色移动到舞台的底部，使它们错开 0.02 秒，并使用模拟弹跳的缓动功能。

![title](img/b081e88cdb3995b972221b80d1990225.png)

## 结论

网站动画真的可以抓住眼球，有助于将访问者转化为客户。但是创作这些动画从来都不容易。您必须使用 JavaScript 来确保您的动画能够在从桌面到移动设备的所有设备上呈现。许多供应商正在开发 JavaScript 工具来帮助这个过程。本文主要关注 GreenSock 工具。使用 GreenSock 的时间轴和补间工具，您可以快速创建具有逼真缓动功能的动画，并且因为这些工具在代码中运行，所以很容易实现修改。在本文中，我们创建了一个简单的例子，我们将一个短语拆分成字符，然后一次一个地将它们放到舞台上，在舞台上它们作为一个短语重新组合在一起。我们只用了四行代码就完成了大部分操作！

*编辑:格林索克的好人们给我们发来了这支笔来补充这篇文章。*

请看 [CodePen](https://codepen.io) 上 GreenSock ( [@GreenSock](https://codepen.io/GreenSock) )为赞美 SitePoint 文章而做的 Pen [演示。](https://codepen.io/GreenSock/pen/mphqE)