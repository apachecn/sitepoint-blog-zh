# 使用 Sass 在语义上扩展 Bootstrap

> 原文：<https://www.sitepoint.com/sass-semantically-extend-bootstrap/>

Bootstrap 提供了一种快速简单的方法来为您的最新网站或应用程序创建支架，无论您是新手还是专业开发人员。由于这个原因，越来越多的开发人员将框架包含在他们的个人工具箱中。

然而，Bootstrap 也有不好的一面，因为它使得编写混乱的、非语义的和不可重用的标记变得非常容易，而这些标记可以在所有浏览器上正确呈现。

在本文中，我将解释如何以更加健壮和语义化的方式使用 Bootstrap。Bootstrap 的最新版本附带了该框架的官方 Sass 端口，因此我们将使用 Sass 最强大的功能之一来实现它:[@ extend 指令](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#extend)。

## 语义学的一些基础知识

首先，让我们看看当我们说我们想让事情变得更“语义化”时，我们指的是什么。HTML 文档旨在从信息层次的角度描述其内容。一个人应该能够阅读它们，并且知道它们是关于什么的，而不是它们会是什么样子。

随着 CSS 尤其是 CSS 框架越来越受欢迎，HTML 的编写通常考虑的是 CSS 表，而不是读者/开发人员。

在现代网站的源代码中，这样的 HTML 标记并不少见:

```
<div class="row">
    <div class="col-md-4">
        Name:
    </div>
    <div class="col-md-4">
        John
    </div>
    <div class="col-md-4">
        Smith
    </div>
</div>
<div class="row">
    <div class="col-md-12">
        <p>
            "I enjoy writing code in my spare time."
        </p>
    </div>
</div>
```

Bootstrap 的 CSS 文件确切地知道该怎么做。两排，一排分成三排，另一排全宽。作为熟悉 Bootstrap 的开发人员，这对于我们来说很容易理解，但是如果您以前从未使用过这个框架呢？这段 HTML 是做什么用的？这些信息是关于什么的？标记没有揭示任何实际的东西。

语义标记是描述其内容而不是其外观的代码。我们可以按如下语义编写上面的代码:

```
<div class="author-name">
    <label class="author-nameLabel">
        Name:       
    </label>
    <span class="author-nameFirst">
        John        
    </span>
    <span class="author-nameLast">
        Smith
    </span>
</div>
<div class="author-bio">
    <p>
        "I enjoy writing code in my spare time."
    </p>
</div>
```

这并没有什么不同，但它直接告诉我们每个元素的用途以及每个元素与其他元素的关系。我们失去的是如何视觉化呈现的知识。但这毕竟不是 HTML 的目的。

## Bootstrap 应该如何设计呢？

您可能想知道如何在标记中使用没有内置类名的 Bootstrap？嗯，我们可以利用 Sass 的`@extend`功能来解决这个问题。

来自 Sass 的文档:

> `@extend`通过在样式表中扩展选择器出现的任何地方插入扩展选择器来工作。

这意味着我们可以使用我们的语义选择器并扩展 Bootstrap 的选择器来获得它的所有优点。方法如下:

```
/* Import bootstrap-sass so that we have access to all of its selectors */
@import "bootstrap";

/* Author Bio and Author Name are just Bootstrap .row elements */
.author-bio,
.author-name {
  @extend .row;
}

/* Author nameLabel, nameFirst and nameLast need
   to be a third of their container's width */
.author-nameLabel,
.author-nameFirst,
.author-nameLast {
   @extend .col-md-4;
}

/* The paragraph inside the author's bio should be full width */
.author-bio p {
    @extend .col-md-12;
}
```

通过`@extend`的魔力，我们的选择器与它们扩展的引导选择器一起被插入到编译好的样式表中。通过检查编译后的代码，您将会看到，我们的选择器拥有与它们所扩展的类完全相同的属性。

例如，除了别的以外，您应该在编译的 CSS 中看到:

```
/* one of the compiled rule sets */
.col-md-4, .author-nameLabel,
.author-nameFirst,
.author-nameLast {
  width: 33.3333333333%;
}
```

你可以在这里和编译好的 CSS [并排查看 Sass。整洁吧？](http://sassmeister.com/gist/9422986)

## 复用性

这种方法的另一个优点是它创建了可读和可重用的组件。例如，您不必每次需要使用 author 组件时都从行和列中重新构建它。相反，组件的每一部分都有合理的名称，这使得它易于构造。您还可以依靠 Bootstrap 来确保它在您的站点中统一呈现。

## 轻便

虽然 Boostrap 是目前最好的框架之一，但是当您想要将您的站点迁移到一个不同的框架或者甚至编写自己的框架时，这一时刻可能会到来。使用上面概述的方法，你可以很容易地做到这一点，因为你的标记是干净地从你的 CSS 中分离出来的。

## 填满你的工具带

如果你还没有尝试过 Bootstrap 的 Sass 版本，那你就等着享受吧。从他们的 github 页面开始很容易。

在这里，斗嘴并不是唯一的事情。如果你真的想在 HTML 和 CSS 中使用下一级语义，我建议采用一种命名约定，比如 [BEM](http://bem.info/method/) 或 [SMACSS](http://smacss.com/) 来保持选择器的标准化和易记性。

## 向前延伸

Bootstrap 为我们提供了一组非常强大的样式——但是很容易将它们拼凑成一些像样的东西，却损失了高质量的标记。有了 Sass 的@extend 指令，您可以自由地编写清楚地表达其内容和阅读它的开发人员的标记。

## 分享这篇文章