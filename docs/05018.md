# JavaScript 事件处理中的新兴模式

> 原文：<https://www.sitepoint.com/emerging-patterns-javascript-event-handling/>

在过去的几个月里，网络上关于处理事件的最佳方式的争论愈演愈烈。首先，几个月前，Google 发布了 JsAction 库；然后，最近，`Object.observe()`方法作为 ECMAScript 7 规范的一部分被引入(但[已经支持 Chrome 36 中的](http://kangax.github.io/compat-table/es7/#Object.observe)和 Node.js 中的 [Harmony](http://wiki.ecmascript.org/doku.php?id=harmony:specification_drafts) )。

开发人员已经表明立场，是否仍然“强制”将所有逻辑限制在脚本文件中，或者是否可以接受甚至更喜欢将该逻辑的选定部分内联到 HTML 中。在这篇文章中，我们将试着整理这场争论，讨论错误处理的不同模式，然后权衡这些替代方案的利弊。

## 事实

JsAction 是一个用于 JavaScript 事件委托的 Google 库。它基于 Closure 库，几年前在 Google maps 上引入，以克服一些与事件监听器管理相关的浏览器错误。JsAction 的目标是将事件与处理它们的方法分离，为此，它将部分事件处理逻辑转移到 HTML 中。

最近出现了一种普遍趋势，不仅将部分逻辑转移到 HTML 文件，而且转移到受该逻辑影响的 DOM 元素中。这不仅仅适用于事件处理:许多基于模板的框架(如 Angular、Ractive、React)正在出现；它们在 web 应用程序中加强了模型-视图-控制器模式，并允许数据绑定和反应式编程。

下一个 ECMAScript 规范中引入的`Object.observe()`方法是朝着这个方向迈出的又一步，因为它允许开发人员将发布者/订阅者模式原生应用于一系列全新的情况，而不仅仅是事件处理。声明式框架已经基于这种逻辑，但是`Object.observe()`的引入将帮助它们在性能上获得惊人的提升。

## 故事到此为止

自从 JavaScript 引入以来，处理事件的传统方式已经改变了几次。最初，如果想给页面上的元素添加动态行为，只有一种方法:给标签本身添加一个属性，并将一段 JavaScript 代码与之相关联。您可以在属性值内部编写代码，或者调用一个或多个先前在全局范围内定义的函数。

例如，要使用按钮将页面背景更改为蓝色，请单击:

```
<button onclick="document.bgColor='lightblue'">Feel Blue</button>
```

没过多久就发现了 HTML `on[event]`属性的局限性和危害性。截至 2000 年 11 月， [addEventListener](https://www.w3.org/TR/DOM-Level-2-Events/ecma-script-binding.html) 方法被添加到 ECMAScript 3 规范中，作为将处理程序绑定到浏览器事件的替代方法。此前，微软已经添加了`attachEvent()`方法，但它花了一段时间才流行起来。虽然单词在 21 世纪初就在网上传播，但直到大约 4 年后，不引人注目的 JavaScript[](http://en.wikipedia.org/wiki/Unobtrusive_JavaScript)*这个术语才被创造出来。*

 *内联事件处理程序的 Netscape 方法确实有一些缺点，而事件侦听器方法解决了这些缺点:

*   混合使用代码和标记会降低代码的可读性和可维护性。

*   全局范围污染:内联代码在全局范围内定义，其中调用的每个函数*也必须*在全局范围内定义。

*   这是 XSS 注入的一个弱点:属性可以包含任何代码，这些代码将在没有任何控制的情况下被提供给“evil”`eval`函数。

2006 年，第一批广泛使用的 Ajax 库 YUI 和 jQuery 的引入，推动了这种新方法的发展，超出了任何预期，它们强制实施了良好的实践，使之成为开发人员最方便的选择。

他们还增加了事件监听器方法:

*   可伸缩性:将一个事件处理程序封装到一个函数中是 DRY 兼容的，因为它允许“原型化”并将相同的逻辑重新分配给多个处理程序；jQuery CSS 选择器增加了一种简单有效的方法，可以通过编程将事件处理程序附加到一组节点上:

```
$(document).ready(function () {
  $('.clickable').click(function () {
    document.body.style.background='lightblue';
    return false;
  });
});
```

*   调试:有了像 [FireBug](http://getfirebug.com/) 和 [Chrome Developer Tools](https://developer.chrome.com/devtools) 这样的浏览器内工具，调试 JavaScript 不再是一场噩梦，但是内嵌代码会让这一切受挫。

## `addEventListener`模式的问题

但是，事件侦听器方法引起了一些严重的问题:

*   如果处理不当，在 JavaScript 中将侦听器附加到对象上会导致闭包泄漏。闭包是 JavaScript 最强大的语言特性之一，但是当它们与 DOM 元素交织在一起时必须小心使用。闭包保持指向其封闭范围的指针。因此，将闭包附加到 DOM 元素可能会创建循环引用，从而导致内存泄漏。这个[例子](https://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml?showone=Closures#Closures)来自[谷歌的 JavaScript 风格指南](https://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml)展示了正确和错误的处理方式。

*   Internet Explorer 对垃圾收集的处理存在相当大的问题，尤其是在涉及事件时。除了众所周知的[相互循环引用](http://msdn.microsoft.com/en-us/library/bb250448(v=vs.85).aspx)问题之外，在旧版本的微软浏览器中，当一个节点从 DOM 中移除时，它的处理程序没有被垃圾收集，这导致了内存泄漏。

## JsAction 又是什么？

这将我们引向 JsAction。正如本文开头提到的，它是一个事件委托库，允许通过名称在事件和处理程序之间进行映射，使用一个名为`jsaction`的自定义 HTML 属性，该属性将由该库直接处理。

每个事件处理程序分别注册在一个或多个 JavaScript 文件或内联脚本中；它们与方法名相关联，由于名称和函数之间的映射由库本身负责，所以没有必要将它们添加到全局作用域中。

综上所述，JsAction 应该提供几个优点:
1。解决一些(旧的)浏览器中的内存泄漏问题；
2。减少或避免全球范围的污染；
3。减少事件和处理程序实现之间的耦合；
4。更好的性能和可伸缩性，因为它允许每页设置一个事件侦听器，然后将事件路由到适当的处理程序；

要查看它如何工作的示例，请查看 [JsAction 的 GitHub 页面](https://github.com/google/jsaction)。

说实话，示例代码并不容易阅读，也不像您预期的那么简单。此外，上面的大多数属性都可以通过几行 JavaScript 获得。例如，可以使用[模块](http://www.adequatelygood.com/JavaScript-Module-Pattern-In-Depth.html)和[名称空间](http://addyosmani.com/blog/essential-js-namespacing/)模式来限制全局范围的污染。通过最初将存根分配给事件处理程序，然后用真正的处理程序异步加载外部脚本，并在完成时重新映射事件，可以轻松实现后期加载。

实现第 3 点和第 4 点有点复杂:我们需要为整个页面设置一个处理程序，在 DOM 元素中设置一个属性，说明哪个方法将用作处理程序，并创建一个“超级处理程序”方法，将工作流路由到适当的方法。

同样，它可能是也可能不是满足您需求的正确解决方案，这取决于您项目的特点。尽管它有许多优点，但仍有一些缺点:

*   这个库并不是轻量级的。

*   用起来看起来不是特别直观，对于新手来说学习曲线大概会比较陡。文档很少，这没有帮助。

*   可能很难上手。如果没有可用的编译版本，您将被迫下载闭包编译器和闭包库。

## 声明性框架

因此，JsAction 可能不是 JavaScript 中事件处理的最终解决方案，正如我们所看到的，它已经存在了一段时间，尽管不是一个开源项目。然而，在它被开源后，一场激烈的争论在网络上开始了，在爱好者和批评家之间。除了对互联网一代与生俱来的热爱之外，我认为一个主要原因可能是这样一个事实，即越来越受欢迎的声明式框架在很大程度上分享了相同的设计选择，表现和逻辑之间的集成程度更高，不仅为事件处理程序，甚至为用内容填充页面元素回归内联代码。
等一下，混合逻辑和表达不好吗？嗯，确实是！我们提到了将逻辑从表示中分离出来的一些优点，最重要的是易于调试和清晰。但是，有时，指定连接到对象本身旁边的对象的逻辑可以提高可维护性。

像 [RactiveJs](http://www.ractivejs.org/) 、 [Angular](https://angularjs.org/) 、 [Ember](http://emberjs.com/) 和 [React](http://facebook.github.io/react/) 这样的框架不仅仅是为了让你在视图中注入代码。它们大量使用基于模板的表示模型，允许您将事件处理程序、数据甚至表示逻辑直接绑定到 DOM 元素中，然后在单独的脚本中指定这些逻辑的细节。基本上，JsAction 使用相同的模式来分离事件处理程序名称和处理程序实现。总而言之，它们通过在更高的程度上加强 MVC 模式的应用来增加表示和逻辑之间的分离，同时它们允许非常方便地使用模板。

这些框架控制的不仅仅是事件处理。它们还允许数据绑定，当您关心模型-视图-控制器分离时，这开始变得重要。它们允许您将视图的一部分绑定到 JavaScript 对象，每次修改后面的对象时都会更新视图。此外，它们以特别有效的方式更新视图，只修改受更改影响的最小 DOM 节点，限制页面重绘，因为这将是大多数 web 应用程序的瓶颈。

为此，Ractive 和 React 使用虚拟 DOM——DOM 的抽象表示，通过最大限度地减少需要进行的 DOM 操作，允许非常快速的操作。它们彼此非常相似，都专注于反应式编程和可视化。虽然 Angular 不仅仅关注 MVC 的视图部分，但它是一个更复杂的框架，同时处理路由、到服务器的连接等。

所有这些框架都支持[双向绑定](https://docs.angularjs.org/guide/databinding)，这是一种增强 DOM 中的值和应用程序逻辑中的状态之间一致性的便捷方式。例如，假设您需要在页面中显示一个项目列表。假设您想要使用传统的命令式范例。然后，您需要做这样的事情:

```
<!doctype html>
<html>
  <body>
    <div id="container" class="container" >
    </div>
    <script type="text/javascript" src="..."></script>
  </body>
</html>
```

```
//...

function createItemHTML (val) {
  return '<span class="">' + val + '</span>';
}

function displayList (container, items) {
  container.empty();
  $.each(items, function (index, val) {
    var element = $('<div>');
    element.attr('id', 'div_' + index);
    element.html(createItemHTML(val));
    container.append(element);
  });
}

function editItem (container, itemId, itemValue) {
  var element = container.find('#' + itemId);
  if (element) {
    element.html(createItemHTML(itemValue));
  }
}
//...
displayList($('#container'), items);
//...
editItem(container, id, newVal);
```

上面的代码利用了一些好的模式来避免重复，但是你仍然可以看到我们混合了逻辑和表示，正好相反。

现在，让我们看看在 Ractive 中如何做同样的事情:

```
<!doctype html>
<html>
  <body>
    <div id="container" class="container" >
    </div>
    <script src="http://cdn.ractivejs.org/latest/ractive.js"></script>
    <script src="logic.js"></script>
    <script id='listTemplate' type='text/ractive'>
      {#items:num}
        <div id="div_{{num}}" on-click="itemClick">
          <span>{{this}}</span>
        </div>
      {/items}
    </script>
  </body>
</html>
```

```
var ractive = new Ractive({
  el: 'container',
  template: '#listTemplate',
  data: {
    'items': items
  }
});

ractive.on({
    'itemClick': function (e) {
      //access e.node and e.context for both the DOM element 
      //  and the Ractive state associated with it
    }
});

//...

//Now update items with a new list
ractive.set('items', newItemsList);
```

就是这样！无需编写代码来更新您的页面。Ractive 会帮你搞定的。它更清晰，更易维护，设计更好，性能更高。我们甚至能够以可伸缩的方式向我们的项目添加事件处理程序。

## `Object.observe()`

`Object.observe()`是对未来的一个展望，因为它甚至还没有成为 ES6 规范，它刚刚被添加到 ES7 中。然而，谷歌已经在 Chrome 36 中实现了它，并且 [Observe-JS](https://github.com/Polymer/observe-js) 聚合物库将在每个浏览器中模拟对它的支持，在可用时利用原生支持。

此方法允许您异步观察对象和数组的变化。观察者将接收到按时间顺序排列的变化记录序列，这些记录描述了在一组被观察对象中发生的一组变化。有了`Object.observe()`，以事件为中心的编程，也称为反应式编程，不再局限于用户界面。例如，您可以使用语言原语实现双向数据绑定——不需要专门为此安装像 Ractive 这样的框架。

### 声明性框架中的数据绑定

提供数据绑定的一个解决方案是*脏检查*(Angular 使用)。任何时候数据可能已经改变，库必须使用摘要周期或改变周期去检查它是否确实改变了。Angular 的摘要循环识别所有注册观看的表情，并检查是否有任何变化。

Ember、Backbone 和 Ractive 使用的另一种解决方案是使用容器对象。框架创建保存数据的对象。这些对象有访问数据的方法，所以每当你设置或获取任何属性时，框架可以捕捉你的动作并在内部传播给所有的订阅者。这种解决方案工作得很好，与脏检查相比，它的性能相对较高，具有良好的算法行为，与更改的内容数量成比例。

### 性能改进

添加到语言中的新方法允许我们观察一个对象，改变属性，并查看发生变化的报告。如果您想观察一个普通的 JavaScript 对象，就像这样简单:

```
// A model can be an object literal
var plainObject = {
  name: 'Counter',
  total: 0
};

// Define an observer method
function observer(changes){
  changes.forEach(function(change, i){
    console.log('what property changed? ' + change.name);
    console.log('how did it change? ' + change.type);
    console.log('whats the current value? ' + change.object[change.name]);
    console.log(change); // all changes
  });
}

// Start watching the object
Object.observe(plainObject, observer);
```

在某些时候，你可能决定不再需要观察那个物体:

```
Object.unobserve(plainObject, observer);
```

### 路标

如上所述，对`Object.observe()`的原生支持只添加到了 Chrome 36 和 nodejs *Harmony* (用`--harmony`标志启用)。据报道，Opera 还将在下一个版本中发布对本地数据绑定的支持。与此同时，在等待其他浏览器赶上来的时候，你可以使用[Observe-JS](https://github.com/Polymer/observe-js)Polymer library，以保证你的应用程序即使在旧版本的浏览器上也能工作。

正如你可以想象的那样，甚至声明式框架也普遍认为这是一个机会:Ember 和 Ractive 正计划在下一个版本中尽快发布对`Object.observe()`的全面支持；在 Angular，他们有一个更“长期”的方法，所以他们正在努力将其添加到框架的第 2 版中。

## 结论

我们花了很长时间回顾了一些设计选择的利弊，并对 web 开发的未来进行了一瞥。希望在通读整篇文章后，你现在至少知道了各种可以帮助你处理事件处理和数据绑定的解决方案和模式。当你面临下一个设计挑战时，请记住，没有一个单一的正确解决方案可以解决所有问题。

## 参考资料和进一步阅读

1.  JavaScript 上的 Crockford 第四集:Ajax 的变形
2.  [谷歌 JavaScript 风格指南](https://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml?showone=Closures#Closures)
3.  [Javascript 闭包](http://jibbering.com/faq/notes/closures/)
4.  [Github 上的 JsAction Repo](https://github.com/google/jsaction)
5.  [活动和角度的区别](http://blog.ractivejs.org/posts/whats-the-difference-between-angular-and-ractive/)
6.  [反应和反应的区别](http://blog.ractivejs.org/posts/whats-the-difference-between-react-and-ractive/)
7.  [ember . js 中的容器&依赖](http://www.slideshare.net/mixonic/containers-di)
8.  [数据绑定革命与 Object.observe()，作者 Addy Osmani](http://www.html5rocks.com/en/tutorials/es7/observe/)

## 分享这篇文章*