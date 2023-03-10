# 如何开始使用 CSS 动画

> 原文：<https://www.sitepoint.com/how-to-get-started-with-css-animation/>

*以下是蒂芙尼新书《CSS 大师，第二版》的简短摘录。*

把 CSS 动画想象成 CSS 过渡的更复杂的姐妹。动画在几个关键方面不同于过渡:

*   动画不会优雅地退化。如果没有浏览器的支持，用户就不走运了。另一种方法是使用 JavaScript。
*   动画可以重复，并且可以无限重复。跃迁总是有限的。
*   动画使用关键帧，这提供了创建更复杂和微妙的效果的能力。
*   动画可以在播放周期的中间暂停。

所有主流浏览器的最新版本都支持 CSS 动画。火狐 15 及更早版本需要一个`-moz-`前缀；以后的版本不要。Internet Explorer 版本 10 和 11 也支持不带前缀的动画，所有版本的 Microsoft Edge 也是如此。

我们可以用几种方法来检查 CSS 动画支持。第一种是通过测试作为`window`对象的方法的`CSSKeyframeRule`的存在:

```
const hasAnimations = 'CSSKeyframeRule' in window; 
```

如果浏览器支持`@supports`规则和`CSS.supports()` API，我们可以使用:

```
const hasAnimations = CSS.supports('animation-duration: 2s'); 
```

与过渡一样，我们只能动画显示可插值的值，如颜色值、长度和百分比。

### 创作你的第一部动画

我们首先必须使用`@keyframes`规则定义一个动画。`@keyframes`规则有两个目的:

*   设置我们动画的名称
*   分组我们的关键帧规则

让我们创建一个名为`pulse`的动画:

```
@keyframes pulse {

} 
```

我们的关键帧将在这个块中定义。在动画中，**关键帧**是动作发生变化的点。特别是对于 CSS 动画，关键帧规则用于设置动画周期中特定点的属性值。介于关键帧规则中的值之间的值将被插值。

一个动画至少需要两个关键帧:一个是`from`关键帧，它是我们动画的开始状态，另一个是`to`帧，它是动画的结束状态。在每个单独的关键帧块中，我们可以定义要制作动画的属性:

```
@keyframes pulse {
    from {
        transform: scale(0.5);
        opacity: .8;
    }

    to {
        transform: scale(1);
        opacity: 1;
    }
} 
```

这段代码将把我们的对象从它的一半大小缩放到它的最大大小，并将不透明度从 80%更改为 100%。

然而，`keyframes`规则只为*定义了*一个动画。它本身不会使元素移动。我们需要应用它。让我们也定义一个`pulse`类，我们可以用它将动画添加到任何元素中:

```
.pulse {
    animation: pulse 500ms;
} 
```

这里，我们使用了`animation`速记属性来设置动画名称和持续时间。为了播放动画，我们需要一个`@keyframes`规则的名称(在这里是`pulse`)和一个持续时间。其他属性是可选的。

`animation`的属性顺序与`transition`相似。第一个可以解析的值成为`animation-duration`的值。第二个值成为`animation-delay`的值。不是 CSS 范围的关键字或动画属性关键字值的单词被假定为`@keyframe`规则集名称。

与`transition`一样，`animation`也接受动画列表。动画列表是以逗号分隔的值列表。例如，我们可以将我们的脉冲动画分成两个规则— `pulse`和`fade`:

```
@keyframes pulse {
    from {
        transform: scale(0.5);
    }       
    to {
        transform: scale(1);
    }
}

@keyframes fade {
    from {
        opacity: .5;
    }
    to {
        opacity: 1;
    }
} 
```

然后我们可以将它们组合成一个动画的一部分:

```
.pulse-and-fade {
    animation: pulse 500ms, fade 500ms;
} 
```

### 动画属性

虽然使用`animation`属性更短，但有时手写属性更清晰。下面列出了手写动画属性:

| 财产 | 描述 | 基础资料 |
| --- | --- | --- |
| `animation-delay` | 执行动画之前要等待多长时间 | `0s`(立即执行) |
| `animation-duration` | 一个动画的周期应该持续多久 | `0s`(无动画出现) |
| `animation-name` | 一个`@keyframes`规则的名称 | 没有人 |
| `animation-timing-function` | 如何计算起始状态和结束状态之间的值 | `ease` |
| `animation-iteration-count` | 动画重复播放多少次 | one |
| `animation-direction` | 动画是否应该反向播放 | `normal`(无反转) |
| `animation-play-state` | 动画是正在运行还是暂停 | `running` |
| `animation-fill-mode` | 指定当动画不运行时应用哪些属性值 | `none` |

`animation-delay`和`animation-duration`属性的功能类似于`transition-delay`和`transition-duration`。两者都接受时间单位作为值，可以是秒(`s`)或毫秒(`ms`)。负时间值对`animation-delay`有效，但对`animation-duration`无效。

让我们使用手写属性重写我们的`.pulse`规则集。这样做可以得到以下结果:

```
.pulse {
    animation-name: pulse;
    animation-duration: 500ms;
} 
```

`animation-name`属性相当简单。它的值可以是`none`或者是`@keyframes`规则的名称。动画名字限制很少。禁止使用`initial`、`inherit`、`default`、`none`等 CSS 关键字。大多数标点符号都不起作用，而字母、下划线、数字和表情符号(以及其他 Unicode)通常可以。为了清晰和可维护性，给你的动画起一个描述性的名字是个好主意，避免使用 CSS 属性或者表情符号作为名字。

### 循环还是不循环:`animation-iteration-count`属性

如果您跟随自己的代码，您会注意到这个动画只出现一次。我们希望我们的动画能够重复。为此，我们需要`animation-iteration-count`属性。

`animation-iteration-count`属性接受大多数数值。整数和小数都是有效值。然而，对于十进制数，动画将在最后一个动画周期中途停止，以`to`状态结束。负的`animation-iteration-count`值被视为与`1`相同。

要让动画无限期运行，请使用`infinite`关键字。动画将会播放无数次。当然，`infinite`实际上是指直到文档被卸载、浏览器窗口关闭、动画样式被移除或者设备关闭。让我们的动画变得无限:

```
.pulse {
    animation-name: pulse;
    animation-duration: 500ms;
    animation-iteration-count: infinite;
} 
```

或者，使用`animation`速记属性:

```
.pulse {
    animation: pulse 500ms infinite;
} 
```

### 播放动画:`animation-direction`属性

然而，我们的动画还有一个问题。与其说是*脉冲*，不如说是重复我们的放大动画。我们想要的是这个元素可以放大和缩小。进入`animation-direction`属性。

`animation-direction`属性接受四个值之一:

*   `normal`:初始值，按规定播放动画
*   `reverse`:翻转`from`和`to`状态，反向播放动画
*   `alternate`:反向播放偶数个动画循环
*   `alternate-reverse`:反向播放奇数动画循环

继续我们当前的例子，`reverse`将我们的对象缩小 0.5 倍。使用`alternate`可以放大奇数周期的对象，缩小偶数周期的对象。相反，使用`alternate-reverse`将缩小奇数周期的对象，放大偶数周期的对象。因为这是我们想要的效果，我们将把我们的`animation-direction`属性设置为`alternate-reverse`:

```
.pulse {
    animation-name: pulse;
    animation-duration: 500ms;
    animation-iteration-count: infinite;
    animation-direction: alternate-reverse;
} 
```

或者，使用速记属性:

```
.pulse {
    animation: pulse 500ms infinite alternate-reverse;
} 
```

### 使用百分比关键帧

我们之前的例子是一个简单的脉冲动画。我们可以使用百分比关键帧创建更复杂的动画序列。与使用`from`和`to`不同，**百分比关键帧**指示动画过程中的特定变化点。下面是一个使用名为`wiggle`的动画的例子:

```
@keyframes wiggle {
    25% {
        transform: scale(.5) skewX(-5deg) rotate(-5deg);
    }
    50% {
        transform: skewY(5deg) rotate(5deg);
    }
    75% {
        transform: skewX(-5deg) rotate(-5deg) scale(1.5);
    }
    100% {
        transform: scale(1.5);
    }
 } 
```

我们在这里使用了 25%的增量，但这些关键帧可能是 5%、10%或 33.2%。随着动画的播放，浏览器将在每个状态之间插入值。和前面的例子一样，我们可以将它分配给一个选择器:

```
/* Our animation will play once */
 .wiggle {
    animation-name: wiggle;
    animation-duration: 500ms;
} 
```

或者使用`animation`速记属性:

```
.wiggle {
    animation: wiggle 500ms;
} 
```

这里只有一个问题。当我们的动画结束时，它回到原始的，动画前的状态。要防止这种情况，请使用`animation-fill-mode`属性。

### `animation-fill-mode`属性

动画在开始播放之前或停止播放之后对属性没有影响。但是正如你在`wiggle`例子中看到的，一旦一个动画结束，它就会恢复到动画前的状态。有了`animation-fill-mode`，我们可以在动画开始和结束之前填充那些状态。

`animation-fill-mode`属性接受四个值之一:

*   不执行时，动画没有任何效果
*   `forwards`:动画结束时，结束状态的属性值仍然适用
*   `backwards`:第一个关键帧的属性值将在动画延迟期间应用
*   `both`:适用于`forwards`和`backwards`的效果

因为我们希望动画元素保持最终的放大状态，所以我们将使用`animation-fill-mode: forwards`。(`animation-fill-mode: both`也行。)

当`animation-delay`属性设置为`500ms`或更高时，`animation-fill-mode: backwards`的效果最为明显。当`animation-fill-mode`设置为`backwards`时，第一个关键帧的属性值被应用，但是动画直到延迟过去才被执行。

### 暂停动画

如前所述，动画可以暂停。转换可以中途反转，或者通过切换类名完全停止。另一方面，动画可以使用`animation-play-state`在播放周期的中途暂停。它有两个定义值——`running`和`paused`——初始值为`running`。

让我们看一个使用`animation-play-state`播放或暂停动画的简单例子。首先，我们的 CSS:

```
.wobble {
    animation: wobble 3s ease-in infinite forwards alternate;
    animation-play-state: paused;
}  
.running {
    animation-play-state: running;
} 
```

这里，我们有两个声明块:`wobble`，它定义了一个摆动动画，和`running`，它设置了一个播放状态。作为我们的`animation`声明的一部分，我们设置了`paused`的`animation-play-state`值。为了运行我们的动画，我们将添加`running`类到我们的元素中。让我们假设我们的标记包括一个**运行**动画按钮，其`id`为`trigger`:

```
const trigger = document.querySelector('#trigger');
const moveIt = document.querySelector('.wobble');

trigger.addEventListener('click', function() {
    moveIt.classList.toggle('running');
}); 
```

将`.running`添加到我们的元素中会覆盖在`.wobble`中设置的`animation-play-state`值，并播放动画。

### 检测动画开始、结束或重复的时间

像过渡一样，动画在结束时触发一个事件:`animationend`。与转场不同，动画还会在开始重复时触发`animationstart`和`animationiteration`事件。与转换一样，您可以使用这些事件来触发页面上的另一个动作。也许你会用`animationstart`来显示一个**停止动画**按钮，或者用`animationend`来显示一个**重放**按钮。

我们可以用 JavaScript 监听这些事件。下面，我们正在收听`animationend`事件:

```
const animate = document.getElementById('animate');

animate.addEventListener('animationend', function(eventObject) {
    // Do something
}); 
```

这里，事件处理函数也接收一个事件对象作为它的唯一参数。为了确定哪个动画结束了，我们可以查询事件对象的`animationName`属性。

## 关于可访问性的注释

过渡和动画可以通过使交互流畅而不是跳跃来增强用户体验，或者给界面带来愉悦。但是它们仍然存在可访问性风险。例如，大型旋转动画可能会导致前庭障碍患者头晕或恶心，如眩晕[5]。闪烁的动画会引发一些光敏性癫痫患者的癫痫发作[6]。谨慎使用它们，并强烈建议用户能够关闭它们。我们将在第章“有条件地应用 CSS”中讨论一种方法，即`prefers-reduced-motion`媒体查询。

## 关于性能的注释

有些属性比其他属性创建的过渡和动画效果更好。如果动画更新了触发回流或重绘的属性，它在低功耗设备(如手机和平板电脑)上的性能可能会很差。

触发回流的属性是影响布局的属性。这些属性包括以下可设置动画的属性:

*   `border-width`(和`border-*-width`属性)
*   `border`(和`border-*`属性)
*   `bottom`
*   `font-size`
*   `font-weight`
*   `height`
*   `left`
*   `line-height`
*   `margin`(和`margin-*`属性)
*   `min-height`
*   `min-width`
*   `max-height`
*   `max-width`
*   `padding`(和`padding-*`属性)
*   `right`
*   `top`
*   `vertical-align`
*   `width`

当这些属性被激活时，浏览器必须重新计算受影响元素(通常是相邻元素)的大小和位置。尽可能使用变换。平移变换的过渡或动画(例如，`transform: translate(100px,200px)`)可以替换`top`、`left`、`right`和`bottom`属性。在某些情况下，`height`和`width`动画可以替换为`scale`变换。

有时，触发回流(或布局更新)是不可避免的。在这些情况下，尽量减少受影响的元素数量，并使用技巧(如负延迟)来缩短感知的动画持续时间。

触发重画的属性通常是那些导致颜色变化的属性。其中包括:

*   `background`
*   `background-image`
*   `background-position`
*   `background-repeat`
*   `background-size`
*   `border-radius`
*   `border-style`
*   `box-shadow`
*   `color`
*   `outline`
*   `outline-color`
*   `outline-style`
*   `outline-width`

对这些属性的更改比那些影响布局的更改的计算成本要低，但是它们仍然有成本。对`box-shadow`和`border-radius`的改变计算起来特别昂贵，尤其是对于低功率器件。制作这些属性的动画时要小心。

## 分享这篇文章