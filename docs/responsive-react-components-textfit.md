# 如何使用 React Textfit 创建响应式 React 组件

> 原文：<https://www.sitepoint.com/responsive-react-components-textfit/>

**在 React 中开发包括定义可重用的组件，并将它们组装到应用程序的各个部分，以实现您想要的用户界面(UI)。在本文中，我们将研究`react-textfit`库，它使创建响应式 React 组件变得容易，无论它们出现在布局中的什么地方，都可以按预期显示。**

## 文本匹配问题

由于 React 组件是描述用户界面特定部分的 JavaScript 代码，它们实际上是相互独立的。他们的视觉风格常常嵌入其中，成为他们定义的一部分。考虑到它们可能在不同的位置和布局中使用，这可能非常有用。

但是在可重用组件中嵌入样式也有一些缺点。一个例子是反应的情况。假设您希望一行文本(如标题)完全填充为其保留的高度和宽度空间，而不是换行，这一切都不必为每种可能的情况编写自定义 CSS 规则。(您可能希望这样做的示例包括商业标语、广告消息或嵌入在 navbar 组件中的文本。)

![Responsive React components: each component you create may have more than one function](img/feacb90421980db66837aa82a5b0fd40.png)

### CSS 和可移植性

在定义 responsive React 组件的样式时，您需要考虑每个可能包装它的父组件的大小、布局或样式，以便相应地调整字体大小。可以想象，考虑所有可能的容器大小并不可行——即使你可以用 CSS 做到这一点。对于编写媒体查询来说，您将会追逐太多的视口场景，这是不切实际的。但是除了媒体查询之外，在 CSS 中并没有真正的方法来确保一个文本块总是适合一行。

### 创建可重用的 React 组件

幸运的是，一些 React 库可以用来毫不费力地解决这个问题。它们允许您定义可重用的 React 组件，无论可重用组件放在哪个容器中，文本都会按照预期的方式运行。到本文结束时，您将能够使用这些库来解决前面提到的文本匹配问题，并使组件可重用。因此，让我们来看看你应该知道的一切，以使你的文本自动适应 React 中的可用空间。

首先，我们将看看为什么面对这样一个问题如此重要，为什么普通的解决方案可能不够，特别是在使用 React 时。然后， [`react-textfit`](https://www.npmjs.com/package/react-textfit) React 库将出现并用于实现单行和多行文本的解决方案。

![Responsive React components: each component you create will properties for different environments](img/36b7a80d0c0f0a581ad0746bbc5a5fe2.png)

## 可重用组件中的文本匹配问题

让我们来看看下面的演示，它用一个例子解释了文本适配问题。

见笔 [文字拟合问题](https://codepen.io/SitePoint/pen/KKqOxQV)由 SitePoint([@ SitePoint](https://codepen.io/SitePoint))
上 [CodePen](https://codepen.io) 。