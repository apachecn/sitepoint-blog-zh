# 使用 Velocity.js(无 jQuery)使您的网站具有交互性和趣味性

> 原文：<https://www.sitepoint.com/how-to-use-velocity-js-without-jquery/>

![Get Up and Running with Velocity.js at Warp Speed (No jQuery)](img/7a09f72e9eb3c74b7aed9653ff8b1b73.png)

 *我特别感谢 [James Hibbard](https://www.sitepoint.com/author/jhibbard/) 和 Velocity 背后的开发人员审阅本文。也感谢多年来为 Velocity 做出贡献的用户和开发人员。*

在本文中，我将介绍 Velocity.js，这是一个由 Julian Shapiro 开发的快速高效的 JavaScript 动画引擎。当你完成所有的演示时，你将能够使用 Velocity.js 创建你自己的动画，并使你的网站更具交互性和用户友好性。所有这些都不需要使用 jQuery，只需要普通的 JavaScript。

这是 CSS:动态 DOM 动画库系列*的第三篇文章。*

以下是我已经介绍过的内容:

*   [*用 Anime.js 制作 DOM 动画*](https://www.sitepoint.com/animating-the-dom-with-anime-js/) 讲述了如何最好地使用 web 上的动画，以及何时可以考虑使用 JavaScript 动画库来代替仅使用 CSS 的动画。然后重点介绍 Anime.js，这是一个免费的轻量级 JavaScript 动画库
*   [KUTE.js 的有趣动画效果](https://www.sitepoint.com/fun-web-animation-effects-with-kute-js/)向您介绍 kute . js，这是一个免费且功能丰富的 JavaScript 动画库。

## 用 Velocity.js 可以制作什么动画

Velocity.js 是一个强大的库，它让 DOM 唾手可得！它可以让您制作动画:

*   CSS 动画属性的数值，包括颜色
*   转换
*   SVG 属性
*   相对于页面或页面内的容器元素的滚动事件
*   淡化和幻灯片动画。

一般来说，Velocity 一次激活一个数字属性值。例如，如果你想沿着 X 和 Y 坐标平移一个元素，你不能使用类似于`translate['10px', '15px']`的东西。相反，你应该伴随着*翻译*属性及其对应的轴，就像这样:`translateX: '10px', translateY: '15px'`。有一个叫做*强制进给*的特性，Velocity 可以让你同时指定两个值。我将在本文后面介绍强制进食。

## 选择

Velocity 的选项对象为你制作动画提供了相当大的灵活性。

下面是您将在本文的演示中看到的选项列表:

*   **时长**:每个动画持续多长时间。持续时间的度量单位是毫秒
*   **缓动** : Velocity 支持大多数 jQuery UI 缓动类型，CSS3 缓动，即“缓动”、“缓入”、“缓出”和“缓出”，贝塞尔曲线，步进缓动，甚至酷酷的 spring physics。你可以玩这个[演示](http://codepen.io/julianshapiro/pen/hyeDg)来看看弹簧物理选项的运行
*   **循环**:动画应该重复多少次。如果您将此选项设置为`true`，它将无限期运行
*   **延迟**:动画开始前等待多长时间。

选项的完整列表可在 [Velocity 的文档](http://velocityjs.org/#duration)页面上找到。

## 句法

如果你是一个 jQuery 用户，Velocity.js 会让事情变得简单。事实上，Velocity 和 jQuery 有相同的 API。要开始使用:

> 下载 Velocity，将其包含在您的页面中，并用`$.velocity()`替换 jQuery 的所有实例`$.animate()`。
> 
> [Velocity.js 文档](http://velocityjs.org/)

然而，您不需要 jQuery 来使用 Velocity，并且您也不打算在本文的演示中使用 jQuery。如果包含 jQuery，语法将与您使用的略有不同。它看起来是这样的:

```
Velocity(element, {property: value}, {option: optionValue});
```

要在同一元素上链接另一个动画，只需在前一个速度调用之后添加另一个速度调用:

```
Velocity(element1, {property: value}, {option: optionValue});
Velocity(element1, {property: value}, {option: optionValue});
```

要将一个动画同时应用到多个元素，只需将所有元素缓存到一个变量中，并对该变量调用 Velocity，无需编写自己的循环。例如:

```
const elements = document.querySelectorAll('<div>');
Velocity(elements, {property: value}, {option: optionValue});
```

可以用`px`、`%`、`rem`、`em`、`vw/vh`、`deg`。如果你不添加单位，Velocity 会为你假设一个合适的单位，通常是`px`。

Velocity 还支持使用 *+* 、*–*、 *** 和 */* 的运算，以及在每个运算符后添加等号( *=* )的相关数学运算，例如`'+=3em'`

### Velocity.js Forcefeeding:传递初始值

不用让 Velocity.js 查询 DOM 来获取元素的初始值，您可以使用以下语法自己设置它:

```
Velocity(element, {
  translateX: [endValue, startValue],
  backgroundColor: [endValue, easing, startValue]
}, {
  //options here
});
```

在这种情况下，您传递两个(可选三个)项目的数组:

*   第**项**是动画的**最终状态**的值
*   第**第二**可选项目是应用于该特定属性的**缓和**选项
*   您的补间动画的**开始状态**，即您在动画开始时为该特定属性显式设置的值，是数组中的**最后一项**。

你可以在 [Velocity 的文档](http://velocityjs.org/#forcefeeding)上阅读更多关于强制进给的内容。

### 控制 Velocity.js 动画

您可以使用以下语法停止、暂停、反转和恢复元素上的所有 Velocity 调用:

*   停止:`Velocity(elem, 'stop');`
*   暂停:`Velocity(elem, 'pause');`
*   要反转:`Velocity(elem, 'reverse');`
*   继续:`Velocity(elem, 'resume');`

有了这些基本说明，您就可以开始研究一些实际的例子了。

## 演示:落球

从一个简单的球从页面顶部落下开始。

```
const ball = document.querySelector('.ball');

Velocity(ball, {
  translateY: '130px'
}, {
  easing: [1000, 20],
  duration: 2000
});
```

上面的代码选择了一个类为`.ball`的 HTML 元素，在 2 秒钟内将它沿 Y 轴平移 130px(只是为了演示，否则我推荐一个更短的持续时间),运动随着它的下落而加速，并在最后反弹。

你可以用速度**弹簧物理**作为缓动选项的数组值来快速实现:第一个数组项代表*张力*，第二个代表*摩擦力*。一个**高张力**值增加总速度和弹性(默认为 500)，一个**低摩擦**值增加最后的振动速度(默认为 20)。

为了好玩，让球的背景颜色从蓝色的初始值变成深色。要设置背景颜色的初始值，需要使用 **Velocity.js forcefeed** :

```
Velocity(ball, {
  translateY: '130px',
  //array items: endValue, startValue
  backgroundColor : ['#222', '#043d99']
}, {
  //options here
});
```

这就是 Velocity.js 的基本实现。请使用下面的代码:

参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )用 Velocity.js 的笔[简单落球。](http://codepen.io/SitePoint/pen/mWYKrR/)