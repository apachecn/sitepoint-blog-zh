# 现在如何使用响应式 Web 组件

> 原文：<https://www.sitepoint.com/responsive-web-components/>

几年前，我开始听到很多关于 Web 组件的事情。我变得非常兴奋，然后随着噪音的平息，我完全忘记了他们。事实证明，已经有了一些好的动向，支持开始增加。特别是响应式 Web 组件，它将真正简化我们实现响应式设计的方式。虽然这听起来很有希望，但是在开始使用它们之前，有四个问题是许多开发人员想知道的:

他们会不会…

*   适应当前的网络状态？
*   工作跨浏览器？
*   适应他们所处的空间？
*   推广模块化代码？

在本文中，我们将探索这些问题的答案！

## 什么是 Web 组件？

本质上，它们是 W3 规范，使您能够构建自己的 HTML 元素。如果你真的对它们一无所知，快速阅读一下它们可能是值得的，因为我在文章中使用的一些术语需要基本的理解。

## 我为什么要用它们？

一个可能需要使用 Web 组件的好例子是当你正在构建浏览器中不存在的 UI 时——如颜色选择器、旋转木马、手风琴或打字头等元素。或者，您可能有一些跨不同站点一直在使用的组件，您厌倦了每次都要重新构建。比如社交按钮、订阅表单或通知。有了 Web 组件，你可以将你的标记、样式和脚本打包到封装的模块中，使用 HTML 标签如`<color-picker></color-picker>`来引用它们。如果您使用过 Angular 指令或 React 组件，这看起来会很熟悉，但这里的好处是它对浏览器和框架是不可知的。

Web 组件使您能够一次构建 UI 的各个部分，并在任何地方使用它们。这对于像 living styleguide 这样的项目来说是理想的，在这些项目中，您希望为您的每个组件构建一个具有单一真实来源的库。这种方法意味着重复更少的代码，并拥有可移植的组件，您或您的团队可以简单地将引用放到您的界面中。

使用 Web 组件的另一大优势是能够将这些组件打包并与其他开发人员或网站所有者共享。本质上，消费者可以在他们的网页中放一个导入声明，例如:

```
<link rel="import"
      href="http://url.com/subscribe-form.html">
```

然后使用您在该组件中定义的定制元素标记。将这个导入到他们的网页中，他们可以在他们的页面上拥有任意多的自定义组件实例。在订阅表单示例中，我们可以在页面的多个位置使用标签`<subscribe-form></subscribe-form>`。当然，如果你这样做，你需要确保你制作的漂亮的组件是非常灵活的，并且可以在各种设备和屏幕尺寸上工作。

## 把这些坏男孩变成有反应的网络组件

我觉得最好的演示方式是通过例子，所以我将继续使用 subscribe 表单，这是一个通用的 UI，在不同的站点和实现之间不会有太大的变化。

这是我之前做的一个:

![Screenshot of email subscription form, two input fields one for name and one for email, side by side](img/9fcd8d04f7c4632c55fb947bf88e21ba.png)

在我的 Web 组件模板中，我有一些表单的基本标记。

```
<ul class="form"> 
  <li class="form__item">
    <label for="name">Name:</label>
    <input id="name" name="name" type="text">
  </li>
  <li class="form__item">
    <label for="name">Email:</label>
    <input id="name" name="name" type="text">
  </li>
</ul>
```

我的标记和 CSS 被藏在我的组件中，利用了规范的影子 DOM 部分。这给了我封装，允许我在代码中自由地使用`label`和`input`选择器，而不用担心其他样式渗入或者我的样式渗入父站点。

为了清晰和简洁，除了用于布局的样式之外，我不打算写出其他样式。我使用了`float: left`和`display: table`的组合来实现这个布局，以防止任何宽度的中断。

```
.form__item {
  display: table;
  float: left;
  width: 50%;  
}

label {
  display: table-cell;
  width: auto;
}

input {
  display: table-cell;
  width: 100%;
}
```

作为演示，我将使用 HTML 导入将其拉入 SitePoint 站点:

![Screenshot of email subscription form imported into main body of SitePoint site](img/0b5474f30496bf8878fd15dccabedc53.png)

现在我们都设置好了，让我们看看一些响应技术。

### 媒体查询

我们所知道和喜爱的经典方法仍然存在于响应式 Web 组件中。如果您想在一些断点中进行烘焙，可以在编写组件代码时在模板内部应用这些方法；或者，如果您想让使用者来决定，可以对容器应用一个挂钩(例如，通过使用类)。在这种情况下，我们需要做的就是剥离浮动，并将其调整为全幅。

```
@media (max-width: 30em) {
  .form__item {
    float: none;
    width: 100%;
  }
}
```

![Screenshot of email subscription form below 30em, form fields are now stacked instead of inline](img/e12dc425e53ba3ce03ea6c917258b97c.png)

然而，这次媒体的询问不足以拯救我们。

我很满意我的注册表单样式，它在正文中运行良好，但是当网站作者想把它放到他们的侧边栏时会发生什么呢？突然，我的表单看起来被压扁了，实际上无法使用。

![Screenshot of email subscription form, squashed up in the Sitepoint website sidebar](img/519480614f3b2ba002652f2f205f57b6.png)

这是因为组件不知道它的边界——它没有上下文。

Web 组件的要点是，你可以把它们放在任何地方，它们都会正常工作。显然这是不行的，但是有一些技巧可以用来确保你的组件是响应性的和上下文相关的。

### Flexbox 欺骗

如果你正在考虑使用 Web 组件，很可能你只支持现代的浏览器，而不担心 IE9，在这种情况下，一点 Flexbox 的魔力可能是完美的，让你把它们变成响应性的 Web 组件。这是我的首选方法，因为它不需要任何额外的 JavaScript。

这里的代码示例再次纯粹展示了我使用的布局样式:

```
.form {
  display: flex;
  flex-wrap: wrap;
}

.form__item {
  align-items: center;   
  display: flex;
  flex: 1 0 320px;
  flex-wrap: wrap;
  max-width: 100%;
}       

label {
  flex: 1 0 90px;
}

input {
  flex: 1 0 230px;
  width: 100%;
}
```

通过将容器设置为`display: flex; flex-wrap: wrap`,`.form__item`元素将并排出现，但是我们仍然需要做一些调整，以便当容器变得太小时，它们可以很好地堆叠。

在`.form__item`中，我使用了`flex: 1 0 320px;`的 flex 简写，用 flex 术语来翻译就是——增长 1，伸缩 0，伸缩 320 像素。将 flex-wrap 设置为 wrap 意味着它不会比我们设置的基数(320px)小，但将其设置为 flex 意味着它将占用剩余的可用空间。

我已经给了`label`和`input`元素类似的处理。它们的组合 flex 值总计为 320，这意味着它们将在较小的容器尺寸下表现出所需的行为。

做了这些调整后，让我们看看 SitePoint 侧边栏中的同一个表单:

![Screenshot of email subscription form, sitting nicely in the sidebar with form elements stacked using Responsive Web Components](img/c8a4483e01d99296b81fee78a4fce344.png)

好多了！然而，给猫剥皮的方法不止一种。

### 元素查询

元素查询的思想是您能够模拟媒体查询类型的功能，但是是在组件级别——对于响应性 Web 组件来说非常方便。实际上，想象一下能够在 CSS 中编写这样的内容:

```
.form__item[max-width~="30em"] {
  /* Styles here */
}
```

这正是我们所能做的。很强大吧？

不幸的是，由于浏览器供应商担心可能导致无限循环，这种技术本身并不可用。然而，一些聪明人编写了插件来实现这一功能。

在我的例子中，我使用了[马克·J·施密特](http://marcjschmidt.de/about.html)的 [CSS 元素查询](https://github.com/marcj/css-element-queries)，这是一个非常好的实现。还有以下类似项目:

*   [初级](https://github.com/filamentgroup/elementary)
*   [要素查询](https://github.com/tysonmatanich/elementQuery)
*   [方程式 js](https://github.com/Snugug/eq.js)

本质上，他们所做的是使用 JavaScript 来检测组件的宽度并修改属性，以便您能够在 CSS 中对其进行样式化。有些是事件驱动的，有些基于调整大小。

```
.form__item[max-width~="30em"] {
  float: none;
  width: 100%;
}
```

通过这些插件中的一个和类似上面的一小段代码的组合，我们能够得到和侧边栏中的 flexbox 实现完全相同的结果。

### 属性

我们可以向响应式 Web 组件的消费者提供的一个很酷的东西是通过属性的 API。例如，您可以设置一个名为 layout 的属性，并设置“小”和“大”的大小。我们的组件消费者可以如下使用它:

```
<subscribe-form layout="small"></subscribe-form>
```

这将触发小型实现的样式。

在 Shadow DOM 中，包含的元素被称为`:host`。一个例子可能是这样的:

```
:host { 
  display: block; 
} 

:host([layout = "small"]) .form__item { 
  float: none;
  width: 100%;
} 

:host([layout = "large"]) .form__item { 
  display: table;
  float: left;
  width: 50%;   
}
```

这将允许组件消费者编写一些简单的 JavaScript，在您提供给他们的不同大小之间切换。这意味着不用在你的模块中加载额外的 JavaScript，你可以让用户自己决定如何连接它。这使得您的组件更加经得起未来的考验。

## 你自己试试！

如果你有兴趣看看我在这个演示中提到的 flexbox 版本在你自己的机器上的运行情况，你可以在 Chrome 中做以下事情:

1.  安装 [CORS 镀铬加长件](https://chrome.google.com/webstore/detail/allow-control-allow-origi/nlfbmbojpeacfghkpbjhddihlkkiljbi?hl=en)并打开。这是为了允许从外部网站导入 HTML。
2.  刷新此页面或在新标签或窗口中打开任何网站，以便扩展生效。
3.  在 Chrome 开发者工具中，将这个添加到`<head>`标签中:

    ```
    <link rel="import" href="http://wsh.webfactional.com/web_components/components/
      cm-subscribe-form--flex.html">
    ```

4.  Finally, add:

    ```
    <cm-subscribe-form-flex></cm-subscribe-form-flex>
    ```

    在`<body>`里面的某个地方，您应该会看到我的订阅表单自动神奇地通过了。尽情享受吧！

## 结论

上述方法实际上可以用于任何 UI 开发，但是由于其可重用和可共享的特性，它们特别适合于响应式 Web 组件。

如果你还没有检查过 Web 组件，我强烈建议你这样做。虽然完整的规格目前只在 Chrome 和 Opera 中提供，但其他浏览器供应商也没有落后于 T1。

如果你有兴趣了解更多关于响应式网页设计的知识，可以看看我们的新书，克里斯·沃德写的《快速启动响应式网页设计》。

## 分享这篇文章