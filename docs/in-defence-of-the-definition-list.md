# 为定义列表辩护

> 原文：<https://www.sitepoint.com/in-defence-of-the-definition-list/>

HTML5 中有几种不同类型的列表元素，在有序列表和无序列表盛行的世界中，可以说**定义列表**永远不会赢得流行竞赛。定义列表已经迷失在模糊的海洋中——但是总有一天我们需要后退一步，以稍微不同的方式看待事物。在这里，我想再次向您介绍语义标记的这一被遗忘(如果不是被误解的话)的特性。

## 结构和语义

定义列表与典型的有序或无序列表没有太大的不同，但与这些项目不同的是，它们由三个主要元素组成:

*   描述列表或
*   的术语

*   的描述

随着 HTML5 的发布，

被重命名为“描述列表”,以消除任何突出的混淆，并使开发人员能够更恰当地使用它。

要创建定义列表，您只需编写:

```
<dl>
 <dt>Term 1</dt>
 <dd>Term 1 (description)</dd>
 <dt>Term 2</dt>
 <dd>Term 2 (description)</dd>
</dl>
```

您还可以为一个术语(

)包含多个描述(

)，如下所示:

```
<dl>
 <dt>Term 1</dt>
 <dd>Term 1 (description 1)</dd>
 <dd>Term 1 (description 2)</dd>
 <dd>Term 1 (description 3)</dd>
 <dt>Term 2</dt>
 <dd>Term 2 (description 1)</dd>
 <dd>Term 2 (description 2)</dd>
 <dd>Term 2 (description 3)</dd>
</dl>
```

我仍然觉得用“term”这个词来指代

是用词不当。我更愿意将其视为一种“价值”，因为在现实世界中，这种模式的使用可以转化为:

```
<dl>
 <dt>News 1 (Title)</dt>
 <dd>News 1 (image)</dd>
 <dd>News 1 (description)</dd>
 <dd>News 1 (link)</dd>
 <dt>News 2 (Title)</dt>
 <dd>News 2 (image)</dd>
 <dd>News 2 (description)</dd>
 <dd>News 2 (link)</dd>
</dl>
```

为了代码视图的清晰，您甚至可能想要扩展它，并将其编写为以下格式:

```
<dl>
 <dt>News Story 1 (Title)</dt>
   <dd>News Story 1 (image)</dd>
   <dd>News Story 1 (description)</dd>
   <dd>News Story 1 (link)</dd>
 <dt>News Story 2 (Title)</dt>
   <dd>News Story 2 (image)</dd>
   <dd>News Story 2 (description)</dd>
   <dd>News Story 2 (link)</dd>
 <dt>News Story 3 (Title)</dt>
   <dd>News Story 3 (image)</dd>
   <dd>News Story 3 (description)</dd>
   <dd>News Story 3 (link)</dd>
</dl>
```

在这种关系中，你可以呈现的内容的范围或类型是无限的。从博客到产品，从事件到统计数据，你只需要意识到，当

标签作为一个附件时，一个术语和一个描述或者一个术语和多个描述的后续分组意味着定义列表是一个近乎完美的语义标记实例。可以这样想:在单个元素中列出由成对值组成的项目，对于设计者、开发者和 SEO 来说都应该是受欢迎的消息。

这可能不是传统的做事方式(诞生于 HTML4 时代)，但这是定义列表战胜其他列表类型的地方。记住，无序列表和有序列表都只能包含列表元素，所以标题和描述不会像定义列表中的相同数据那样得到语义描述。如果您指出定义列表可以做的事情也可以在嵌套表或复杂的

标签数组中实现，我不会不同意。但是，如果您比较结果，并忽略代码膨胀的问题，这将是您的逻辑选择？

## 文体论

像许多其他列表类型一样，定义列表的最基本输出可能看起来有点乏味。例如，让我们检查以下代码:

```
<dl>
  <dt>News Story 1 (title)</dt>
    <dd>News Story 1 (date)</dd>
    <dd>News Story 1 (story)</dd>
  <dt>News Story 2 (title)</dt>
    <dd>News Story 2 (date)</dd>
    <dd>News Story 2 (story)</dd>
</dl>
```

但是，您可以在描述(

)中包含块级元素，如下所示:

```
<dl>
 <dt>Product Title</dt>
 <dd><strong>Product Code</strong></dd>
 <dd><p>Product Description</p></dd>
 <dd>
   <ul>
     <li>Product Feature 1</li>
     <li>Product Feature 2</li>
     <li>Product Feature 3</li>
   </ul>
 </dd>
</dl>
```

同样，这个例子的本质可能并不适合所有人，但是您注意到了在

元素中包含了额外的 HTML 标记吗？例如，如果您想要开发一个电子商务解决方案，您可以增强上面的代码并生成类似于下面的内容:

```
<dl>
 <dt>Product 1 (Title)</dt>
  <dd><p>Product 1 (description)</p></dd>
  <dd><p>Product 1 (price)</p></dd>
  <dd><p>Product 1 (image)</p></dd>
  <dd>
   <ul>
     <li><span>Product 1/Feature 1 (Title)</span></li>
     <li><span>Product 1/Feature 2 (Title)</span></li>
     <li><span>Product 1/Feature 3 (Title)</span></li>
     <li><span>Product 1/Feature 4 (Title)</span></li>
     <li><span>Product 1/Feature 5 (Title)</span></li>
     <li><span>Product 1/Feature 6 (Title)</span></li>
   </ul>
  </dd>
 <dt>Product 2 (Title)</dt>
  <dd><p>Product 2 (description)</p></dd>
  <dd><p>Product 2 (price)</p></dd>
  <dd><p>Product 2 (image)</p></dd>
  <dd>
   <ul>
     <li><span>Product 2/Feature 1 (Title)</span></li>
     <li><span>Product 2/Feature 2 (Title)</span></li>
     <li><span>Product 2/Feature 3 (Title)</span></li>
     <li><span>Product 2/Feature 4 (Title)</span></li>
     <li><span>Product 2/Feature 5 (Title)</span></li>
     <li><span>Product 2/Feature 6 (Title)</span></li>
   </ul>
  </dd>
</dl>
```

另一方面，您可以使用定义列表来使表单清晰，如下所示:

```
<form>
      <dl>
        <dt>First Name:</dt>
          <dd><input type="text" id="firstname" /></dd>
        <dt>Last Name:</dt>
          <dd><input type="text" id="lastname" /></dd>  
        <dt>Email Address:</dt>
          <dd><input type="text" id="email" /></dd>
        <dt>Comments:</dt>
          <dd><textarea rows="5" cols="30" id="comments"></textarea></dd>
      </dl>
      <input type="submit" value="Submit" />
</form>
```

您应该知道,

不能包含任何块级元素。是的，有一些可访问性限制会阻止您使用定义列表作为表和其他各种可能需要标签和标题的数据绑定函数的完全替代。当考虑视觉美感时，问问你自己:不能向一个近乎语义完美的对象添加块级元素是否意味着设计限制，或者这个基本规则是否提供了可以通过少量 CSS 克服的功能清晰性？

为了回答这个问题，我们将从这样一个基本结构开始:

```
<dl>
  <dt>News Item 1</dt>
    <dd>Lorem ipsum dolor sit amet, consectetur adipiscing elit. Maecenas vestibulum orci eu purus viverra imperdiet. Cras scelerisque commodo dictum. Mauris sed libero non justo adipiscing tempor tincidunt eget eros. Praesent eu cursus enim. Donec dictum turpis sit amet dui sagittis dapibus.read more ...</dd>
  <dt>News Item 2</dt>
    <dd>Lorem ipsum dolor sit amet, consectetur adipiscing elit. Maecenas vestibulum orci eu purus viverra imperdiet. Cras scelerisque commodo dictum. Mauris sed libero non justo adipiscing tempor tincidunt eget eros. Praesent eu cursus enim. Donec dictum turpis sit amet dui sagittis dapibus.read more ...</dd>
  <dt>News Item 3</dt>
    <dd>Lorem ipsum dolor sit amet, consectetur adipiscing elit. Maecenas vestibulum orci eu purus viverra imperdiet. Cras scelerisque commodo dictum. Mauris sed libero non justo adipiscing tempor tincidunt eget eros. Praesent eu cursus enim. Donec dictum turpis sit amet dui sagittis dapibus.read more ...</dd>
</dl>
```

可以这样增强:

```
<style type="text/css">
  dl.wrapper1 {margin:0;padding:.5em;width:460px;background-color:#fff;font-size:1em;font-family:Georgia, "Times New Roman", Times, serif;}
  dt {margin:0;padding:.5em;font-weight:bold;background-color:#e0e0e0;}
  dd {margin:0;padding:0;padding-top:.5em;padding-bottom:.5em;}
  dd .div1 {padding-left:.5em;border-left:1px solid #333;font-size:.9em;clear:both;}
  dd .div2 {color:#900;font-size:.76em;margin-top:.5em;margin-bottom:.5em;clear:both;}
</style>

<dl class="wrapper1">
  <dt>News Item 1</dt>
    <dd><div class="div1">Lorem ipsum dolor sit amet, consectetur adipiscing elit. Maecenas vestibulum orci eu purus viverra imperdiet. Cras scelerisque commodo dictum. Mauris sed libero non justo adipiscing tempor tincidunt eget eros. Praesent eu cursus enim. Donec dictum turpis sit amet dui sagittis dapibus.</div><div class="div2">read more ...</div></dd>
  <dt>News Item 2</dt>
	<dd><div class="div1">Lorem ipsum dolor sit amet, consectetur adipiscing elit. Maecenas vestibulum orci eu purus viverra imperdiet. Cras scelerisque commodo dictum. Mauris sed libero non justo adipiscing tempor tincidunt eget eros. Praesent eu cursus enim. Donec dictum turpis sit amet dui sagittis dapibus.</div><div class="div2">read more ...</div></dd>
  <dt>News Item 3</dt>
	<dd><div class="div1">Lorem ipsum dolor sit amet, consectetur adipiscing elit. Maecenas vestibulum orci eu purus viverra imperdiet. Cras scelerisque commodo dictum. Mauris sed libero non justo adipiscing tempor tincidunt eget eros. Praesent eu cursus enim. Donec dictum turpis sit amet dui sagittis dapibus.</div><div class="div2">read more ...</div></dd>
</dl>
```

一个简单的条形图怎么样，其中这个(我特意使用内联 CSS 来使它更好，更容易用动态值定制):

```
<style>
  dt {margin:0;padding:.5em;font-weight:bold;background-color:#e0e0e0;}
  .chartwrapper {width:480px;color:#000;margin-top:10px;margin-bottom:10px;font-size:.6em;clear:both;font-family:Arial, Helvetica, sans-serif;}
  .charttitle {font-size:1.2em;font-weight:bold;color:#ccc;clear:both;}
  dl.chart{margin:.6em;margin-left:0em;margin-right:0em;padding:.6em;padding-right:0em;padding-left:0em;width:480px;}
  dl.chart dd{margin:.6em;margin-left:0em;margin-right:0em;padding:.3em;padding-right:0em;padding-left:0em;text-align:right;}
  dl.chart dt{font-weight:bold;margin:.6em;margin-left:0em;margin-right:0em;padding:.3em;padding-right:0em;padding-left:1em;}
</style>

<div class="chartwrapper">
  <div class="charttitle">An simple chart showing browser views</div>
	<dl class="chart">
        <dt>Firefox</dt>
        <dd style="background:#ffcc00;width:47%;"><div style="padding-right:5px;">Sunday<br />47%</div></dd>
        <dd style="background:#ffcc00;width:23%;"><div style="padding-right:5px;">Monday<br />23%</div></dd>
        <dd style="background:#ffcc00;width:45%;"><div style="padding-right:5px;">Tuesday<br />45%</div></dd>
        <dd style="background:#ffcc00;width:23%;"><div style="padding-right:5px;">Wednesday<br />23%</div></dd>
        <dd style="background:#ffcc00;width:25%;"><div style="padding-right:5px;">Thursday<br />25%</div></dd>
        <dd style="background:#ffcc00;width:60%;"><div style="padding-right:5px;">Friday<br />60%</div></dd>
        <dd style="background:#ffcc00;width:10%;"><div style="padding-right:5px;">Saturday<br />10%</div></dd>
        <dt>IE</dt>
        <dd style="background:#ffcc00;width:13%;"><div style="padding-right:5px;">Sunday<br />13%</div></dd>
        <dd style="background:#ffcc00;width:47%;"><div style="padding-right:5px;">Monday<br />47%</div></dd>
        <dd style="background:#ffcc00;width:20%;"><div style="padding-right:5px;">Tuesday<br />20%</div></dd>
        <dd style="background:#ffcc00;width:27%;"><div style="padding-right:5px;">Wednesday<br />27%</div></dd>
        <dd style="background:#ffcc00;width:25%;"><div style="padding-right:5px;">Thursday<br />25%</div></dd>
        <dd style="background:#ffcc00;width:20%;"><div style="padding-right:5px;">Friday<br />20%</div></dd>
        <dd style="background:#ffcc00;width:30%;"><div style="padding-right:5px;">Saturday<br />30%</div></dd>
        <dt>Opera</dt>
        <dd style="background:#ffcc00;width:40%;"><div style="padding-right:5px;">Sunday<br />40%</div></dd>
        <dd style="background:#ffcc00;width:30%;"><div style="padding-right:5px;">Monday<br />30%</div></dd>
        <dd style="background:#ffcc00;width:35%;"><div style="padding-right:5px;">Tuesday<br />35%</div></dd>
        <dd style="background:#ffcc00;width:50%;"><div style="padding-right:5px;">Wednesday<br />50%</div></dd>
        <dd style="background:#ffcc00;width:50%;"><div style="padding-right:5px;">Thursday<br />50%</div></dd>
        <dd style="background:#ffcc00;width:20%;"><div style="padding-right:5px;">Friday<br />20%</div></dd>
        <dd style="background:#ffcc00;width:60%;"><div style="padding-right:5px;">Saturday<br />60%</div></dd>
	</dl>
</div>
```

我使用的例子并不惊人，但我的意图是为您提供一个起点来说明正在讨论的内容。找点乐子，玩玩 CSS，我敢肯定，用不了多久你就会明白我一直想说的话。

## 这都是关于环境的

本文的目的不是提倡大规模移除或替换无序列表或有序列表。但是考虑到经常被误解和未被使用的定义列表确实有一个作用，使它在制作你的网站时成为一个非常有用的工具。

我相信你们中的许多人仍然会争辩说，定义列表的使用只有在为演讲创建术语和定义或对话时才是相关的。从技术上讲，这些理由仍然有效，但它们是自我强加的限制，如果我们认为定义列表(本质上)是一个关系建模工具，我确信这可以有力地支持在更多情况下使用该元素。

## 分享这篇文章