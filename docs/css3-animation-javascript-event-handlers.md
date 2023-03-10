# 如何在 JavaScript 中捕获 CSS3 动画事件

> 原文：<https://www.sitepoint.com/css3-animation-javascript-event-handlers/>

CSS3 动画实现起来流畅快捷，但是不像 JavaScript，你没有逐帧控制。幸运的是，您可以对任何元素应用事件处理程序来确定动画状态。这允许细粒度的控制，例如按顺序播放不同的动画。

考虑这个简单的 CSS3 动画:

```
#anim.enable
{
  -webkit-animation: flash 1s ease 3;
  -moz-animation: flash 1s ease 3;
  -ms-animation: flash 1s ease 3;
  -o-animation: flash 1s ease 3;
  animation: flash 1s ease 3;
}

/* animation */
@-webkit-keyframes flash {
  50% { opacity: 0; }
}

@-moz-keyframes flash {
  50% { opacity: 0; }
}

@-ms-keyframes flash {
  50% { opacity: 0; }
}

@-o-keyframes flash {
  50% { opacity: 0; }
}

@keyframes flash {
  50% { opacity: 0; }
} 
```

当`enable`类应用于 ID 为`anim`的元素时，名为 *flash* 的动画运行三次。每次迭代持续一秒钟，在此期间元素淡出，然后进入。

动画出现时会触发三种类型的事件:

**动画开始**

```
var anim = document.getElementById("anim");
anim.addEventListener("animationstart", AnimationListener, false); 
```

当动画首次开始时，将触发 animationstart 事件。

**动画迭代**

```
anim.addEventListener("animationiteration", AnimationListener, false); 
```

animationiteration 事件在每个新动画迭代开始时触发，即除第一个迭代之外的每个迭代。

**动画结束**

```
anim.addEventListener("animationend", AnimationListener, false); 
```

动画结束时将触发 animationend 事件。

## 浏览器兼容性

在撰写本文时，Firefox、Chrome、Safari、Opera 和 IE10 都支持 CSS3 动画和相关的事件处理程序。此外，Opera、IE10 和 webkit 浏览器使用前缀，并且为了更好地衡量，还加入了一些大小写变化…

| **W3C 标准** | **火狐** | **网络工具包** | **歌剧** | **IE10** |
| 动画开始 | 动画开始 | webkitAnimationStart | oanimationstart | MSAnimationStart |
| 动画迭代 | 动画迭代 | webkitAnimationIteration | oanimationiteration | MSAnimationIteration |
| 动画结束 | 动画结束 | webkitAnimationEnd | oanimationend | MSAnimationEnd |

绕过前缀诡计的最简单方法是使用自定义函数为所有带前缀和不带前缀的名称调用 addEventListener:

```
var pfx = ["webkit", "moz", "MS", "o", ""];
function PrefixedEvent(element, type, callback) {
	for (var p = 0; p code>
```

现在可以使用一行代码分配跨浏览器事件处理程序:

```
// animation listener events
PrefixedEvent(anim, "AnimationStart", AnimationListener);
PrefixedEvent(anim, "AnimationIteration", AnimationListener);
PrefixedEvent(anim, "AnimationEnd", AnimationListener); 
```

## 事件对象

在上面的代码中，每当发生动画事件时，都会调用 AnimationListener 函数。事件对象作为单个参数传递。除了标准属性和方法，它还提供:

*   **animation name**:CSS3 动画名称(即 *flash*
*   **elapsedTime** :动画开始后的时间，以秒为单位。

因此，我们可以检测到*闪烁*动画何时结束，例如

```
if (e.animationName == "flash" && 
    e.type.toLowerCase().indexOf("animationend") >= 0) {
    ...
} 
```

例如，该代码可以删除现有的类，或者以特定的顺序应用另一个 CSS3 动画。

[**查看 JavaScript 演示中的 CSS3 动画事件**](https://blogs.sitepointstatic.com/examples/tech/animation-api/index.html)

演示页面显示一个按钮。当点击它时，一个“enable”类被切换，启动 *flash* 动画。当动画事件触发时，状态显示在控制台中。当动画结束时,“enable”类将被删除，以便可以再次单击该按钮。

让我知道你是否在任何有趣的项目中使用动画事件捕捉。

如果你喜欢读这篇文章，你会爱上[可学的](https://learnable.com?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)；向大师们学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和交互式在线课程，如真实世界的 [HTML5 & CSS3。](https://learnable.com/courses/html5-css3-for-the-real-world-1484?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)

对本文的评论已经关闭。对 CSS 有疑问？为什么不在我们的[论坛](https://www.sitepoint.com/forums/forumdisplay.php?53-CSS-amp-Page-Layout?utm_source=sitepoint&utm_medium=link&utm_campaign=forumlink)上问呢？

## 分享这篇文章