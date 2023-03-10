# Bootstrap 5 的新功能

> 原文：<https://www.sitepoint.com/bootstrap-5-new-features-examples/>

Bootstrap 是最流行的 CSS 库之一。它允许开发人员轻松地使用漂亮的样式和组件，并创建响应性网站。使用 Bootstrap 可以节省开发人员的时间，尤其是对于几乎每个项目中都使用的组件。

Bootstrap 5(当前的主要版本，于 2021 年 5 月发布)带来了大量的变化和改进，包括添加新组件、新类、旧组件的新样式、更新的浏览器支持、删除一些旧组件等等。

在本文中，我们将介绍 Bootstrap 5 中的变化，已经放弃的内容，以及最令人兴奋的新内容。

## 何时使用引导程序(何时不使用)

Bootstrap 标榜自己是“世界上最受欢迎的构建响应迅速、移动优先的网站的框架”，GitHub 上有 152k 颗星星，我不认为这种说法太牵强。特别是对于初学者来说，Bootstrap 是一个开始创建现代干净网站的好方法。它使实现复杂的、移动优先的设计变得容易，并提供了许多您可能在多个项目中需要的组件。

Bootstrap 的学习曲线很低，非常适合不需要构建步骤的静态网站，因为您可以从 Bootstrap 的 CDN 中引用这个库。这与其他一些流行的 CSS 框架形成了鲜明的对比，这些框架可能会针对捆绑器或任务运行器进行优化。

然而，你也应该意识到，Bootstrap *并不是*的灵丹妙药。对于外行来说，Bootstrap 很容易产生混乱和复杂的标记。就千字节数而言，它也是一个相对较大的库(尽管每个版本都在改进)，所以如果您只使用它的一两个特性，它可能不是最佳选择。与任何抽象一样，如果您很好地掌握了底层技术，并且能够就何时使用它做出明智的决定，那么它将会有很大的帮助。

## 从引导数据库 4 升级到 5

从 Bootstrap 4 升级到 Bootstrap 5 通常非常容易。Bootstrap 4 中可用的大多数组件、它们的类和实用程序类在 Bootstrap 5 中仍然可用。迁移时您应该关注的主要事情是您正在使用的类或组件[是否已经被删除](#whats-dropped)。如果它们已经被删除了，那么有一些替代方法或途径可以使用实用程序类来实现相同的结果。您应该关注的第二件事是在需要 JavaScript 作为其功能一部分的组件中从`data-*`属性切换到`data-bs-*`。(我们将在下一节详细介绍这一点。)

如果使用 Bootstrap 的 Sass 文件，有一些变量和 mixins 已经被重命名。Bootstrap 5 有一个关于[定制](https://getbootstrap.com/docs/5.0/customize/overview/)的广泛而详细的部分，以及关于每个组件的 Sass 变量和 mixins 的详细信息，在它们各自的文档页面中。

## 什么变了

Bootstrap 5 对作为库的 Bootstrap 进行了核心更改，更改了所需的依赖性、浏览器支持等。它还为我们在以前的版本中一直使用的组件和类带来了变化。

### jQuery 不再是依赖项

与以前的版本相比，jQuery 的一个主要变化是不再依赖 Bootstrap。现在，您可以在没有它的情况下完全使用 Bootstrap，但您仍然需要 Popper.js。这一变化使得在不需要或不使用 jQuery 的项目中使用 Bootstrap 变得更容易——例如在使用 Bootstrap 和 React 时。

如果它是你的网站的一部分，你仍然可以使用 jQuery 和 Bootstrap。如果 Bootstrap 在`window`对象中检测到 jQuery，它会自动将所有组件添加到 jQuery 的插件系统中。因此，如果您从 v4 迁移到 v5，您不需要担心这种变化，并且您仍然可以在必要时使用 jQuery 和 Bootstrap。

但是如果你在你的网站中使用 jQuery，但是你不希望 Bootstrap 使用 jQuery 呢？您可以通过向文档的 body 元素添加属性`data-bs-no-jquery`来实现这一点:

```
<body data-bs-no-jquery="true">
</body> 
```

没有 jQuery，Bootstrap 如何工作？例如，在 v4 中，您可以使用以下 JavaScript 代码来创建一个 [Toast](https://getbootstrap.com/docs/5.0/components/toasts) 元素:

```
$('.toast').toast() 
```

在 Bootstrap 5 中，如果您的网站已经使用 jQuery，您可以使用相同的代码创建一个 Toast 元素。如果没有 jQuery，您将需要使用类似下面的代码来创建 Toast 元素:

```
const toastElList = [...document.querySelectorAll('.toast')]
const toastList = toastElList.map((toastEl) => {
  return new bootstrap.Toast(toastEl)
}) 
```

这只是使用普通的 JavaScript 在文档中查询具有`.toast`类的元素，然后使用`new bootstrap.Toast()`初始化元素上的 Toast 组件。

### 浏览器支持更改

在 v4 之前，Bootstrap 一直支持 Internet Explorer (IE) 10 和 11。从 Bootstrap 5 开始，对 IE 的支持已经完全停止。所以，如果你的网站需要支持 IE，你在迁移到 v5 时应该小心。

浏览器支持的其他变化包括:

*   从版本 60 开始支持火狐和 Chrome
*   从版本 12 开始支持 Safari 和 iOS
*   从版本 6 开始支持 Android 浏览器和 WebView

### 数据属性的变化

Bootstrap 5 更改了数据属性的命名，这些属性通常由使用 JavaScript 作为其功能一部分的组件使用。以前，大多数依赖于一些 JavaScript 功能的组件都有以`data-`开头的数据属性。例如，要在 Bootstrap 4 中创建工具提示组件:

```
<button 
  type="button" 
  class="btn btn-secondary" 
  data-toggle="tooltip" 
  data-placement="top" 
  title="Tooltip"
>
  Tooltip
</button> 
```

注意`data-toggle`和`data-placement`的用法。在 Bootstrap 5 中，这些组件的数据属性现在以`data-bs`开始，以方便命名 Bootstrap 属性。例如，要在 Bootstrap 5 中创建工具提示组件:

```
<button 
  type="button" 
  class="btn btn-secondary" 
  data-bs-toggle="tooltip" 
  data-bs-placement="top" 
  title="Tooltip"
>
  Tooltip
</button> 
```

我们不用`data-toggle`和`data-placement`，而是用`data-bs-toggle`和`data-bs-placement`。如果您使用 JavaScript 在 Bootstrap 中创建组件，可能不需要做任何更改。但是，如果您的组件只依赖于数据属性来运行，您需要将所有以`data`开头的数据属性改为以`data-bs`开头。

### 修复了错误

在 Bootstrap 4 的文档中的[浏览器和设备](https://getbootstrap.com/docs/4.6/getting-started/browsers-devices/)下，有一个在一些浏览器上发生的错误列表。这些错误不再被列在【Bootstrap 5 的同一列表中。该列表包括以下内容:

*   悬停样式被应用到 iOS 上触摸事件的元素中，这是不可取的，因为它被认为是一种意外的行为。
*   在 Safari 8 及以上版本中使用`.container`导致打印时字体变小。
*   边框半径被验证反馈覆盖(但可以通过添加额外的`.has-validation`类来修复)。

Bootstrap 4 文档中的错误和问题列表还详细列出了不再受支持的浏览器版本中出现的错误。

### 其他变化

还有更多的变化要么很小，要么不会引起剧烈的、明显的变化。这些变化是:

*   自举 5 现在使用 [Popper v2](https://popper.js.org/docs/v2/) 。请确保升级您的 Popper 版本。Popper v1 将不再工作，因为 Bootstrap 5 需要`@popperjs/core`而不是之前的`popper.js`。

*   使用作为 ECMAScript 模块构建的 Bootstrap [版本，Bootstrap 5 现在可以在浏览器中用作模块。](https://getbootstrap.com/docs/5.0/getting-started/javascript/#using-bootstrap-as-a-module)

*   由于 [Libsass 和节点 sass](https://github.com/sass/node-sass) (在 Bootstrap 4 中使用)现在已被弃用，Bootstrap 5 使用 [Dart Sass](https://sass-lang.com/dart-sass) 将源 Sass 文件编译成 CSS。

*   一些 Sass 变量值发生了变化，比如`$zindex-modal`从 1050 变为 1060，`$zindex-popover`从 1060 变为 1070，等等。因此，建议在 Bootstrap 5 的文档中检查每个组件或实用程序类的 Sass 变量。

*   以前，为了使一个元素隐藏起来，但又能被辅助技术发现，使用了类`.sr-only`。这个类现在换成了[。视觉隐藏](https://getbootstrap.com/docs/5.0/helpers/visually-hidden/)。

*   以前，要隐藏一个交互元素，但让它对辅助技术可见，需要同时使用`.sr-only`和`.sr-only-focusable`类。在 Bootstrap 5 中，你只需要使用`.visually-hidden-focusable`而不需要`.visually-hidden`。

*   带有命名源的块引号现在由一个`<figure>`元素包装。

    下面是 Bootstrap 5 中块报价的一个示例:

    在 [CodePen](https://codepen.io) 上看笔 [Bootstrap 5 block quotes](https://codepen.io/SitePoint/pen/qBmwvZE)by SitePoint([@ SitePoint](https://codepen.io/SitePoint))
    。