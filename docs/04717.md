# 使用 scrollReveal.js 显示元素

> 原文：<https://www.sitepoint.com/revealing-elements-scrollreveal-js/>

自 1996 年创立以来，CSS 是我们在网络上用来设计网站风格的语言。今天，我们能够使用 CSS 创建令人敬畏的效果，并且由于形状、flex-box 和动画的引入，它每天都变得更加强大。这当然是真的，除了一个众所周知的笑话，即我们可以创建动画，但直到几年前，我们还不能轻松地垂直居中元素(这在一些版本的 Internet Explorer 中仍然是真的)。

尽管 CSS 是完成特定任务的语言，但很长一段时间以来，JavaScript 一直被用来创建漂亮的动画。甚至有完整的 JavaScript 库专门用于创建动画。在本文中，我将介绍其中一个名为 scrollReveal.js 的库。

## scrollReveal.js 是什么？

[scrollReveal.js](https://github.com/julianlloyd/scrollReveal.js) 是一个库，<q>可以在元素进入视窗时轻松显示它们。</q>它的体积非常小，如果缩小和压缩，重量大约为 3Kb。它没有依赖项，所以不需要像 jQuery 一样添加其他库来使用它。除了它支持的漂亮效果，我真正喜欢这个库的是，你可以使用自然语言来定义你想要运行的动画。我们稍后将回到这个特性，但是首先我想向您展示一个简单例子，说明这个库可以为您做什么:

[https://jsfiddle.net/zczc93vr/embedded/](https://jsfiddle.net/zczc93vr/embedded/)

很好，不是吗？这个演示是用很少几行代码创建的。这个演示的代码可以通过 JSFiddle 获得。

既然我已经让你感兴趣了，让我们有条不紊地进行吧。

## scrollReveal.js 入门

为了在我们的网页中使用 scrollReveal.js，我们必须获得库的副本。通常，这可以通过从其 [GitHub 库](https://github.com/julianlloyd/scrollReveal.js)下载或者通过 Bower 使用以下命令来完成:

```
bower install scrollReveal.js
```

下载后，您必须通过添加一个`script`元素将库包含在您的页面中，如以下代码所示:

```
<script src="path/to/scrollReveal.min.js"></script>
```

如果您已经使用 Bower 下载了它，路径应该如下所示:

```
<script src="bower_components/scrollReveal.js/dist/scrollReveal.min.js"></script>
```

有了库之后，我们需要添加或修改一些库将激活的元素。具体来说，scrollReveal.js 搜索页面上具有名为`data-sr`的属性的元素。正如我们将在下一节中看到的，您可以通过设置该属性的值来更改应用的动画，但是在简单的使用中，拥有该属性就足够了。

假设您的页面中有以下 HTML 代码:

```
<body>
   <div data-sr>This is the first div</div>
   <div data-sr>This is the second div</div>
   <div data-sr>This is yet another div</div>
</body>
```

有了这个简单的代码，您需要做的就是添加以下 JavaScript 代码:

```
new scrollReveal();
```

完成最后一步后，我们现在可以打开页面了。这个库将对所有的`div`执行一个简单的淡入。这段代码的结果，带有一点更令人愉快的风格，如下所示，并且[也可以作为 JSFiddle](https://jsfiddle.net/8b7qz2ca/) 使用:

[https://jsfiddle.net/8b7qz2ca/embedded/](https://jsfiddle.net/8b7qz2ca/embedded/)

现在让我们来看看一些可用的效果。

## 配置和效果

默认情况下 [scrollReveal.js](https://github.com/julianlloyd/scrollReveal.js) 让你的元素从页面底部进入视窗，但是你可以通过使用`enter`关键字后跟方向来改变每个元素的效果。所以，你可以写`enter left`。您还可以使用`move`关键字后跟像素数来指定给定元素需要移动多少像素，例如`move 15px`。在这些指令之间，您可以放置任何您喜欢的文本。例如，您可以按如下方式定义元素:

```
<div data-sr="please, can you enter left and then move 15px ?">This is the first div</div>
```

请不要在这件事上太放肆，因为你可能会在关键词之间写单词，得到一个意想不到的结果。例如，这个定义是*不正确的*:

```
<div data-sr="please, can you enter left and then move of 15px ?">This is the first div</div>
```

你能在“move”和“15px”之间看到“of”这个词吗？很好！让我们继续讨论。

使用 scrollReveal.js，您还可以让您的元素按给定的百分比放大或缩小。如果希望元素变大，可以使用关键字`scale`后跟单词`up`来创建这种效果；如果希望元素变小，可以使用关键字`down`，后跟百分比本身。

如果你想在给定的秒数后产生效果，你可以使用`wait`关键字，后跟等待的秒数。这两个指令的使用示例如下:

```
<div data-sr="hey bro! wait 2s and then scale up 20% this element">This is another div</div>
```

正如我所说的，您也可以在不为`data-sr`属性指定值的情况下执行基本的动画。发生这种情况是因为这个库有一组我们在本节和其他部分讨论过的关键字的默认值。我们还可以通过在调用`scrollReveal()`方法时传递一个配置对象来更改这些默认值。以下是与其属性相关的默认值的完整列表:

```
defaults: {
  enter:    'bottom',
  move:     '8px',
  over:     '0.6s',
  wait:     '0s',
  easing:   'ease',
  scale:    { direction: 'up', power: '5%' },
  opacity:  0,
  mobile:   false,
  reset:    false,
  viewport: window.document.documentElement,
  delay:    'once',
  vFactor:  0.60,
  complete: function( el ) {}
}
```

在下一个例子中，我们将使用配置对象的一些属性和我在本节中描述的关键字。首先，我们将编写 HTML 代码。我们将定义三个`div`:一个将执行缺省动画，一些缺省值被改变，而另外两个将有它们自己的特定属性:

```
<div data-sr id="d1">
   <h1>I run the default animation</h1>
</div>
<div data-sr="wait 1s and enter bottom" id="d2">
   <h1>I have specific values</h1>
</div>
<div data-sr="scale down 10% and then ease-in-out 100px" id="d3">
   <h1>I have specific values too</h1>
</div>
```

要更改默认值，我们将设置一个配置对象，并将其传递给`scrollReveal()`方法，如下所示:

```
var config = {
   enter: 'right',
   wait: '0.5s',
   move: '20px'
}

new scrollReveal(config);
```

这段代码的最终结果如下所示，其中的[可作为 JSFiddle](https://jsfiddle.net/9u24jcs3/) 获得:

[https://jsfiddle.net/9u24jcs3/embedded/](https://jsfiddle.net/9u24jcs3/embedded/)

## 结论

在本文中，我们讨论了 scrollReveal.js，这是一个用于在网站上创建动画的小巧而漂亮的库。如您所见，使用这个库非常容易。永远记住不要滥用动画，因为它们会很快变得令人讨厌和分心。让你的用户关注你的内容，然后在可能的情况下，用一些好的效果来改善他们的体验。

## 分享这篇文章