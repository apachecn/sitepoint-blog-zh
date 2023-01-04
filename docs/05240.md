# jQuery 滚动动画简介

> 原文：<https://www.sitepoint.com/introduction-jquery-scroll-based-animations/>

网络每天都在变化。新的技术和技巧出现了，其他的消失了。出于这个原因，网页设计师和前端开发人员必须熟悉许多最新的网页设计趋势。视差滚动、固定标题、平面设计、单页网站和动画是当前最热门的网络趋势。

在本教程中，我们将使用 CSS 和 jQuery 来看看基于滚动的动画和效果。

我们将要创造的四种效果可以在这个演示中看到。但是在我们得到效果之前，让我们有一个简短的介绍。

*注意:本教程中使用的代码可以通过对象缓存和使用 CSS 动画而不是 jQuery 的“animate()”方法来改进，但是为了简单起见，我们重复了对象声明，并且我们将所有内容都放在 jQuery 中，以便将重点放在概念上。*

## 什么是基于滚动的动画和效果？

基于滚动的动画和效果是一种新的但众所周知的技术，它使前端开发人员能够创建丰富的交互式 web 体验。它们是在用户向下滚动页面时触发的，可以用 CSS 和 jQuery 轻松操作和实现。

为了检测用户是否向下滚动页面，我们使用 jQuery 的 [scroll()](http://api.jquery.com/scroll/ "Scroll Event") 事件。

一旦我们知道用户正在滚动，我们就可以使用 jQuery 的 [scrollTop()](http://api.jquery.com/scrollTop/ "ScrollTop() Method") 方法获得窗口滚动条的垂直位置，并应用所需的效果:

```
$(window).scroll(function() {
    if ($(this).scrollTop() > 0) {
        // apply effects and animations
    }
});
```

## 他们有反应吗？

如果我们对创建响应性的基于滚动的效果感兴趣，我们必须定义以下属性:

1.  浏览器窗口的`width`属性。
2.  浏览器窗口的`height`属性。

如果没有定义这些属性，我们创建的基于滚动的效果是“静态的”,当用户水平或垂直调整窗口大小时将不能正常工作。

我们可以使用 jQuery 的 [width()](http://api.jquery.com/width/ "Width") 和 [height()](http://api.jquery.com/height/ "Height") 方法轻松检索这些属性的值。

下面的代码片段显示了为了创建基于滚动的效果，我们必须考虑的所有必要的检查。

```
$(window).scroll(function() {
    if ($(this).width() < 992) {
        if ($(this).height() <= 768) {
            if ($(this).scrollTop() < 500) {
                // apply effects
            }
            if($(this).scrollTop() > 1000) {
            // apply effects
            }
        }
    }
});
```

现在我们已经介绍了基于滚动的效果的基础知识，让我们通过四个不同的例子来看看它们的作用。

*注意:为了简单起见，我们只关注检查这些效果是如何基于窗口的`width`属性的不同值而变化的。同样的过程也可以用于它的`height`属性。*

## 示例#1

当窗口滚动条的顶部位置超过 60px 时，会触发此效果。在这种情况下，执行的代码片段如下:

```
if ($(window).scrollTop() > 60) {
    $('.banner h2').css('display', 'none');
    $('.banner .info').css('display', 'block');
} else {
    $('.banner h2').css('display', 'block');
    $('.banner .info').css('display', 'none');
}
```

上面的代码隐藏了`.banner`元素的`h2`子元素，并显示了其最初被隐藏的`.info`子元素。

这段代码也可以写成如下形式:

```
if ($(window).scrollTop() > 60) {
    $('.banner h2').hide();
    $('.banner .info').show();
} else {
    $('.banner h2').show();
    $('.banner .info').hide();                               
}
```

要查看实际效果，请在 CodePen 上查看完整的演示。

## 实施例 2

下一个效果不仅取决于浏览器滚动条的顶部位置，还取决于窗口滚动条的宽度。更具体地说，我们做出以下假设:

1.  窗口的`width`属性的值小于或等于 549px。在这种情况下，只有当窗口滚动条的顶部位置超过 600 像素时，才会触发动画。
2.  窗口的`width`属性的值介于 550 像素和 991 像素之间。在这种情况下，只有当窗口滚动条的顶部位置超过 480 像素时，才会触发动画。
3.  浏览器的`width`属性的值大于 991px。在这种情况下，只有当窗口滚动条的顶部位置超过 450 像素时，才会触发动画。

上述假设导致以下代码片段:

```
if ($(window).width() <= 549) {
    if($(window).scrollTop() > 600) {
        // the animation that's executed
        firstAnimation();
    }
} else if ($(window).width() > 549 && $(window).width() <= 991) {
    if($(window).scrollTop() > 480){
        // the animation that's executed
        firstAnimation();
    }
} else {
    if ($(window).scrollTop() > 450) {
        // the animation that should be executed
        firstAnimation();
    }
}
```

包含要执行的动画的代码如下:

```
var firstAnimation = function () {
    $('.clients .clients-info').each(
        function () {
            $(this).delay(500).animate({
                opacity: 1,
                height: '180',
                width: '250'
            }, 2000);
        }
    );
};
```

上面的代码显示了`.clients-info`元素的`opacity`、`height`和`width`属性。

要查看这种效果，请在 CodePen 上查看完整的演示。

## 实施例 3

第三种效果不仅取决于窗口滚动条的顶部位置，还取决于窗口的`width`属性。更具体地说，我们做出以下假设:

1.  窗口的`width`属性的值小于或等于 549px。在这种情况下，只有当窗口滚动条的顶部位置超过 1750 像素时，才会触发动画。
2.  窗口的`width`属性的值介于 550 像素和 991 像素之间。在这种情况下，只有当窗口滚动条的顶部位置超过 1150 像素时，才会触发动画。
3.  窗口的`width`属性的值大于 991px。在这种情况下，只有当窗口滚动条的顶部位置超过 850 像素时，才会触发动画。

下面是基于上述内容的代码:

```
if ($(window).width() <= 549){
    if($(window).scrollTop() > 1750){
        secondAnimation();
    }
} else if ($(window).width() > 549 && $(window).width() <= 991) {
    if ($(window).scrollTop() > 1150) {
        secondAnimation();
    }  
} else {
    if ($(window).scrollTop() > 850) {
        secondAnimation();
    }
}
```

包含要执行的动画的代码如下:

```
var secondAnimation = function() {          
    $('.method:eq(0)').delay(1000).animate({
				opacity: 1
			}, 'slow', 
			function() {
				$(this).find('h4').css('background-color', '#b5c3d5');
        }
    );

    $('.method:eq(1)').delay(2000).animate({
				opacity: 1
			}, 'slow', 
		    function() {
    	        $(this).find('h4').css('background-color', '#b5c3d5');
        }
    );

    $('.method:eq(2)').delay(3000).animate({
				opacity: 1
			}, 'slow', 
		    function() {
    	        $(this).find('h4').css('background-color', '#b5c3d5');
        }
    );

    $('.method:eq(3)').delay(4000).animate({
				opacity: 1
			}, 'slow', 
	        function() {
    	        $(this).find('h4').css('background-color', '#b5c3d5');
        }
    );
};
```

上面的代码按顺序动画显示了`.method`元素的`opacity`属性，然后更改了它们的`h4`子元素的`background-color`属性。

要查看实际效果，请在 CodePen 上查看完整的演示。

## 实施例 4

这种效果不仅取决于窗口滚动条的顶部位置，还取决于窗口的`width`属性。更具体地说:

1.  如果窗口的`width`属性的值小于或等于 549px，则只有当窗口滚动条的顶部位置超过 3500px 时，才会触发动画。
2.  如果窗口的`width`属性的值介于 550px 和 991px 之间，则只有当窗口滚动条的顶部位置超过 2200px 时，才会触发动画。
3.  如果窗口的`width`属性的值大于 991px，则只有当窗口滚动条的顶部位置超过 1600px 时，才会触发动画。

这会产生以下代码:

```
if ($(window).width() <= 549) {
    if ($(window).scrollTop() > 3500) {
        thirdAnimation();
    }
} else if ($(window).width() > 549 && $(window).width() <= 991) {
    if ($(window).scrollTop() > 2200) {
        thirdAnimation();
    }
} else {
    if ($(window).scrollTop() > 1600) {
        thirdAnimation();
    }
}
```

动画的代码如下:

```
var thirdAnimation = function() {
    $('.blogs').find('p').delay(1400).animate({
            opacity: 1, 
            left: 0
        }, 'slow'
    );

    $('.blogs').find('img').delay(2000).animate({
            opacity: 1, 
            right: 0
        }, 'slow'
    );

    $('.blogs').find('button').delay(2500).animate({
            opacity: 1, 
            bottom: 0
        }, 'slow'
    );
};
```

上面的代码依次显示了`p`、`img`、`button`元素的`opacity`、`left`、`right`和`bottom`属性。

要查看实际效果，请查看 CodePen 上的完整演示。

## 结论

我希望这里的四个例子演示了如何使用 jQuery 创建基于滚动的动画和效果。

如果你对所使用的代码有任何想法，或者知道任何关于这种效果的好的高级例子或演示，欢迎在评论中分享。

## 分享这篇文章