# 使用 CSS3 动画构建出色的应用程序

> 原文：<https://www.sitepoint.com/build-awesome-apps-with-css3-animations/>

由于新的 CSS3 规范，今天的 HTML5 应用程序可以提供令人敬畏的体验。其中一个就是 **CSS3 动画**。它可以帮助你在 HTML 元素上构建丰富的动画。这可以为用户提供有趣的反馈，并实现快速的&流畅的用户界面。由于这些新动画大部分时间都是由 GPU 加速的硬件，它们无疑提高了新一代 HTML5 应用程序的质量。

根据 W3C 网站上的“CSS 动画模块级别 3”规范，CSS3 动画*引入了已定义的动画，这些动画指定了 CSS 属性在给定时间间隔内将取的值。该规范是对 CSS 转换*的扩展。

由于 CSS3 动画是 CSS3 转场的**扩展，你应该先在这里阅读我的同事大卫·卡图赫关于转场的文章:[CSS3 转场介绍](https://blogs.msdn.com/b/eternalcoding/archive/2011/11/01/css3-transitions.aspx)。**

在本文中，我们将看到一个有趣的演示，突出了 CSS3 动画的潜力，如何构建简单的动画以及如何在 JavaScript 中处理回退:

1.  [CSS3 动画](https://blogs.msdn.com/b/davrous/archive/2011/12/06/introduction-to-css3-animations.aspx#intro)
2.  [浏览器支持](https://blogs.msdn.com/b/davrous/archive/2011/12/06/introduction-to-css3-animations.aspx#support)
3.  [CSS3 动画 JavaScript 回退库](https://blogs.msdn.com/b/davrous/archive/2011/12/06/introduction-to-css3-animations.aspx#fallback)
4.  [结论](https://blogs.msdn.com/b/davrous/archive/2011/12/06/introduction-to-css3-animations.aspx#conclusion)

我们先从快速演示什么是 CSS3 动画开始。这里有一个星球大战 AT-AT 的示例动画，它使用 CSS3 动画制作运输工具的部分动画(如果您的浏览器不支持 CSS3 动画，它将退回到 JavaScript):

[https://david.blob.core.windows.net/html5/css3atat/index.htm](https://david.blob.core.windows.net/html5/css3atat/index.htm)

您也可以在一个单独的窗口中测试这个示例:[https://David . blob . core . windows . net/html 5/css3 tat/index . htm](https://david.blob.core.windows.net/html5/css3atat/index.htm "https://david.blob.core.windows.net/html5/css3atat/index.htm")

**注:** 这个样本已经在 IE10 PP3/PP4、Chrome 15、Firefox 8 & iPad 2 下用原生动画测试成功，在 IE9 桌面&移动(Windows Phone)下用 JS 回退测试成功。由于未知的原因，它在 Opera 11.50 下表现怪异，但在 11.60 下工作正常。此外，我们可爱的博客平台大部分时间都在通过 meta 标签强制 IE9 渲染引擎。要强制回到 IE10 标准模式，请按 F12 键，并将“文档模式”的值改回 IE10。否则，请在单独的窗口中查看演示。

这个样本基于安东尼·卡尔扎迪拉的出色工作。你可以在他的网站上查看其他令人难以置信的演示:https://www.anthonycalzadilla.com。我是使用 SVG & CSS3 动画的 [I twitty the fool](https://www.anthonycalzadilla.com/i-twitty-the-fool/) 示例的超级粉丝。

### CSS3 动画

#### 介绍

让我们先回顾一下你可以用什么来制作动画。CSS3 动画的工作原理与 CSS3 过渡基本相同。

他们在这里:

*   **颜色**:通过红色、绿色、蓝色和 alpha 分量进行插值(将每个分量视为一个数字，见下文)
*   **长度**:实数插补。
*   **百分比**:插值为实数。
*   **整数**:通过离散步长插值(整数)。插值发生在实数空间，并使用 floor()转换为整数。
*   **数字**:内插为实(浮点)数。
*   **转换列表**:参见 CSS 转换规范:【https://www.w3.org/TR/css3-2d-transforms/】T2
*   **矩形**:通过 x、y、宽度和高度分量进行插值(将每个分量视为一个数字)。
*   **可见性**:通过离散步长插值。插值发生在 0 和 1 之间的实数空间，其中 1 是“可见的”,所有其他值是“隐藏的”。
*   **阴影**:通过颜色、x、y 和模糊组件进行插值(在适当的地方将它们视为颜色和数字)。在有阴影列表的情况下，较短的列表在末尾用阴影填充，阴影的颜色是透明的，并且所有长度(x，y，模糊)都是 0。
*   **渐变**:通过每一站的位置和颜色进行插值。它们必须具有相同的类型(径向或线性)和相同的停止次数，才能设置动画。
*   **画图服务器** (SVG):插值只支持:渐变到渐变和颜色到颜色。然后他们如上所述工作。
*   **以上空格分隔的列表**:如果列表有相同数量的项目，列表中的每个项目都使用以上规则进行插值。否则，不插值。
*   **一个速记属性**:如果一个速记的所有部分都可以被动画化，那么就像每个属性都被单独指定一样进行插值。

动画必须支持以下属性:

*   背景色(*颜色*)
*   背景图像(*仅渐变*)
*   背景位置(*百分比和长度*
*   边框-底部-颜色(*颜色*
*   边框-底部-宽度(*长度*
*   边框颜色(*颜色*)
*   左边框颜色(*颜色*)
*   边框左侧宽度(*长度*
*   右边框颜色(*颜色*)
*   右边框宽度(*长度*
*   边框间距(*长度*
*   边框顶部颜色(*颜色*
*   边框顶部宽度(*长度*
*   边框宽度(*长度*
*   底部(*长度和百分比*
*   颜色(*颜色*)
*   裁剪(*矩形*)
*   字体大小(*长度和百分比*
*   字体粗细(*数字*)
*   网格-* ( *各种*)
*   高度(*长度和百分比*)
*   左侧(*长度和百分比*
*   字母间距(*长度*
*   行高(*数字、长度和百分比*)
*   边距-底部(*长度*
*   左边距(*长度*
*   右边距(*长度*
*   页边距-顶部(*长度*
*   最大高度(*长度和百分比*
*   最大宽度(*长度和百分比*
*   最小高度(*长度和百分比*
*   最小宽度(*长度和百分比*)
*   不透明度(*数字*
*   轮廓颜色(*颜色*)
*   轮廓偏移(*整数*)
*   轮廓宽度(*长度*
*   衬垫-底部(*长度*
*   左填充(*长度*)
*   右填充(*长度*)
*   填充顶部(*长度*
*   右(*长度和百分比*)
*   文本缩进(*长度和百分比*
*   文本-阴影(*阴影*)
*   顶部(*长度和百分比*
*   垂直对齐(*关键字，长度和百分比*)
*   能见度(*能见度*
*   宽度(*长度和百分比*)
*   单词间距(*长度和百分比*
*   z 索引(*整数*)
*   缩放(*号*)

#### 挽救（saving 的简写）

SVG 对象的属性在 SVG 规范中定义为 **animatable:true** 时是可动画的:【https://www.w3.org/TR/SVG/struct.html】[。但是在写这篇文章的时候，我还没有设法在任何最新版本的浏览器中将 CSS3 动画直接结合到 SVG 元素上。今天网上的例子做了一点小把戏:它们将 SVG 资源嵌入到不同的由 CSS3 制作的 DIV 动画中，比如](https://www.w3.org/TR/SVG/struct.html) [I twitty the fool](https://www.anthonycalzadilla.com/i-twitty-the-fool/) 例子。

#### 声明

要在 CSS 文件中声明一个动画，您需要编写以下通用代码:

```
@keyframes name_of_the_animation {

  from {

    property_to_animate: initial_value;

  }

  50% {

    property_to_animate: intermediate_value;

  }

  to {

    property_to_animate: final_value;

  }

}
```

也可以这样写:

```
@keyframes name_of_the_animation {

  0% {

    property_to_animate: initial_value;

  }

  50% {

    property_to_animate: intermediate_value;

  }

  100% {

    property_to_animate: final_value;

  }

}
```

这个动画定义声明了 3 个步骤 0、50%和 100%。你至少应该设置一个从***(或 0%)和一个*(或 100%)的步骤来构建一个正确的动画(因此最少 2 个步骤)。完成后，您可以添加任意数量的关键帧(介于 0%和 100%之间),以精确处理动画的各个步骤。****

 *一旦定义被声明，您可以使用经典的 CSS3 选择器将它影响到一个元素，并且您还需要配置动画选项。这里是你将看到的那种通用块:

```
#id_of_the_html_element {

    animation-name: name_of_the_animation;

    animation-duration: number_of_seconds s;

    animation-iteration-count: number | infinite;

}
```

为了更好地理解，我们来回顾一个真实的例子。首先，由于 CSS3 动画规范仍处于草案阶段，您需要使用合适的供应商前缀。那么，让我们使用 IE10 作为带有–ms 前缀的示例。现在让我们看看 AT-AT 的头部是如何运动的。

下面是动画声明:

```
@-ms-keyframes rotate-skull {

    0% {

        -ms-transform: rotate(0deg)

    }

    25% {

        -ms-transform: rotate(15deg)

    }

    50% {

        -ms-transform: rotate(-5deg)

    }

    55% {

        -ms-transform: rotate(0deg)

    }

    75% {

        -ms-transform: rotate(-10deg)

    }

    100% {

        -ms-transform: rotate(0deg)

    }

}
```

我们有 6 个步骤(0，25，50，55，75 & 100%)通过改变旋转值来处理 CSS3 2D 变换属性。

然后通过 CSS 规则应用动画:

```
#skull

{

    -ms-animation-name: rotate-skull;

    -ms-animation-duration: 7s;

    -ms-animation-iteration-count: infinite;

}
```

我们的目标是具有“`id=**skull**`”的`<div>`元素，我们在它上面应用了名为“ **`rotate-skull`** ”的动画。动画必须在**7 秒**内完成，并无限播放**次**。

如果你的浏览器支持 CSS3 动画，下面是真实的结果:

[https://david.blob.core.windows.net/html5/css3atat/AnimSkullATAT.htm](https://david.blob.core.windows.net/html5/css3atat/AnimSkullATAT.htm)

我们可以使用 animation 速记属性以更短的方式编写这条规则:

```
#skull {

    -ms-animation: rotate-skull 7s infinite;

}
```

一旦应用了匹配规则，就会触发动画。然后，您可以简单地通过 **JavaScript** 或通过 CSS3 来播放或停止动画，以播放受标签影响的**类。**

#### 非线性动画

如果您想要非线性动画，可以使用“动画-计时-功能”属性。您甚至可以在每个关键帧期间混合计时功能的类型。

基本上，CSS3 动画将使用[三次贝塞尔曲线](https://en.wikipedia.org/wiki/B%C3%A9zier_curve#Cubic_B.C3.A9zier_curves)通过在动画持续时间内计算不同的速度来平滑动画。

支持以下功能:

*   *线性*:恒速
*   *三次贝塞尔曲线*:速度将根据由两个控制点定义三次贝塞尔曲线来计算:P0 和 P1(所以你必须在这里定义 4 个值:P0x，P0y 和 P1x，P1y。
*   *ease* :速度会用三次贝塞尔(0.25，0.1，0.25，1)来计算
*   *渐增*:速度将通过三次贝塞尔曲线(0.42，0，1，1)计算
*   *渐出*:速度将通过三次贝塞尔曲线(0.42，0，0.58，1)计算
*   *缓出*:速度用三次贝塞尔曲线(0，0，0.58，1)计算

这里有一个由 [David Catuhe](https://blogs.msdn.com/b/eternalcoding/archive/2011/11/01/css3-transitions.aspx) 编写的模拟工具，它使用纯 JavaScript 来显示每个计时函数的影响:

[https://www.catuhe.com/msdn/transitions/easingfunctions.htm](https://www.catuhe.com/msdn/transitions/easingfunctions.htm)

**注:** 该工具使用 Firefox、Chrome、Opera 11.60 & IE9/10 支持的内嵌 SVG。因此，它在 iPad 上的 Opera 11.50 & Safari 下无法正常工作。

这是一个使用 SVG 的很棒的工具。你甚至可以在自定义函数上玩鼠标来编辑曲线。如果你想了解更多关于这个工具的信息，请再看看 David 的文章。

如果您的浏览器支持 CSS3 动画，现在让我们来看一个简单的演示，使用**缓动函数来制作包含 CSS3 动画精灵的画布标签**。

以下是将在本演示中使用的 CSS3 动画代码:

```
@-ms-keyframes demo {

    from {

    -ms-animation-timing-function: ease;

    -ms-transform: translateX(0px);

    }

    50% {

    -ms-animation-timing-function: ease-in;

    -ms-transform: translateX(300px);

    }

    to {

    -ms-animation-timing-function: ease-inout;

    -ms-transform: translateX(900px);

    }

}

#testCanvas

{

    -ms-animation-delay: 0s;

    -ms-animation-duration: 6s;

    -ms-animation-iteration-count: infinite;

    -ms-animation-name: demo;

}
```

以及所有供应商前缀变体，以确保它也可以在谷歌 Chrome 和 Mozilla Firefox 中工作。这是实时输出:

[https://david.blob.core.windows.net/html5/css3animcanvas/sprites.htm](https://david.blob.core.windows.net/html5/css3animcanvas/sprites.htm)

如果您的浏览器不支持 CSS3 动画，但支持 canvas，则应该显示精灵的运行动画，但角色不会移动通过屏幕的宽度。

**注:** 如果你想了解更多关于画布和精灵动画的知识，可以看看这篇文章: [HTML5 Gaming:用 EaselJS 在画布上制作精灵动画](https://blogs.msdn.com/b/davrous/archive/2011/07/21/html5-gaming-animating-sprites-in-canvas-with-easeljs.aspx)

#### 耽搁

“animation-delay”属性只允许动画在应用一段时间后开始执行。

#### 事件

3 **事件**可以在动画过程中引发。分别命名为“动画*开始*、“动画*结束*、“动画*迭代*”。例如，根据您的浏览器，正确的名称应该是:

*   chrome:*webkitAnimationEnd*
*   火狐:*mozamationend*
*   网络浏览器: *MSAnimationEnd*

该活动将为您提供以下详细信息:

*   *animationName* :引发事件的动画名称
*   *elapsedTime* :动画已经运行的时间，以秒为单位

以下是 IE10 的使用示例:

```
elementToAnimate.addEventListener("MSAnimationEnd", function () {

    alert("the end !");

}, false);
```

### 关于 CSS3 动画的更多信息

CSS3 动画非常有用，主要有两个原因:

*   **硬件加速:** CSS3 动画大部分时间由 GPU 直接处理，可以产生更平滑的结果。对于移动设备来说，这可能是一个非常有趣的方法。
*   代码和设计之间更好的分离:我知道在这一点上有一些争论，但是对于 David，我们认为开发人员不应该尽可能地了解动画或任何与设计相关的东西。同样，设计师/艺术家也不应该知道 JavaScript。CSS3 提供了这种可能性，可以让设计者用他们的经典工具在元素上、屏幕之间等产生适当的动画。

为了突出性能的重要性，我使用全帧

<canvas>: [HTML5 平台](https://blogs.msdn.com/b/davrous/archive/2011/09/09/html5-platformer-the-complete-port-of-the-xna-game-to-lt-canvas-gt-with-easeljs.aspx "https://blogs.msdn.com/b/davrous/archive/2011/09/09/html5-platformer-the-complete-port-of-the-xna-game-to-lt-canvas-gt-with-easeljs.aspx")编写的以下 HTML5 游戏在我的 PC 上以 IE9/IE10 的 60 fps 运行，但在 iPad 2 上最高以 10 fps 运行。这是因为它的 CPU 更加有限，而且 iPad 目前没有硬件加速<画布>。使用 CSS3 过渡/动画来制作几个更小的<画布>元素的动画可以为这个游戏提供巨大的性能提升。当您以移动设备为目标时，请想一想！</canvas>

### 浏览器支持

自从 IE10 的平台预览版 3 在 [Windows 开发者预览版](https://msdn.microsoft.com/en-us/windows/apps/br229516)中可用以来，我们支持 CSS3 动画。正如你在下面由 caniuse.com[制作的报告中所看到的，CSS3 动画现在被广泛的浏览器所支持:](https://caniuse.com/#search=CSS3%20animation)

![browser support](img/dbfd68aba1003c7e323281826ca0df71.png "fig2")

但是由于规范还没有完成(*工作草案*)，您必须使用供应商的前缀，例如–ms-、–moz-、–WebKit-、–o-来创建一个跨浏览器兼容的应用程序。

但问题可能是:如何处理不支持这一新功能的浏览器？

第一种选择是什么都不做。感谢优雅退化的美丽，如果你工作正常，你可以让用户只看到静态图像。这是安东尼的两个原始样本的例子:[我是傻瓜 Twitty！](https://www.anthonycalzadilla.com/i-twitty-the-fool/)和[纯 CSS3 AT-AT 行者](https://www.anthonycalzadilla.com/css3-ATAT/)。在 IE9 中观看时，看起来我们只有静态图像。当在 IE10 中观看时，完全相同的代码显示出漂亮的动画。届时，IE10 用户将拥有一个增强版，而 IE9 仍能正常浏览和使用该网站。你的浏览器越现代，你的视觉效果就越好。

第二种选择是通过像 Modernizr 这样的 JS 库来检测这个特性，并尝试通过一个模仿动画的 JavaScript 库来提供相同的动画。这就是我们通常所说的回退机制。不幸的是，我今天还没有找到一个完整的 JS 库，可以在浏览器不支持 CSS3 动画时替代它。

然后，我编写了一个或多或少专门为 AT-AT 示例设计的示例 JS 库。

### CSS3 动画 JavaScript 后备库

动画只不过是由关键帧定义的特定持续时间分隔的一系列过渡。然后，我重用了 David Catuhe 在他的过渡助手库中构建的概念。我让您阅读他的文章，以检查代码背后的概念基础。

在我这边，我已经添加了一些支持动画的 CSS3 2D 变换旋转和平移值，以及一种迭代关键帧的方法。

以下是您需要查看的库的主要部分:

```
// Animation object

// It need the HTML targeted element, the name of the animation, its duration & iteration count and

// the keyframes contained in an array object

// View the animation simply as a sequence of transitions played a certain number of times

ANIMATIONSHELPER.animation = function (target, name, duration, iterationcount, keyframes) {

    // saving the properties values

    this.name = name;

    this.duration = duration;

    this.iterationcount = iterationcount;

    this.target = target;

    var elapsedtime = 0;

    var keyframeduration = 0;

    var elapsedtime = 0;

    // Transforming the percentage of each keyframe into duration value

    for (var i = 0; i < keyframes.length; i++) {

        keyframeduration = ((keyframes[i].percentage * duration) / 100) - elapsedtime;

        keyframes[i].duration = keyframeduration;

        elapsedtime += keyframeduration;

    }

    this.currentTransition = { isPlaying: false };

    this.keyframes = keyframes;

    this.keyframesCount = keyframes.length;

    this.currentKeyFrameIndex = 0;

    // The nextTransition() function return the next transition to run

    // based on the current keyframe to play

    this.nextTransition = function (keyframe, ease, customEaseP1X, customEaseP1Y, customEaseP2X, customEaseP2Y) {

        var properties = [];

        var finalValues = [];

        var transition;

        // Compared to the original TRANSITIONSHELPER of David Catuhe

        // We need a specific code to play with the CSS3 2D Transform properties values

        if (keyframe.propertyToAnimate === "transform") {

            for (var i = 0; i < keyframe.transformType.length; i++) {

                properties.push(keyframe.transformType[i].type);

                if (keyframe.transformType[i].type == "rotate") {

                    finalValues.push({ deg: keyframe.transformType[i].value1 });

                }

                else {

                    finalValues.push({ x: keyframe.transformType[i].value1, y: keyframe.transformType[i].value2 });

                }

            }

            // Create a new transition

            transition = {

                name: this.name + this.currentKeyFrameIndex,

                target: this.target,

                properties: properties,

                finalValues: finalValues,

                originalValues: ANIMATIONSHELPER.extractValues(target.style[ANIMATIONSHELPER.currentTransformProperty], this.name),

                duration: keyframe.duration,

                startDate: (new Date).getTime(),

                currentDate: (new Date).getTime(),

                ease: ease,

                customEaseP1X: customEaseP1X,

                customEaseP2X: customEaseP2X,

                customEaseP1Y: customEaseP1Y,

                customEaseP2Y: customEaseP2Y,

                isPlaying: true,

                type: "transform"

            };

            return transition;

        }

        // If it's a classic property to animate, we're using more or less the TRANSITIONSHELPER as-is

        else {

            return TRANSITIONSHELPER.transition(this.target, keyframe.propertyToAnimate, keyframe.value, keyframe.duration, TRANSITIONSHELPER.easingFunctions.linear);

        }

    };

    // each animation object has a tick function

    // that will be called every 17 ms (to target 60 fps)

    // This ticker is monitoring the current state of the transition and

    // create a new transition as soon as the old one is finished/dead

    this.tick = function () {

        if (this.iterationcount > 0) {

            if (!this.currentTransition.isPlaying) {

                this.currentTransition = this.nextTransition(this.keyframes[this.currentKeyFrameIndex], ANIMATIONSHELPER.easingFunctions.linear);

                // We're using our own global ticker only for the 2D transformations

                // Otherwise, we're using the one from the TRANSITIONSHELPER library

                if (this.currentTransition.type === "transform") {

                    ANIMATIONSHELPER.currentTransitions.push(this.currentTransition);

                }

                this.currentKeyFrameIndex++;

                // We've reached the last keyframe (100%). We're starting back from the beginning

                if (this.currentKeyFrameIndex >= this.keyframesCount) {

                    this.currentKeyFrameIndex = 0;

                    this.iterationcount--;

                }

            }

        }

    };

};
```

代码的第一部分是遍历每个关键帧，计算每个百分比指定的确切持续时间。然后我们定义一个`nextTransition()`函数，它将根据关键帧集合中的当前索引动态构建下一个要播放的过渡。最后，我们得到了一个`tick()`函数，它将监视所应用的转换的当前状态。一旦过渡结束或终止，它会请求下一个过渡，将其推入要播放的过渡堆栈，并移动索引。

这个`tick()`函数的调用得益于这段代码:

```
ANIMATIONSHELPER.launchAnimation = function (animation) {

    // Launching the tick service if required

    if (ANIMATIONSHELPER.tickIntervalID == 0) {

        ANIMATIONSHELPER.tickIntervalID = setInterval(ANIMATIONSHELPER.tick, 17);

    }

    // Little closure to launch the tick method on the appropriate animation instance

    setInterval(function () { animation.tick(); }, 17);

};
```

最后，我们有了这样的代码来帮助我们构建关键帧:

```
// Object to build a new generic keyframe (not working on the CSS3 2D Transform properties thus)

ANIMATIONSHELPER.keyframe = function (percentage, propertyToAnimate, value) {

    this.percentage = percentage;

    this.propertyToAnimate = propertyToAnimate;

    this.value = value;

};

//Objects to build specific rotation keyframes

ANIMATIONSHELPER.rotationkeyframe = function (percentage, value) {

    this.percentage = percentage;

    this.propertyToAnimate = "transform";

    this.transformType = [];

    this.transformType.push(new ANIMATIONSHELPER.transformType("rotate", value));

};
```

为了突出它的用法，让我们用这个库重新创建以前的简单 CSS3 动画头骨示例:

```
// number of times you'd like the animations to be run

var iterationsNumber = 100;

var skullElement = document.getElementById("skull");

var keyframes = [];

keyframes.push(new ANIMATIONSHELPER.rotationkeyframe(25, 15));

keyframes.push(new ANIMATIONSHELPER.rotationkeyframe(50, -5));

keyframes.push(new ANIMATIONSHELPER.rotationkeyframe(55, 0));

keyframes.push(new ANIMATIONSHELPER.rotationkeyframe(75, -10));

keyframes.push(new ANIMATIONSHELPER.rotationkeyframe(100, 0));

var animation1 = new ANIMATIONSHELPER.animation(skullElement, "rotate-skull", 7000,

                            iterationsNumber, keyframes);

ANIMATIONSHELPER.launchAnimation(animation1, ANIMATIONSHELPER.easingFunctions.linear);
```

下面是现在可以在所有支持 CSS3 2D 变换的浏览器中运行的结果:

[https://david.blob.core.windows.net/html5/css3atat/indexSkullJS.htm](https://david.blob.core.windows.net/html5/css3atat/indexSkullJS.htm)

最后，本文开头演示的第一个示例使用 Modernizr 来检查对 CSS3 动画的支持。如果不是这样，它将加载模拟 master.css、moz-master.css & ms-master.css 文件中定义的关键帧的代码:

```
// Checking if CSS3 animations is supported

if (!Modernizr.cssanimations) {

// if so, we can use our JS fallback library

    supportElement.innerHTML = "CSS3 Animations <strong>are not supported</strong>";

    LoadJSAnimationsFallback();

}

else {

    // if CSS3 animation is supported, we have nothing to do.

    // The *master.css stylesheets will be automatically applied & used.

    supportElement.innerHTML = "CSS3 Animations <strong>are supported</strong>";

}
```

*loadjsanimationfallback()*函数被定义在 *jsfallback-master.js* 中，该函数仅包含所有关键帧声明和模仿 Anthony 在纯 CSS3 中创建的行为所需的 19 个动画。在这种方法中，设计者需要使用库重写所有规则。另一种方法是使用 XHR 调用解析其中一个 CSS 文件，并动态创建对库的 JavaScript 调用。这需要更多的工作，因为你几乎需要用 JavaScript 重新实现 CSS3 动画规范！

现在，您已经知道如何在开始使用最新的 CSS3 规范的同时，构建一个后备机制来支持更多的浏览器。

您可以从这里下载主示例的文件:[https://David . blob . core . windows . net/html 5/css3 tat/css3 atonminified . zip](https://david.blob.core.windows.net/html5/css3atat/CSS3ATATNonMinified.zip "https://david.blob.core.windows.net/html5/css3atat/CSS3ATATNonMinified.zip")

它包含 animationsHelper.js、transitionsHelper.js、jsfallback-master.js JavaScript 文件的未合并版本，以及主要供应商前缀的各种 CSS3 declinaison 文件。

### 结论

CSS3 动画是将 HTML5 应用程序推向新水平的强大技术。它提供了有趣的场景。设计师可以使用它来创建新一代的 UI 屏幕，具有平滑流畅的动画，而不需要开发人员。由于它大部分时间都是硬件加速的，开发人员也应该注意这个规范。最终，双方可以合作了。设计师可以制作一系列涵盖大多数场景的预定义动画。开发人员可以创建 JavaScript 库来实现这些动画。这个库可以以一种透明的方式提供 2 种实现:动态生成 CSS3，或者为旧的浏览器提供一个后备。

### 更进一步

*   David Catuhe 关于 CSS3 过渡的文章:[CSS3 过渡介绍](https://blogs.msdn.com/b/eternalcoding/archive/2011/11/01/css3-transitions.aspx)
*   CSS3 动画规格:[https://www.w3.org/TR/css3-animations/](https://www.w3.org/TR/css3-animations/ "https://www.w3.org/TR/css3-animations/")
*   IE 试驾 CSS3 动画:[https://IE . Microsoft . com/Test Drive/Graphics/hands-on-CSS3/hands-on _ animations . htm](https://ie.microsoft.com/testdrive/Graphics/hands-on-css3/hands-on_animations.htm "https://ie.microsoft.com/testdrive/Graphics/hands-on-css3/hands-on_animations.htm")

**其他有用的帖子:**

*   CSS3 动画规范中的事件相对有限。Joe Lambert 正在提出一个有趣的解决方案来触发每个关键帧上的事件: [CSS 动画关键帧事件(Javascript 解决方案)](https://blog.joelambert.co.uk/2011/05/20/css-animation-keyframe-events-javascript-solution/)
*   [CSS3 动画教程](https://www.sitepoint.com/playing-with-fire-organic-css3-animation/ "CSS3 animation tutorial") form SitePoint 自己的首席设计师 Alex Walker，例子满满。

如果你喜欢读这篇文章，你会喜欢[可学的](https://learnable.com/)；向大师们学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和互动在线课程，如 [Learn CSS3](https://learnable.com/courses/learn-css3-203) 。* 

## *分享这篇文章*