# CSS3 Pong:与 CSS #17 有关的疯狂事情

> 原文：<https://www.sitepoint.com/css3-pong-insane-things-to-do-with-css/>

[![](img/431984e5c9004c01a62d79b3f07f4e89.png)](https://www.sitepoint.com/wp-content/uploads/2012/05/minecraft.jpg)

突击测验:这是什么？

在我们开始之前，让我们试着快速问一下“我是什么？”。这是照片吗..：

a)。星球大战里的帝国歼星舰？

b)。汪克尔转子发动机的乐高模型？

c)。科学计算器？

如果你回答了“***C——一个计算器*** ”，给自己一个大大的笑脸戳！你现在看到的是一个全功能的科学计算器，完全构建在虚拟世界[《我的世界》](http://www.minecraft.net/)中。

现在，《我的世界》——如果你不熟悉的话——是一个在线建筑游戏世界，有着非常 8 位的美学。想想“虚拟乐高”。

大多数矿主花时间建造精致的宫殿/堡垒。有时这些结构包括相对罕见的“红石”，可以用作简单的开关来控制门、大门等。

在上面的例子中，一个 16 岁的孩子(‘MaxSGB’)已经将虚拟的红石山连接到一个体育场大小的工作计算器——具有绘图能力，正弦、余弦、正切和平方根功能。

如果你还不明白这一点，想象一个孩子用乐高思维风暴建造了他自己的 Playstation II——你就快明白了。

太神奇了。疯狂。可笑。顽强。我不确定哪个是最好的描述。

这是一个好的计算器吗？

不。事实是，我五年级的时候有一块电子表，它有一个比这个发明更好的内置计算器。

但这不是重点。

通过突破界限，我们只能猜测这个家伙一路走来自学了多少东西——从建筑到工程到编程到电子，再到简单的解决问题。

## 这个计划

抱着推着东西直到它们痛得发抖的想法，我们打算用 CSS 做一些同样愚蠢的事情——构造一个可玩的游戏—[Pong(1972)](http://en.wikipedia.org/wiki/Pong)。没有 JavaScript 或 Flash。只有 HTML，CSS 和一群健康的疯子教授。

在此过程中，我们将:

1.  用 CSS 动画做一些有趣的东西
2.  摆弄 CSS3 兄弟选择器
3.  希望能对我们所使用的独创性/疯狂一笑置之

我们将使用很多非常现代的 CSS3。理论上，它应该可以在 Firefox、Opera 和 Safari 上运行，但是我只保证在这个代码演示中使用 Chrome。

我还将在示例中引用 CSS 的官方 W3C 版本，以保持代码整洁——尽管现实世界的浏览器需要它们的前缀(-moz，-webkit 等..).

我的大部分演示代码在运行时使用 prefixfree 来添加适当的特定于浏览器的前缀，但是你可能更喜欢使用 [Compass](http://compass-style.org/) 、 [Fire](http://fireapp.handlino.com/) 、Scout、 [Sass](http://sass-lang.com/) 、 [Less](http://lesscss.org/) 或者甚至硬编码你自己的“前缀化”CSS。

如果你想知道我们正在走向何方，[这里是最后一个演示](hhttp://codepen.io/alexmwalker/pen/paHcG)。

## 第 1 部分–动画

### 1).设立法院

首先，让我们建立一个球场和球玩。

这是我们的起始标记:

```
<h1>CSS PONG</h1>
<div id="court">
    <div id="horizontal">
	<span id="ball"></span>
    </div>
</div>
```

外部#court DIV 定义了我们的比赛场地。我们会给它一个像原来的游戏一样的绿色虚线边框。

我们有一个#球，它位于一个名为#horizontal 的包装 DIV 内，该包装 DIV 贯穿球场的整个宽度。这个不可见的 DIV 将成为一个有用的平台，以后可以在这个平台上悬挂其他屏幕元素。

```
#court{
	margin: 30px auto;
	width: 600px;
	height: 300px;
	position: relative;
	border: 4px dotted #3f3;
```

我们的#球场需要一个球网。我们可以使用“:before”伪元素将它附加到我们的 court。

```
#court:before {
	left: 50%;
	content:'';
	width: 10px;
	height: 300px;
	position: absolute;
	border-left: 4px dashed #3f3;
	}
```

我们的球是一个简单的 20px 乘 20px 圆角格。不需要外部图像。

```
#ball {
	position: absolute;
	width: 20px;
	height: 20px;
	display: block;
	background :#3f3;
	border-radius: 50%;
	transform: translate3d(10px,0,0) }
```

![](img/ad667aceb1fb8be8593b92d0dad84c00.png)

我们现在有了我们的初始结构。

### 2).制作球的动画

让我们后退一点。如果你已经是一个 CSS 动画向导，请随意向下翻页到[回到 Pong](#btp) 。如果没有，我们将快速浏览一些一般的“需要知道的事项”。

首先，大多数(但不是全部)CSS 属性是“可动画化”的。一般来说，如果你能用一个数字来描述它，那么它*应该*是可动画的。

例如，我们**可以**制作动画:

*   `left: 0px → left: 100px`
*   `margin-top: 10% → margin-top:90%`
*   `opacity: 0 → opacity: 1`
*   `z-index: 1 → z-index: 99`

然而，我们**不能**动画化:

*   `float: left → float: right`
*   `display: block → display: none`
*   `visibility: hidden → visibility: visible`

你会注意到第二组由离散的“状态”组成。没有中间状态——像“稍微隐藏”或“有点阻塞”,这就是为什么你不能动画显示这些属性。

令人欣慰的是，颜色总是很容易被动画化——即使当你使用“暗兰”或“矢车菊蓝”等外来关键字时——因为你的浏览器会自动将它们转换成数字。

但是还有更多要考虑的。

通常，我们想要在屏幕上移动东西，有很多方法可以做到。理论上，制作这些属性的动画是可行的:

*   `left`
*   `right`
*   `top`
*   `bottom`
*   `margin`
*   `padding`
*   `transform:translate(*x,y*)`
*   `transform:translate3d(*x,y,z*)`

虽然它们都可以工作，但令人惊讶的是，浏览器处理它们的方式却有很大的不同。

信不信由你，`translate3d()`是用 CSS3 移动对象最有效的方法——即使你只是在制作 2d 对象的动画！

显然，`translate3d()`将动画的繁重工作转移到图形处理器上，这大大加快了速度。当然，当我使用`translate3d()`时，我的 MacBook Pro 处理器保持了更低的温度。

秘诀:每当你在屏幕上移动东西时，试着使用`translate3d()`。

**回乓。**

让我们利用我们所知道的`translate3d()`，给我们的球一个简单的从左到右的反弹。

`transform:translate3D`要求我们提供三个值——X、Y 和 z。动画 X 使对象左右移动，而动画 Y 使对象上下移动。我们可以将 Z 值设为 0。

让我们创建一个“左右”动画，并将其应用到我们的#球。

```
@keyframes leftright {
0%,100% {transform: translate3d(10px,0,0)}
50% {transform: translate3d(570px,0,0)}
}
#ball{
 ...
 animation: leftright 2s infinite linear
 }
```

在我们的#court DIV 中，球现在应该每两秒钟从左向右反弹一次。

![](img/87f15521573e7dd948faf1bd1b0f1c5a.png)

步骤 1:设立法院

现在让我们创建一个名为“updown”的新动画——你可以猜到它是干什么的——并将其应用到我们的#horizontal platform。

```
@keyframes updown {
 0%,50%,100% {transform: translate3d(0,0,0)}
 25% {transform: translate3d(0,142px,0)}
 75% {transform: translate3d(0,-136px,0)} }

 #horizontal{
 ...
 animation: updown 2s infinite linear; }
```

![](img/e6c5d95b061818f25e78b7c242d268d4.png)

这是我们目前掌握的情况。

如你所见，我们的球每两秒钟在球场上完成一个完美的菱形循环。

很好…但是很快就变得无聊了。

如果我们稍微调整一下动画时间会发生什么？让我们将#球设置为每 1.9 秒循环一次，将#水平平台动画设置为 2.3 秒，而不是 2.0 秒。

![Changing up the timing on our animations makes the path much more interesting](img/df7993852f43fde20d79d435f8a60ff9.png)

[下面是结果](https://www.sitepoint.com/examples/css3pong/step2-a.html)。

如你所见，这个微小的时间变化对球的路径有很大的影响。这是真的，这条路径*将*重复，但不是两秒的重复周期，而是现在的 46 秒周期。

一个更有趣的结果。

### 添加玩家

让我们加入一些球员。我们将在我们的平台中插入两个新跨度(#player1 和#player2)，并将它们放在两端。

```
<h1>CSS PONG</h1><div id="court">
 <div id="platform">
     <span id="ball"></span>
         <span id="player1"></span>
         <span id="player2"></span>
    </div>
 </div>
```

```
#player1, #player2 {
 background:#3f3;
  position:absolute;
  width:7px;
  height:44px;
  left:4px;
  margin-top:-12px;
}
#player2{right:4px}
```

这里有一个工作演示。

由于球员被包含在我们的移动平台内，他们真的不能不完美地从上到下跟踪球。

但是我们 ***知道*** 真正的玩家是不会那样玩的。他们抽搐。他们向前冲。他们潜水。他们争夺。

如果我们创建一个新的“抽搐”动画关键帧，使蝙蝠相对于平台不规则地抽搐，会怎么样？

```
@keyframes twitchy { /* make player twitch like a real player  */
  0%,
  100%{transform: translate3d(0, 0px, 0); }
  20% {transform: translate3d(0, -45px, 0); }
  44% {transform: translate3d(0, 25px, 0); }
  46% {transform: translate3d(0, -15px, 0); }
  48% {transform: translate3d(0, 15px, 0); }
  50% {transform: translate3d(0, 50px, 0); }
  70% {transform: translate3d(0, 60px, 0); }
  85% {transform: translate3d(0, -30px, 0); }
  95% {transform: translate3d(0, 30px, 0); }
}

#player1, player#2{ animation: leftright 2.3s infinite linear; }
#player1 {animation-delay:1.15s} /* delay player 2 for half a cycle */
```

现在，只要我们保持蝙蝠动画和“左-右”球动画在同一时间，它们应该总是在抽搐之间碰到球。

让我们把它放到演示中(在 ESPN 超级慢动作中！).

这是一个进步。蝙蝠来回抽搐，就像一对 7 岁的孩子喝了太多的红色补酒。

因此，在这一点上我们有一个有趣的动画例子，但它不是游戏。

## 第 2 部分:让它可以玩…有点

嘿，如果你真的能玩它，那不是很酷吗？

这是凯西看到动画实验的第一个评论。当时我笑着耸耸肩，但是在接下来的几天里，**确实让我思考了起来。**

 **显然游戏和动画的区别在于*交互性*。如果我没有使用 JavaScript，我需要一种方法让我的 CSS 与用户交互。我列出了 CSS 可以利用的所有内置“用户事件”。

*   `:hover`
*   `:active`
*   `:visited`
*   `:checked`
*   `:focus`
*   `:enabled`
*   `:disabled`
*   `:selection`
*   `:target`

也许我们可以利用这些事件中的一个？我决定`:hover`是我最好的机会，所以我们的光标将成为我们的球棒。

```
#court {
cursor: url(/bat.gif), text;
}
```

接下来，我们需要一个新元素作为动画的触发器。我将在#horizontal platform 之前插入一个名为' #targetzone '的 DIV。我们也可能失去自动化玩家#1。

```
<h1>CSS PONG</h1>
   <div id="court">
	<div id="court">
        <span id="targetzone"></span>
	<div id="horizontal">
            <span id="ball"></span>
	    <span id="player2"></span>
	</div>
  </div>
```

```
#targetzone {
  background: rgba(0,0,255,0.25);
  position: absolute;
  margin:-50% 0 0 -50%;
  z-index:100;
  width:800px;
  height:600px;
}
```

CSS 的“兄弟选择器”——`+`和`~`——非常强大，但经常被忽视。在这种情况下，我们可以使用它们将一个`:hover`状态从一个 HTML 元素传递到它旁边的元素。这让我们使用#targetzone 来触发我们的动画。

因此，我们需要删除所有当前动画，并将它们转移到新的“悬停触发”规则，如下所示:

```
#targetzone:hover{
    animation: updown 2.3s infinite linear;
}
#targetzone:hover + #horizontal{
    animation: updown  2.3s infinite linear;
}
#targetzone:hover + #horizontal #player2{
    animation: twitchy 1.9s infinite linear; 
    animation-delay:0.95s;
}
#targetzone:hover + #horizontal #ball{
    animation: leftright 1.9s infinite linear;
}
```

现在，我们的动画只在光标悬停在#targetzone 上时播放——为了演示，我将#targetzone 设置为半透明的蓝色，但玩家通常看不到它。

[像这样](https://www.sitepoint.com/examples/css3pong/step4)。我们越来越接近了。

现在，让我们想想任何一场真实世界的网球比赛。

网球有趣的一点是:大部分时间比赛不需要你。

不能接球，不能运球，不能带球。对于每个点的 98%,球在球场上行进(或移动),远离你的触及或直接影响。

每次对打，你都有机会在几秒钟内把球打回去，或者犯错。

所以，如果你把它分解成步骤，我们的网球逻辑可以写成:

1)开始:球动画。动画:将球传给对手。忽略玩家
3)动画:球回给玩家。忽略球员
4)检查:球员的光标位置
5)球员是否挡住了球的路径？
5)如果:是-转到:步骤#2
6)如果:否-结束动画&电脑得 1 分。

因此，如果当球接近底线时，我们要将#targetzone 触发器快速缩小到球前面的区域，玩家必须在那个位置才能继续动画。我们会有一些非常非常基本的 CSS“碰撞检测”。

下面是调整#targezone 大小的新关键帧动画。

```
@keyframes targetzone {
  /* ball is in general play - targetzone is big */
  0%, 96% {
    width: 800px;
    height: 600px;
    margin: -50% 0 0 -50%;
  }

  /* ball is approaching player 1's baseline - targetzone shrinks*/
  96.1%, 100% {
    width: 150px;
    height: 100px;
    top: 0;
    left: 0;
    margin: 10% 0 0 -50px;
  }
}
```

使用逗号，我们可以将这个新动画添加到#targetzone 触发器中。

```
#targetzone:hover {
  animation: 
    updown 2.3s infinite linear, 
    targetzone 1.9s infinite linear;
}
```

这是结果。

它基本上按计划运行，但有一个问题。

任何玩家错过球的微秒(悬停状态结束)，#targetzone 重置并立即重新触发动画再次开始。你必须密切关注，甚至注意到点结束了。

我们还需要一个动画来迫使两点之间的短暂休息(就像真正的网球一样)。该动画将在每次新的拉力赛中只播放一次(而不是循环播放)，并将从`height: 0`到`height: 150px`重塑#targetzone。

```
@keyframes preparetoserve {
  /* Give targetzone no height so trigger is hidden */
  0%, 90% {
    height: 0px;
  }

  /* after a short time, resize the targetzone, ready to serve*/
  90.1%, 100% {
    height: 100px; 
   }
}
```

我们可以直接把它附加到#targetzone，因为我们不需要玩家触发它。

```
#targetzone {
  ...
  animation: preparetoserve 2s 1 linear;
}
```

 [我们把这个加到演示里吧。](https://www.sitepoint.com/examples/css3pong/step6)

它正在成形！

### 第 3 部分:记分

正如奥斯卡·王尔德所说，“没有什么比过度更成功了”，那么为什么现在就停止呢？这只小狗需要一个 CSS 支持的评分系统，goshdarnit！

现在，我不会分解每一行代码——这已经是一篇很长的文章了——我会告诉你一般的原则，如果你感兴趣的话，你可以把演示拆开。

### 用 HTML 和 CSS 维护状态

要更新任何得分线，我们需要一种使用 HTML/CSS 来“维护状态”的方法，以跟踪到目前为止发生了什么。这通常由服务器和 cookies 来处理。

几年前，Lea Verou 写了一篇非常有趣的文章，讲述了如何使用重新设计的复选框来构建一个 CSS 驱动的井字游戏。它展示了 HTML 表单如何记录鼠标点击，以及 CSS 如何即时响应这些点击。她还展示了 HTML 表单元素不必看起来像 HTML 表单元素。

我认为这是超级聪明的东西。

那么，如果:

1).我用一系列五个 HTML 单选按钮替换了我的单个# targetzone SPAN 每个按钮对应一个得分点(1-5)？稍微调整一下 CSS，我就可以让我们的动画要求单选按钮既被选中又被悬停(即`input:checked:hover`)。

2).我还将记分板显示与这些输入中的哪一项相关联？一旦玩家 1 点击发球，记分牌就会滴答作响。是的，令人悲伤的事实是，计算机目前不能输掉这场比赛——所以唯一真正的问题是*‘1 号玩家(你)还需要多长时间才能错过球？*’。

我知道，当你发现的时候会觉得不公平。确实是。

当然，为了戏剧效果，我们希望我们的玩家认为他们有机会赢。事实是，我们只能在点击发生时更新我们的单选按钮记分板，当有人错过球时没有点击。

这意味着我必须在发球点击上更新分数，但是在得分结束之前不能让玩家看到新的分数。嗯嗯…

3).那么，如果我把新的乐谱放在视野之外*而鼠标悬停在适当的位置*会怎么样呢？就像用铅笔撬开旧窗户一样，只要我们一拔出铅笔，窗户就会砰的一声关上。在我们的例子中，移除悬停将使我们的新分数崩溃到位。

CSS 过渡使新乐谱平滑地滑入到位。

我没说它漂亮。

但这很有效。

下面是最终代码的 Codepen 版本:

参见 [CodePen](http://codepen.io) 上 Alex ( [@alexmwalker](http://codepen.io/alexmwalker) )的 Pen [CSS3 Pong](http://codepen.io/alexmwalker/pen/paHcG/) 。**