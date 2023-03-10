# 使用专门的插件定制您的 jQuery 移动界面

> 原文：<https://www.sitepoint.com/how-to-change-texts-in-your-jquery-mobile-apps-without-breaking-widgets/>

像 jQuery Mobile 这样的 JavaScript 库可以帮助您快速开发移动应用程序，但是您的结果通常有一种通用的感觉，使您的应用程序类似于许多其他 jQuery 移动应用程序。如果您希望您的移动开发工作与众不同，您可能需要偏离标准的 jQuery 移动库，使用插件或其他形式的定制。

在本文中，我将演示这些专门插件中的一个——au dero Text Changer，这是一个我设计的用于解决开发人员在使用 jQuery Mobile 框架时遇到的一个常见问题的 [jQuery Mobile](https://www.sitepoint.com/whats-new-in-jquery-mobile-1-2-0/ "What's New in jQuery Mobile 1.2.0?") 插件。您可能知道，jQuery Mobile 中的链接被呈现为按钮，但从技术角度来看，它们实际上并不是按钮。因此，按钮小部件不适用于它们。假设您想要更改一个移动布局中的几个元素的文本，包括其中的一些链接。因为链接不是按钮，所以你不能直接改变它们里面的文本并使用方法`button('refresh')`来重画按钮，因为你会得到一个错误。所以，要重新控制你的移动链接标签，你可以依靠 Audero 文本转换器。

正如你马上会看到的，Audero 文本转换器非常简单和轻便。事实上，最小化版本不到 1kb，但它允许您轻松地更改移动页面布局的所有元素的文本，而不会破坏移动框架的增强功能。它还具有非常高的向后兼容性，因为它可以在早至 1.0.1 的 jQuery Mobile 版本上工作。

现在让我们深入研究代码。

### 基础知识

因为我喜欢“以正确的方式”编码，并举例说明最佳实践，所以插件将使用 [jQuery 插件建议的指南](http://docs.jquery.com/Plugins/Authoring)。解释如何构建 jQuery 插件，或者从另一个角度来说，重新排列链接页面的内容，超出了本文的范围，所以我只给你一个简单的概述。我将使用一个<abbr title="Immediately Invoked Function Expression">life</abbr>这样插件就不会与其他使用美元符号作为缩写的库冲突。按照指南的建议，我还将使用名称间距，这样插件被同一页面加载的其他库覆盖的几率会更低。从插件名就能猜到，选择的命名空间是`auderoTextChanger`。在下一个代码片段的第 2 行，我添加了名称空间作为`$.fn`对象的属性。为了确保我不会重载`$.fn`对象，而不是给它添加每一个方法，我会把它们写在一个[对象文字](https://www.sitepoint.com/back-to-basics-javascript-object-syntax/ "Back to Basics: JavaScript Object Syntax")中。这样，您可以通过将方法名作为字符串传递来调用插件的方法。

请注意，插件只需要一个方法，就可以改变布局元素的文本。它唯一的参数是一个表示要写入的文本的字符串。如您所见，所列代码的第 5 行与指南相比略有不同，因为我添加了测试`typeof method === 'string'`。这样，你可以简单地通过传递一个原始字符串来调用插件，而不是将后者包装在一个对象文本中。

```
(function($){
   $.fn.auderoTextChanger = function(method) {
      if (methods[method])
         return methods[method].apply(this, Array.prototype.slice.call(arguments, 1));
      else if (typeof method === 'object' || typeof method === 'string' || !method)
         return methods.init.apply(this, arguments);
      else
         $.error('Method ' +  method + ' does not exist on jQuery.auderoTextChanger');
   };
})(jQuery);
```

### 入门指南

正如我在介绍中指出的，你不必担心给插件的元素类型，因为它会为你管理它们。当 jQuery Mobile 应用其代码增强时，它会向 <abbr title="Document Object Model">DOM</abbr> 添加几个元素，并根据元素的类型，在不同元素的位置添加不同的元素(如`<span>`和`<div>`)(有时作为父元素，有时作为子元素)。这一事实导致许多元素的文本被从它们的原始位置移走或者被复制到其他元素中。例如，如果增强标签是一个`<button>`，jQuery Mobile 将它包装在一个`<div>`中。此外，它还添加了一个子元素`<span>`，该子元素还有另一个`<span>`，它实际上包含了所需的文本。我知道这可能有点复杂，但这就是框架的工作方式。因此，基于元素的类型，我们的插件必须搜索正确的元素来替换文本。看看下面的代码。

```
var methods = {
   init: function(text) {
      return this.each(function() {
         if ($(this).is('a'))
            $(this).find('.ui-btn-text').attr('title', text).text(text);
         else if ($(this).is('button, input[type="submit"], input[type="reset"]'))
            $(this).closest('.ui-btn').find('.ui-btn-text').text(text);
         else
            $(this).text(text);
      });
   }
};
```

我们来解释一下上面的函数。如果元素是一个链接，插件会改变它的 title 属性和带有`class="ui-btn-text"`的内部元素文本。这是 jQuery Mobile 放置链接显示文本的元素。如果元素是 true button 或 submit 或 reset 类型的输入，则文本不是这些元素的子元素，而是子元素中的子元素。

### 如何使用插件

使用这个插件非常简单。只需对想要修改的元素调用`auderoTextChanger()`方法，改变它们的显示文本。你不需要担心元素的类型，插件会为你管理这个过程。假设您有以下代码:

```
<div id="box">
   <a href="#" id="info-button" data-icon="info">Info</a>
   <button id="demo-button">Button</button>
   <input id="reset-button" type="reset" value="Reset" />
   <input id="submit-button" type="submit" value="Submit" />
</div>
```

对插件的一个基本调用是:

```
<script>
   $(document).on(
      'pageinit',
      function () {
         $('#info-button').auderoTextChanger('About...');
         $('#demo-button').auderoTextChanger('A Private Text');
         $('#box input').auderoTextChanger('A New Text!');
      }
   );
</script>
```

### 结论

正如您在本文中看到的，在不破坏 jQuery Mobile 框架的情况下保持对界面内容的控制的问题可以通过几行有用的 jQuery Mobile 插件代码轻松解决。请随意在你的项目中使用 Audero Text Changer，因为我发布了它的双重许可，分别是麻省理工学院和 T2 的 GPL-3.0 和 T3 许可。你可以[通过](https://github.com/AurelioDeRosa/Audero-Text-Changer "Repository Audero Text Changer")[我的库](https://github.com/AurelioDeRosa/ "Aurelio De Rosa's repository")下载插件来研究整个代码或者下载缩小版。

## 分享这篇文章