# 用 CSS 重新创建 Google 图片搜索布局

> 原文：<https://www.sitepoint.com/recreating-google-images-search-layout-css/>

在我最近参与的一个项目中，我被要求重新创建[谷歌的图片搜索](https://images.google.com/)扩展功能，类似于下面显示的截图，但采用了严格的网格格式。

![Google Images search example](img/1a0c26f2caa8d0351a6adf2957b7ab0d.png)

我的第一反应是，我需要使用 JavaScript 来设置一些布局和盒子模型属性，这是我一直不愿意做的，只有在万不得已的情况下才会这么做。因为已经有了一个完美的例子，我决定开放开发者工具来看看 Google 是怎么做的(为什么要重新发明轮子，对吗？)

原来 Google 把这个结构分成两个 div，一个 div 包含所有的图像单元格，另一个 div 用于扩展区域。单击(并展开)图像后，JavaScript 会在被单击的 div 所在行的最后一个图像单元格后插入一个间隔 div。JavaScript 将其高度设置为与展开的 div 相同，并将展开的 div 完全定位到间隔 div 所占据的位置。这很聪明，但并不理想，因为它严重依赖 JavaScript。

我有一个基本的想法，我已经设法开发成一个工作演示使用 CSS 的所有布局和框模型属性。唯一需要的 JavaScript 是根据扩展开关更改类名。

## 基本标记

首先，我们需要连同每个`.image__cell`一起构造`.image-grid`容器。这是 HTML:

```
<section class="image-grid">
  <div class="image__cell is-collapsed">
    <div class="image--basic">
      <a href="#expand-jump-1">
        <img id="expand-jump-1" 
                 class="basic__img"
                 src="https://lorempixel.com/250/250/fashion/1" alt="Fashion 1">
      </a>
      <div class="arrow--up"></div>
    </div>
    <div class="image--expand">
      <a href="#close-jump-1" class="expand__close"></a>
      <img class="image--large" 
           src="https://lorempixel.com/400/400/fashion/1" alt="Fashion 1">
    </div>
  </div>
  ...
</section>
```

上面的标记包含一个示例`.image cell`元素，需要为网格中的每个图像复制该元素。请注意`#close-jump-1`和`#expand-jump-1`的标识符，后续的`href`属性需要对`.image__cell`是唯一的。散列链接，如:`href="#expand-jump-1"`使浏览器在按下时跳转到活动图像单元格。

## CSS

首先，我们使用通用选择器将`box-sizing: border-box;`应用于所有元素，包括`:before`和`:after`伪元素。这将允许轻松处理混合百分比宽度和固定填充值的元素，因为它组合了它们。

```
/* apply a natural box layout model to all elements,
   but allowing components to change */

html {
  box-sizing: border-box;
}

*, *:before, *:after {
  box-sizing: inherit;
}
```

给`.image-grid`元素一个 [clearfix](https://www.sitepoint.com/clearing-floats-overview-different-clearfix-methods/#method-2-the-overflow-way) `overflow: hidden;`来维护基于图像单元格浮动的布局。

```
.image-grid {
  width: 100%;
  max-width: 1310px;
  margin: 0 auto;
  overflow: hidden;
  padding: 10px 5px 0;
}

.image__cell {
  float: left;
  position: relative;
  width: 20%;
}

.image--basic {
  padding: 0 5px;
}

.basic__img {
  display: block;
  max-width: 100%;
  height: auto;
  margin: 0 auto;
}

.image__cell.is-collapsed .arrow--up {
  display: block;
  height: 10px;
  width: 100%;
}

.image--large {
  max-width: 100%;
  height: auto;
  display: block;
  padding: 40px;
  margin: 0 auto;
  box-sizing: border-box;
}
```

图像单元格的宽度等于 100 除以每行的项目数，以百分比表示。在本例中，每行有 5 个项目，这意味着每个`.image__cell`的宽度为 20%。

请注意，`padding: 10px 5px 0;`应用于`.image-grid`并与`.image--basic`上的`padding: 0 5px;`和`.image__cell.is-collapsed .arrow--up`上的`height: 10px;`相结合，在平铺图像周围产生相等的窗口框架效果。我们可以通过改变这些值来增加图像之间的间隙。

最后，`.basic__img`图像元素被赋予`display: block;`以防止任何间距问题。`max-width: 100%;`和`height: auto;`声明使图像能够缩放到其容器的宽度，同时不超过其自身的宽度。

下面的 CSS 提供了可扩展区域的布局。

```
.image__cell.is-collapsed .image--basic {
  cursor: pointer;
}

.image__cell.is-expanded .image--expand {
  max-height: 500px;
  margin-bottom: 10px;
}

.image--expand {
  position: relative;
  left: -5px;
  padding: 0 5px;
  box-sizing: content-box;
  overflow: hidden;
  background: #222;
  max-height: 0;
  transition: max-height .3s ease-in-out,
              margin-bottom .1s .2s;
  width: 500%;
}
```

以下是从上述代码中提取的一些注意事项:

*   当光标仅处于折叠状态时，悬停在`.image--basic`上时，光标变为指针。这为用户提供了一个视觉指示器，表明点击图像将会有所动作。
*   `.image-expand`元素的`max-height`在其初始状态下被设置为 0。当`.image-cell`元素具有`.is-expanded`类时，`max-height`的值为 500px。请注意，如果扩展区域要增加，最大高度值也需要增加，以便显示整个区域。
*   当切换扩展区域时，应用于`max-height`和`margin-bottom`的过渡允许滑动效果。
*   视觉上，我们希望扩展区域与`.image-grid`对齐。为此，我们需要否定在`.image-grid`元素中设置的水平填充。

    1.  首先，`.image--expand`被赋予`box-sizing: content-box;`从其宽度中排除填充值。
    2.  `.image--expand`元素的宽度是其父元素`500%`的 5 倍。这允许扩展区域占据`.image-grid`的整个宽度，减去填充。
    3.  为了占据剩余的空间，`.image--expand`元素被赋予 5px 左右的填充。
    4.  `position: relative;`和`left: -5px`声明将扩展区域稍微向左移动，以抵消`.image-grid`的左填充值。

## 聪明的一点

我们希望将所有的`.image--expand`元素移动到最左侧，与`.image-grid`的左侧对齐。为此，我们根据它在行中的位置设置一个负边距。

这就是`nth-of-type`的用武之地:

```
.image__cell:nth-of-type(5n+2) .image--expand {
  margin-left: -100%;
}

.image__cell:nth-of-type(5n+3) .image--expand {
  margin-left: -200%;
}

.image__cell:nth-of-type(5n+4) .image--expand {
  margin-left: -300%;
}

.image__cell:nth-of-type(5n+5) .image--expand {
  margin-left: -400%;
}
```

最初，我使用`nth-child`来达到同样的效果，但在其他项目中，我发现 [iOS8 Safari 在这一点上很容易出错](http://stackoverflow.com/questions/26032513/ios8-safari-after-a-pushstate-the-nth-child-selectors-not-works)，所以我尽量避免使用它。相反，我使用`nth-of-type`,因为它在很大程度上服务于相同的目的。如果你感兴趣，你可以在这里找到对`nth-of-type`的简要解释

在上面的 CSS 中，我们的目标是每行上的第二、第三和第四个`.image__cell`可扩展区域。`margin-left`值也取决于元素在行中的位置。注意，每行的第一个元素不需要设置`margin-left`值，因为它已经在期望的位置了。元素离左侧越远，我们就越需要将可扩展区域推回到左侧(增量为-100%)。如果不这样做，可扩展区域将与其父区域对齐，如下所示:

![Alignment problem](img/c36006517e0de025eae3e9ff57c2833b.png)

我们还需要插入如下所示的 CSS，以确保当前面的`.image__cell`元素被展开时，除了第一行之外的每一行的第一个`.image__cell`都保持在原来的位置。

```
.image__cell:nth-of-type(5n+6) {
  clear: left;
}
```

既然基本布局已经就绪，我们可以添加一些样式来改善用户体验。

首先，一个向上的箭头指示扩展块属于哪个图像:

```
.image__cell.is-expanded .arrow--up {
  display: block;
  border-bottom: 8px solid #222;
  border-left: 8px solid transparent;
  border-right: 8px solid transparent;
  height: 0;
  width: 0;
  margin: 2px auto 0;
}
```

注意，箭头样式是通过创建一个 [CSS 三角形](http://css-tricks.com/snippets/css/css-triangle/)来实现的，这样就保存了一个 HTTP 请求。巧妙利用边框，将高度和宽度设置为 0，就可以轻松实现这种效果。

我们还希望箭头只在展开`.image__cell`元素时出现。这是通过添加`.is-expanded`类来实现的。最后，我们希望箭头保持在`.image__cell`元素的水平中心，所以应用了`margin: 0 auto;`。

现在我们已经准备好设计“关闭”按钮的样式，它将允许用户关闭扩展区域。

```
.expand__close {
  position: absolute;
  top: 10px;
  right: 20px;
  color: #454545;
  font-size: 50px;
  line-height: 50px;
  text-decoration: none;
}

.expand__close:before {
  content: '×';
}

.expand__close:hover {
  color: #fff;
}
```

注意，通过使用一个`:before`伪元素，我们能够在页面上插入一个‘×’字符，而不会出现在 DOM 中，这又节省了至少一个 HTTP 请求。插入的特殊字符是 [Boostrap 也使用](http://getbootstrap.com/2.3.2/components.html#misc)的乘法字符。

## jQuery

最后，下面的 jQuery 只是在单击每个图像单元格和关闭按钮时在`.is-expanded`和`.is-collapsed`类之间切换。

```
var $cell = $('.image__cell');

$cell.find('.image--basic').click(function() {
  var $thisCell = $(this).closest('.image__cell');

  if ($thisCell.hasClass('is-collapsed')) {
    $cell.not($thisCell).removeClass('is-expanded').addClass('is-collapsed');
    $thisCell.removeClass('is-collapsed').addClass('is-expanded');
  } else {
    $thisCell.removeClass('is-expanded').addClass('is-collapsed');
  }
});

$cell.find('.expand__close').click(function() {
  var $thisCell = $(this).closest('.image__cell');
  $thisCell.removeClass('is-expanded').addClass('is-collapsed');
});
```

当然，通过使用“classList()”和其他本机技术，您可以很容易地完全避免 jQuery，但是除非您愿意进行聚合填充，否则您不会获得如此深入的浏览器支持。

## 让电网做出响应

在较小的设备上每行有 5 个图像单元并不理想，所以我们可以使用媒体查询来改变每行的项目数。例如，下面的 CSS 将其减少到每行 2 个图像。

```
@media only screen and (max-width: 530px) {

    .image__cell {
      width: 50%;
    }

    .image__cell:nth-of-type(2n+2) .image--expand {
      margin-left: -100%;
    }

    .image__cell:nth-of-type(2n+3) {
      clear: left;
    }

    .image--expand {
      width: 200%;
    }

}
```

为了防止前面应用的 CSS 与每行 5 个项目相关，我们需要重置这些值或提取属性并将它们放在自己的媒体查询中，这在下面的 CodePen 中与前面的代码一起完成。

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )在 CSS 中查看笔[谷歌图片搜索。](http://codepen.io/SitePoint/pen/EabNrX/)