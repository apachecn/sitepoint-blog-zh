# 如何避免 CSS3 动画缩小混乱

> 原文：<https://www.sitepoint.com/avoid-css3-animation-minification-muddles/>

缩小是一种更容易的网站优化技术。即使是最简单的处理器也可以通过将单独的文件连接成一个文件并删除不必要的字符来减少三分之一的 CSS 下载量。通常，这包括删除规则集中的注释、空白和最后一个分号，例如

```
/* my page elements */
.element1, element2 {
  font-family: sans-serif;
  font-size: 1em;
  padding: 0px 0em 0vh 0rem;
  margin: 10px;
}
```

可以简化为:

```
.element1,element2{font-family:sans-serif;font-size:1em;padding:0px 0em 0vh 0rem;margin:10px}
```

你的浏览器不在乎可读性差；就它而言，精简的代码是相同的。

更高级的缩小器通过删除不必要的值和单位来进一步处理这个过程。例如，前一个代码块中的填充声明可以简化为:

```
padding:0 0 0 0;
```

或者:

```
padding:0;
```

在最近的一个项目中，正是这种优化让我沮丧了几个小时。我的本地站点运行良好，但是在生产版本中，CSS3 动画要么无法启动，要么在某些浏览器中表现不稳定。原因？缩小…

## 关键帧 0%！= 0

第一个问题是 `declaration:`

```
@keyframes blink {
  0%   { background-color: #fff; }
  50%  { background-color: #000; }
  100% { background-color: #fff; }
}
```

有些缩小器会将该示例中的第一个关键帧转换为:

```
0{background-color:#fff}
```

不幸的是，零不是一个有效的关键帧值。一些浏览器会将其解析为 0%，但其他浏览器可能会从下一个有效的关键帧开始，或者完全放弃动画。

## 计时 0s！= 0

`animation`速记属性表示以下手写动画属性:

`<animation-name> || <time> || <timing-function> || <time> || <animation-iteration-count> || <animation-direction> || <animation-fill-mode>`

例如:

```
animation: blink 2s ease 0s;
```

零秒后启动“闪烁”动画，并使用 *ease* 计时功能每两秒重复一次。但是，代码可能会缩减为:

```
animation:blink 2s ease 0;
```

有些浏览器会假设最终属性不是时间，而是将其作为`animation-iteration-count`值读取。由于该值为零，动画将永远不会开始。

## 修补缩小的混乱

如果您的微型计算机表现出这种行为，您有两种选择:

1.  使用更好、更新或更简单的缩小流程。 [YUI 压缩器](http://yui.github.io/yuicompressor/)在 2014 年 2 月获得了 CSS3 动画的修复。
2.  改变你写代码的方式，即使用关键帧关键字`from`代替`0%`，并在速记`animation`声明中省略`0s`或`0ms`的计时。

这两个选项都不理想，但是这些是我们 web 开发人员每天都面临的挑战！无论如何，我希望这能让你少受一点我所忍受的痛苦。

有没有发现其他 CSS 或者 JavaScript 缩小的问题？

## 分享这篇文章