# 面向初学者的 Flexbox 友好介绍

> 原文：<https://www.sitepoint.com/flexbox-css-flexible-box-layout/>

近年来，CSS 布局已经成熟，用于复杂布局的专用工具取代了使用表格、浮动、绝对定位等各种变通方法。 [Flexbox](http://dev.w3.org/csswg/css3-flexbox/) (或柔性盒布局模块)是这些专用布局模块中的第一个，随后是 [CSS 网格布局](https://www.sitepoint.com/introduction-css-grid-layout-module/)。在本文中，我们提供了一个用户友好的 flexbox 介绍。

随着 CSS 网格布局的引入，你可能会问是否需要 flexbox。虽然它们可以做的事情有一些重叠，但是它们在 CSS 布局中都有非常具体的目的和角色。作为一个非常通用的规则，flexbox 的优势在于一维布局(比如一串相似的项目)，而 Grid 非常适合二维布局(比如整个页面的元素)。

也就是说，flexbox *可以*用于全页面布局，从而为尚不支持网格的浏览器中的网格布局提供了一个方便的后备。(不可否认，对网格的支持已经在大多数现代浏览器中迅速[传播，但是](https://caniuse.com/#feat=css-grid)[对 flexbox](https://caniuse.com/#feat=flexbox) 的支持仍然更广泛，如果你需要你的布局在一些稍微老一点的浏览器中工作，这是很方便的。)

## 使用 Flexbox 的优势

flexbox 的一些优势包括:

*   页面内容可以向任何方向布局(向左、向右、向下甚至向上)
*   一些内容的视觉顺序可以颠倒或重新排列
*   项目可以“伸缩”它们的大小以适应可用空间，并且可以相对于它们的容器或彼此对齐
*   实现等列布局(不考虑每列的内容量)轻而易举。

为了说明各种属性和可能性，让我们为本文中的一些演示假设以下简单的布局:

```
<div class="example">
  <header>
    header content here
  </header>
  <main class="main">
    <nav>
      nav content here
    </nav>
    <div class="content">
      main content here
    </div>
    <aside>
      aside content here
    </aside>
  </main>
  <footer>
    footer content here
  </footer>
</div>
```

第一步是将元素并排放置在`.main`中，即`<nav>`和`<aside>`。如果没有 flexbox，我们可能会浮动所有这三个元素，但让它按预期工作不会很简单。此外，传统的做事方式会带来一个众所周知的问题:每一列都和它的内容一样高。因此，你需要为所有三列设置相同的高度，或者使用某种[黑客](http://pmob.co.uk/pob/equal-columns.htm)。

进入 flexbox 拯救世界。

## 让我们放松一下

flexbox 的核心元素是`display`属性的新`flex`值，需要为容器元素设置。这样做可以将其子项转换为“灵活项目”。默认情况下，这些项目会获得一些方便的属性。例如，它们并排放置，没有指定宽度的元素会自动占据剩余的空间。

因此，如果您为`.main`设置了`display: flex`，那么它的`.content`子元素会自动被夹在`<nav>`和`<aside>`之间。不用再计算了，多方便啊？作为一个特别的奖励，所有这三个元素神奇地具有相同的高度。

```
.main {
  display: flex;
}
```

查看下面的演示，了解所有细节:

参见钢笔 [Flexbox 教程:在](https://codepen.io/SitePoint/pen/bRjmNN/) [CodePen](https://codepen.io) 上通过 SitePoint ( [@SitePoint](https://codepen.io/SitePoint) )显示属性。