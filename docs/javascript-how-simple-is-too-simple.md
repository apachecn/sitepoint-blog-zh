# JavaScript:有多简单才算太简单？

> 原文：<https://www.sitepoint.com/javascript-how-simple-is-too-simple/>

以下内容转载自《科技时报》第 167 期。
[![](img/a764fda4790e57af91108ff709fcfacf.png)](https://www.sitepoint.com/blog/) 
**当我们第一次坐下来写[简单的 JavaScript](https://www.sitepoint.com/blog/) 时，Cameron 和我必须决定我们想让事情变得多复杂。一方面，这是一本初学者的书，所以我们希望保持简单(毕竟它被称为“简单的 JavaScript”)。**

另一方面，JavaScript 在现实世界中并不那么简单，我们觉得太多的其他书籍没有给读者提供理解和编写现实世界代码所需的工具。我们不想犯同样的错误，把语言“简化”成一本易读的书。

为了向您展示我的意思，让我们使用不同复杂程度的代码来构建同一个 JavaScript 示例。这个例子很简单:我们将提示用户询问是否应该在新窗口中打开外部站点的链接。然而，正如我们将会看到的，即使是这样一个简单的例子也会变得非常复杂！

## 简单却丑陋

如果代码简单是最终目标，这个例子可能非常简单:

```
<a href="http://example.com/" onclick="
    if (confirm('Open this link in a new window?')) {
      window.open('http://example.com/');
      return false;
    }
">external link</a>
```

让我们暂停一下，让观众中所有的 JavaScript 老手都把眼睛挖出来。

虽然这段代码肯定可以工作，并且在一本初学者的 JavaScript 书中非常容易解释，但这是一个非常混乱的问题解决方案。代码位于 HTML 内容的中间，并且必须为页面上这种类型的每个链接重复。此外，链接的 URL 在 JavaScript 代码中重复出现，这带来了两者不同步的风险。

## 还是太简单了？

我们上面看到的代码片段在 1997 年左右会被认为是尖端的。让我们来看一个更现代的替代方案，它仍然试图让事情变得简单:

```
<a href="http://example.com/" class="ext">external link</a>
<script type="text/javascript">
  var links = document.getElementsByTagName("a");
  for (var i = 0; i < links.length; i++) {
    var link = links[i];
    if (link.className == "ext") {
      link.onclick = clickHandler;
    }
  }

  function clickHandler() {
    if (confirm("Open this link in a new window?")) {
      open(this.href);
      return false;
    }
  }

</script>
```

这个版本不是将 JavaScript 代码直接添加到文档中的每个外部链接，而是使用一段 JavaScript 代码来检测文档中的所有外部链接(带有`class="ext"`的链接)，并为每个链接分配相同的 JavaScript 函数(`clickHandler`)来处理`click`事件。这个函数可以在所有外部链接之间共享，因为它使用`this.href`从被点击的链接中获取要打开的 URL。

过去几年中发布的许多 JavaScript 书籍都对我们在这个脚本版本中所做的改进感到满意。代码足够整洁，并且它的行为与广告宣传的一样。初学者还能要求什么呢？

这种想法的问题是，从这样的例子中学习的初学者走到网络的荒野中，发现用更高级的 JavaScript 编码结构编写的例子，会感到困惑。

## 更好

我们刚刚看到的脚本和 web 专业人员在现实世界中编写的代码之间的主要区别可以归结为两个词:**不引人注目的脚本**。

非介入式脚本是一系列技术的名称，开发人员使用这些技术捆绑他们的 JavaScript 代码，以便它不会干扰同一页面上可能正在运行的其他脚本。例如，我们刚刚看到的脚本会干扰任何其他定义了名为`clickHandler`的函数或者在页面链接上注册了`click`事件处理程序的脚本。

因为 JavaScript 是一种非常灵活的语言，所以有许多巧妙的方法来实现不引人注目的脚本。一些流行的方法大大增加了编写简单脚本的复杂性，对变量和函数的引用会根据它们在代码中出现的位置而发生巨大的变化。

当你写一本简单的 JavaScript 之类的书时，面临的挑战是向初学者展示如何编写不引人注目的脚本，而不会使代码对编程新手来说太难掌握。这是我们想到的:

```
var ExternalLinks = {

  init: function() {
    var links = document.getElementsByTagName("a");
    for (var i = 0; i < links.length; i++) {
      var link = links[i];
      if (link.className == "ext") {
        Core.addEventListener(
          link, "click", ExternalLinks.clickHandler);
      }
    }
  },

  clickHandler: function(event) {
    if (confirm("Open this link in a new window?")) {
      open(this.href);
      Core.preventDefault(event);
    }
  }

};

Core.start(ExternalLinks);
```

整个脚本被打包在一个名为`ExternalLinks`的 JavaScript 对象中，这是脚本中唯一可能与另一个脚本冲突的元素，因此被选择为相当独特的元素。该脚本由一组函数组成，这些函数被定义为该对象的方法。

这种结构使得这些函数在代码中出现的任何地方都可以用相同的名称调用(例如`ExternalLinks.clickHandler`)。

一旦定义了脚本，整个事情就通过调用它的`init`方法开始了，这是由上面代码的最后一行`Core.start(ExternalLinks);`触发的。

事件处理程序冲突的问题由`Core`函数库(`Core.start`、`Core.addEventListener`、`Core.preventDefault`等)解决。)，其内部工作原理在书中适当的地方有解释。

虽然这种方法缺少[更复杂的替代方案](http://yuiblog.com/blog/2007/06/12/module-pattern/)的一些特性，但它足够简单，初学者可以掌握它，并有信心按照相同的模式编写自己的脚本。这也让他们接触到更高级的语言特性，比如对象，所以当他们需要采用一种更高级的编码风格时，他们很有可能理解他们将在网上找到的示例代码。

通过从第一页就使用这种编码结构，JavaScript 避免了教初学者坏习惯。你也不会看到最近其他几本 JavaScript 书的第 100 页附近出现的尴尬的一章，作者解释说，到目前为止，书中呈现的代码风格是有缺陷的，应该避免。

## 一本独特的书

[![](img/deaee27ad7331bd602a5486348bce403.png)](https://www.sitepoint.com/blog/)

如果这篇文章中讨论的 JavaScript 语法的复杂性超出了你的理解范围，我道歉。然而，我真的希望你能考虑拿起一份简单的 JavaScript 的拷贝，这样你就能知道你错过了什么！

对于那些了解 JavaScript 的人，我很想听听你们对我们所采用的语言方法的想法。这一点，加上对主要 JavaScript 库的覆盖和我们古怪的幽默感，是这本书如此独特的原因。

## 分享这篇文章