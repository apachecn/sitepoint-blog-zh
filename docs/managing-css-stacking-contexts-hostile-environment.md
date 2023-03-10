# 在“敌对”环境中管理 CSS 堆栈上下文

> 原文：<https://www.sitepoint.com/managing-css-stacking-contexts-hostile-environment/>

注:这是我最近为雅虎写的一份提案。它是为外部观众编辑的。

渲染树被绘制到画布上的顺序是根据堆叠上下文来描述的。

当作者不熟悉页面的堆叠环境或者只是忘记了他们的状态时，事情就变得复杂了。

## 互动广告局指南`z-index`

从 T2 的角度来看，事情非常简单。他们有一个完整的表格，为几乎所有类型的内容指定`z-index`范围*，而不仅仅是广告。*

| z 指数范围 | 内容类型 | 细节 |
| --- | --- | --- |
| < 0 | 背景元素 |  |
| 0 – 4,999 | 主要内容，标准广告 | 标准广告标签与常规内容放在一起。包括 OBA 自律信息(清晰广告通知) |
| 5,000 – 1,999,999 | 扩张广告 | 整个可扩展广告单元应设置在此范围内 |
| 2,000,000 – 2,999,999 | 浮动广告 | 页面广告(OTP) |
| 3,000,000 – 3,999,999 | 弹出元素 | 聊天窗口、消息通知 |
| 4,000,000 – 4,999,999 | 非锚定浮动元素 | 调查招聘小组 |
| 5,000,000 – 5,999,999 | 展开站点导航元素 | 下拉导航、网站警告等。只有导航元素的扩展部分应该包含在这一层。 |
| 6,000,000+ | 整页覆盖 | 全窗口跨页(OTP)广告和页面间广告(如果它们覆盖页面内容) |

*来源: [IAB 展示广告指南](http://www.iab.net/guidelines/508676/508767/displayguidelines)*

## `z-index`到底能有多大？

CSS 规范没有提到`z-index`的上限，但是*有一个最大值*，这是因为用来存储该值的变量类型(32 位有符号整数)；因此，现代浏览器的极限是`2,147,483,647`。

尽管如此，还是有广告指南推荐使用`2,147,483,648`。

当使用更大的值时会发生什么，就像我曾经在一个页面中发现的一样？

```
<div id="fresco-thirdparty-tag" style="position: absolute;
z-index: 10000000000; display: none; top: 0; left: 0;
width: 970px; height: 250px;">
```

现代浏览器将这些值视为上限。换句话说，使用`100,000,000,000`和使用`2,147,483,647`是一样的。

### 这些有关系吗？

简短的回答:**不，一点也不重要**。

这是因为堆叠上下文是原子性的:

> 堆叠上下文可以包含更多的堆叠上下文。从其父堆栈上下文的角度来看，堆栈上下文是原子的；其他堆叠上下文中的盒子不能出现在它的任何盒子之间。
> 
> [9.9 分层演示](https://www.w3.org/TR/CSS2/visuren.html#z-index)

换句话说，**没有`z-index`值可以改变一个盒子相对于它所属的**栈之外的盒子的位置。这就是为什么在不了解文档树和页面的堆叠上下文的情况下，不可能以可预测的方式管理堆叠。

## 那么`z-index`指南是为了什么？

这些指导方针是基于什么？当绘制渲染树时，源顺序以及祖先的 z 顺序起着太大的作用——所以简单地设置`z-index`范围**不能成为**的解决方案。

例如，如果你看一下 IAB Z-Index 指南的动态演示，你会发现事情进展顺利*，因为它们是针对一组特定的需求*而设计的；但实际上，这是一个相当脆弱的构造，因为即使是简单的更改也可能需要重新考虑页面中的大多数堆叠上下文。

首先，想象一下用`position: fixed`设计演示页面的“Header”样式(这样当用户向下滚动时，它会停留在视窗的顶部)；这里应该使用什么`z-index`值？因为页眉需要显示在“300×250 Base Ad”之上，所以它的`z-index`至少应该是`2,000,000`。但是请注意，任何高于该值的内容都会使标题与“浮动广告”竞争，浮动广告的“T4”范围是“T5”。

现在假设我们在左/右导航上方有一个搜索框，带有一个下拉菜单“[搜索助手](http://searchengineland.com/search-suggestions-on-steroids-yahoo-search-assist-11791)”；这里应该使用什么`z-index`值？因为下拉菜单需要显示在导航上，所以它的`z-index`值至少应该是`6,000,000`。但是请注意，任何高于该值的值都会使下拉列表与“全页覆盖”竞争，因为“全页覆盖”的范围是“T4”(即使两者同时显示的可能性很小)。

当然，也有与“国家”相关的问题。任何给定的盒子都可能需要根据其行为或周围盒子的行为(下拉、弹出、工具提示、展开广告等)在堆栈中放置不同的位置。).在这种情况下，`z-index`值需要动态更新–参见下面的演示。

### 导致灾难的因素

正如 IAB 演示所示，不属于某个堆栈的盒子可以放置在*相对于其他堆栈*的任何位置。这就是为什么管理“粒度”堆栈需要集中`z-index`值并执行与上下文相关的严格规则——这在多团队环境中*注定会失败*,在这种环境中，开发人员可能无法访问数据或工具(即预处理器文件中的[变量)。](http://www.smashingmagazine.com/2014/06/12/sassy-z-index-management-for-complex-layouts/)

## 建议

这是关于实现一个*防御*机制来通过页面管理堆栈上下文。我们需要一个解决方案，允许我们相对于同一堆栈中的其他盒子，甚至是它所属堆栈之外的盒子来定位一个盒子。

### 显式上下文

这个想法是设计页面主框的样式来创建一个明确的“顶层”*堆栈顺序*。这利用了堆栈上下文的原子性质，并确保了最高级别的包容。然后我们可以预测嵌套盒*的行为，而不管它们自己的`z-index`值*。

### 动态堆栈

一旦我们有了顶层堆栈上下文，在堆栈中上下移动一个主框就足以在堆栈中移动它的嵌套框。

允许堆栈上下文的**改变**是至关重要的，因为有时候盒子需要在堆栈中被不同地定位。

当不时查看演示时，SitePoint 会删除单独的 HTML 页面上的多年前的演示。我们这样做是为了降低过时代码的风险，因为暴露的漏洞会给我们的用户带来风险。谢谢你的理解。)，**注意右边横杆顶端黑色圆圈中出现的数字**。该数字就是**列**的`z-index`值。当用户与列中的元素交互时，该值会发生变化。这一改变允许盒子在堆叠上下文之间切换(盒子可以在不同的堆叠中移动)。

### 声明开关

我们依靠`data-*`属性来暴露在堆栈中移动任何给定盒子所需的`z-index`值(参见下面的[代码示例)。](#code-example)

这样，我们可以管理堆栈中盒子的排序，而无需了解页面本身的任何信息(无论是 DOM 树还是堆栈上下文)。

同样的机制可以用于重置主框的`z-index`,以允许其嵌套的框参与主堆栈上下文。

### 更明确的上下文

从主堆叠上下文中移除盒子允许其嵌套堆叠上下文成为更高堆叠的一部分。这意味着这些内部堆栈的`z-index`决定了它们在主箱排序中的位置。

例如，在演示(*)中，SitePoint 会不时删除存放在单独 HTML 页面上的多年前的演示。我们这样做是为了降低过时代码的风险，因为暴露的漏洞会给我们的用户带来风险。谢谢你的理解。*)，当模态被触发时，从顶层堆叠上下文中移除右栏允许广告与页面上的其他主框竞争。换句话说，如果广告的`z-index`为`10+`，那么*就会出现在标题*的顶部。

由于与重置主框的`z-index`相关的风险，最好为我们怀疑样式化为高`z-index`值的模块创建显式堆栈。这些可以是广告或其他模块，页面所有者无法控制。沙箱保护这些元素可以防止盒子出现在“z”轴上的任何地方。这样，我们仍然可以控制这些盒子，即使它们的祖先是用`z-index: auto`设计的。

这两个问题都是由于相同的`z-index`值(最大值)导致的，这些值根据元素在标记中的位置在呈现树中绘制元素。标题显示在广告的后面，因为它在最前面。模态按钮显示在广告前面，因为它在最后。

## 履行

这个解决方案需要跨网格共享一个简单的设置和一个通用的词汇表(当涉及到不同的网格时，例如雅虎网站的情况——金融、体育、主页、答案等等)。).

“四步计划”:

1.  页面上的所有主框必须用除了`auto`之外的`z-index`来定位和样式化。
2.  这些盒子中的每一个都必须通过一个公共类来识别:`stacking-context`。
3.  那些相同的盒子必须有`data-zindex-max`和`data-zindex-top`属性，包含在堆栈中向上移动一个盒子所必需的`z-index`值。
4.  类`inner-stack`被应用到任何模块的*的包装器中，这些模块容易被赋予高`z-index`值。*

例如:

```
<div id="right-rail" class="stacking-context"
data-zindex-max="5" data-zindex-top="10">
<div class="inner-stack">
<div id="fresco-thirdparty-tag"
style="position: absolute; z-index: 10000000000;">
```

**data-zindex-max**
该属性用于声明框允许使用的最高 z 顺序。例如，这可能是一个总是显示在标题后面的列。

**data-zindex-top**该属性用于声明页面内最高的 z 顺序集合。例如，这可能是堆栈中头部的位置。用那个`z-index`值设计的任何元素都将出现在标题的前面(只要该元素出现在源代码的后面)。

**。内部堆栈**
这个类通过阻止模块自己的`z-index`进入来控制模块。这个`z-index`值是由页面/网格所有者通过 CSS 设置的。

## 该解决方案的优势

**它降低了破损的风险**
通过利用堆栈上下文的原子性质，我们可以用沙箱保护模块，使它们的`z-index`值与页面上的其他框无关。

**这是可以预见的**
不再猜谜游戏；作者不需要了解任何关于文档树或其堆叠上下文的知识，就能够在堆叠中移动框。

**它集中了职责**
一切都掌握在负责页面本身的团队手中——这个团队了解文档的一切——它的树、它的堆叠上下文等等。

## 进一步思考

在使用单个网格的项目中，以及在共享一个公共网格的项目中，通过使用预设的规则来基于有意义的类交换`z-index`，事情可以变得更加*声明性*。例如:

*   `.coverTheHeader {...}`
*   `.coverAllColumns {...}`
*   `.coverTheRightRail {...}`
*   `.coverTheLeftRail {...}`
*   `.coverTheMainContent {...}`
*   等等。

## 代码示例

下面是主演示页面中使用的 JavaScript 代码(*site point 会不时删除单独的 HTML 页面上的多年前的演示。我们这样做是为了降低过时代码的风险，因为暴露的漏洞会给我们的用户带来风险。谢谢你的理解。*)。这个脚本负责切换包含广告和“模态”按钮的列的`z-index`值。它还通过重置包装器上设置的`z-index`值来“释放”这些元素。正是这两种风格的结合，使得这些元素即使在应用了非常高的`z-index`的情况下也能按照预期表现。

```
(function(document) {
function findAncestor(el, cls) {
while ((el = el.parentElement) && !el.classList.contains(cls));
return el;
}

// constants
var AUTO = 'auto',
CSS_MODAL_ON = 'modal-on',
CSS_INNER_STACK = 'inner-stack',
DOC = document,
WIN = DOC.defaultView,
HTML = DOC.documentElement,

// elements
theAd = DOC.getElementById('ad'),
theAncestorStack = findAncestor(theAd, 'stacking-context'),
theInnerStack = DOC.getElementsByClassName(CSS_INNER_STACK)[0],
theModal = DOC.getElementById('modal'),
theStackUpdate = DOC.getElementById('stackUpdate'),

// globals
theInnerStackAncestor,
zIndexAncestor = WIN.getComputedStyle(theAncestorStack, null).zIndex,
zIndexCurrent, // will be assigned
zIndexInnerStack = WIN.getComputedStyle(theInnerStack, null).zIndex,
zIndexMax = theAncestorStack.getAttribute('data-zindex-max'),
zIndexTop = theAncestorStack.getAttribute('data-zindex-top');

function switchStackingContext (e) {
// we move the ancestor through the stack according to zIndexMax value (highest value allowed for that ancestor box)
if (WIN.getComputedStyle(theAncestorStack, null).zIndex === zIndexMax) {
theAncestorStack.style.zIndex = zIndexAncestor;
} else {
theAncestorStack.style.zIndex = zIndexMax;
}

// we get the ancestor that wraps boxes with potential high/crazy z-index
theInnerStackAncestor = findAncestor(e.target, CSS_INNER_STACK);

// we check for the actual value before we change it
zIndexCurrent = WIN.getComputedStyle(theInnerStackAncestor, null).zIndex;

// we reset the z-index from the parent ancestor allowing the box to move up (wherever it wants to)
if (zIndexCurrent !== AUTO) {
theInnerStackAncestor.style.zIndex = AUTO;
} else {
theInnerStackAncestor.style.zIndex = zIndexInnerStack;
}

// we update the value on the page
theStackUpdate.innerText = theAncestorStack.style.zIndex;
}

function resetStackingContext (e) {
// we reset the z-index of the ancestor to free the inner box
if (WIN.getComputedStyle(theAncestorStack, null).zIndex === AUTO) {
theAncestorStack.style.zIndex = zIndexAncestor;
} else {
theAncestorStack.style.zIndex = AUTO;
}

// we get the ancestor that wraps boxes with potential high/crazy z-index
theInnerStackAncestor = findAncestor(e.target, CSS_INNER_STACK);

// we check for the actual value before we change it
zIndexCurrent = WIN.getComputedStyle(theInnerStackAncestor, null).zIndex;

// we reset the z-index from the parent ancestor allowing the box to move up (wherever it wants to)
if (zIndexCurrent !== AUTO) {
theInnerStackAncestor.style.zIndex = AUTO;
} else {
theInnerStackAncestor.style.zIndex = zIndexInnerStack;
}

// we toggle this class to contextually style the modal (nothing to do with the solution per se)
HTML.classList.toggle(CSS_MODAL_ON);

// we update the value on the page
theStackUpdate.innerHTML = theAncestorStack.style.zIndex;
}

theAd.addEventListener('mouseenter', switchStackingContext, false);
theAd.addEventListener('mouseleave', switchStackingContext, false);
theModal.addEventListener('click', resetStackingContext, false);

}(document));
```

作为参考，下面是文章中引用的两个演示链接。第一个(本文中多次提到)是使用 JavaScript 的，它在用户与广告或“模态”交互时“管理”堆栈。第二个没有剧本也是一样的。它展示了如果我们依靠第三方来管理堆栈将会面临的问题。

*   管理堆栈上下文:演示 1( *有时，SitePoint 会删除存放在单独的 HTML 页面上的多年前的演示。我们这样做是为了降低过时代码的风险，因为暴露的漏洞会给我们的用户带来风险。谢谢你的理解。*)
*   [管理堆叠上下文:演示 2](https://uploads.sitepoint.com/wp-content/uploads/2014/12/1418671887zindex-demo-sansfix.html) (无脚本)

## 分享这篇文章