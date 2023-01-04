# 将跨浏览器上下文菜单实现为 jQuery 插件

> 原文：<https://www.sitepoint.com/implementing-a-cross-browser-context-menu-as-a-jquery-plugin/>

通常你需要在页面上的某个地方有一个自定义的上下文菜单。很有可能你已经在你的网站上使用 jQuery 了，因为有一半的网站都在使用 jQuery。本教程将向你展示如何实现你自己的 jQuery 插件来创建跨浏览器的上下文菜单。最终结果将由一个 JavaScript 文件和一个 CSS 文件组成，它们可以很容易地包含在您的页面中。

为了促进良好的实践，插件将使用 [jQuery 插件建议指南](http://docs.jquery.com/Plugins/Authoring "jQuery plugin suggested guidelines")作为起点。如果你需要一些额外的技巧，你也可以看看[开发更好的 jQuery 插件的 10 个技巧](https://www.sitepoint.com/10-tips-better-jquery-plugins/ "10 Tips for Developing Better jQuery Plugins")。

## 基础知识

在本教程中，该插件将被称为“Audero 上下文菜单”这个名字是任意的，所以你想怎么叫都可以。JavaScript 文件的起点取自 jQuery 指南页面。总而言之，我们将使用一个<abbr title="Immediately Invoked Function Expression">生命</abbr>来确保插件不会与其他使用美元符号的库冲突，比如[原型](http://prototypejs.org/ "Prototype framework official website")。我们还将使用命名空间来确保插件被同一页面上的其他代码覆盖的几率非常低。选择的名称空间是`auderoContextMenu`。在下面代码片段的第 2 行，我们添加名称空间作为`$.fn`对象的属性。我们不会将每个方法都添加到`$.fn`对象中，而是按照指南的建议，将它们放在一个对象文字中。然后，插件的方法可以通过以字符串形式传递方法名来调用。

```
(function($) {
  $.fn.auderoContextMenu = function(method) {
    if (methods[method])
      return methods[method].apply(this, Array.prototype.slice.call(arguments, 1));
    else if (typeof method === 'object' || typeof method === 'string' || ! method)
      return methods.init.apply(this, arguments);
    else
      $.error('Method ' + method + ' does not exist on jQuery.auderoContextMenu');
  };
})(jQuery);
```

如您所见，`else if`条件与指导方针略有不同。我们添加了一个测试来检查`method`参数是否是一个字符串。这允许用户通过简单地传递一个参数来使用插件，这个参数应该是自定义上下文菜单的`id`。这意味着我们正在构建的上下文菜单，你将看到它只是一个列表，将成为 <abbr title="Document Object Model">DOM</abbr> 的一部分。该插件通过替换右键事件的默认行为来工作，但是正如您稍后将看到的，覆盖左键也很容易。

## 入门指南

到目前为止，我们已经有了接受方法调用的代码，以及一些参数。接下来的问题是，我们需要什么方法？如果你想到浏览器的上下文菜单，很明显我们需要显示和隐藏菜单的方法。我们还需要一个方法来初始化上下文菜单和一些默认设置。所以，重申一下，我们需要以下组件。

1.  `init()`方法
2.  `show()`方法
3.  `hide()`方法
4.  设置默认值

因为我们太酷了，我们正在构建的插件将允许在同一个页面上有几个上下文菜单。每个元素都将完全独立于其他元素。支持多个上下文菜单需要修改我们的代码。所以，让我们来看看插件是如何变化的。

```
(function($) {
  // default values used for the menu
  var defaultValues = {'idMenu': null, 'posX': null, 'posY': null};
  // settings for all the elements and menu specified by the user
  var elementsSettings = {};
  var methods = { /* here we'll write the init, show and hide methods */ }
  $.fn.auderoContextMenu = function(method) {
    // Here is the code shown previously
  };
})(jQuery);
```

现在是时候看看上面提到的`init()`、`show()`和`hide()`方法的细节了。

## `init()`法

如您所料，该方法初始化上下文菜单的设置，并覆盖右键单击事件的默认行为。它还定义了单击的元素、选择的上下文菜单及其显示位置。`init()`方法接受一个参数，可以是对象或字符串。如果提供了一个对象，它应该包含菜单的`id`和定位它的坐标。如果用户提供一个对象，它将使用 [jQuery extend()方法](http://api.jquery.com/jQuery.extend/ "Official documentation of the jQuery extend method")与默认设置合并。如果提供了一个字符串，它将作为菜单的`id`来显示。

```
this.on('contextmenu auderoContextMenu', function(event) {
  event.preventDefault();
  event.stopPropagation();
  var params = $.extend({}, elementsSettings[id]);
  if (elementsSettings[id].posX == null || elementsSettings[id].posY == null) {
    params.posX = event.pageX; params.posY = event.pageY;
  }
  methods.show(params, event, id);
});
```

显然，这种方法最重要的部分是替换默认的上下文菜单。为了附加自定义菜单，我们需要使用 [jQuery `on()`方法](http://api.jquery.com/on/ "jQuery on method")来监听`contextmenu`事件。`on()`将回调函数作为其第二个参数。回调函数阻止显示浏览器本机上下文菜单的默认行为。接下来，我们测试菜单是必须显示在一个固定的位置还是在点击坐标上。函数的最后一部分调用我们插件的`show()`方法(不是 jQuery 方法)。

## `show()`法

`show()`方法在适当的位置显示菜单。该方法从隐藏将要显示的菜单开始。之所以这样做，是因为由于之前调用了方法，它可能已经可见。使用 [jQuery hide()方法](http://api.jquery.com/hide/ "Official documention of jQuery hide method")可以隐藏菜单，但是由于我们的插件定义了一个`hide()`方法，我们将使用如下所示的方法。

```
methods.hide(idMenu);
```

下一步是使用用户提供的坐标，或者使用单击事件发生时的鼠标坐标。完成这项工作的代码如下所示。

```
if (typeof params !== 'object' || params.posX == undefined || params.posY == undefined) {
  if (event == undefined) {
    params = {'idMenu': params, 'posX': 0, 'posY': 0}
  } else {
    params = {'idMenu': params, 'posX': event.pageX, 'posY': event.pageY}
  }
}
```

实际显示菜单的代码非常简单。我们使用 jQuery 通过它的`id`获得菜单，然后从左上角开始设置位置(以像素为单位)。最后，使用 jQuery `show()`方法显示菜单。多亏了 jQuery 链接，这些步骤只用一条语句就完成了，如下所示。我们神奇的菜单现在神奇地出现了。

```
$('#' + idMenu)
.css('top', params.posY + 'px')
.css('left', params.posX + 'px')
.show();
```

## `hide()`法

方法将被用来隐藏一个菜单。因为我们的插件允许多个上下文菜单同时可见，所以有机会一次隐藏所有菜单会很方便。我们的`hide()`方法接受一个可选参数，表示要隐藏的菜单。如果指定，参数可以是字符串或字符串数组。如果参数是`null`或`undefined`，那么`elementsSettings`中的所有菜单将被递归隐藏。

```
hide: function(id) {
  if (id === undefined || id === null) {
    for(var Key in elementsSettings)
      methods.hide(elementsSettings[Key].idMenu);
  } else if ($.isArray(id)) {
    for(i = 0; i < id.length; i++)
      methods.hide(id[i]);
  } else
      $('#' + id).hide();
}
```

## 增加一些风格！

我们希望我们的自定义上下文菜单尽可能像本地上下文菜单一样工作。为此，我们需要一些 CSS。我们想要隐藏包含菜单的列表，只在需要的时候显示它。此外，我们需要使用绝对定位来移动页面上的元素。最后一个相关的选择是使用边框来分隔菜单的不同条目。所有这些选择将产生下面的 CSS 代码。

```
ul.audero-context-menu {
  position: absolute;
  display: none;
  background-color: menu;
  list-style-type: none !important;
  margin: 0px !important;
  padding: 0px !important;
}
ul.audero-context-menu * {
  color: menutext;
}
ul.audero-context-menu > li {
  border: 1px solid black;
  margin: 0px !important;
  padding: 2px 5px !important;
}
ul.audero-context-menu > li:hover {
  background-color: activecaption;
}
ul.audero-context-menu > li a {
  display: block;
}
```

## 使用插件

我们的插件非常容易使用。事实上，它的基本用法只包含一行代码。例如，假设我们有下面这段 HTML。

```
<ul id="context-menu" class="audero-context-menu">
  <li><a href="http://www.sitepoint.com">SitePoint</a></li>
  <li><a href="http://ug.audero.it">Audero user group</a></li>
</ul>
<div id="area">Right click here to show the custom menu.</div>
```

当右键单击`area`时，为了允许插件显示自定义的上下文菜单`context-menu`，您应该编写以下代码。

```
$(document).ready (function() {
  $('#area').auderoContextMenu('context-menu');
});
```

如果您也想在左键单击时显示自定义菜单，只需添加以下代码。

```
$('#area').click (function(event) {
  $(this).auderoContextMenu('show', 'context-menu', event);
});
```

## 结论

本教程展示了如何创建一个 jQuery 插件来创建自定义的上下文菜单。要了解它是如何工作的，请看一下[在线演示](http://www.audero.it/public/audero-context-menu/doc/examples.html "Audero Context Menu demo")或下载[源代码](https://bitbucket.org/AurelioDeRosa/auderocontextmenu "Audero Context Menu source code")。如果您需要更多的例子或方法的详细说明，请参考[官方文档](http://www.audero.it/public/audero-context-menu/doc/doc.html "Audero Context Menu official documentation")。Audero 上下文菜单是完全免费的，由 3.0 许可证在 [CC 下发布。](http://creativecommons.org/licenses/by/3.0/ "CC BY 3.0 license")

## 分享这篇文章