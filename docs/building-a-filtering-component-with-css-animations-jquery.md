# 用 CSS 动画和 jQuery 构建过滤组件

> 原文：<https://www.sitepoint.com/building-a-filtering-component-with-css-animations-jquery/>

几个月前，我写了一篇关于 MixItUp 的文章，这是一个流行的用于过滤和排序的 jQuery 插件。在今天的文章中，我将向您展示如何用 jQuery 和 CSS 动画构建您自己的简单可过滤组件。

事不宜迟，我们开始吧！

## 设置 HTML

首先，我将向您展示组件的 HTML 结构。考虑以下标记:

```
<div class="cta filter">
  <a class="all active" data-filter="all" href="#" role="button">Show All</a>
  <a class="green" data-filter="green" href="#" role="button">Show Green Boxes</a>
  <a class="blue" data-filter="blue" href="#" role="button">Show Blue Boxes</a>
  <a class="red" data-filter="red" href="#" role="button">Show Red Boxes</a>
</div>

<div class="boxes">
  <a class="red" data-category="red" href="#">Box1</a>
  <a class="green" data-category="green" href="#">Box2</a>
  <a class="blue" data-category="blue" href="#">Box3</a>

 <!-- other anchor/boxes here ... -->

</div>
```

请注意，我已经设置了一些非常基本的标记。下面是对它的解释:

*   首先，我定义了过滤器按钮和我想要过滤的元素(我们称它们为目标元素)。
*   接下来，我将目标元素分成三个类别(蓝色、绿色和红色)，并赋予它们`data-category`属性。该属性的值决定了每个元素所属的类别。
*   我还为过滤器按钮分配了`data-filter`属性。该属性的值指定了所需的过滤器类别。例如，带有`data-filter="red"`属性/值的按钮将只显示属于`red`类别的元素。另一方面，带有`data-filter="all"`的按钮将显示所有元素。

现在你已经对所需的 HTML 有了一个概述，我们可以继续探索 CSS。

## 设置 CSS

每当一个过滤器类别被激活时，其对应的过滤器按钮接收`active`类。默认情况下，具有`data-filter="all"`属性的按钮获得这个类。

![Box with active class](img/81c01e0a980e1c158fdd1a82ab70d03e.png)

以下是相关的样式:

```
.filter a {
  position: relative;
}

.filter a.active:before {
  content: '';
  position: absolute;
  left: 0;
  top: 0;
  display: inline-block;
  width: 0;
  height: 0;
  border-style: solid;
  border-width: 15px 15px 0 0;
  border-color: #333 transparent transparent transparent;
}
```

此外，我将使用 flexbox 为目标元素创建布局。

![Using flexbox for the layout](img/94bb5ab6dd2894c3993c5a1d3ef62465.png)

请参见下面的相关样式:

```
.boxes {
  display: flex;
  flex-wrap: wrap;
}

.boxes a {
  width: 23%;
  border: 2px solid #333;
  margin: 0 1% 20px 1%;
  line-height: 60px;
}
```

最后，我将定义两个不同的 CSS 关键帧动画，稍后我将使用它们来展示元素:

```
@keyframes zoom-in {
  0% {
   transform: scale(.1);
  }
  100% {
    transform: none;
  }
}

@keyframes rotate-right {
  0% {
    transform: translate(-100%) rotate(-100deg);
  }
  100% {
    transform: none;
  }
}

.is-animated {
  animation: .6s zoom-in;
  // animation: .6s rotate-right; 
}
```

有了标记和 CSS，我们就可以开始构建 JavaScript/jQuery 了。

## 设置 jQuery

看看下面的代码，然后我会解释发生了什么:

```
var $filters = $('.filter [data-filter]'),
    $boxes = $('.boxes [data-category]');

$filters.on('click', function(e) {
  e.preventDefault();
  var $this = $(this);
  $filters.removeClass('active');
  $this.addClass('active');

  var $filterColor = $this.attr('data-filter');

  if ($filterColor == 'all') {
    $boxes.removeClass('is-animated')
      .fadeOut().promise().done(function() {
        $boxes.addClass('is-animated').fadeIn();
      });
  } else {
    $boxes.removeClass('is-animated')
      .fadeOut().promise().done(function() {
        $boxes.filter('[data-category = "' + $filterColor + '"]')
          .addClass('is-animated').fadeIn();
      });
  }
});
```

每次单击过滤器按钮时，都会发生以下情况:

*   从所有按钮中移除`active`类，并仅将其分配给选定的按钮。
*   检索并评估按钮的`data-filter`属性的值。
*   如果`data-filter`的值是`all`，那么所有的元素都应该出现。为此，我首先隐藏它们，然后，当所有元素都隐藏起来时，我使用`rotate-right`或`zoom-in` CSS 动画显示它们。
*   如果值不是`all`，应该会出现对应类别的目标元素。为此，我首先隐藏所有的元素，然后，当所有的元素都被隐藏时，我使用`rotate-right`或`zoom-in` CSS 动画只显示相关类别的元素。

此时，澄清一件事很重要。注意上面代码中的`fadeOut()`方法的语法。它看起来如下:

```
$boxes.fadeOut().promise().done(function() {
  // callback's body
});
```

不过，您可能更熟悉这个语法:

```
$boxes.fadeOut(function() {
  // callback's body
});
```

这些声明有不同的含义:

*   在第一种情况下，只有在所有目标元素都隐藏起来之后，才会执行回调。您可以通过访问 jQuery 文档的[promise()部分](http://api.jquery.com/promise/)来了解关于这种方法的更多信息。
*   在第二种情况下，回调被多次触发。具体来说，每当一个元素被隐藏时，它就会被执行。

下面的演示使用了`zoom-in`动画:

用 [CodePen](http://codepen.io) 上的 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看笔[一个带有 CSS 和 jQuery(带缩放动画)](http://codepen.io/SitePoint/pen/YqxvzP/)的排序/过滤组件。