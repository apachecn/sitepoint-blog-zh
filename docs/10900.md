# 让 CSS 倒计数

> 原文：<https://www.sitepoint.com/making-css-count-backwards/>

你知道 CSS 计数器可以倒退吗？

我也是，直到我需要它的时候，我才发现,`counter-reset`和`counter-increment`属性可以用额外的数字“播种”,以允许更灵活的编号，这也可以用来使它们倒计数！

## 向前计数

计数器的基本思想是，它们允许你在 <abbr title="Cascading Style Sheets">CSS</abbr> 中实现数字系统，本质上是替换和补充[有序列表](https://reference.sitepoint.com/html/ol)属性，如 [`value`](https://reference.sitepoint.com/html/li/value) 和 [`start`](https://reference.sitepoint.com/html/ol/start) 。有了两个属性，您可以在基本选择器中声明计数变量的名称，然后在实例选择器中递增它；然后通常使用`content`属性输出结果。下面是一个简单的例子，它重新创建了标准有序列表的编号( [example1](https://www.sitepoint.com/examples/countbackwards/#example1) ):

```
ol
{
    list-style-type:none;
    counter-reset:item;
}
ol > li
{
    counter-increment:item;
}
ol > li:before
{
    content:counter(item) ". ";
}
```

但是正如我前面所说的，您可以用额外的值“播种”这两个属性，以便它们从不同的数字开始和/或在不同的步骤中计数。下面是另一个例子，这次从 10 开始，两个一起来计数([例 2](https://www.sitepoint.com/examples/countbackwards/#example2) ):

```
ol
{
    list-style-type:none;
    counter-reset:item 10;
}
ol > li
{
    counter-increment:item 2;
}
ol > li:before
{
    content:counter(item) ". ";
}
```

## 倒数！

现在狡猾的一点来了！`counter-increment`中的附加值不一定是正的，也可以是**零或负的**。这是一个计数器从十倒数到一的例子([例 3](https://www.sitepoint.com/examples/countbackwards/#example3) ):

```
ol
{
    list-style-type:none;
    counter-reset:item 11;
}
ol > li
{
    counter-increment:item -1;
}
ol > li:before
{
    content:counter(item) ". ";
}
```

需要注意的是，我们必须如何开始`counter-reset` **比我们想要显示的最大数字**高一个计数，因为迭代属性`counter-increment`已经在第一个元素显示时*已经计数了一次*(它已经计数了该元素)。

当然，我们必须对起始数字进行硬编码，这使得它很难在野外使用，因为它遇到的列表通常会有不同的项目数。接下来要做的事情是**在样式表和内联`style`规则之间拆分规则**。

## 生产代码

所以首先我们有下面的样式表规则，它定义了激活`class`和计数规则(也有几个 <abbr title="Cascading Style Sheets">CSS</abbr> 用于 <abbr title="Internet Explorer 6">IE6</abbr> 和 <abbr title="Internet Explorer 7">IE7</abbr> ，以恢复它们正常的`list-style`，因为计数器不起作用):

```
ol.count-backwards
{
    list-style-type:none;
}
ol.count-backwards > li
{
    counter-increment:start-from -1;
}
ol.count-backwards > li:before
{
    content:counter(start-from) ". ";
}

* html ol.count-backwards { list-style-type:decimal; }
*+html ol.count-backwards { list-style-type:decimal; } 
```

然后我们*用`counter-reset` `style`实例化*一个计数器实例，定义起始数(或者更确切地说，比起始数高 1，正如我们已经看到的)。我使用了`"start-from"`作为计数器名称，这样就很容易记住它是什么和它做什么([例 4](https://www.sitepoint.com/examples/countbackwards/#example4) ):

```
<ol class="count-backwards" style="counter-reset:start-from 11">
    <li>In tenth place..</li>
    <li>In ninth place..</li>
    <li>In eighth place..</li>
    <li>In seventh place..</li>
    <li>In sixth place..</li>
    <li>In fifth place..</li>
    <li>In fourth place..</li>
    <li>In third place..</li>
    <li>In second place..</li>
    <li>In first place..</li>
</ol>
```

我们甚至可以定义额外的`class`规则来实现不同类型的计数器。这里有一个小写希腊语([例 5](https://www.sitepoint.com/examples/countbackwards/#example5) ):

```
ol.count-backwards.lower-greek > li:before
{
    content:counter(start-from, lower-greek) ". ";
}
```

但这一切是为了什么？嗯，我想为我的网站做一个博客文章的存档列表，把最近的列在最上面。因为第一个和最早的在底部，这意味着列表需要倒计数*。*

 *我相信您可以想到在您自己的情况下，这可能是有用的，并且有一种立即实现的方便方法，而没有使用脚本实现时不可避免的输出延迟。

如果缺乏 IE6/7 让你烦恼，你也可以添加一个脚本层…

## 事后反思:入侵 IE

或者在必要时，您可以用一个`expression`属性来做这件事(在之前我已经[写过了)，并利用 <abbr title="Internet Explorer">IE</abbr> 的`<ol>` `start`属性从(](https://www.sitepoint.com/techy-treasures-5-fudging-css-counters-in-internet-explorer/) [example6](https://www.sitepoint.com/examples/countbackwards/#example6) )中读取起始编号:

```
<ol start="10" class="count-backwards" style="counter-reset:start-from 11">
```

```
 ol.count-backwards li
{
    zoom:expression(
        (typeof counter == "undefined" 
            ? counter = parseInt(this.parentNode.getAttribute('start'), 10) 
            : counter--), 
        this.innerHTML = (typeof this.processed == "undefined" 
                ? ("<strong>" + counter + ". </strong>") 
                : "") 
            + this.innerHTML, 
        this.style.marginLeft = "-1.5em",
        this.processed = true, 
        this.runtimeStyle.zoom = "1"
        );
} 
```

<abbr title="Internet Explorer 8">IE8</abbr> 将忽略这段代码，因为它不再实现`expression`，但它首先支持计数器！呜-呼！

*缩略图鸣谢:[斯蒂芬文斯](http://www.flickr.com/photos/stephenvance/4524024065/)*

## 分享这篇文章*