# 用 Sass 管理 z 索引的更好的解决方案

> 原文：<https://www.sitepoint.com/better-solution-managing-z-index-sass/>

最近我们看到了不少关于用 Sass 管理`z-index`的文章:

*   Jackie Balzer 在 Smashing Magazine 发表的文章
*   [Sass 和 z 指数:一个(稍微)更好的方法](http://viget.com/extend/sass-maps-and-z-indexes)
*   CSS-Tricks 的 Chris Coyier 的[处理 z-index](http://css-tricks.com/handling-z-index/)

虽然 Chris Coyier 的上一篇文章确实帮助我开始了我的解决方案，但是从技术的角度来看，以前的文章都没有成功地提出一种简单易用的方法。

因此有了这篇文章。我将带你浏览我的非常简单的系统，向你展示我是如何构建它的，以及你如何在你的项目中使用它。

## 为什么需要管理`z-index`值？

首先让我们回答这个问题:*为什么我们需要用 Sass 管理 z-index？*我们没有看到无数关于用 Sass 管理`padding`的文章冒出来，对吧？那么为什么要为`z-index`做呢？

嗯，这与`z-index`大多被误解的事实有关。此外，接受-无穷大和无穷大之间的无单位值会给你很多机会把事情搞砸。

因此，与其使用像`9999999`这样的值，对着屏幕大喊大叫，因为你不明白发生了什么，不如使用一个额外的工具——在这种情况下是 CSS 预处理器——来帮助改进事情。

## 什么概念？

这里的概念在关于这个主题的所有文章中都是相同的:构建一个接受关键字作为参数的函数，它被映射到一个值。该值是将被打印出来的 z 索引。

杰基·巴尔泽使用列表。道格·艾弗里混合使用列表和地图。克里斯·科伊尔使用地图。实际上，Chris Coyier 的解决方案非常接近我最终得到的结果，除了它缺少这个使整个系统非常好用的额外功能。

最终我们会得出这样的结论:

```
.element {
  z-index: z("modal");
}
```

## 为什么使用函数而不是 Mixin？

道格·艾弗里在他的文章中使用了 mixin。函数的主要区别在于您使用它的方式:

```
// With a mixin
// Me no likey!
.element {
  @include z("modal");
}
```

虽然我知道这实际上是一个偏好问题，但是当只输出一个 CSS 属性时，我确实倾向于避免使用 mixins。该函数是这类事情的完美用例。

## 打开野兽

所以我们需要一个接受单一参数的函数。随便叫什么；我和`layer`一起去的。

```
@function z($layer) {
  // ... where the magic happens.
}
```

这个函数到底在做什么？它将在图层映射中寻找给定的参数，看它是否映射到一个`z-index`值。如果是，则返回值，否则返回错误消息。所以我们需要一张地图。

```
$z-layers: (
  "goku":            9001,
  "shoryuken":       8000,
  "default":            1,
  "below":             -1,
  "bottomless-pit": -9000
);
```

这里有两件事:

1.  我喜欢把我的配置变量从使用它们的 mixins 函数中取出，放在一个专用的文件中(例如`_config.scss`)。如果您喜欢，可以随意将其移动到`z()`功能中。
2.  您可以添加/删除/更新任意多的键/值，这些只是示例。

现在回到我们的函数。

```
@function z($layer) {
  @return map-get($z-layers, $layer);
}
```

在这一点上，我们还没有为`map-get($z-layers, ...)`做更多语法上的工作。这实际上很酷，因为反复输入这个东西很快就会变得很烦人。

如果键存在于映射中，它将返回映射到它的索引值。如果键没有被定义，那么它将返回`null`。每当属性有一个`null`值时，Sass 编译器不会输出它。

所以万一你调用了一个未知的密钥，Sass 就会无声无息地失败，这并不理想。让我们通过使用`@warn`指令来提醒开发人员(也就是您),以防键在映射中不存在:

```
@function z($layer) {
  @if not map-has-key($z-layers, $layer) {
    @warn "No layer found for `#{$layer}` in $z-layers map. Property omitted.";
  }

  @return map-get($z-layers, $layer);
}
```

那里。如果您要求一个未定义的键(例如“SitePoint”)，Sass 将在控制台中打印以下内容:

> 在$z 图层地图中没有找到`SitePoint`的图层。省略了属性。

很酷，对吧？

## 玩新玩具

现在我们已经完成了函数的构建，是时候开始玩了！正如您在本文开头看到的，使用该函数相当简单:

```
.modal {
  // ...
  z-index: z("modal");
}

.modal-overlay {
  // ...
  z-index: z("modal") - 1;
}
```

这个想法是在定义`z-index`值时总是使用这个函数。只在部分时间使用它是没有意义的，而且几乎会破坏系统。

还有，我觉得图层贴图还是尽量淡一点比较好。添加的图层越多，Z 轴比例就越复杂。尝试找到几个重复出现的值，将它们映射到通用的关键字，这样就可以了。

## 用嵌套的上下文进一步推动事情

你可能不是不知道`z-index`值不是绝对的。都和自己的堆叠上下文有关。这基本上意味着，如果你试图让来自 Context-A 的元素出现在来自 Context-B 的元素之上，但是 Context-B 是在 Context-A 之上绘制的，那么即使是超过九千个的索引[也是不够的。](https://www.youtube.com/watch?v=SiMHTK15Pik)

*注意:要了解更多关于堆栈环境和`z-index`的信息，请务必阅读[Philip Walton 的这篇精彩文章](http://philipwalton.com/articles/what-no-one-told-you-about-z-index/)。*

现在，如果我们想让我们的系统知道堆叠上下文，我们可以有嵌套的地图。例如，如果我们的`modal`正在初始化一个新的堆栈上下文，并且我们想要在其中对元素进行排序，我们可以更新 map:

```
$z-layers: (
  "goku":            9001, 
  "shoryuken":       8000,
  "modal": (
    "base":           500,
    "close":          100,
    "header":          50,
    "footer":          10
  ),
  "default":            1,
  "below":             -1,
  "bottomless-pit": -9000
);
```

问题是，我们不能很容易地从嵌套的映射中获取值，无论如何不能用`map-get`来获取。幸运的是，构建这样一个函数很容易:

```
@function map-deep-get($map, $keys...) {
  @each $key in $keys {
    $map: map-get($map, $key);
  }

  @return $map;
}
```

这就够了。很简单，不是吗？现在，我们可以这样编写我们的模态模块:

```
// New stacking context
.modal {  
  position: absolute;
  z-index: z("modal", "base");

  .close-button {
    z-index: z("modal", "close");
  }

  header {
    z-index: z("modal", "header");
  }

  footer {
    z-index: z("modal", "footer");
  }
}
```

这将产生以下结果:

```
.modal {
  position: absolute;
  z-index: 500;
}

/* This is `100` in the modal stacking context */
.modal .close-button {
  z-index: 100;
}

/* This is `50` in the modal stacking context */
.modal header {
  z-index: 50;
}

/* This is `10` in the modal stacking context */
.modal footer {
  z-index: 10;
}
```

## 敬未来！

在我们梦想的光明未来的某一天，我们将拥有原生 CSS 变量。那我们就不再需要这些了。相反，这是将要发生的事情:

```
:root {
  --z-goku:            9001;
  --z-shoryuken:       8000; 
  --z-modal:            500;
  --z-default:            1;
  --z-below:             -1;
  --z-bottomless-pit: -9000;
}

.modal {
  z-index: var(--z-modal);
}
```

看到了吗？几乎是一样的，除了它是`var()`而不是`z()`和`--key`而不是`key`。打字时间稍长，可能会更混乱，因为这些是单独的变量，而不是地图。但是它很好地完成了任务，同时保持了它的原生性。

## 最后的想法

我不知道你怎么想，但是我认为这是在 Sass 项目中处理`z-index`的一个很好的方法。它不仅可以用图层地图中存储的每个 z 索引来组织事物，而且很容易对运算符进行细微的更改，正如我们在上一个示例中看到的那样。

就是这样。这里有一个 Sassmeister 演示让你开始。从今天开始，没有理由不在你的项目中应用它。

## 分享这篇文章