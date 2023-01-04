# 了解 CSS 动画填充模式属性

> 原文：<https://www.sitepoint.com/understanding-css-animation-fill-mode-property/>

我确信我们所有从事前端开发的人都曾在某个时候摆弄过 [CSS 基于关键帧的动画](http://dev.w3.org/csswg/css-animations/)。我们中的一些人可能已经用这个特性创建了一些非常复杂的演示和实验。

如果你想全面了解这个话题，[我 2011 年在《粉碎》杂志上的文章](http://www.smashingmagazine.com/2011/05/17/an-introduction-to-css3-keyframe-animations/)仍然是一个不错的选择。然而，在本文中，我想集中讨论 CSS 动画规范的一个部分:`animation-fill-mode`属性。

这是一个基于动画的属性，不太容易理解。例如，没有人真的会被`animation-name`、`animation-duration`等等弄糊涂…但是“填充模式”到底是什么意思呢？我们简单考虑一下这个，举几个例子。

## 定义填充模式的语法

您可能已经知道，基本的关键帧动画是使用`@keyframes` at-rule 定义的。但是关键帧不会做任何事情，除非您将它们与动画名称相关联。这里有一个简写的`animation`财产声明，所以你可以明白我的意思:

```
.example {
  animation: myAnim 2s 500ms 2 normal ease-in forwards;
}
```

当然，同样的简写行可以扩展为:

```
.example {
  animation-name: myAnim;
  animation-duration: 2s;
  animation-delay: 500ms;
  animation-iteration-count: 2;
  animation-direction: normal;
  animation-timing-function: ease-in;
  animation-fill-mode: forwards;
}
```

在这两个例子中，`animation-fill-mode`属性是最后定义的，并且在这两个例子中，该值都被设置为“forwards”。我们不必最后定义它，但这可能是一个很好的实践。

同样，即使你从来没有使用过 CSS 动画，你也可能知道上面声明中的所有东西都在做什么——除了`animation-fill-mode`。

## 填充模式的规范定义

那么关于这个属性，规范说了些什么呢？

> `animation-fill-mode`属性定义动画在执行时间之外应用什么值。

正如它继续解释的那样，执行“之外”的时间，具体地说，是指动画应用于元素时和元素实际开始动画的时间之间的时间。基本上，使用填充模式值，您可以定义动画元素在动画发生之外但在应用动画之后的外观。听起来可能会令人困惑，但你马上就会明白我的意思。

让我们通过查看每一个可能的值来建立这个基本定义(可能仍然有点混乱)。

## 分解价值观

`animation-fill-mode`属性可以接受四个值之一:`none`、`forwards`、`backwards`或`both`。这是每一项的细目分类。

### 值:无

这是`animation-fill-mode`的初始值或默认值。唯一一次你可以定义一个值`none`而不显得多余的时候是当你通过 JavaScript 设置它来改变它的值，或者当你在层叠中覆盖一些东西的时候。

为了理解值`none`的作用，这里有一个 CodePen 演示，展示了一个没有定义`animation-fill-mode`的动画(因此，它的值为`none`):

参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )的动画填充模式无的笔[示例。](http://codepen.io/SitePoint/pen/KwBezM/)