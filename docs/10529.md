# WDS 标题序列解释:HTML5 和 CSS3 的行动

> 原文：<https://www.sitepoint.com/the-wds-title-sequence-explained-html5-and-css3-in-action/>

我刚从悉尼的 Web Directions South 回来，在那里我看到一些鼓舞人心的演讲者在台上极客，并与一群其他有 Web 头脑的人混在一起。但我看到的最鼓舞人心的事情发生在发布会的前十分钟:卡梅隆·亚当斯的片头片尾。乍一看，这只是一个动画，显示了所有发言者的名字，并带有一些很酷的效果，播放的是电影 *Inception* 的混音配乐。然后您会了解到它是在浏览器中使用 HTML5、CSS3 和 JavaScript 创建的。

从[开始检查](http://www.themaninblue.com/experiment/wds10/)；你必须使用 Chrome 浏览器来查看。为了让它正常工作，您需要将屏幕分辨率调整为 1024 x 768，加载页面，切换到全屏模式，然后刷新页面，以正确的分辨率从头开始查看。

尽管它是基于《盗梦空间》的原声音乐，卡梅隆的演示并不依赖于演员向我们解释三个多小时发生了什么。出于这个原因，我想我应该深入到源代码中去一窥幕后的神奇之处。该演示由 800 多行 CSS 和 2，400 行 JavaScript 组成(不包括 jQuery 和一些为其添加 CSS3 支持的 monkey 补丁)，因此我将略述一些更酷的效果。如果你对所涉及的内容(以及在此过程中遇到的一些奇怪的错误)更感兴趣，可以看看[卡梅隆的博客文章](http://themaninblue.com/writing/perspective/2010/10/18/)。

## 悦耳的音乐

当您查看演示页面的源代码时，首先会注意到在`<body>`标记中只有一个 HTML 元素——一个`audio`元素:

```
<audio id="music" src="Music.mp3" oncanplaythrough="start();" preload="auto"></audio>
```

当然，这是用于原生浏览器内音频的新 HTML5 元素。

这里的关键是`oncanplaythrough`属性。像任何`on-` something 属性一样，它指的是浏览器中触发的事件。在这种情况下，当元素已经加载了足够多的媒体文件，能够在没有缓冲的情况下一直播放到结束时，就会触发`canplaythrough`事件。Cameron 用它来调用 JavaScript 中的`start()`方法，让动画开始播放。

## 时机就是一切

像这样的动画最棘手的一个方面是音轨的各种效果的定时。当这些圆圈围绕前几个名字跳动时，它们的时间正好与音乐中令人难忘的钢琴音符相吻合。

这怎么可能呢？Cameron 的 JavaScript 代码从一个名为`TIME_CODE`的大对象数组开始。数组中的每个对象都包含一个时间代码和一个函数名。时间代码可以是一个数字(从音轨开始算起的秒数)，也可以是一个类似于`"+4"`的字符串，意思是“最后一个时间代码之后的 4 秒钟”当你查看数组时，你会看到几个时间码被指定为一个`BEAT`常数的倍数。这个常数就是音乐开始几秒钟内钢琴音符之间的确切间隔:1.79 秒。

音频准备好播放时触发的`start()`方法(正如我们前面看到的)如下所示:

```
 function start()
{
  $("body").unbind("click");

  stageWidth = $(window).width();
  stageHeight = $(window).height();

  startDate = new Date();

  var music = document.getElementById("music");
  music.play();

  setInterval(tick, 10);
} 
```

最重要的行是最后两行。首先，他开始演奏音乐；然后，他设置一个间隔，每十毫秒运行一次`tick`方法。`tick`方法是整个脚本的基石:

```
 function tick()
{
  var music = document.getElementById("music");

  if (TIME_CODE.length > 0)
  {
    var time = 0;

    if (typeof TIME_CODE[0].time == "string")
    {
      time = lastTime + parseFloat(TIME_CODE[0].time.replace(/+/, ""));
    }
    else
    {
      time = TIME_CODE[0].time;
    }

    if (music.currentTime >= time)
    {
      TIME_CODE[0].func();
      lastTime = time;
      TIME_CODE.splice(0, 1);
    }
  }
} 
```

这仍然相当简单:代码获取了`TIME_CODE`数组中的第一个元素；然后检查该对象的`time`属性是绝对的还是相对的(即，它是一个字符串还是一个数字)。如果是相对的，代码会将它添加到当前时间代码的`lastTime`变量中。然后它得到音乐的`currentTime`；这是 HTML5 `audio`元素的内置属性。如果我们等待的时间代码已经到达，代码将触发与之相关的函数，设置`lastTime`，然后使用`splice`删除`TIME_CODE`数组的第一个元素。

这些允许代码在特定的时间运行每个函数，时间分辨率为 10 毫秒。事实证明，这是足够接近我们的大脑不告诉区别，屏幕上的事件似乎与音乐完美地同步。

## 过渡、变换和动画，天啊！

`TIME_CODE`中的第一个函数是`presents()`，但我将跳过其中的大部分，因为它真正做的是迭代字符串“Web Directions Presents:South 2010”的所有字母，并淡入淡出。然而，有趣的是，衰退发生在 CSS 中，而不是 JavaScript 中。JavaScript 代码只是简单地将每个字母的`opacity`设置为 0.9 或 0，这通常看起来像是字母的开关。然而，在 CSS 中你会看到这样的规则:

```
-webkit-transition: all 3s ease-in;
```

这意味着无论何时目标元素的任何属性被改变(无论是通过 JavaScript 还是在 CSS 本身中改变伪类选择器)，它都将使用`ease-in`计时函数在三秒钟内被激活。有关计时功能和其他可用选项的更多信息，请查看 CSS 转换模块上的 [W3C 规范。最终结果是，每个字母看起来会平滑地淡入淡出。](https://www.w3.org/TR/css3-transitions/#transition-timing-function_tag)

现在让我们来看看一个更有趣的效果:我之前提到的那些脉动的圆圈。每个圆实际上是一个`div`和一个设置为其一半大小的`border-radius`。那些从每个圆圈发出的光脉冲仅仅是应用了一些 CSS 动画的完全相同的`div`。下面是相关的 CSS:

```
 ._1pulse
{
  position: absolute;
  background: -webkit-gradient(radial, 50% 50%, 0, 50% 50%, 30, from(rgba(255,255,255,0)), to(rgba(255,255,255,1)));
  opacity: 0.4;
  -webkit-animation-name: _1pulseAnim;
  -webkit-animation-duration: 1.85s;
  -webkit-animation-timing-function: ease-out;
}

@-webkit-keyframes _1pulseAnim
{
  0% {opacity: 0.4; -webkit-transform: scale(1)}
  100% {opacity: 0; -webkit-transform: scale(5)}
} 
```

这段代码包含了 CSS3 魔力的两个独立部分。首先，脉冲有一个应用了`-webkit-gradient`的背景径向渐变。第二，也是最有趣的，是动画。CSS3 动画需要首先使用`@keyframes`声明(或者在本例中使用`@-webkit-keyframes`)设置一些关键帧。关键帧集被赋予一个名称(`_1pulseAnim`，然后可以从 CSS 规则中引用它。每个关键帧都有一个选择器:一个百分比告诉浏览器关键帧位于动画中的哪个点。在这个非常简单的例子中，Cameron 只有两个关键帧:一个用于初始状态(0%)，一个用于最终状态(100%)。只有两个属性是动画:`opacity`和`scale`。然后，通过将这些关键帧包含在一个`-webkit-animation-name`声明中，将它们分配给给定元素的动画，就像上面代码中的那样。

最终结果是，当脉冲`div`被附加到文档时，动画运行。在逐渐消失时，它的大小是中心圆圈的五倍。

## 对于他的下一个魔术…

在这个页面上还有更多的动画:在这些圆圈之间延伸的线条网络，后面是滚动的字母墙，暂停以突出显示其他演讲者的名字。我在这里没有足够的空间来详细说明每一个是如何完成的，但是如果您有兴趣的话，可以自己深入研究代码。然而，我确实想简要地谈一谈对我来说印象最深的动画:在屏幕上旋转的白盒显示出它们背后的名字。信不信由你，几乎整个动画都是通过向单个`div`添加一个类来完成的。Cameron 首先创建所有的名字，然后设置一个超时，在给定的时间淡入每个名字。然后他拿出一个巨大的包含所有圆角矩形盒子的`div`，并添加了类`fly`:

```
 $("#_13blocks").addClass("fly") 
```

这一行无害的代码触发了下面的 CSS 动画:

```
 #_13blocks.fly
{
  -webkit-animation-name: fly, flyOrigin;
  -webkit-animation-duration: 19.7s, 19.7s;
  -webkit-animation-timing-function: ease-in-out, ease-in-out;
  -webkit-animation-fill-mode: forwards, forwards;
}

@-webkit-keyframes fly
{
  0% {-webkit-transform: rotate(0deg) scale(1);}
  8.3% {-webkit-transform: rotate(-90deg) scale(3);}
  16.6% {-webkit-transform: rotate(-180deg) scale(4.3);}
  24.9% {-webkit-transform: rotate(-270deg) scale(5.6);}
  33.2% {-webkit-transform: rotate(-360deg) scale(7.5);}
  41.5% {-webkit-transform: rotate(-450deg) scale(10);}
  49.8% {-webkit-transform: rotate(-540deg) scale(13);}
  51.5% {-webkit-transform: rotate(-540deg) scale(13);}
  58.1% {-webkit-transform: rotate(-630deg) scale(13);}
  59.6% {-webkit-transform: rotate(-630deg) scale(13);}
  66.4% {-webkit-transform: rotate(-720deg) scale(13);}
  67.9% {-webkit-transform: rotate(-720deg) scale(13);}
  74.7% {-webkit-transform: rotate(-810deg) scale(13);}
  76.2% {-webkit-transform: rotate(-810deg) scale(13);}
  95.0% {-webkit-transform: rotate(-990deg) scale(1);}
  96.0% {-webkit-transform: rotate(-990deg) scale(1); -webkit-animation-timing-function: ease-in;}
  100% {-webkit-transform: rotate(-990deg) scale(15);}
}
```

每个关键帧指定一个`rotation`和一个`scale`(其中大多数都非常高，以便使`div`比浏览器的视口大几倍)。结果是:`div`在屏幕上飞来飞去，停在一个对齐的白色区域，显示一行文本。由于之前在 JavaScript 中指定的超时，每一行都会在那个时刻淡入。然后容器再次旋转以显示下一个名字。`ease-in-out`计时功能让动画看起来流畅自然，有一点弹跳。总体效果是 CSS3 动画强大功能的全面展示。

## 憎恨者会憎恨

你们中的反对者现在会大声说，“这只能在特定浏览器的特定分辨率下工作，并且使用了大量的供应商前缀！在现实世界里是没用的！”关键是，它在设计的环境中工作:它在浏览器中播放，投影到屏幕上，让我们大吃一惊。这甚至不是创建动画的最简单的方法，但它确实向我们展示了我们的前进方向，以及对于 web 开发人员来说即将发生的事情。随着 IE9 支持许多 CSS3 特性(尽管不幸的是不是创建这个演示的动画)，浏览器战争又开始了，只是这一次，它们是在标准上进行的。

我几乎没有接触到演示中三分之一的酷动画和效果，所以如果你真的想了解发生了什么，请确保打开源代码并进行播放。

向卡梅隆的出色工作致敬。我甚至无法想象为了创造这样一个无缝的序列，将所有这些元素精心排列在一起需要多少工作，更不用说音频了。我还要感谢捷星航空公司，是他们延误了我的航班，让我花了几个小时坐在候机室的地板上阅读所有代码。

## 分享这篇文章