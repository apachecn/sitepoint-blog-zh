# 用 BEM 和 Sass 构建 Step 向导

> 原文：<https://www.sitepoint.com/building-step-wizard-bem-sass/>

步骤向导是一个用户界面组件，它可以帮助站点的访问者了解在多步骤操作中发生了什么。例如，在线购买。它通常用在支付表单上，描述完成所需操作的不同步骤以及当前步骤。

我认为这是一个很好的 UI 组件，因为它不仅有助于计算一个动作需要多长时间，它还提供了一些上下文:到目前为止我做了什么？还能做什么？我能回去吗？我可以跳过这一步吗？

本文不讨论这种模式的利弊。没有。今天，我想展示如何用 CSS 构建这样一个组件，确保涵盖很多内容。包括:

*   步骤的动态编号；
*   移动友好视图；
*   直观地定义当前步骤、已完成的步骤和待完成的步骤；
*   使用语义标记；
*   旧浏览器的退路；
*   创建易于维护的样式。

![Step Wizard](img/d79bba94186c271917f17357d9f6b19c.png)

## 加价

我认为分步向导的标记应该是有序列表。由于步骤是一个接一个推进的，所以顺序很重要。因此，一个有序列表(`<ol>`)。每个条目应该是一个列表条目(`<li>`)，包含一个链接(`<a>`)或者一个匿名元素(`<span>`)，如果没有链接的话。就是这样。

```
<ol>
  <li>
    <a href="#">Cart</a>
  </li>
  <li>
    <a href="#">Authentication</a>
  </li>
  <li>
    <a href="#">Delivery</a>
  </li>
  <li>
    <span>Summary</span>
  </li>
  <li>
    <span>Payment</span>
  </li>
</ol>
```

![Step Wizard 2](img/9cee93ac280b603c320b41be1bd5323a.png)

关于命名，我使用了类似 BEM 的东西(块元素修饰符),因为这非常适合这种方法。如果你不熟悉 BEM，我建议你读一下[这篇文章](http://csswizardry.com/2013/01/mindbemding-getting-your-head-round-bem-syntax/)由(还有谁)哈里·罗伯茨(Harry Roberts)所作的介绍。

使用边界元法，我们将得到类属性:

*   `steps`为块状；
*   `steps__item`为个别步骤；
    *   `steps__item--done`为一个完成的步骤；
    *   `steps__item--current`为当前步骤；
    *   `steps__item--first`为第一步(非强制使用`:first-of-type`)；
    *   `steps__item--last`为最后一步(非强制使用`:last-of-type`)；
*   `steps__link`用于连接或跨度。

此外，我们将为当前步骤之后的项目添加一个`disabled`属性。

让我们将这些类属性添加到前面的标记中，考虑到第 3 步是当前的一步(出于演示目的):

```
<ol class="steps">
  <li class="steps__item  steps__item--done  steps__item--first">
    <a href="#" class="steps__link">Cart</a>
  </li>
  <li class="steps__item  steps__item--done">
    <a href="#" class="steps__link">Authentication</a>
  </li>
  <li class="steps__item  steps__item--active">
    <a href="#" class="steps__link">Delivery</a>
  </li>
  <li class="steps__item" disabled="disabled">
    <span class="steps__link">Summary</span>
  </li>
  <li class="steps__item  steps__item--last" disabled="disabled">
    <span class="steps__link">Payment</span>
  </li>
</ol>
```

好吧，这似乎是一个很好的开始！

## 配置

在我们开始设计样式之前，我们应该声明几个变量。这是为了避免重复可以定制的值，并将所有可以定制的内容放在样式表的顶部。

```
/**
 * Text color
 * 1\. Done and active steps
 * 2\. Disabled steps
 */
$step-text-color: #333; /* 1 */
$step-text-color-disabled: #999; /* 2 */

/**
 * Background color
 * 1\. Everything but active step
 * 2\. Active step
 */
$step-background-color: #EFEFEF; /* 1 */
$step-background-color-active: #FFF; /* 2 */

/**
 * Number background color
 * 1\. Disabled steps
 * 2\. Done and active steps
 */
$step-counter-color: #BCBCBC; /* 1 */
$step-counter-color-active: #22A4BC; /* 2 */

/**
 * Border that's being used pretty much everywhere
 */
$step-border: 1px solid #CCC;

/**
 * Common spacing value
 */
$step-baseline: .5em;

/**
 * Breakpoint triggering mobile view
 */
$step-breakpoint: 767px;
```

现在我们已经设置好了所有的变量，我们准备好继续前进了。

## 干净的盒子模型

首先，让我们用一个像样的盒子模型。

```
html {
  box-sizing: border-box;
}

*,
:before,
:after {
  box-sizing: inherit;
}
```

框尺寸:框住所有的东西！

## 集装箱

现在，集装箱。因为我们不能对有序列表中的数字应用样式，所以我们要做的是:

*   隐藏默认编号；
*   初始化`.steps`上的 CSS 计数器；
*   增加`.steps__item`上的计数器；
*   用`:before pseudo-element from`显示计数器。步骤 __item `。

*注意:如果你不熟悉 CSS 计数器，我建议你在 Codrops* 上阅读[这篇来自我](http://tympanus.net/codrops/2013/05/02/automatic-figure-numbering-with-css-counters/)的文章。

```
/**
 * 1\. Prevents user-selection from doing ugly things on screen
 * without causing any accessibility issue since there is no point
 * in selecting the step-wizard.
 * 2\. Hides default numbering.
 * 3\. Initializes a CSS counter called `steps` on the step-wizard.
 * 4\. Clears inner floats.
 * 5\. Resets initial padding.
 * 6\. Applies vertical rhythm.
 */
.steps {
 user-select: none;                    /* 1 */
 list-style: none;                     /* 2 */
 counter-reset: steps;                 /* 3 */
 overflow: hidden;                     /* 4 */
 padding: 0;                           /* 5 */
 margin: 0 0 ($step-baseline * 2) 0;   /* 6 */
}
```

![step-wizard-3](img/a2582c099007366f52bcdc93d0eee272.png)

## 泰晤士报

所以，我们已经准备好了容器，现在可以给它的子容器一些样式了。

```
/**
 * Step item
 * 1\. Putting them all on the same line
 * 2\. Context positioning for pseudo-elements
 * 3\. Incrementing `steps` counter at each new item
 * 4\. Top and border bottoms only
 * 5\. Making sure content doesn't wrap
 */
.steps__item {
 float: left;                            /* 1 */
 position: relative;                     /* 2 */
 counter-increment: steps;               /* 3 */
 border-top: $step-border;               /* 4 */
 border-bottom: $step-border;            /* 4 */
 white-space: nowrap;                    /* 5 */
 background: $step-background-color;

  /**
   * Changing the cursor on disabled items
   * to something a bit more explicit.
   */
  &[disabled] {
 cursor: not-allowed;
  }
}
```

![step-wizard-4](img/393414aabf69e55b78dd8cb4886e83f9.png)

### 确定项目的大小

你可能已经注意到我们还没有确定台阶的大小。还记得我们希望允许任何数量的项目(至少在一个合适的范围内)吗？这意味着我们需要知道调整它们的步骤数。哎哟，真难受。我们要怎么做呢？

在这一点上，有很多解决方案:

*   使用 JavaScript 调整元素大小；
*   在数据属性中传递来自后端/模板的步骤数(例如`data-steps-count`)；
*   使用一些 CSS 魔法。

前两个解决方案有很好的浏览器支持，但是我认为使用 JavaScript 来实现这个目的是个坏主意。当我第一次构建这个步骤向导时，我曾经在一个数据属性中传递步骤数，然后像这样使用它:

```
// Loop from 1 through 10 because it is rather unlikely
// to have a step-wizard hosting over 10 items.
// In most cases, looping through 1 to 5 is more than enough.
@for $i from 1 through 10 {
    .steps[data-steps-count="#{$i}"] .steps__item {
 width: (100% / $i);
    }
}
```

这将生成如下 CSS，根据 data-attribute 中传递的值调整`.steps__item`的大小:

```
.steps[data-steps-count="1"]  .steps__item { width: 100%; }
.steps[data-steps-count="2"]  .steps__item { width: 50%; }
.steps[data-steps-count="3"]  .steps__item { width: 33.33333%; }
.steps[data-steps-count="4"]  .steps__item { width: 25%; }
.steps[data-steps-count="5"]  .steps__item { width: 20%; }
.steps[data-steps-count="6"]  .steps__item { width: 16.66667%; }
.steps[data-steps-count="7"]  .steps__item { width: 14.28571%; }
.steps[data-steps-count="8"]  .steps__item { width: 12.5%; }
.steps[data-steps-count="9"]  .steps__item { width: 11.11111%; }
.steps[data-steps-count="10"] .steps__item { width: 10%; }
```

所以这是安全的方法，可以追溯到 Internet Explorer 7(不是 6，它不支持属性选择器)。受 Lea Verou 的技术启发，我采用了 CSS crazy 方式，根据兄弟姐妹的数量设计元素。

主要思想是当有 N 个元素时，结合使用`:first-child`、`:nth-last-child`和通用兄弟操作符(`~`)来确定容器中所有元素的大小。规则是:

```
E:first-child:nth-last-child(N),
E:first-child:nth-last-child(N) ~ E {
    /* Styles for E when there are N elements in E's parent */
}
```

从字面上看，这个选择器意味着 *style 第一个子元素，也是第 n 个到最后一个子元素，以及容器*中跟在它后面的所有元素。通过复制这个规则改变 N 的值，我们可以根据兄弟元素的总数来设计元素的样式。在我们的案例中，我们可以得出以下结论:

```
.steps__item {
    /* Other styles... */

 @for $i from 1 through 10 {sp 
 &:first-child:nth-last-child(#{$i}),
 &:first-child:nth-last-child(#{$i}) ~ & {
 width: (100% / $i);
        }
    }
}
```

这个可爱的小循环产生了这个 CSS:

```
.steps__item:first-child:nth-last-child(1), 
.steps__item:first-child:nth-last-child(1) ~ .steps__item {
 width: 100%;
}
.steps__item:first-child:nth-last-child(2), 
.steps__item:first-child:nth-last-child(2) ~ .steps__item {
 width: 50%;
}
.steps__item:first-child:nth-last-child(3), 
.steps__item:first-child:nth-last-child(3) ~ .steps__item {
 width: 33.33333%;
}
.steps__item:first-child:nth-last-child(4), 
.steps__item:first-child:nth-last-child(4) ~ .steps__item {
 width: 25%;
}
.steps__item:first-child:nth-last-child(5), 
.steps__item:first-child:nth-last-child(5) ~ .steps__item {
 width: 20%;
}
.steps__item:first-child:nth-last-child(6), 
.steps__item:first-child:nth-last-child(6) ~ .steps__item {
 width: 16.66667%;
}
.steps__item:first-child:nth-last-child(7), 
.steps__item:first-child:nth-last-child(7) ~ .steps__item {
 width: 14.28571%;
}
.steps__item:first-child:nth-last-child(8), 
.steps__item:first-child:nth-last-child(8) ~ .steps__item {
 width: 12.5%;
}
.steps__item:first-child:nth-last-child(9), 
.steps__item:first-child:nth-last-child(9) ~ .steps__item {
 width: 11.11111%;
}
.steps__item:first-child:nth-last-child(10), 
.steps__item:first-child:nth-last-child(10) ~ .steps__item {
 width: 10%;
}
```

好吧，这肯定是一些体面的 CSS 数量，但这确实有用！是的，正如我以前说过的，一个步骤向导中的最大步骤数很可能是 5 或 6，而不是 10 或更多。

我们不能忘记的一件事是给第一项添加左边界，给最后一项添加右边界。还有，最后一项不应该有箭头(至少在我看来是这样)。要做到这一点，有两种方法:要么我们使用伪类:

```
.steps__item:first-of-type {
 border-left: $step-border;
}

.steps__item:last-of-type {
 border-right: $step-border;

 &:after {
 content: none;
  }
}
```

或者，如果我们想避免使用太多的高级 CSS 特性来保持一些向后兼容性，我建议我们向 DOM 添加类属性:

```
.steps__item--first {
 border-left: $step-border;
}

.steps__item--last {
 border-right: $step-border;

 &:after {
 content: none;
  }
}
```

![step-wizard-5](img/45223d20dec9621ffe43301d11eefdfe.png)

## 扭结

每个`.steps__item`必然包含一个子元素；该部分未禁用时为链接，禁用时为`span`。这个元素负责内容样式:字体、颜色、对齐等等…

```
/**
 * Links
 * 1\. Move to block to enlarge mouse area
 * 2\. Center the label inside the item
 * 3\. Remove the initial underline in case it's a `a`
 * 4\. Add some extra space on top and bottom
 * 5\. Visual sugar
 */
.steps__link {
 display: block;                     /* 1 */
 text-align: center;                 /* 2 */
 text-decoration: none;              /* 3 */
 padding: $step-baseline 0;          /* 4 */
 transition: .25s ease-out;          /* 5 */
 color: $step-text-color-disabled;

  /**
   * Hover/focus styles
   * Changing the text color is enough
   */
 &:hover,
 &:focus {
 color: $step-text-color;
  }

  /**
   * Prevent any hover state on disabled items
   */
  [disabled] &:hover,
  [disabled] &:focus {
 color: $step-text-color-disabled;
  }
}
```

与其为悬停/焦点状态设置一些样式，然后在禁用该步骤时覆盖那些样式，我们可以使用`:not()`伪类重写它(但是请记住，它对[浏览器的支持](http://caniuse.com/#feat=css-sel3))略低一些:

```
.steps__link {
  /* Other styles... */

 :not([disabled]) > &:hover,
 :not([disabled]) > &:focus {
 color: $step-text-color;
  }
}
```

在这一点上，它应该有所进展。它仍然需要一些额外的细节，但至少它做了工作。不再有布局问题，它是可访问的、可读的等等。

![step-wizard-6](img/0627900b3a995958b7972bdea646eb10.png)

## 添加编号

我们使用了有序列表，但我们删除了数字。我们初始化了一个 CSS 计数器，它在每一步都会递增，所以现在我们应该使用一个伪元素来显示这个计数器。这个伪元素将由`.steps__link`托管，因为此时它是文本内容。

这里没有魔法，它是一个大小合适的伪元素，带有计数器内容以及一些额外的样式来使它变得漂亮。

```
/**
 * Numbers
 * 1\. Display the current `steps` counter value
 * 2\. Make it inline-block so it can be styled as a block
 * 3\. Size the it according to the baseline
 * 4\. Horizontal centering
 * 5\. Vertical centering
 * 6\. Extra space around it
 * 7\. Make it round
 * 8\. Color stuff
 */
.steps__link {
  /* Other styles...* /

 &:before {
 content: counter(steps);            /* 1 */
 display: inline-block;              /* 2 */
 width:  $step-baseline * 3;         /* 3 */
 height: $step-baseline * 3;         /* 3 */
 text-align: center;                 /* 4 */
 line-height: $step-baseline * 3;    /* 5 */
 margin: 0 $step-baseline * 2;       /* 6 */
 border-radius: 50%;                 /* 7 */
 background: $step-counter-color;    /* 8 */
 color: white;                       /* 8 */
  }
}
```

![step-wizard-7](img/9d3edb7a3a31e93a321734493f72d093.png)

## 添加箭头

让我们使用伪元素添加小箭头！基本上，除了最后一项，每一项都有一个形状为正方形的伪元素，旋转后看起来像一个箭头。设置起来有点棘手，但我们会成功的！

现在，让我们从一点数学开始，好吗？我们将把正方形旋转 45 度，使它们的角度正对上、右、下和左。为了使它看起来正确，我们需要确保正方形的对角线严格等于步骤项目的高度。这涉及到一些计算。

首先，让我们计算一个台阶项目的高度:

`step_height = height of tallest content + twice the vertical padding + twice the border`

我们知道最高的内容是我们刚刚添加的数字，我们将它的高度硬编码为基线的三倍。我们知道链接上的垂直填充等于基线。最后，我们有两个 1px 边框。轻松点。

```
$step-height: ($step-baseline * 3) + ($step-baseline * 2) + (1/16 * 1em * 2);
```

结果变成了`2.625em`。我们知道伪元素的对角线必须是`2.625em`,但是我们不能根据元素的对角线来确定元素的大小，所以我们需要根据这个值来计算它的边长。公式是:

```
side_length = diagonal * sqrt(2) / 2
```

太好了！如果你使用[指南针](http://compass-style.org/)或[指南针](https://github.com/Team-Sass/Sassy-math)，有一个`sqrt()`函数，否则我们需要包括一个对 2 的平方根实际上是多少的粗略估计。我跟了:`1.4142135623730951`。为了充分利用这两个世界，我们可以这样写:

```
$sqrt-2: if(function-exists('sqrt') == true, sqrt(2), 1.4142135623730951);
```

好了，我们得到了一切，让我们写代码吧！

```
/**
 * Arrows
 * 1\. Sizing (yay math!)
 * 2\. Putting it on the right edge
 * 3\. Moving it back to the left from one half of it's size
 * 4\. Slightly translating it from the top
 * 5\. Rotating it 45 degrees, obviously
 * 6\. Moving it on top of other things
 * 7\. Applying borders on the two visible sides
 */
.steps__item {
  /* Other styles... */

 &:after {
 $sqrt-2: if(function-exists('sqrt') == true, sqrt(2), 1.4142135623730951);
 $step-height: ($step-baseline * 3)  + ($step-baseline * 2) + (1/16 * 1em * 2);
 $step-arrow-size: $step-height * $sqrt-2 / 2;
 $top-offset: 1 / $sqrt-2 / 2;

 content: '';
 width:  $step-arrow-size;               /* 1 */
 height: $step-arrow-size;               /* 1 */
 position: absolute;
 left: 100%;                             /* 2 */
 margin-left: -$step-arrow-size / 2;     /* 3 */
 top: $top-offset;                       /* 4 */
 transform: rotate(45deg);               /* 5 */
 z-index: 2;                             /* 6 */
 background: inherit;
 border-right: $step-border;             /* 7 */
 border-top: $step-border;               /* 7 */
  }
}
```

![step-wizard-8](img/63d0965073cff3eed42d657318810004.png)

## 处理当前步骤

几乎没有什么要做的，所以当前步骤实际上看起来像是被突出显示了。我们只需要改变背景颜色。因为伪元素的颜色是相对于背景颜色的，所以一切都会很好。

```
.steps__item--active {
 background: $step-background-color-active;
}
```

我们还必须更改已完成和活动步骤的文本颜色，以及数字背景颜色。小事情，但这就是为什么继巫师这么好。

```
.steps__item--done .steps__link,
.steps__iten--active .steps__link {
 color: $step-text-color;

 &:before {
 background: $step-counter-color-active;
  }
}
```

## 处理小屏幕

最后但同样重要的是，我们必须确保它在小屏幕上看起来没问题。我把所有的项目一个接一个的叠起来，所以感觉还是一个基于步骤的过程。

```
.steps__items {
  /* Other styles... */

 @media (max-width: $step-breakpoint) {
 width: 100% !important;
 border: $step-border;
 border-bottom: none;
 padding: ($step-baseline * 2) 0;

    /**
     * Removing the arrows
     */
 &:after {
 content: none;
    }
  }
}

.steps__item--last {
  /* Other styles... */

 @media (max-width: $step-breakpoint) {
 border-bottom: $step-border;
  }
}
.steps__link:before {
  /* Other styles... */

  /**
   * Moving numbers on the left rather than stuck to the content
   */
 @media (max-width: $step-breakpoint) {
 float: left;
 margin-right: 0;
  }
}
```

就是这样！

![step-wizard-9](img/3dc1109cf8a2d9169f124d5a5286c33c.png)

## 处理旧浏览器

要处理较旧的浏览器，有两种不同的方法:

*   使用[Modernizr](http://modernizr.com/)；
*   使用[功能查询](https://www.w3.org/TR/css3-conditional/#at-supports)。

挑一个你喜欢的，没关系。我们的分步向导使用了一些旧浏览器不支持的东西:

*   高级 CSS 选择器；
*   CSS 转换；
*   生成的内容(伪元素)。

不幸的是，第一个不能简单地仅用 CSS 填充，所以如果你必须支持传统浏览器，我建议你完全避免高级 CSS 选择器:只需向 DOM 添加更多的类。

关于用于箭头的 CSS 转换，当它们不被支持时，很容易添加一个解决方案:

```
// With Modernizr

.no-csstransforms .steps__item {
 border-right: $step-border;

 &:after {
 content: none;
  }
}

// With Features Queries
@supports not (transform: rotate(45deg)), not (-webkit-transform: rotate(45deg)), not (-ms-transform: rotate(45deg)) {
  .steps__item {
 border-right: $step-border;

 &:after {
 content: none;
    }
  }
}
```

最后但同样重要的是，生成的内容:不幸的是，无法检测生成的内容对特性查询的支持，所以为了解决这最后一点，我建议您使用 Modernizr。

```
.no-generatedcontent .steps {
 list-style-type: decimal;
 list-style-position: inside;
}
```

如果无法显示伪元素，此规则会从有序列表中收回默认编号。非常方便！

![step-wizard-10](img/78cd93998365c21d1534bb91c9e96816.png)

## 最后的想法

就是这样，伙计们！希望你喜欢这个解释，如果你能想到任何可以改进的地方，一定要分享！

参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )用 BEM 和 Sass 构建的 Pen [一步向导。](http://codepen.io/SitePoint/pen/CpAcK/)