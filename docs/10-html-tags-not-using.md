# 你可能不会使用的 10 个 HTML 标签

> 原文：<https://www.sitepoint.com/10-html-tags-not-using/>

作为一名前端开发人员，您无疑会经常使用 HTML，并且可能会觉得它不再有任何未知之处。然而，它的发展方式(特别是随着 HTML5 的出现)有时可能会让你吃惊。在本文中，我将向您展示 10 个您可能没有使用或者甚至还没有意识到的 HTML 标签，它们有助于提高 web 页面的语义和可维护性。

## 1.

在某些时候，我们可能需要在网页上表达一个度量。它可以是从考试结果到磁盘使用的任何东西。HTML5 引入了一个名为`<meter>`的新元素，它表示一个已知范围内的标量度量，或者一个分数值。

根据规范中对该元素的定义，`<meter>`*不*适合测量外部温度之类的东西吗——因为它没有固定的范围(你可以定义，但它是任意的)。这个元素有几个属性。最常见的有:`value`、`min`、`max`。第一个用于指示度量，而另外两个用于指定范围。所以，如果你想指出一个 500Gb 的硬盘占用了 300Gb，你可以写:

```
<meter value="300" min="0" max="500">300Gb of 500Gb</meter> occupied.
```

## 2.

自古以来，开发人员已经创建了小部件来通知用户下载或任务的进度。今天有一个名为`<progress>`的本地 HTML5 标签。它有两个属性:`value`(指定进度的状态)和`max`(指示要达到的最大值)。如果没有设置`max`值，则假定范围为 0-1，并且`value`可以是该范围内的任意浮点数。因此，要显示任务完成 50%时的进度条，您可以写:

```
<progress value="50" max="100">50%</progress>
```

或者相当于:

```
<progress value="0.5">50%</progress>
```

元素内的文本用作旧浏览器的后备。通常，这个元素不会被静态地使用，而是与 JavaScript 或 CSS 动画结合使用，以指示持续的进度。

## 3.& 4.<cite>和</cite>

写作时，我们经常发现自己在引用一本书、一篇文章或一个人。在纸面上，我们通常使用双引号(“…”)来界定引用部分，以及介词 *from* 或 *by* 来指明我们引用的是谁或来自什么来源。

在 HTML5 中，我们用`<q>`指定引用，用`<cite>`指定来源。注意，直到最近,`<cite>`还只能用来表示作品的名称(书、文章、电影等)。)，不是一个人。然而，这已经被更新，所以我们也可以用它来“引用”人。`<q>`标签有一个名为`cite`的属性，它允许我们指出引用来源的链接。

现在举个例子，假设我们想引用埃兹拉·庞德的一句名言(我最喜欢的一句)。使用 HTML，我们将编写:

```
We should fight for our rights because, as <cite>Ezra Pound</cite> said,
<q>If a man isn't willing to take some risk for his opinions, either his 
opinions are no good or he's no good.</q>
```

## 5.

元素允许我们显示源代码中出现的预格式化文本。这意味着多个空白字符不会合并成一个(改变浏览器处理空白的默认方式)。当您需要显示一段代码时，这个标签是理想的，因为它有助于保留缩进。例如，在一个页面中，我们可能会看到这样的内容:

```
<pre><code>
function sayHello(name) {
    for (var i = 0; i &lt; 3; i++) {
        console.log('Hi ' + name + '!');
    }
}

sayHello('Aurelio');
</code></pre>
```

## 6.& 7.`和<samp></samp>`

如果您是一名技术作家，您可能会经常讨论需要使用终端或 shell 命令的工具和技术。因此，您可能还想显示这些命令的结果。这种情况最适合`<kbd>`和`<samp>`。前者代表用户输入，例如但不限于键盘输入。后者代表程序或计算系统输出的样本。这些元素与前面介绍的`pre`元素配合得很好。这些元素的使用示例与规范中使用的示例相似，如下所示:

```
<samp><span>jdoe@mowmow:~$</span> <kbd>ssh demo.example.com</kbd>
Last login: Tue Apr 12 09:10:17 2005 from mowmow.example.com
<span class="prompt">jdoe@demo:~$</span> _</samp>
```

## 8.<small></small>

 <small>在 HTML5 之前，`<small>`元素只是一个表示性的元素，用于使用较小的字体书写单词。在 HTML5 中`<small>`有一些语义值。现在,`<small>`元素表示经常出现在小字体中的文本，如免责声明、警告、法律限制或版权。其使用示例如下所示:

```
This article is offered to you by Aurelio De Rosa <small>Copyright © 2014</small>
```

## 9.

`<output>`标签表示计算的结果。它的主要属性是`for`，它包含了一个由空格分隔的元素 id 列表，这些元素参与了计算，或者影响了计算。如果你有一个提供汽车定价或人寿保险报价的网站，这个元素正是你所需要的。

为了看到它的实际效果，想象一下你公司的网站上有一个小部件，用户可以在那里指定投资你公司的金额，以换取每年一定比例的回报。基于这种情况，您可能有一个表单，使用如下所示的`output`元素:

```
<form id="form-calculation">

  <label for="amount">Amount:</label> <input id="amount" type="number"/>
  <label for="percentage">Percentage:</label>
  <input id="percentage" type="number">

  <label for="years">Years:</label>
  <input id="years" type="number">

  <input type="submit" value="Submit">

  <label for="total">Total:</label>
  <output id="total" for="amount percentage years"></output>
</form>
<script> function calculateTotal(amount, percentage, years) {
   for(var i = 0; i < years; i++) {
      amount += amount * percentage / 100;
   }

   return amount;
}
document.getElementById('form-calculation').addEventListener('submit', function(event) {
      event.preventDefault();

      document.getElementById('total').textContent = calculateTotal(
         Number(document.getElementById('amount').value),
         Number(document.getElementById('percentage').value),
         Number(document.getElementById('years').value)
      );
   },
   false
); </script>
```

## 10.<dfn>(锦上添花)</dfn>

在说这个元素之前，我想坦白一件事。当我决定写这篇文章时，我开始考虑要包含哪些标签。当我到达列表中的第 9 个标签时，我想如果能加上一些特别的东西作为结论就好了。所以，我决定扫描 HTML5 标签列表，下面是我的反应。

现在你知道我是如何偶然发现`<dfn>`的，我们可以继续描述这个标签。元素允许我们用元素定义术语。最简单的形式是，它包含您想要定义的术语，然后用一个段落、一个描述列表组或一个包含定义的部分来包装它。为了理解这个概念，假设我们正在编写一个描述 HTML 的页面，我们想要定义它。要使用`<dfn>`标签做到这一点，我们可以写:

```
<dfn>HTML</dfn> is the primary language for marking up web content.
```

在这种情况下，我们定义了术语 *HTML* ，它是一个缩写。因此，我们可以通过编写以下代码来进一步增强我们的标记:

```
<dfn><abbr title="HyperText Markup Language">HTML</abbr></dfn> is the primary language for marking up web content.
```

为了选择定义的术语，标准规定了由 3 个点组成的优先级列表。优先级赋予在`<dfn>`标签的`title`属性中指定的任何东西。然后，我们拥有在`dfn`的`abbr`元素子元素中定义的任何东西(如第二个例子所示)。最后一个优先级是 dfn 元素的文本(如第一个例子所示)。

## 最后

在这篇文章中，我们已经讨论了一些很少使用并且经常被遗忘的 HTML 标签。我建议你不时阅读一下提供的 HTML 标签的完整列表。通过这种方式，你将刷新你的语义元素知识，也许你会在这个过程中发现一个惊喜(就像我对`<dfn>`标签所做的那样)。

## 分享这篇文章</small>