# jQuery 1.4.x 的最大亮点:Earle 的精选

> 原文：<https://www.sitepoint.com/jquery-1-4-greatest-hits/>

当 jQuery 1.3 发布时，巴拉克·奥巴马还没有当选美国总统，猪流感还没有流行，迈克尔·杰克逊还活着。那确实使它看起来像很久以前。因此，在这段时间里，jQuery 团队为这个库的 1.4 版本创造了大量新特性和增强功能也就不足为奇了。

现在 jQuery 1.4 已经发布了几个月，我们已经看到了 1.4.1 和 1.4.2 版本，我想介绍一下我最感兴趣的特性。这不是 1.4.x 分支中所有新特性的目录(为此，您应该查看发行说明)；相反，这是我最喜欢的:我已经开始广泛使用的，或者我认为最有前途的。

## 快速有趣的

在我们深入研究之前，让我们先浏览几个有趣的、新的(有些人可能会说是早该有的)API 方法。首先是`delay`动作。jQuery 论坛上肯定有成百上千的问题在问“我如何才能在我的动画链中设置延迟？”答案相当难看——你可以用`animate`来伪装它，或者设置一个 JavaScript 定时器，或者……一些其他可怕的解决方法。但不会再有了！`delay`方法接受一个常规的 jQuery duration 参数(即毫秒数或字符串`'fast'`或`'slow'`中的一个)，并在进入下一个命令之前简单地等待:

```
$('p')  .slideUp()  *.delay(2000)*  .slideDown();
```

太好了。接下来，每个人最喜欢的类弯曲工具`toggleClass`得到了一个整洁的升级:你可以提供多个`class`名称(用空格分隔)来切换。如果`class`已经存在于一个元素中，它将被移除，如果它不存在，它将被添加。这一个可以认真清理你的导航或折叠菜单代码。

最后，还有两个有用的快捷方式可以帮助提高 jQuery 链的可读性:`first`和`last`。这些是 jQuery 方法遍历类别的一部分，除了第一个或最后一个元素之外，它们将过滤掉所有的元素:

```
$('p')  .first()  .addClass('first-paragraph')  .end()  .last()  .addClass('last-paragraph');
```

`first`方法只是`eq(0)`的别名，`last`是`eq(-1)`的别名。呃，那是什么？减一？是的。另一个方便的功能是向`eq`提供一个负数来表示从选择的结尾开始的位置，而不是从开始。

## 改变一些基本面

如果你把`jQuery1.4.2-min.js`文件放到旧项目中，你很可能会发现一切都在继续工作——这可能会让你怀疑没有什么新的东西值得你关注。但是有几个奇妙的技巧可以很好地补充您的 jQuery 工具箱。

我们都知道 jQuery 最好的部分是它能够将动作链接在一起。因此，1.4 中最酷的新增功能之一——快速元素构造——旨在减少从头开始构造 DOM 元素时的链接数量，这有点令人惊讶:

```
$('<div/>', {  id: 'myDiv',  css: {    backgroundColor : 'blue',    padding : '5px'  },  html: '<strong>My Div</strong>',  click: function() {    alert('clicked me!')  }}).appendTo('body');
```

在我们的构造选择器字符串之后的新参数，是一个包含我们想要应用到新元素的细节的对象。它的形式是我们熟悉的 object literal——但就其内容而言，它很特别。我们可以指定想要添加到元素中的任何属性，还可以指定一些将自动应用的 jQuery 属性和命令。

上面，我们添加了 css 和 html 属性，并添加了 click hander:它们都不是`div`元素的属性。jQuery 使用一些智能在内部处理这些问题，并应用正确的结果(如果您有兴趣发现您可以指定的所有内容，请在源代码中搜索`attrFn`的出现)。

您需要注意的第二个重大变化是用函数设置值的能力。这个能力以前就有(比如在`attr`函数里)，现在却无处不在！`css`、`attr`、`val`、`html`、`append`、`wrap`、`addClass` …大多数方法——在有意义的地方——现在将接受一个函数来设置它们的值:

```
$('div').html(function(i) {  return 'This is div number ' + i;}); 
```

如果这还不够，还有第二个因素使 setter 更好:许多 setter 函数还接收项目的当前值——可以在函数内部使用，而不必重新选择项目:

```
$('a').attr('href', function(i, current) {  return current + '?linkid=' + i;}); 
```

在这里，我们以非常简洁优雅的方式将一个查询字符串值附加到页面上每个链接的末尾。

## 新方法

如果没有一些新的命令，一个新的版本就不会很有趣，所以让我们遍历一些重要的 API 添加。

在 jQuery 1.4 之前，选择兄弟的子集通常需要一些小心的链接，或者潜在的令人困惑的`slice`方法。为了帮助解决这个问题，我们现在有了 groovy 的`nextUntil`和`prevUntil`方法，让我们选择从当前选择到某个指定端点的所有兄弟节点。

在下面的例子中，我们将选择带有`id` `first`的元素，然后选择它的每个兄弟元素，直到(但不包括)带有`id` `secondLast`的兄弟元素。我们还包括可选的第二个参数，它进一步过滤选择。最终结果是我们选择了在`#first`和`#secondLast`之间的所有`div`元素的兄弟元素:

```
$('#first')  .nextUntil('#secondLast', 'div')  .css('background-color', 'red');
```

`prevUntil`以完全相同的方式工作，除了它选择出现在当前选择的 之前 *的元素，而不是之后。同一类别中的另一种新方法是`parentsUntil`方法。`parentsUntil`将从选中的元素开始向上*遍历 DOM，直到到达与传入的选择器字符串相匹配的祖先。**

 *坚持使用新的遍历方法，添加了`has`方法来匹配它的选择器过滤器对应物。现有的`:has`过滤器接受一个选择器字符串，并且只返回子元素与该选择器匹配的元素:

```
$('p:has(.warning)').addClass('warning-parent');
```

在 jQuery 1.4 中，我们现在有了一个在 jQuery 链中完成相同任务的方法:

```
$('p').has('.warning').addClass('warning-parent');
```

我们要看的最后一个新方法是非常有用的`unwrap`命令。`wrap`、`unwrap`的旅行伙伴将删除给定元素选择的父元素，只留下元素本身。例如，如果您的页面有一堆包含图像的链接，那么命令`$('a img').unwrap();`将删除周围的链接标签，只留下页面上的图像。*  *## 让 jQuery 更像程序员

jQuery 隐藏了许多执行跨浏览器 DOM 操作所需的极其复杂的 JavaScript，但这样做往往会让人们认为它试图取代 JavaScript。没有什么比这更偏离事实了:当然，您可能会在单独使用 jQuery 添加简单效果时遇到困难——但是在某个阶段，您必须咬紧牙关，让您的 JavaScript 能力达到要求。

jQuery 1.4 引入了一系列可选的约定和高级特性，肯定会让经验丰富的 JavaScript 爱好者喜笑颜开。我们已经看到了一个快速元素构造的例子。类似地，可以用一个对象文字将多个事件处理程序绑定到不同的函数:

```
$('p').bind({  mousemove: function(e) {    $(this).text('moved to:' + e.pageX + ':' + e.pageY);  },  mouseout: function() {    $(this).text('moved away.');  },  click: function() {    $(this).unbind('mousemove mouseout');  }});
```

像快速元素构造一样，这种技术允许您避开对链接的需要。我确信一些 jQuery 用户(像我一样)会喜欢它，而另一些人会讨厌它。

## 活动委托和范围

对`live`功能进行了很好的修改，最终以您期望的方式工作:适用于所有事件。它还补充了一种处理事件委托的新方法:`delegate`。`delegate`是一种将事件处理程序附加到元素的方法，无论是在元素被调用时，还是在将来元素匹配选择器时。

听起来很像`live`，不是吗？是的，但是有几个关键的区别。首先，`delegate`方法附加到您选择的元素上，而`live`将事件冒泡到一个特殊的容器中。通过定义它所绑定的元素，代码可读性更强，效率更高。在下面的例子中，我们将绑定到`#container`元素中的任何当前或未来的`div`元素:

```
$('#container').delegate('div', 'mousemove', function(e) {  $(this).text('Moved to:' + e.pageX + ',' + e.pageY);});
```

`delegate`的另一个优点是，与`live`不同，它绑定到使用 jQuery 遍历方法选择的元素没有问题。

好吧，继续。如果你很难理解 JavaScript 中的作用域概念(或者很难向同事解释)，那么有一些新方法可以帮助你。对于害怕闭包的人(或者那些认为闭包会产生不可读代码的人)来说,`proxy`方法是一个很棒的魔法。现在，您可以将事件处理程序绑定到一个指定的对象，这样处理程序中的`this`将引用该对象，而不是触发事件的元素。在下面的例子中，我们将`controller.update`方法绑定到段落的`click`处理程序。当它被调用时，`this`将是`controller`对象——而不是 DOM 元素:

```
$('p')  .click($.proxy(controller, 'update'))  .mouseout(function() {    // "this" is the paragraph element – as usual    $(this).text('moved away.');  });var controller = {  clicks: 0,  update: function(event) {    // "this" is the controller object - no closure required!   this.clicks++;    $(event.target).text('Number of clicks: ' + this.clicks);  }};
```

如果您喜欢代理，您会喜欢这个:jQuery 1.4 允许我们在 Ajax 请求中指定一个可选参数来设置回调函数的上下文:

```
$.ajax({  url: 'reset.html',  context: controller,  complete: function() {    // "this" is the controller object!    this.clicks = 0;  }});
```

您还可以将上下文设置为 DOM 元素，这样在回调函数中就可以编写类似于`$(this).fadeIn()`的代码，并让它自动应用到正确的元素。

## 但是等等，还有更多

正如我在开始时所说的，1.4 版本比我在这里概述的几个特性要多得多。如果您还没有这样做，请前往[新 API 站点](http://api.jquery.com/)并查看 1.4、1.4.1 和 1.4.2 的发行说明，以确保没有任何您错过的 jQuery 黄金版。

很明显，jQuery 团队和社区去年肯定没有坐以待毙。它们已经成为不可阻挡的编码力量——释放出巨大的性能增益，并增加了许多很酷的新特性。

但是，无论它们有多么光滑和闪亮，如果你让它在 CDN 上积灰，这一切都是徒劳的。是时候在您的下一个项目中删除这个 JavaScript 文件了，看看您能让它做些什么。

## 分享这篇文章*