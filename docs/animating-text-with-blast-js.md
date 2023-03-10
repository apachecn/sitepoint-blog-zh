# 用 Blast.js 制作文本动画

> 原文：<https://www.sitepoint.com/animating-text-with-blast-js/>

如今，在库的帮助下，用 CSS 或 JavaScript 制作 HTML 元素的动画或多或少是一件容易的事情。但是，您只能为完整的现有元素制作动画。

这意味着如果你想让一个段落中的一个单词有动画效果，你必须把这个单词放在一个单独的元素中(比如一个`span`)，然后相应地定位它。

如果你只有一两个像这样的`span`元素，这没什么大不了的，但是如果你想把一个段落中的每个角色都做成动画呢？对于每个字符，你将不得不创建一个`span`，这增加了许多额外的标记，使得文本难以编辑。这就是 [Blast.js](http://julian.com/research/blast/) 存在的原因。

## Blast.js 是什么？

Blast.js 是一个 jQuery 插件，可以让你制作单个字符、单词或句子的动画。在本文中，我将提供一些示例，以便您可以了解如何使用 Blast.js。要使用 Blast.js 并尝试下面的示例，您将需要 jQuery，当然还有 Blast.js 文件，该文件可以在 Blast.js 项目网站的[上找到。](http://julian.com/research/blast/)

如前所述，Blast.js 允许我们围绕字符、单词或句子创建元素，但该库不仅限于这些选项。在下一节中，我们将看到一些具体的例子来介绍一些选项。我们将看到的选项列表并不详尽；可在项目网站上找到可用选项的完整列表。

## 爆破我们的第一个文本块

在第一个例子中，我们将爆炸一个标题来激活每个角色，并将它们一个接一个地向右移动。唯一需要的 HTML 代码如下:

```
<h1>Hello World!</h1>
```

在包含 jQuery 和 Blast.js 之后，这一部分的下一段代码将全部放在 jQuery 的 ready 处理程序中的自定义 JavaScript 文件中，以确保页面准备就绪:

```
$(function() {
  // Animation code
});
```

现在我们可以爆我们的头衔了。在我们的例子中，仅仅使用`$('h1')`来定位元素就足够了，但是在您的例子中，您将使用任何合适的选择器来定位元素。

Blast.js 在 jQuery 对象上提供了一个新方法:`.blast()`，它接受一个参数:一个列出了您想要使用的所有选项的对象。在第一个例子中，我们将只使用一个选项`delimiter: 'character'`，来表示我们想要逐个字符地爆破我们的标题。

如果我们没有包含该参数，将使用默认值`'word'`(而不是“character”)，因此将在每个单词周围创建一个`span`(而不是像我们在这里希望的那样在每个字符周围创建)。

```
$('h1').blast({
  delimiter: 'character'
});
```

这样，我们简单的`h1`元素将被分解，并生成如下 DOM:

```
<h1 class="blast-root">
  <span class="blast">H</span>
  <span class="blast">e</span>
  <span class="blast">l</span>
  <span class="blast">l</span>
  <span class="blast">o</span>

  <span class="blast">W</span>
  <span class="blast">o</span>
  <span class="blast">r</span>
  <span class="blast">l</span>
  <span class="blast">d</span>
  <span class="blast">!</span>
</h1>
```

注意，单词之间的空格被保留了下来，没有被封装成一个`span`。

现在，我们需要检索生成的`span`元素。例如，你可以试试`$('.blast')`，但是有一个更简单的方法。默认情况下，`.blast()`方法返回生成的元素，因此检索这些元素的唯一方法就是将它们存储在一个变量中。

```
var chars = $('h1').blast({
  delimiter: 'character'
});
```

获取生成的元素是有用的，但不是所有时候都有用。因此，如果您希望`.blast()`方法返回父元素(您正在爆破的主元素)而不是生成的元素，可以使用另一个选项:`returnGenerated`来完成。默认设置为`true`；将其设置为`false`，您将拥有自己的父元素:

```
var chars = $('h1').blast({
  delimiter: 'character',
  returnGenerated: false
});
```

让我们回到我们的例子来为我们收集的元素制作动画。由于 jQuery 的`.each()`方法，我们将一个接一个地制作每个角色的动画。

`.each()`方法将为 jQuery 对象中存储的每个元素执行一个函数。这是我们将使用的一个，在评论中有解释。

```
// A character by character animation
chars.each(function(i) {
  // initialize position
  $(this).css({
    position: 'relative',
    left: 0
  })

  // Delay: we don't want to animate
  // characters simultaneously
  .delay(i * 45)

  // Animate to the right
  .animate({ left: '50px' }, 300);
});
```

为了解释这段代码:首先，我们使用`$(this)`检索当前元素(即当前字符)。我们将它的相对位置初始化为零，最后，我们将这个位置制作成动画。

如代码中相应注释所述，`.delay()`方法在定义的毫秒数后启动动画，使用`i * 45`，其中`i`是 jQuery 提供的计数器(我们作为参数传入)。对于第一个角色，`i`等于`0`，所以动画立即启动，然后等于`1`，第二个角色在 45 毫秒后动画，以此类推。

我们的动画已经准备好了，而且它工作了！您可以在下面查看一个活生生的例子。

参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )的 Pen [Blast.js 示例](http://codepen.io/SitePoint/pen/KwXBMa/)。