# iPhone 开发:让您入门的 12 个技巧文章

> 原文：<https://www.sitepoint.com/iphone-development-12-tips-2/>

##### 4:方向变化

您的页面和应用程序可以在两种方向模式下查看:纵向和横向。对你的应用程序来说，对方向的变化做出反应可能是有用的。

使用 JavaScript，您可以访问属性`window.orientation`，它可以有以下值:

*   0–正常纵向(主屏幕按钮在底部)
*   -90 °-从纵向顺时针旋转后的横向(主页按钮向左)
*   90–从纵向逆时针旋转后的横向(主页按钮向右)
*   180-目前不支持，但会纵向翻转，以便主页按钮在顶部

还有一个`orientationchange`事件，当用户倾斜设备时，你可以用它来执行一个动作。这里有一个例子，它只是在方向改变时给出一个警告，显示`window.orientation`值:

```
window.onorientationchange = function() {

    alert(window.orientation); 

}
```

你可以在[示例 7](https://www.sitepoint.com/examples/iphone-development-12tips/orientation-change.html) 中看到这是如何工作的:旋转手机查看提醒。在 iPhone 模拟器中，你会在硬件菜单下找到旋转设备的选项。

![](img/f37c5ac906d77f619287779709391875.png)

##### 5:特定于方向的样式

让我们将方向改变事件用于更实际的用途。有时，您可能需要使用取决于方向的样式来设置内容的格式。这需要三个简单的步骤:

1.  在我们的标记中，给`body`元素添加一个类名(`portrait`和`landscape`一样好！)

3.  在我们的样式表中，为`body.portrait`和`body.landscape`指定不同的内容样式，以及任何可能需要它的后代元素

5.  当方向改变时，使用 JavaScript 更新 body 元素的`class`属性的值

所以第一步是添加一个默认的类名，就像这样:

```
<body class="portrait">
```

然后可以添加样式:

```
body.portrait p {  

    color: red;  

}  

body.landscape p {  

    color: blue;  

}
```

最后，JavaScript。我们需要使用:

1.  用于最初设置类名的`load`事件的监听器

3.  `orientationchange`事件的另一个监听器

5.  发生`orientationchange`事件时交换类名的函数

让我们来看看事件侦听器:

```
window.addEventListener('load', setOrientation, false);  

window.addEventListener('orientationchange', setOrientation, false);
```

这里有一个`setOrientation`函数改变 body 元素的类名:

```
function setOrientation() {  

  var orient = Math.abs(window.orientation) === 90 ? 'landscape' : 'portrait';  

  var cl = document.body.className;  

  cl = cl.replace(/portrait|landscape/, orient);  

  document.body.className = cl;  

}
```

[例 8](https://www.sitepoint.com/examples/iphone-development-12tips/orientation-styles.html) 展示了这些定向特定的样式。

![](img/33e5b71d2f3e3bf2f819ad88ca147d4b.png)

##### 6:隐藏工具栏

你可能已经注意到，当一个页面第一次加载到 iPhone 上时，URL 工具栏是可见的，当你向下滚动页面时，URL 工具栏消失了。

![](img/da68809f740193dba9ae4bce758dc30b.png)

如果您想让您的 web 应用程序感觉更像一个原生的 iPhone 应用程序，您会希望在页面加载时立即删除工具栏。为此，只需使用`window.scrollTo`将页面向下滚动一个像素:

```
window.addEventListener('load', function(){

  setTimeout(scrollTo, 0, 0, 1);

}, false); 
```

[例 9](https://www.sitepoint.com/examples/iphone-development-12tips/hide-toolbars.html) 显示了这种行为。

![](img/e18905a0c149284662685fa00fabd314.png)

如果你的页面太短而不能滚动，会发生什么？在这种情况下，我们需要一个额外的`meta`元素。为了使页面可滚动，我们可以使用`meta`元素设置视窗的高度:

```
<meta name="viewport" content="height=device-height,width=device-width" />
```

##### 7:圆角

你得有圆角，对吧？你可以使用你最喜欢的圆角方法，这种方法可以在主流的桌面浏览器上工作，或者，如果你只针对 Safari，你可以从`-webkit-border-radius` CSS 扩展中获益。因为 Firefox 也有类似的`-moz-border-radius`,所以你可以在 Firefox 和 Safari 中拥有一个漂亮的圆形元素，它可以在 IE 和 Opera 中很好地退化为一个简单的盒子:

```
.box {  

    -webkit-border-radius: 5px;  

    -moz-border-radius: 5px;  

    background: #ddd;  

    border: 1px solid #aaa;  

}
```

[例 10](https://www.sitepoint.com/examples/iphone-development-12tips/rounded.html) 展示了`div`元素上的这些简单的圆角。

![](img/a83afdec0ce1318ce1b97384e8678b95.png)

##### 8:触摸事件

当然，你用手指而不是鼠标使用 iPhone 你不用点击，而是点击。更重要的是，你可以用几个手指去触摸和敲击。在 iPhone 上，鼠标事件被触摸事件取代。它们是:

*   `touchstart`
*   `touchend`
*   `touchmove`
*   `touchcancel`(系统取消触摸时)

当您订阅这些事件中的任何一个时，您的事件监听器都会收到一个`event`对象。`event`对象有一些重要的属性，比如:

*   `touches`–触摸对象的集合，每个触摸屏幕的手指一个。例如，`touch`对象具有包含页面内触摸坐标的`pageX`和`pageY`属性。
*   `targetTouches`–类似于触摸，但只记录目标元素上的触摸，而不是整个页面。

下一个例子是拖放的简单实现。让我们在空白页上放一个盒子，然后拖动它。您需要做的就是订阅`touchmove`事件，并随着手指的移动更新盒子的位置，就像这样:

```
window.addEventListener('load', function() {  

  var b = document.getElementById('box'),  

      xbox = b.offsetWidth  / 2, // half the box width  

      ybox = b.offsetHeight / 2, // half the box height  

      bstyle = b.style; // cached access to the style object  

  b.addEventListener('touchmove', function(event) {  

    event.preventDefault(); // the default behaviour is scrolling  

    bstyle.left =  event.targetTouches[0].pageX - xbox + 'px';  

    bstyle.top  =  event.targetTouches[0].pageY - ybox + 'px';  

  }, false);  

}, false);
```

`touchmove`事件监听器首先取消手指移动的默认行为——否则 Safari 会滚动页面。集合`event.targetTouches`包含当前在目标`div`元素上的每个手指的数据列表。我们只关心一个手指，所以用`event.targetTouches[0]`。然后`pageX`给我们手指的 X 坐标。从这个值中减去`div`宽度的一半，这样手指就停留在盒子的中心。

我们已经将所有代码放在了例 11 中。

**Go to page:** [1](https://sitepoint/iphone-development-12-tips) | [2](https://sitepoint/iphone-development-12-tips-2/) | [3](https://iphone-development-12-tips-3/)

## 分享这篇文章