# 为您的网站构建出色的移动菜单

> 原文：<https://www.sitepoint.com/building-great-mobile-menus-website/>

网站需要看起来很棒，并具有最佳的功能，不管它们是在笔记本电脑、平板电脑还是手机上浏览。开发人员正在适应以移动为中心的设计理念，专注于创建易读大小的字体，使图像具有响应性，调整内容以适应等。然而，菜单本身经常被忽视或忽略。在本教程中，我将解释如何创建手机友好的菜单。

## 移动菜单及其重要性

![A Typical menu](img/e3a2596f7b98d55609292f49e7e6ac3b.png)

移动菜单有特定的注意事项:

### 更小的设备尺寸

物理空间更少，这意味着需要一种易于使用的方式来组织菜单及其链接。移动菜单通过定制主菜单或显示另一个专用于移动设备(如手机或平板电脑)的菜单来实现这一点

这些移动菜单应该整洁有序，大小合适，让用户可以轻松浏览。

### 触摸屏和交互性

开发人员长期以来依赖于伪状态如`:hover`和`:active`来创建交互式下拉菜单。在触摸屏上，这些功能要么不可靠，要么根本不起作用(例如，你不能将鼠标悬停在一个链接上，然后期望它的子菜单出现在它的下方)。

![Pseudo Fail](img/bf36d9443cae53a0ba17e498a9aedbec.png)

### 速度和响应能力

导航在移动设备上更加重要，因为菜单已经成为用户点击导航的默认位置，所以菜单越快越重要。没有什么比打开一个菜单，看着它慢慢地动画，切和滑动它的方式打开或关闭更糟糕的了！

移动菜单应该尽可能的快捷(在适当的地方进行修饰)

## 为菜单动画提供动力——CSS 或 jQuery

对于移动菜单的动画，有两种主要的方法:

![Menu animations](img/2678b691a25b06565a755487c580ef9d.png)

*   CSS 动画:使用 CSS 转换或变换来操作你的菜单并制作动画。
*   jQuery :使用 jQuery 的动画功能来制作菜单动画。

虽然这两种方法都可行，但在选择下一种方法之前，需要考虑一些因素。

### 跨浏览器兼容性

与桌面环境不同，在桌面环境中，Internet Explorer 的局限性仍然是一个问题，移动领域由 Chrome、Safari 和 Firefox 主导，谢天谢地，这些浏览器中的大多数都支持动画。

![Browsers](img/1178d98b0631da3a06ecbe4862d69a93.png)

#### CSS 过渡/转换

如果你想用 CSS 制作动画，你可以使用`transitions`或者`transformations`来应用你的样式。

*跃迁*

所有移动浏览器都支持过渡，大多数现代版本都支持使用`transition`的 CSS3 标准版。老版本的浏览器将需要`-moz`、`-webkit-`或`-ms`前缀。在大多数情况下，你可以设置一个属性值，如`height`或`left`，然后让过渡动画的项目。

*转换*

变换有 2D 和 3D 两种形式。这些版本决定了项目是否可以沿 2D 或 3D 平面进行变换。所有移动设备都支持 2D 和 3D 形式的变换。例如`transform: translate(50px,0px)`或`transform: translate3d(50px,50px,1px)`

过渡和转换在手机上都有惊人的支持。这意味着依赖于 3D 变换和过渡的设计可以在知道一切都将正确工作的情况下构建(只要包括正确的前缀)。

#### jQuery 动画

因为我们讨论的是移动浏览器，所以几乎所有浏览器和版本都支持 jQuery(jQuery 解决了兼容性问题)

### 易于使用/实施

这两种方法都很相似，很容易设置。然而，当构建复杂的运动或优化动画速度时，复杂性往往会增加。

#### CSS 过渡/转换

CSS 驱动的动画易于使用。指定一个起始值和结束值，浏览器会自动显示。

例如，移动一个盒子:

```
/*Move an item when hovering over it*/
.my-container .my-box{
    left: 0px;
    transition: all 350ms linear;
    position: relative;
}
.my-container:hover .my-box{
    left: 100px;
}
```

这从一个值移动到另一个值，并且浏览器用过渡填充间隙。

对于更复杂的动画或者动画到一个未知的值(比如页面上另一个元素的位置)，需要 JavaScript 进行计算(然后可以转换成一个过渡或变换)

#### jQuery 动画

使用 jQuery 的 animate 函数的一个最强有力的理由是，它易于实现，并且提供了对附加信息的访问。

```
//On hover, move the box inside left or right
$('.my-container').hover(
  function() {
    $('.my-box').animate({left: "100px"},500);
  }, function() {
    $('.my-box').animate({left: "0px"},500);
  });
```

因为动画是由 jQuery 支持的，所以浏览器支持不是问题。不同的浏览器不需要不同版本的动画。

由于这些动画是通过 jQuery 实现的，开发人员可以在制作动画之前找到复杂的值并执行计算(例如，将动画结束值设置为页面上另一个元素的值)。

### 速度和响应能力

![Browser Speed](img/ac5eac3f41955dfaff9c6547bb962c69.png)

响应性和速度是移动界面的关键部分，影响着动画创作者的选择。

#### CSS 过渡/转换

使用 CSS 的动画几乎总是比 jQuery 的 animate 函数快。尽管 jQuery 在最近的版本中得到了优化，但 CSS 驱动的动画通常更流畅、更快速，让浏览器直接处理动画。即使在 CSS 中，动画的速度也是由动画的形式决定的。没有硬件加速，浏览器将计算一切。如果硬件加速被触发，浏览器会将一些工作转移到 GPU，从而加快处理速度。

*CSS 转换*

```
/*transition an item with its top value when its active*/
.transition-item{
    position: relative;
    top: 0px;
    transition: top 300ms ease;
}
.transition-item.active{
    top: 300px;
}
```

在这个例子中，当项目设置为激活状态时，它将从顶部移动 300 像素。这种转变会很快，但不会触发硬件加速。

*CSS 转换*

```
/*Apply an animation when the item is in its active state*/
.animation-item{
    position: relative;
    transition: top 300ms ease;
    transform: translate3d(0px,-300px,1px);
}
.animation-item.active{
    transform: translate3d(0px,0px,1px);
}
```

由于使用了基于 3D 的移动变换，这将触发硬件加速。因为设置了过渡，所以它将平滑地激活其顶部位置。

### 带翻译 3d 的滑出式菜单

该菜单最初将位于网站可视部分之外，在屏幕的左侧。

当您打开或关闭菜单时，它会将菜单滑入或滑出。这种类型的菜单利用 3D 变换来驱动其移动(同时使用 translate3d 和 rotate)

animate 函数通过使用 JavaScript 每秒移动元素 *X* 次来工作。

虽然这种方法很好，但问题是它仍然很慢，并且在项目更新其位置时会产生明显的移动“滞后”或“抖动”。

动画制作过程中的这种抖动会让网站感觉缓慢和没有反应。

虽然很难对速度进行精确的测量，但 jQuery animate 通常不会像 CSS 转换`translate3d`和`rotate`那样快速或平稳地运行。

这里有个活生生的例子:

[//codepen.io/simoncodrington/embed/LVZXbP/?height=268&theme-id=0&default-tab=result](//codepen.io/simoncodrington/embed/LVZXbP/?height=268&theme-id=0&default-tab=result)

见笔<a href = " http://codepen . io/simoncodrington/Pen/LVZXbP/">手机导航菜单&ndash；Simon Codrington(<a href = " http://code pen . io ">@ simoncodrington</a>)在<a href = " http://code pen . io ">code pen</a>上从左< /a >的菜单幻灯片。<br>

该菜单将`transformations`与`transitions`结合使用，将旋转/滑动动画应用于菜单。

菜单本身是绝对定位的，其宽度设置为窗口宽度的 65%。

在加载时，jQuery 会找出身体的高度，然后设置`nav-menu`的高度来匹配。这确保了它们的大小相同(因为一个小菜单需要整个页面向下延伸)。

使用带有`translate3d`的`transformation`，`nav-menu-wrap`容器对于用户来说实际上是隐藏在左边的。使用`rotate`的二次变换将菜单旋转了–90 度。通常这种旋转会使菜单从中间旋转，但是菜单的`transform-origin`被设置在左上角(有效地将菜单向左上方推)。

当`nav-menu-toggle`被激活时，jQuery 将`nav-menu-wrap`设置为活动的。这会将`translate3d`动画设置回 0%,同时旋转 0 度，使菜单从左上角开始摆动。

当`nav-menu-wrap`正在制作动画时，一个名为`nav-menu-background`的辅助元素正在转换它的`opacity`和`z-index`。这个元素是黑色背景，位于页面内容之上，但是在菜单之下。

如果用户按下`nav-menu-background`项或`close`项(菜单的第一个元素)。它将再次触发状态改变(并将切换到非活动状态)。

### 带有滑动子菜单的弹出菜单

这个菜单有点不同。当我们切换菜单时，它将弹出导航菜单，该菜单将淡入和放大(从屏幕中央弹出)。另一个有趣的附加功能是可以通过点击子开关来访问子菜单项。点击后，子菜单会在菜单上滑动，让您可以访问这些新链接。

这里有个活生生的例子:

[//codepen.io/simoncodrington/embed/yNammy/?height=268&theme-id=0&default-tab=result](//codepen.io/simoncodrington/embed/yNammy/?height=268&theme-id=0&default-tab=result)

见笔<a href = " http://codepen . io/simoncodrington/Pen/ynamy/">手机导航菜单&ndash；标度从 0 到 1 打开< /a >由 Simon Codrington(<a href = " http://CodePen . io/simoncodrington ">@ simoncodrington</a>)在<a href = " http://CodePen . io ">CodePen</a>。<br>

该菜单可以在移动菜单中显示子菜单(无需在复杂的列表中上下滚动)。

`nav-menu`元素应用了一个`overflow:hidden`，并且最初将其`opacity`设置为 0，同时将`scale3d`设置为 0.5。

激活后，`nav-menu-toggle`将切换`nav-menu`的状态。如果菜单被打开，它将使用`scale3d`应用另一个变换，将其缩放比例设置为 1，同时将其不透明度设置为 1(这将使菜单从中心出现并弹出)。

里面的每个菜单项都可能有`sub-menu`元素。任何有子元素的菜单元素都有一个浮动在元素右边的开关(这可以访问下一个菜单)。

`sub-menu`元素被绝对定位并放置在左侧 100%和顶部 0%的位置。这将强制任何`sub-menu`向其父节点的右侧偏移。

当一个`sub-menu-toggle`被激活时，它将切换其兄弟`sub-menu`的状态。这个菜单(现在是活动的)将应用一个`translated`变换来滑动菜单上的项目，覆盖旧菜单。

在每个`sub-menu`中有一个`close`项，它将切换`sub-menu`的状态(向右滑动元素，远离菜单)。

菜单及其所有子菜单都可以在一个易于使用的列表中导航。菜单支持溢出，所以如果在一个小设备上查看，菜单将上下滚动。

当菜单打开时，名为`nav-menu-background`的背景元素打开。这为用户提供了一种快速关闭菜单的方法(它移除了菜单的活动状态)

## 最后

总体思想是，您可以将 CSS 和 jQuery 结合起来，为移动用户创建出色的交互界面，不仅外观好看，而且速度快如闪电！看完这些例子后，你应该很好地理解了如何使用变换或转换来创建快速响应的菜单。你可以复制其中一个例子，并加以扩展，甚至用你自己的想法进行实验。

你会先做什么？如果您有任何问题或意见，请告诉我们。

## 分享这篇文章