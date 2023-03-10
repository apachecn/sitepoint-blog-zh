# 标题 CSS:CSS 类命名的简单方法

> 原文：<https://www.sitepoint.com/title-css-simple-approach-css-class-naming/>

如果你像我一样，那么你会花太多时间为一个元素想出一个完美的类名。你可以谷歌一下同义词，或者想象一下如果这个元素是现实生活中的物体，它会是什么。你知道任何语义名称都可以，但不知何故，试图想出一个完美的名字似乎是值得的。

老实说，完美的名称不会对样式表有太大帮助，但是使用 CSS 方法会有很大的不同。

## CSS 方法的例子

OOCSS 是一个环保的建议，帮助你通过循环利用的方式编写可持续的课程。

SMACSS 是一个包罗万象的 CSS 游戏计划，它将指导你学习所有适当的技巧。

是一个分析型的房屋清洁工，把所有的东西都整理得整齐划一，便于识别和安心。

还有 [BEM](http://bem.info/method/) ？嗯，BEM 是 CSS 类命名的黄金标准，所有 CSS 类命名方案都是根据它来衡量的。

## 那么，为什么还要讨论类命名呢？

BEM 方法是关于编写可伸缩的 CSS，强调可读性和避免冲突。简而言之，BEM 代表 Block _ _ Element-Modifier。块是包含一小块相关元素的元素(在 SMACSS 中，它被称为模块)。该元素是块的后代，通常没有块就不存在。修改器控制块的状态。

在 BEM 中，为块编写一个普通的类名，为任何元素复制块名并附加元素名。

传统的边界元法是这样的:

```
<div class="block block--modifier">
    <p class="block__element">
</div>
```

这很好，因为任何人都会理解“block__element”与“block”相关，并且“block__element”类在项目的其他地方使用的可能性很小。

但是这种方法有一个问题。你整天都在写 CSS，你不想写长的类名来弄脏你干净的标记。

Title CSS 是关于给你 BEM 的好处，而不用给你的类名添加任何前缀或特殊字符。

## 给 CSS 加标题的技巧很简单

使用 Title CSS，你可以做到以下几点:对于任何全局 CSS 类，使用大写名称(标题大小写)。对于任何修饰符或子类，使用小写字母作为名字的开头。

这意味着使用 Title CSS，您可以将样式表中引用的没有父类的任何类名大写。这意味着即使是 OOCSS 中的对象也要大写。区别很简单；样式表中任何大写的部分都不能重复使用。

以下是使用标题 CSS 时标记外观的示例:

```
<div class="Title isModified">
    <p class="descendant">
</div>
```

下面是相应 CSS 的样子:

```
.Title {}
    .Title.isModified {}
    .Title .descendant {}
```

## 为什么标题 CSS 有效

块标识符或“标题”类为块中的所有派生类创建了一个范围。后代类可以在其他标题栏中重复，而不会发生样式冲突。

这对于该方法的工作并不重要，但是由于 [HTML 类名是区分大小写的](http://dbaron.org/css/test/casesens),“Title”类也可以作为子类自由重复。

## 标题 CSS 有什么帮助？

使用标题 CSS 方法，您将看到以下好处:

*   以更自然的方式编写 CSS 类。
*   CSS 选择器类似于书面语言，就像以大写字母开头的英语句子。
*   较短的类名打字更快，也更容易浏览。
*   标题案例类很容易在标记中发现；要查看小写子类属于什么，只需遍历 Title 类的节点。

## 一个陷阱和解决方法

当您使用标题块来包含其他块时，标题 CSS 可能会有问题。如果一个包含的标题栏和它封装的标题栏有相同的后代选择器类，那么就会有冲突，在这种情况下，你应该在作为容器的标题栏中使用[子组合子](https://www.w3.org/TR/CSS21/selector.html#child-selectors)。

为了说明这个问题，下面是一些存在问题的示例标记:

```
<div class="Container">
    <header class="header"></header>
    <main class="body">
        <section class="Title">
            <div class="header"></div>
            <div class="body"></div>
        </section>
        <section class="Title">
            <div class="header"></div>
            <div class="body"></div>
        </section>
    </main>
</div>
```

以及附带的 CSS:

```
.Container {}
    .Container .header {}
    .Container .body {}
.Title {}
    .Title .header {}
    .Title .body {}
```

注意，应用于`.Container`中的`.header`和`.body`元素的样式也将应用于进一步嵌套的其他`.header`和`.body`元素。为了避免这种情况，以下是解决方案:

```
.Container {}
    .Container > .header {}
    .Container > .body {}
```

通过使用子组合符(`>`)的选择器，样式将只应用于直接子元素，而不会应用于具有相同类名的进一步嵌套的元素。

## 关于萨斯的一句话

预处理程序提供了一个编写标题 CSS 的好方法。嵌套功能允许您在样式表中轻松识别标题块。

这里是一个标题 CSS 在 SCSS 的例子:

```
.Title {
    &.modifier {}

    .descendant {}

    > .tightlyBound {}
}
```

## 反馈？

正如 BEM、SMACSS 和 OOCSS 所建议的，保持块或模块较小是很重要的。只包含与 Title 类密切相关的元素在性能和可维护性方面都有好处。

如果你对标题 CSS 有任何意见或反馈，我很乐意在评论中听到。如果你想获得更多信息或想要合作，请务必查看 GitHub repo 的 Title CSS 。

## 分享这篇文章