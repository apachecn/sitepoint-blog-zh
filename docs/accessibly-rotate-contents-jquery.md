# 如何使用 jQuery 方便地旋转内容

> 原文：<https://www.sitepoint.com/accessibly-rotate-contents-jquery/>

与[视差滚动效果](https://www.sitepoint.com/introduction-parallax-scrolling-using-stellar-js/)一起，旋转内容是你经常在网站中看到的另一个实现效果的例子。你可以看到它被用来旋转新闻、推文、脸书帖子等等。当使用 jQuery 构建时，开发人员通常使用`hide()`和`show()`方法(或类似的方法，如`fadeIn()` / `fadeOut()`和`slideUp()` / `slideDown()`)来创建小部件。它们的问题是，在执行动画(如果有的话)之后，这些方法分别将所选元素的`display`属性的值更改为`none`和返回原始值。这种行为会导致可访问性问题。

在本文中，我们将描述问题是什么，以及如何使用不同的 jQuery 方法来达到相同的效果，但要注意可访问性。

## 问题是

一些人，通常是但不限于视力受损的人，使用`TAB`键来导航网站。如果你不熟悉这个概念，那么每次用户按下`TAB`键，页面的一个*可聚焦的*元素就会被聚焦。元素被聚焦的顺序遵循它们在 DOM 中出现的顺序([异常适用](https://www.sitepoint.com/learning-to-focus/))，从页面的最开始开始。一些可聚焦元素的例子(在 <q>[中了解更多关于元素何时成为焦点的信息？](https://www.sitepoint.com/when-do-elements-take-the-focus/)</q> )是链接、输入字段、按钮以及所有`tabindex`属性的值大于或等于`0`的东西([了解何时以及如何使用`tabindex`](https://www.sitepoint.com/5-things-learned-accessibility/) )。需要理解的重要一点是，通过将其`display`属性设置为`none`而隐藏的元素是*不可聚焦的。*

记住这一点，你可以看到，如果一个`TAB`用户正在聚焦一个使用`hide()`方法隐藏的元素(例如一个链接),然后用户按下`TAB`键，就会出现问题。以这种方式隐藏就像元素被临时从 DOM 中移除，所以没有下一个元素需要关注。浏览器在这种情况下所做的是重置位置，通常是聚焦页面的 URL。这给用户带来了很多挫折，因为每当他们到达这个*死亡区域*时，他们都必须从页面的开始处开始。更有甚者，你的一些用户甚至看不到你令人敬畏的旋转效果——他们想做的只是方便地访问你的内容。

## 给我看看代码

为了更好地处理这种情况，请考虑以下标记:

```
<div class="rotating-content">
   <p>This is a text with a <a href="#">link 1</a> and <a href="#">another link 1</a></p>
   <p>This is a text with a <a href="#">link 2</a> and <a href="#">another link 2</a></p>
   <p>This is a text with a <a href="#">link 3</a> and <a href="#">another link 3</a></p>
   <p>This is a text with a <a href="#">link 4</a> and <a href="#">another link 4</a></p>
</div>
```

要旋转`div`的内容，您可以编写如下代码:

```
$elements = $('.rotating-content').find('p');
$elements.hide().first().show();

setInterval(function() {
   $elements.filter(':visible').fadeOut('slow', function() {
      $next = $(this).next();
      if ($next.length === 0) {
         $next = $elements.first(); 
      }
      $next.fadeIn('slow');
   });
}, 4000);
```

将一切付诸行动会产生[下面的演示](https://jsfiddle.net/redLsn8z/):

[https://jsfiddle.net/redLsn8z/embedded/](https://jsfiddle.net/redLsn8z/embedded/)

## 可访问的旋转内容

为了解决这个可访问性问题，同时达到相同的效果，我们需要使用不同的 jQuery 方法`fadeTo()`，以及我最喜欢的 CSS 助手类之一，通常称为`visuallyhidden`或`visually-hidden`。这个助手类的代码如下所示:

```
.visually-hidden
{
   border: 0;
   clip: rect(0 0 0 0);
   height: 1px;
   margin: -1px;
   overflow: hidden;
   padding: 0;
   position: absolute;
   width: 1px;
}
```

将该类添加到任何元素中都会使**在视觉上隐藏**该元素，而不会将其`display`属性设置为`none`。

现在，我们将根据需要添加或移除`visually-hidden`类，而不是使用`hide()`方法隐藏元素或`show()`方法显示元素。此外，为了重新创建漂亮的动画，我们将使用`fadeTo()`方法。后者允许你设置你希望元素达到的不透明度，但是当它完成任务时，它不会更新`display`属性(要了解更多关于这个方法的信息，你可以参考官方文档)。因此，当我们想要隐藏元素时，我们将把值`0`传递给它，当我们想要显示元素时，将值`1`传递给它。最后，我们还会将所有想要制作动画的元素的初始`opacity`值设置为`0`。

生成的代码如下所示:

```
$elements = $('.rotating-content').find('p');
$elements
   .not(':first-child')
   .addClass('visually-hidden')
   .css('opacity', 0);

setInterval(function() {
   $elements.filter(':not(.visually-hidden)').fadeTo('slow', 0, function() {
      $next = $(this).addClass('visually-hidden').next();
      if ($next.length === 0) {
         $next = $elements.first(); 
      }
      $next.removeClass('visually-hidden').fadeTo('slow', 1);
   });
}, 4000);
```

将一切付诸行动会产生[下面的演示](https://jsfiddle.net/9b27ps4q/):

[https://jsfiddle.net/9b27ps4q/embedded/](https://jsfiddle.net/9b27ps4q/embedded/)

通过代码中的这一简单更改，我们创建了一个更易于访问的小部件来显示旋转内容。为了了解两个演示之间的区别，我邀请您使用`TAB`键来浏览页面。

## 结论

在本文中，我描述了一个重要的可访问性问题，这个问题可以在一些用于创建旋转内容效果的代码中找到。你们中的一些人可能无意中把它添加到了一个建立在小部件中的网站上，就像我们讨论的那样，但是这没有什么好羞愧的。每个人在了解一些重要的概念之前都会忽略它们。你的读者不久前才真正发现了这个重要的问题(感谢史蒂夫·福克纳和 T2·莱奥妮·沃森等人的工作)。

虽然学习这个简单的技巧可能会对你有所帮助，但这里的主要教训是，当你开发网站的任何功能时，你也应该验证它是可访问的，否则你可能会将一些用户锁定在令人沮丧和无法访问的内容的地狱中。

## 分享这篇文章