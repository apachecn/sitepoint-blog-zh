# 如何在网格布局中排序和对齐项目

> 原文：<https://www.sitepoint.com/order-align-items-grid-layout/>

![Order and Alignment Properties in CSS Grid Layout Module](img/44ee66d20d8151a7beafab48f0332cd5.png)

在本教程中，您将学习如何使用网格布局模块控制项目的放置顺序。之后，我们将讨论如何控制网格中不同项目的对齐。

在过去，我们讨论了一些与网格相关的重要主题。我们从学习[在网格布局中放置元素的不同方式](https://www.sitepoint.com/seven-ways-you-can-place-elements-using-css-grid-layout/)开始，然后继续学习[网格自动放置算法](https://www.sitepoint.com/a-step-by-step-guide-to-the-auto-placement-algorithm-in-css-grid/)的步骤，因为它会自动放置不同的项目。

如果你想跟随本文中的演示，我推荐你使用最新的 [Firefox(撰写本文时的版本 52)或 Chrome(撰写本文时的版本 57)](http://caniuse.com/#search=css%20grid)。默认情况下，这两个版本都支持网格布局。

## Order 属性在网格布局中的工作方式

属性可以用来指定不同条目在网格中的放置顺序。默认情况下，项目按照它们在 DOM 中出现的顺序放置。例如，如果在实际的源文档中，A 项位于 B 项之上，那么它也将被放在 B 项之前的网格中，这可能是也可能不是所希望的行为，具体取决于您的项目。

属性非常有用，尤其是当有很多条目或者条目是动态添加的时候。在这种情况下，如果您希望一个项目总是放在网格的末尾，您可以使用`order`属性轻松地做到这一点。

具有最低顺序值**的项目被放在网格中的第一个**位置。具有较高**值的项目被放在后面**。具有**相同订单值的项目将按照它们在源文件**中出现的顺序排列。

让我们看一个例子。

这是我们的标记:

```
<div class="container">
  <div class="item a">A</div>
  <div class="item b">B</div>
  <div class="item c">C</div>
  <div class="item d">D</div>
  <div class="item e">E</div>
  <div class="item f">F</div>
  <div class="item g">G</div>
  <div class="item h">H</div>
  <div class="item i">I</div>
  <div class="item j">J</div>
</div>
```

下面是放置网格项目的 CSS:

```
.c {
  grid-row-start: 1;
  grid-row-end: 2;
}

.e {
  grid-row-start: 1;
  grid-row-end: 3;
}

.b, .j {
  order: 2;
}

.a, .i {
  order: 3;
}
```

![CSS Grid Layout Order Property Demo](img/44407d5aea05015e95b2b0cced6f9c5a.png)

如果您回忆一下[自动放置算法](https://www.sitepoint.com/a-step-by-step-guide-to-the-auto-placement-algorithm-in-css-grid/)教程中的步骤，您会知道该算法会在放置没有明确位置的项目之前放置具有明确指定行位置的项目。因此，即使没有确定行或列位置的 D 项在实际文档中位于 E 项之前，它仍将位于 E 项之后(E 项具有确定的行位置:`grid-row-start: 1`和`grid-row-end: 3`)。

在没有明确位置的项目中，`order`值最低的项目将被放在最前面。这就是为什么项目 D、F、G 和 H 被放置在项目 A 和 B 之前。B 和 J 具有相同的`order`值，因此它们被以它们在文档中出现的相同顺序放置。请注意，B 和 J 仍然放在 A 和 I 之前，因为它们的`order`值更低。

通过 [CodePen](http://codepen.io) 上的 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )查看网格布局中的笔[order 属性。](http://codepen.io/SitePoint/pen/zZrdWP/)