# 构建低内存 Web 应用程序

> 原文：<https://www.sitepoint.com/building-a-low-memory-web-application/>

随着移动设备和平板电脑的增加，web 应用程序经常在缓慢和低内存的环境中加载。在构建 web 应用程序时，人们可能会考虑做出减少应用程序消耗的内存量的设计选择，以便用户体验保持快速响应。

减少应用程序内存占用的一般想法是减少应用程序中的代码量，从而减少它占用的物理内存量。这包括函数、变量、事件处理程序、内务代码等。

## 技巧 1:使用一个事件侦听器而不是多个事件侦听器

通常执行以下操作将事件侦听器添加到同一组的多个节点。

```
$("#list .countries").on("click", function() {
  $("box").value = this.innerHTML;
});
```

如果您的网站有 100 个节点，那么您将添加 100 次事件侦听器。这意味着这 100 个节点中的每一个都被修改以响应一个`onclick`事件，并且每个修改都以引用指针和状态标志的形式消耗额外的内存。

下次考虑这样做:

```
$("#list").on("click", function() {
  if($(this).hasClass("countries")) $("box").value = this.innerHTML;
});
```

有了这个，您只需要监听一个父节点的`onclick`事件，并以可忽略的执行时间为代价，拯救了 99 个其他节点免于变胖。

## 技巧 2:使用内联事件处理程序而不是外部事件处理程序

```
<div onclick="menus.activate(event);">activate</div>
```

这个提示肯定会让一些人激动起来。人们理所当然地认为应该远离内联事件处理程序，因为它将行为与内容混合在一起。无意冒犯不使用它们的“最佳实践”理由，您会发现内联事件处理程序可以节省大量内存。

首先，您不需要编写事件注册代码(即`addEventListener()/removeEventListener()`)，这至少会节省几行代码。您的应用程序也不需要花时间执行 JavaScript(您的事件注册函数)，这比本地代码(DOM 解析器)慢得多。另一个好处是，您不必担心由于忘记注销处理程序而导致的内存泄漏，因为处理程序会随着节点一起死亡。

您也不需要为节点分配一个 ID 以便在代码中找到它，也不需要遍历 DOM 来找到该节点(这是 jQuery 普及的一种方法)。您只是在那里节省了一些内存，并防止您的应用程序做额外的工作。

而且，因为内联事件处理程序可以保留上下文，所以它允许您通过消除创建闭包来封装上下文的需要来节省内存。您可能没有意识到，每当您用匿名函数包装事件处理程序时，您都在创建闭包:

```
node.addEventListener("click", function(e) {
  menus.activate(e);
}, false);
```

一些库将它隐藏在一个帮助函数后面，但这仍然是同一个问题:

```
node.addEventListener("click", menus.activate.bind(menus), false);
```

问题升级是因为一些库将为每个事件处理程序注册创建一个新的匿名函数，这意味着闭包的数量将随着处理程序注册的数量线性增长，这意味着宝贵的内存被浪费了。使用内联事件处理程序，您不需要创建额外的函数或闭包。

```
<div onclick="menus.activate(event);">activate</div>
```

## 技巧 3:在 DOM 创建中使用模板

如果您创建的不是几个节点，那么通过将一个 HTML 字符串赋给`innerHTML`属性来生成一个 DOM 树比用`document.createElement()`一个一个地创建节点需要更少的代码。而且，您不必担心您的 HTML 内容被限制在您的 JavaScript 代码中，因为您可以将它安全地隐藏在您的网站中，如下所示。

```
<body>
  <script type="text/html" id="mytemplate1">
    <div>hello</div>
  </script>
</body>
```

注意 HTML 内容不会被呈现，因为它被放在一个`<script>`标签中。`<script>`标签还使用了一个`text/html` mimetype，这样浏览器就不会将其与 JavaScript 混淆。您可以用下面的代码检索字符串。

```
var text = document.getElementById("mytemplate1").innerHTML;
```

很多时候，我们不只是想要纯文本，而是想要嵌入了允许动态变量替换的符号的文本。这是模板库的工作，它使得使用模板创建 DOM 更加强大。从长远来看，一个小的模板库应该可以减少 DOM 创建代码的占用。

## 技巧 4:考虑无状态单例而不是有状态对象

应用程序由组件组成，每个组件通常是一个由 JavaScript 对象支持的 DOM 节点，用于存储数据。组件越多，JavaScript 对象就越多。但是，如果可以与多个 DOM 节点共享同一个 JavaScript 对象，那么可以通过重用 JavaScript 对象来节省一些内存。要实现这一点，需要将 JavaScript 对象设计得像单例对象一样。组件没有状态，只用来调用单例来执行一个通用函数。或者，它可以将一些基本数据作为属性存储在 DOM 节点上，singleton 可以从节点中读取数据并对其进行操作。

在更复杂的场景中，DOM 节点可以存储一个惟一的 ID 作为属性，singleton 可以将 ID 映射到存储在其他地方的复杂数据对象。(更复杂的问题最好留待以后的文章来解决。)

这种技术最适合于同时使用大量组件的组件，比如列表项，或者非常简单且无状态的组件，比如按钮。例如:

```
<input type="button" onclick="GenericButton.onclick(event);" data-command="copy">
```

```
GenericButton.onclick = function(e) {
  e = e.target || e.srcElement;
  var cmd = e.getAttribute("data-command");
  if(cmd == "copy") //...
};
```

## 技巧 5:充分利用原型继承

如果你实例化了许多相同类型的对象，选择原型继承而不是属性注入。当您将属性注入到对象中时，您正在将引用复制到每个对象上。这导致引用的数量随着对象的数量线性增长。

```
function A() {
  this.value = 100;  //injecting value into this object
}
var obj1 = new A();
var obj2 = new A();
```

相反，如果您允许这些属性被继承，那么这些引用只在该对象的原型上存在一次。然后，引用的数量不会线性增长，除非以后修改属性值。

```
function A() {
}
A.prototype.value = 100;
var obj1 = new A();
var obj2 = new A();
```

## 技巧 6:使用发布-订阅系统来简化交流

与其使用观察者模式(例如，`addActionListener()`)在组件之间进行通信，不如考虑使用发布-订阅模式在它们之间进行通信。发布-订阅模式使用更少的代码进行通信，并允许您的应用程序更好地解耦，因此不需要太多的代码来维护耦合。网上有许多发布-订阅系统的实现，它们节省了您可以使用的内存。

## 技巧 7:尽可能使用较小的库

最后一点是最明显的。大型库消耗大量内存，小型库消耗较少。有一个专门展示微型图书馆的网站叫做 [microjs](http://microjs.com "Microjs: Fantastic Micro-Frameworks and Micro-Libraries for Fun and Profit!") ，在那里你可以找到一个仅仅满足你需求的图书馆。

## 分享这篇文章