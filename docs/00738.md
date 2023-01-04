# 作为 WordPress 开发者需要了解 React 吗？

> 原文：<https://www.sitepoint.com/do-you-need-to-know-react-as-a-wordpress-developer/>

*这篇关于作为 WordPress 开发者是否需要了解 React 的文章是[最初由 Torque 杂志](https://torquemag.io/2018/10/do-you-need-to-know-react-as-a-wordpress-developer/)发表，经允许在此转载。*

新的 WordPress 内容编辑系统 Gutenberg 将在 WordPress 5.0 中支持 WordPress 文章编辑器。古腾堡是一个“基于块”的编辑器。创作内容时，一切都是块。如果你有一篇只有一段，一个标题，然后是两段的文章，那就是四个区块。

Gutenberg 提供了一组默认的“核心”块——段落、标题、最近的文章、图片、块引用等。如果你使用 Gutenberg 来创建内容，你可以使用那些由你网站上安装的 WordPress 插件提供的块或者自定义块。

Gutenberg 是一个 JavaScript 驱动的接口。具体来说，它是使用脸书的开源用户界面库“React”构建的。这篇文章解释了一些关于使用 JavaScript 创建你自己的定制块用于 Gutenberg 编辑器的内容。您不必使用 JavaScript 来创建块。高级定制字段(ACF)最近宣布了一个用 PHP 创建定制块的简洁系统。

## 什么是反应？

在前端开发中，您做的性能最低的事情是从 DOM 中读取和写入。跨浏览器一致地引用和更新 DOM 是一件非常困难的事情。React 为此提供了一个更好的系统，它实现了一个反应式编程模型和一个虚拟 DOM 抽象。

React 不是直接与 DOM 交互，例如使用`jQuery.html()`或`jQuery.val()`，而是创建 DOM 的虚拟表示。我们称之为虚拟 DOM 或 VDOM。VDOM 是一个 JavaScript 对象，表示 DOM 的结构。每当您的 React 代码对任何数据中的更改做出反应时，都会重新计算 VDOM。之后，React 计算 DOM 在更改之前和更改之后的差异。然后 React(React DOM 或 React Native)只更新 DOM 中需要更改的部分。这是如何做到的并不重要。

## React 在古腾堡是如何使用的？

React 是一个用于创建可重用组件的库。因为它们是可重用的，我们可以用组件组成接口。这是一个在脸书创建的开源项目。

> 一切都是一块。文本、图像、图库、窗口小部件、短代码，甚至大块的定制 HTML，不管它是通过插件还是其他方式添加的。你应该只需要学习掌握一个单一的接口:块接口，然后你就知道如何做所有的事情。–[古腾堡手册](https://wordpress.org/gutenberg/handbook/reference/design-principles/#vision)

积木是古腾堡的基本单位。我们由一个或多个块组成内容。

组件是 React 的原子单位，我们用组件组成 React 应用。Gutenberg 是用 React 创建的，所以每个块都由一个或多个组件组成。

值得注意的是，我将在这个系列的文章中更多地讨论这一点，但是 Gutenberg 在 React 上添加了一个很薄的抽象层。在我们的古腾堡代码中，我们将使用 wp.createElement 代替`React.createElement`。它的工作原理是一样的，但是当 React 的 API 发生变化时，WordPress 可以决定何时支持这些变化并提供向后兼容的包装器，或者决定不提供。

这是对未来很好的规划，但是现在，只是反应。

## 我需要知道 React 来和 Gutenberg 一起开发吗？

所以，这给我们带来了一个大问题，你需要学习反应吗？不，你没有。这些都不重要，除非你想自己做积木。如果你只是想使用 core 或 plugins 提供的块，你永远不需要自己制作块类型。

### 不。但是…

您可以在不了解太多 JavaScript 的情况下创建一个基本块。看看这个基本的示例块，它是从古腾堡的官方示例简化而来的:

```
( function( blocks, element ) {
    var el = element.createElement;
    blocks.registerBlockType( 'namespace/block-name', {
        title: __( 'Example: Basic', 'gutenberg-examples' ),
        icon: 'universal-access-alt',
        category: 'layout',
        edit: function() {
            return el(
                'p',
                {},
                'Hello World, step 1 (from the editor).'
            );
        },
        save: function() {
            return el(
                'p',
                {},
                'Hello World, step 1 (from the frontend).'
            );
        },
    } );
}(
    window.wp.blocks,
    window.wp.element
) ); 
```

有一点可能是新的，那就是使用 wp.createelement —在本例中，它在变量“El”中—来创建 HTML。这是创建“p”类型 html 元素的一种奇特方式。我将在本系列的下一篇文章中讨论这个问题。

WordPress 在 React 上有一个抽象层，所以你真正需要知道的只是几个函数:`wp.createElement`，它创建 HTML 和 [`setAttribute()`，它用来更新你的块属性](https://wordpress.org/gutenberg/handbook/blocks/introducing-attributes-and-editable-fields/)。

我建议浏览古腾堡手册的创建模块部分，然后[查看示例报告](https://github.com/WordPress/gutenberg-examples/blob/master/01-basic/block.js)以及来自[word camp Miami 2018 古腾堡研讨会](https://github.com/brichards/gutendev)的示例代码。这就是你不用深入 React 就能使用的所有代码。

### 是的，如果…

如果你只需要制作简单的积木，也许用一两个控件，那么你不需要知道比我之前提到的两个概念更多的东西。但是，如果你想创建一个更复杂的块，请在 Gutenberg 和其他 React 应用程序之间共享组件，例如，wp-admin 设置屏幕或插件的移动应用程序。

React 是一个非常有趣的库，精通 React 是一项非常抢手的技能。更重要的是，一旦你学会了 React，你就可以更容易地理解更高级的 Gutenberg 概念——状态管理、单元测试等等。

## 如何为 WordPress 和 Gutenberg 学习 React

这是古腾堡 React 开发系列的开始。下一次我将介绍 [React 基础知识](https://torquemag.io/2018/10/react-basics-for-wordpress-developers/)，然后介绍如何在 Gutenberg 模块中应用它们。从那里，我们将制作动态块，然后查看状态管理和测试。

我在我的网站上有一个[古腾堡开发者讲座的列表，你可能会觉得有用。在这个系列中，我将解释 React 的概念，但是如果你想深入学习 JavaScript 和 React，](http://joshpress.net/a-list-of-developer-facing-talks-about-the-new-wordpress-gutenberg-block-editor/)[韦斯·博斯](https://wesbos.com/)和[扎克·戈登](https://javascriptforwp.com/)有关于 React 和 JavaScript 的很棒的课程可以帮助你入门。

## 分享这篇文章