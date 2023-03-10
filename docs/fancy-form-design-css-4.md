# 使用 CSS 文章的花式表单设计

> 原文：<https://www.sitepoint.com/fancy-form-design-css-4/>

**解决 Internet Explorer 的传奇问题**

在完全出乎意料的情况下(是的，没错！)Internet Explorer 处理图例的方式与其他浏览器不同。根据实验，似乎 Internet Explorer 将`[legend](https://reference.sitepoint.com/html/legend)`元素视为在`[fieldset](https://reference.sitepoint.com/html/fieldset)`的内的*，而其他浏览器将它们视为在`[fieldset](https://reference.sitepoint.com/html/fieldset)`的*外的*。我不是说任何浏览器都是错的，但我们必须以某种方式规避这些差异，创建一个单独的 IE 样式表似乎是最好的解决方案。*

如果你把一个`[background-color](https://reference.sitepoint.com/css/background-color)`和一个`[legend](https://reference.sitepoint.com/html/legend)`放在一个`[fieldset](https://reference.sitepoint.com/html/fieldset)`上，如图 14 所示，你就能清楚地看到这个问题。

![legend-differences](img/af592e0f78ae446da7f7087f092278f6.png)
*图 14:带有背景色的`[fieldset](https://reference.sitepoint.com/html/fieldset)`元素的浏览器渲染([在新窗口中查看大图](https://i2.sitepoint.com/graphics/forms_legend-differences.png))。)*

左边的`[fieldset](https://reference.sitepoint.com/html/fieldset)`显示了大多数浏览器如何用背景色渲染`[legend](https://reference.sitepoint.com/html/legend)`和`[fieldset](https://reference.sitepoint.com/html/fieldset)`。右边的`[fieldset](https://reference.sitepoint.com/html/fieldset)`显示了 Internet Explorer 是如何渲染它的——`[fieldset](https://reference.sitepoint.com/html/fieldset)`的`[background-color](https://reference.sitepoint.com/css/background-color)`似乎超出了它的边框，伸展到适合`[legend](https://reference.sitepoint.com/html/legend)`的高度。

避免此问题的方法是为 Internet Explorer 浏览器提供一个使用条件注释的单独样式表:

```
<!--[if lte IE 7]>     
<style type="text/css" media="all">     
@import "css/fieldset-styling-ie.css";     
</style>    
<![endif]-->
```

//

此声明包括 Internet Explorer 7 和更早版本的样式表，因为这些版本当前显示这种异常行为。其他任何浏览器都会忽略它。我们可以使用适用于任何版本的 Internet Explorer 的样式表——包括将来发布的版本——但是到那时,`[legend](https://reference.sitepoint.com/html/legend)`显示差异可能会得到纠正，所以最安全的做法是将其应用于我们目前已知的版本。

在样式表中，我们使用`[legend](https://reference.sitepoint.com/html/legend)`上的相对定位将其向上移动，以与`[fieldset](https://reference.sitepoint.com/html/fieldset)`的顶部对齐:

```
legend {     
position: relative;     
left: -7px;     
top: -0.75em;    
}    
fieldset ol {     
padding-top: 0.25em;    
}
```

在这种情况下，我们给`[legend](https://reference.sitepoint.com/html/legend)`的`top` — `0.75em`的值恰好是让`[legend](https://reference.sitepoint.com/html/legend)`与`[fieldset](https://reference.sitepoint.com/html/fieldset)`对齐的正确值。这可能取决于我们可能应用于`[legend](https://reference.sitepoint.com/html/legend)`的其他样式(例如`[margin](https://reference.sitepoint.com/css/margin)`和`[padding](https://reference.sitepoint.com/css/padding)`)。这是一个相当健壮的解决方案——我们使用了相对单位，所以如果用户改变浏览器中的文本大小,`[legend](https://reference.sitepoint.com/html/legend)`的位置会相应地移动，并且仍然对齐。

除了移动`[legend](https://reference.sitepoint.com/html/legend)`的顶部，我们通过应用`-7px`的`[left](https://reference.sitepoint/com/css/left)`值将其向左移动 7px。这一步与 Internet Explorer 的一个怪癖相反——IE 总是将图例向右移动 7px(不管文本大小如何),所以我们需要取消这种移动，以使`[legend](https://reference.sitepoint.com/html/legend)`和`[label](https://reference.sitepoint.com/html/label/)`元素整齐排列。

因为我们相对上移了图例，所以它会在`[legend](https://reference.sitepoint.com/html/legend)`下面创造更多的空间。为了抵消这个空间，我们将有序列表顶部的填充减少相同的量，将其从原始值`1em`更改为`0.25em`。

Internet Explorer 的最后一个修正是相对定位`[fieldset](https://reference.sitepoint.com/html/fieldset)`本身:

```
fieldset {     
position: relative;    
}
```

如果没有这条规则，Internet Explorer 会在`[legend](https://reference.sitepoint.com/html/legend)`周围产生一些奇怪的视觉效果。有多奇怪？你可以在图 5.15 中看到这有多奇怪。

![ie-weird](img/ecea83bd704e41deda499b4077906f7b.png)
*图 15:Internet Explorer 中的视觉像差([在新窗口中查看大图](https://i2.sitepoint.com/graphics/forms_ie-weird.png))。)*

我们真的需要避免我们已经看到的 IE 失常，但我们就快到了——现在我们只需将`[fieldset](https://reference.sitepoint.com/html/fieldset)`的`[position](https://reference.sitepoint.com/css/position)`设置为`relative`,让一切恢复正常。

**样式图例和字段集**

在所有浏览器中，`[legend](https://reference.sitepoint.com/html/legend)` s 默认会有一些`[padding](https://reference.sitepoint.com/css/padding)`。不同浏览器的`[padding](https://reference.sitepoint.com/css/padding)`数量不同，所以为了让`[legend](https://reference.sitepoint.com/html/legend)`与`[label](https://reference.sitepoint.com/html/label)`很好地匹配，我们将在主样式表中去掉`[padding](https://reference.sitepoint.com/css/padding)`:

```
fieldset-background-color.css (excerpt)    
legend {     
margin-left: 1em;     
padding: 0;     
color: #000;     
font-weight: bold;    
}
```

`[fieldset](https://reference.sitepoint.com/html/fieldset)`元素的默认`[border](https://reference.sitepoint.com/css/border)`通常是一个内嵌边框——这与一些网站不匹配——所以这里我们将把它做成一个 1px 的平面边框。此外，我们将添加一种背景颜色，使`[fieldset](https://reference.sitepoint.com/html/fieldset)`元素从普通页面背景中突出出来，将它们标记为特殊区域:

```
fieldset-background-color.css (excerpt)    
fieldset {     
float: left;     
clear: both;     
width: 100%;     
margin: 0 0 1.5em 0;     
padding: 0;     
border: 1px solid #BFBAB0;     
background-color: #F2EFE9;    
}
```

一般来说，我们不希望 submit `[fieldset](https://reference.sitepoint.com/html/fieldset)`后面有任何边框或背景颜色，所以很容易关闭它们:

```
fieldset-background-color.css (excerpt)    
fieldset.submit {     
float: none;     
width: auto;     
border-style: none;     
padding-left: 12em;     
background-color: transparent;    
}
```

现在我们有了带有背景色的`[fieldset](https://reference.sitepoint.com/html/fieldset)`元素和与所有其他表单元素整齐排列的`[legend](https://reference.sitepoint.com/html/legend)`,如图 16 所示。

![fieldset-background-color](img/9336b76e026ced84e3cc9c9034b2a751.png)
*图 16: `[fieldset](https://reference.sitepoint.com/html/fieldset)`元素用`[background-color](https://reference.sitepoint.com/css/background-color)`设置并调整为`[legend](https://reference.sitepoint.com/html/legend)`*

从图 17 所示的`[legend](https://reference.sitepoint.com/html/legend)`的放大图中可以看出，`[legend](https://reference.sitepoint.com/html/legend)`后面的颜色分割有时看起来有点突兀。

![fieldset-background-color-zoom](img/15a73012af400d199564d5e1301541d0.png)
*图 17:`[legend](https://reference.sitepoint.com/html/legend)`的放大图——背景色的截止明显*

如果我们使用与普通页面背景色有更多对比的`[fieldset](https://reference.sitepoint.com/html/fieldset)`背景色，这种截止会变得更加明显。如果您想抵消这种影响，可以在`[fieldset](https://reference.sitepoint.com/html/fieldset)`中放置一个渐变背景图像，将页面背景颜色(白色)平滑地变为您选择的`[fieldset](https://reference.sitepoint.com/html/fieldset)`背景颜色:

```
fieldset-background-image.css (excerpt)    
fieldset {     
float: left;     
clear: both;     
width: 100%;     
margin: 0 0 1.5em 0;     
padding: 0;     
border: 1px solid #BFBAB0;     
background-color: #F2EFE9;     
background-image: url(images/fieldset_gradient.jpg);     
background-repeat: repeat-x;    
}
```

那个`[background-image](https://reference.sitepoint.com/css/background-image)`规则也将应用于我们的提交`[fieldset](https://reference.sitepoint.com/html/fieldset)`，所以为了保持一个干净、透明的背景，我们还必须取消`[fieldset](https://reference.sitepoint.com/css/ on the submit <code>/#l#/https://reference.sitepoint.com/html/fieldset)`:

```
fieldset-background-image.css (excerpt)    
fieldset.submit {     
float: none;     
width: auto;     
border-style: none;     
padding-left: 12em;     
background-color: transparent;     
background-image: none;    
}
```

参见图 18——表单看起来平滑多了，不是吗？

![fieldset-background-image](img/05c363c0a77f093142ca08a3c7be64b6.png)
*图 18: `[fieldset](https://reference.sitepoint.com/html/fieldset)`应用了背景色和渐变图像的元素*

**Go to page:** [1](/fancy-form-design-css) | [2](/fancy-form-design-css-2/) | [3](/fancy-form-design-css-3/) | [4](/fancy-form-design-css-4/) | [5](/fancy-form-design-css-5/) | [6](/fancy-form-design-css-6/) | [7](/fancy-form-design-css-7/)

## 分享这篇文章