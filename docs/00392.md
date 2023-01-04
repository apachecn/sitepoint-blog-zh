# GreenSock 3 网络动画:了解 GSAP 的新功能

> 原文：<https://www.sitepoint.com/greensock-3-animation-features/>

【2019 年 11 月 1 日，[绿巨人动画平台(GSAP)](https://greensock.com/) 发布了第 3 版，这是其迄今为止最重大的一次升级。

GSAP 是一个强大的、向后兼容的、高性能的、成熟的 JavaScript 动画库，它允许你制作网络上几乎任何东西的动画——比如 DOM 元素、JS 对象、SVG、CSS 属性等等。我们以前报道过 GSAP，例如在[我们的初学者系列](https://www.sitepoint.com/web-animation-tutorial-part-1/)和[插件指南](https://www.sitepoint.com/fancy-web-animations-made-easy-greensock-plugins/)中，我们是他们的忠实粉丝。

其直观的语法使开发人员能够在相对较短的时间内用最少的代码创建一些令人兴奋的动画效果。

在这篇文章中，我将重点介绍 GreenSock 3 的一些伟大的新功能，并让您开始了解如何使用它们来让事情在 web 上迅速发展。

## GreenSock 3 的新功能

### 更多功能和更小的文件大小

GreenSock 是使用 ES6 模块从零开始重建的。它仍然是功能齐全的，有 50 多个新的库功能，但它只有一半的大小！

### 快速编码的精简语法

如果你以前用过 GSAP，你应该已经熟悉它直观的、初学者友好的语法。新的 API 更加简化，学习和使用起来更快。

例如，现在您不需要决定是要使用 TweenLite 还是 TweenMax、TimelineLite 还是 TimelineMax。只有一个对象，即`gsap`对象:

```
// tween
gsap.to(selector, {})

// timeline
const tl = gsap.timeline() 
```

另外，`duration`不再是`to()`、`from()`或`fromTo()`方法的参数，而是被设置在`vars`对象内部。尤其是老年人

```
TweenMax.to(selector, 1, {}) 
```

成为

```
gsap.to(selector, {
  duration: 1
}) 
```

可读性更强，也更灵活。事实上，例如，您现在可以使用函数来设置`duration`。

这个新的、简化的 API 的另一个好处是，编写惊人的动画轻而易举。不需要使用旧的`staggerTo()`、`staggerFrom()`和`staggerFromTo()`方法，因为您可以将交错直接添加到常规补间的`vars`对象中:

```
gsap.to(selector, {
  x: 50,
  duration: 1,
  stagger: 0.5
}) 
```

为了获得更大的灵活性，您可以像这样使用交错对象:

```
gsap.to(selector, {
  x: 50,
  duration: 1,
  stagger: {
    amount: 2,
    from: 'center'
  }
}) 
```

最后，GSAP 的例子总是很棒，但是有点冗长。现在不行。GreenSock 3 为设置缓动提供了更简洁、更易读的语法。例如，代替:

```
Elastic.easeOut
Elastic.easeIn
Elastic.easeInOut 
```

你只需写下:

```
'elastic'  // same as 'elastic.out'
'elastic.in'  
'elastic.inOut' 
```

另一个简化是可以用符号`<`代替标签来表示最近添加的动画的开始时间，用符号`>`来表示最近添加的动画的结束时间。以下是如何在时间线中使用标签的示例:

```
gsap.timeline()
  .add('start')
  // this tween starts at the beginning of the
  // entire animation
  .to(selector, {}, 'start')
  // this tween starts 0.5 seconds after
  // the previous tween
  .to(selector, {}, 'start+=0.5') 
```

现在，您可以按如下方式重写上面的代码:

```
gsap.timeline()
  .to(selector, {}, '<')
  .to(selector, {}, '<0.5') 
```

### 时间线的可继承默认值

如果时间轴中的某些设置值在整个子补间中保持不变，那么必须一遍又一遍地写入这些值的日子就一去不复返了。GreenSock 3 允许您在父时间轴中设置这些属性一次，它们将被子补间继承。

例如，不要写:

```
const tl = new TimelineMax()
tl.to(selector, 2, {
  ease:  Power2.easeInOut,
  rotation:  -180
})
  .to(selector, 2, {
    ease:  Power2.easeInOut,
    rotation:  -360
}) 
```

现在，你可以简单地写:

```
const tl = gsap.timeline({defaults: {
  duration: 2,
  ease:  'power2.inOut'
}} )

tl.to(selector, {
  rotation:  -180  
})
.to(selector, {
  rotation:  -360
}) 
```

### 关键帧

您的 GreenSock 3 代码通过关键帧变得更加简洁易读。关键帧是 CSS 开发人员非常熟悉的一个概念，当您想要以不同的方式制作同一元素的动画时，它非常有用。现在，您可以将一组关键帧传递给`{} vars`对象，而不是为动画的每个阶段创建一个单独的补间，动画的所有步骤将在同一个补间内完美排序。这是一个工作原理的例子:

```
gsap.to(selector, {keyframes: {
  {x: 250, duration: 1},
  {y: 100, duration: 1, delay: 0.5}
}}) 
```

### 更多实用功能

GreenSock 库的这个最新版本附带了一系列新的[实用方法](https://greensock.com/docs/v3/GSAP/UtilityMethods)，如`snap()`，它允许您将一个值捕捉到一个增量或数组中最接近的值，还有`random()`，它使基于参数生成随机数或随机选择数组中的一个元素变得轻而易举，等等。

## 简单动画

这里有一个使用三种基本 GSAP 方法的例子:`to()`、`from()`和`fromTo()`:

参见 [CodePen](https://codepen.io) 上的笔 [gsap 3-to-from-to-examples](https://codepen.io/SitePoint/pen/oNxjjyO)by SitePoint([@ SitePoint](https://codepen.io/SitePoint))
。