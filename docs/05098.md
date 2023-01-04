# Sass 和 Bourbon 适用于轻量级语义网格

> 原文：<https://www.sitepoint.com/sass-bourbon-neat-lightweight-semantic-grids/>

如果你已经是 [Sass](http://sass-lang.com/) 和 [Bourbon](http://bourbon.io) 的粉丝，你将会爱上[Neat](http://neat.bourbon.io/)——一个轻量级的语义网格系统，可以与 Bourbon 和 Sass 无缝协作，来自 [Thoughtbot](http://thoughtbot.com) 编写 Bourbon 的同一批人。

如果这一切对你来说听起来很新鲜，Sass 是 CSS 的预处理器，可以帮助你极大地改善 CSS 工作流程。波旁威士忌是萨斯的混合酒，它增强并扩展了萨斯的力量。如果你不熟悉萨斯和波旁威士忌，看看我以前的一篇文章，这篇文章描述了如何通过几个简单的步骤学会萨斯和波旁威士忌。一旦你经历了这些，你应该准备好继续学习所有关于整洁的知识。

## 模块化方法

Bourbon 框架的目标之一是保持事物的模块化，这样你就不会在你的代码中加入你实际上并不使用的特性。考虑到这一点，波旁威士忌的制造商发布了他们整洁的网格系统，作为波旁威士忌的可选附件。他们想要一个小型的语义网格系统，可以帮助完成大多数常见的布局任务，而不会太固执己见。

我们的目标是做一些你想要的时候可以利用的东西，当你不想要的时候它会离开你。如果你需要的话，Neat 可以包含在一个 Bourbon 项目中，但是除非你使用它，否则它不会给你最终提供给访问者的编译后的 CSS 增加任何东西。

## 什么是语义网格？

语义是 web 开发中经常使用的一个术语。出于整洁的目的，使这个网格系统具有语义的是，它允许您根据 HTML 元素的命名方式来定位它们，并按照它们的预期行为来使用它们，从而保持标记的整洁。

一个简单的例子可能会有所帮助。

假设您正在构建一个页面，该页面需要显示一篇在`section`中带有`aside`的文章。您希望使用正确的 HTML 元素，并且不希望页面被不必要的类或包装器弄得乱七八糟。在典型的桌面显示中，你希望`aside`停留在文章的左边。您知道，您希望能够灵活地在不同的设备上以不同的方式呈现相同的元素。

从一些简单的 HTML 开始:

```
<section>
  <aside>
    <p>Highlighted information</p>
  </aside>
  <article>
    <p>This is the content of the article</p>
  </article>
</section>
```

在没有任何 CSS 的情况下，这将把`aside`内容放在顶部，紧接着是`article`。为了使表示使用网格布局，您需要用 Neat 的网格类来定位适当的元素。您将`section`元素定义为一个容器，然后确定分配给其子元素`aside`和`article`的列数(默认为 12 列)。

```
@import bourbon/bourbon
@import neat/neat

section {
 @include outer-container;
  aside {
 @include span-columns(3);
  }
  article {
 @include span-columns(9);
  }
}
```

这一切都与您通常的 Sass 和 Bourbon 约定以及您可能更喜欢使用的所有类和 CSS 选择器无缝地工作。所以，如果你想让这种情况只发生在有特定类的`section`元素上，Neat 会做好它的工作，不会妨碍你。所以对于下面的 HTML:

```
<section class="article-container">
  <aside class="interesting-insight">
    <p>Highlighted information</p>
  </aside>
  <article class="syndicated-content">
    <p>This is the content of the article</p>
  </article>
</section>
```

这个简洁的定义将按照您预期的方式工作(我们将假设导入行位于 Sass 文件的顶部):

```
.article-container {
 @include outer-container;
  .interesting-insight {
 @include span-columns(3);
  }
  .syndicated-content {
 @include span-columns(9);
  }
}
```

## 响应式布局

上面例子产生的页面将在`article`内容的左边显示`aside`内容，不管窗口变得多大或多小。但是假设你想让`aside`在设备显示为 500 像素或更窄时弹出到顶部。

您可以通过使用 Neat 的`new-breakpoint()`函数在 500px 处定义一个`$mobile`断点变量来实现这一点。这甚至允许您为上下文中的所有元素指定应该代表新屏幕大小的整个宽度的列数:

```
$mobile: new-breakpoint(max-width 500px 4);

section {
 @include outer-container;
  aside {
 @include span-columns(3);
 @include media($mobile) {
 @include span-columns(4);
    }
  }
  article {
 @include span-columns(9);
 @include media($mobile) {
 @include span-columns(4);
    }
  }
}
```

现在，`aside`和`article`将被堆叠，如果显示器为 500px 或更窄，则设置为完整的四列宽度以水平填充屏幕。在更大的显示器上，访问者将得到和以前一样的并排显示。

因此，让我们来看看编译这个基本响应示例时生成的 CSS:

```
* {
  -webkit-box-sizing: border-box;
  -moz-box-sizing: border-box;
  box-sizing: border-box;
}

section {
  max-width: 68em;
  margin-left: auto;
  margin-right: auto;
}

section:after {
  content: "";
  display: table;
  clear: both;
}

section aside {
  float: left;
  display: block;
  margin-right: 2.35765%;
  width: 23.23176%;
}

section aside:last-child {
  margin-right: 0;
}

@media screen and (max-width: 500px) {
  section aside {
    float: left;
    display: block;
    margin-right: 7.42297%;
    width: 100%;
  }

  section aside:last-child {
    margin-right: 0;
  }
}

section article {
  float: left;
  display: block;
  margin-right: 2.35765%;
  width: 74.41059%;
}

section article:last-child {
  margin-right: 0;
}

@media screen and (max-width: 500px) {
  section article {
    float: left;
    display: block;
    margin-right: 7.42297%;
    width: 100%;
  }

  section article:last-child {
    margin-right: 0;
  }
}
```

这里有许多最佳实践和 CSS 技巧，可以使生成的样式更加健壮。例如，Bourbon 和 Neat 负责清除部分，为容器和子元素设置表格显示样式，确保最后一个子元素没有右边距，甚至计算基于百分比的宽度和边距，以精确到小数位和大数位，显示在`aside`和`article`元素上。对于所付出的努力来说，这是很有价值的。

## 内容优先级排序

如果您希望在特定视图中切换元素的显示顺序，而不改变 HTML 中内容的顺序，Neat 支持方便直观的负行定位。您可以像下面这样轻松地在父元素内移动每一列:

```
section {
 @include outer-container;
  aside {
 @include span-columns(3);
 @include shift(-12);
  }
  article {
 @include span-columns(9);
 @include shift(3);
  }
}
```

现在,`article`元素将在左边，而`aside`将在右边。您还可以像以前一样添加移动样式，以保持响应显示的一致性:

```
$mobile: new-breakpoint(max-width 500px 4);

section {
 @include outer-container;
  aside {
 @include span-columns(3);
 @include shift(-12);
 @include media($mobile) {
 @include span-columns(4);
    }
  }
  article {
 @include span-columns(9);
 @include shift(3);
 @include media($mobile) {
 @include span-columns(4);
    }
  }
}
```

## 自定义设置

Neat 的一个真正优势是，它非常灵活地使用内置设置来配置它。Neat 允许您在开发您的站点时为您可能想要设置的大多数定制传递您自己的值，并且还允许您为诸如列数和装订线宽度之类的值使用实际且合理的默认值。

Neat 默认采用 12 列网格，这对于大范围的布局来说足够灵活，但是如果通过改变`$grid-columns`变量的值更方便的话，您可以使用 16 列网格。

```
$grid-columns: 16;

section {
 @include outer-container;
  aside {
 @include span-columns(4);
  }
  article {
 @include span-columns(12);
  }
}
```

## 嵌套子部分

Neat 还允许布局中容器内的子网格。这可以支持灵活的表示，在嵌套元素中清晰地显示多个并排的元素。您所需要做的就是指定内部元素的列应该根据嵌套父元素所占用的列数来计算。

例如，这里有一个包含三个嵌套的`div`元素的`article`:

```
<section>
  <aside>
    <p>Highlighted information</p>
  </aside>
  <article>
    <div>This is the first sub-nested element</div>
    <div>This is the second sub-nested element</div>
    <div>This is the third sub-nested element</div>
  </article>
</section>
```

在这种情况下，您可以在简单的 Sass 代码中指定，每个元素都应该在包含元素中占有一部分可用列:

```
section {
 @include outer-container;
  aside {
 @include span-columns(3);
  }
  article {
 @include span-columns(9);
    div {
 @include span-columns(3 of 9);
    }
  }
}
```

因为 Bourbon 和 Neat 只是对 Sass 的增强，所以它们完全可以使用标准的 Sass 开发技术进行修改。

## 可定制且不显眼

Neat 足够灵活和简单，可以支持大多数标准的布局开发方法。如果您想使用移动优先的布局，在平板电脑上扩展为两列，在桌面上扩展为三列，或者如果您想消除列之间的间距，或者在一组兄弟元素中支持多行项目，Neat 可以满足您的需求。

查看一下 [Neat 文档](http://neat.bourbon.io/docs/)，浏览一下 Neat 网站上的一些[例子，为你的下一个项目获得灵感。有了 Bourbon 和 Neat 在你的 Sass 之上，你就可以一头扎进去，马上开始玩设计和布局选项，知道你不会给你的代码增加很多额外的膨胀，或者束缚你的语义 HTML 风格。](http://neat.bourbon.io/examples/)

## 分享这篇文章