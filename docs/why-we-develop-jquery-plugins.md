# 我们为什么开发 jQuery 插件

> 原文：<https://www.sitepoint.com/why-we-develop-jquery-plugins/>

jQuery 是 web 开发人员和设计人员中最流行的 JavaScript 库之一。并且，每天都有很多效果惊人的新插件被创造出来。开发人员和用户倾向于尽可能选择 jQuery 插件。如果你仍然对 jQuery 犹豫不决，这篇文章将帮助你理解为什么它们如此受欢迎。

插件是向应用程序添加自定义功能的独立模块。启用或禁用插件不会影响核心系统或其他插件。作为开发人员，我们倾向于寻找快速修复，而不是专注于可维护的解决方案。随着经验的积累，我们改变我们的方法来考虑可维护性和未来的增强。要理解为什么插件是如此有吸引力的选择，我们必须首先理解没有它们生活会是什么样子。

## 无插件开发

假设我们的任务是将一个由无序列表组成的菜单转换成一个选择框。这将是我们响应式网页设计工具包中一个非常方便的特性。因此，我们将使用它作为理解我们为什么开发 jQuery 插件的基本示例。让我们看一个基本的 jQuery 实现。

```
<script>
$(document).ready(function(){
var select = $('<select>');
$('#menu li').each(function() {

var link = $(this).find('> a');
var level = $(this).parents('li').length;

var indent = '';
for(i=0;i<level;i++){
indent += '-';
}

var option = $('<option>').text(indent + ' ' + link.text())
.val(link.attr('href'))
.appendTo(select);
});

$('#menu').replaceWith(select);
});
</script>
```

上面的代码片段将无序列表项转换成选择框中的选项。您可以看到无序列表 ID 是硬编码的。如果我们想将另一个列表转换成选择框，我们必须复制现有的代码，这很容易出错。相反，我们将代码转换成一个函数，使其可重用。代码应类似于以下内容:

```
<script>
function convertMenusToSelect(menu){
// Previous code with dynamic item instead of hard coding #menu
}
</script>
```

现在，我们有了一个可以反复重用的通用函数。但是，突然出现了一个新的客户需求，它包含以下两个任务。

*   选定的菜单选项应该用给定的颜色突出显示。
*   用于子菜单的缩进符号应该是动态的。

最直接的解决方案是向 **convertMenusToSelect()** 函数添加另外两个参数来处理颜色和缩进符号。然而，随着更多需求的到来，我们将不得不添加额外的参数，这使得维护变得很困难。相反，我们可以考虑将参数作为对象传递，如下所示。

```
<script>
function convertMenusToSelect(params){

}

var params={menuID: '#menu', color:'#EEE', indentMark : '#' };
convertMenusToSelect(params);
</script>
```

对象中可以有强制参数和可选参数，因此我们需要手动检查这些参数，以便提取正确的值。随着新需求的出现，我们的解决方案变得非常难以维护。因此，让我们来看一个使用 jQuery 插件的解决方案，看看它如何处理不断变化的需求。

## 使用 jQuery 插件开发

我们用普通的 jQuery 代码启动了列表到选择框转换器，并在流程中将它转换为可重用的函数。现在，让我们看一个使用下面代码的 jQuery 插件实现。

```
<script>
(function($){
$.fn.extend({
selectConvert: function(options) {
var defaults = {
color: '#EEE',
indentMark: '-',
}

var options = $.extend(defaults, options);

this.each(function() {
var o = options;
var currentItem = $(this);
var select = $('<select>');

$(currentItem).find('li').each(function() {
var link = $(this).find('> a');
var level = $(this).parents('li').length;
var indent = '';
for(i=0;i<level;i++){
indent += o.indentMark;
}

var option = $('<option>').text(indent + ' ' + link.text())
.val(link.attr('href')).css('background',o.color)
.appendTo(select);
});

$(currentItem).replaceWith(select);
return select;
});
}
});
})(jQuery);
</script>
```

在上面的代码中,`selectConvert`将是我们的插件名，并且将是 jQuery.fn 对象中的一个单独的名称空间。每个插件方法都将被放在`selectConvert()`函数中。插件函数使用一个名为`options`的变量来处理初始化时传递的数据。`defaults`变量包含所有允许的变量及其默认值。我们也可以给`defaults`部分分配功能。

然后，我们使用 jQuery `extend()`函数将`defaults`与传递给插件的选项进行比较。最终输出将包含传递的选项值和默认值，这些值在初始化时没有提供。因此，使用 jQuery 插件添加新参数并不是一件很困难的事情。最后，我们使用提供的选项执行转换代码，并返回转换后的项目。

## jQuery 插件的重要性

由于普通 jQuery 实现中的问题，我们决定实现一个插件。现在，让我们看看为什么我们更喜欢 jQuery 插件而不是纯 JavaScript 实现。

*   **防止函数冲突**我们在唯一的名称空间内创建所有插件相关的函数。所以，插件降低了名称冲突的可能性。
*   **可扩展能力** jQuery 插件可以自动扩展插件中的参数和函数。我们可以在插件中定义默认参数和函数，并在运行时使用扩展值和函数覆盖它们。这减少了处理必需和可选参数的工作。
*   **可链接性**我认为可链接性是 jQuery 插件中最强大的因素。在纯 JavaScript 中，我们必须调用函数，将结果赋给变量，并将它们传递给下一个函数。使用 jQuery，我们可以在一行代码中对相同的返回结果调用另一个函数或插件。考虑下面的代码，它结合了 jQuery 自动完成选择框插件和我们的设计。您可以在同一个结果集上链接任意数量的函数或插件，这样就可以很容易地动态添加和删除功能。

    ```
    <script>
    $(document).ready(function() {
    $('#menu').selectConvert().combobox();
    });
    </script>
    ```

*   **Reusability**jQuery plugins allows us to use the same functionality over multiple elements without duplicating code. We used a single menu in our initialization code as shown below.

    ```
    <script>
    $(document).ready(function() {
    $('#menu').selectConvert();
    });
    </script>
    ```

    一般来说，我们需要用两行来转换两个菜单。但是 jQuery 使得为两个菜单重用相同的初始化代码行成为可能，如下面的代码所示。

    ```
    <script>
    $(document).ready(function() {
    $('#menu,#menu2').selectConvert();
    });
    <script>
    ```

*   封装我们可以在 jQuery 插件中定义公共函数和私有函数。因此，我们可以将逻辑封装在插件中，并决定哪些组件是公开可见的，哪些是插件私有的。因此，插件开发者和用户将只能调用公开可用的特性。
*   **可用性**大多数使用 jQuery 插件的人对 JavaScript 或 jQuery 都不是很有经验。作为开发人员，我们需要尽可能简单地使用插件。jQuery 插件提供了一种通用的初始化技术，您可以在任何兼容的元素上调用插件初始化函数。因此，用户只需复制初始化代码，一切都将正常工作。

## 结论

在本教程中，我们使用了 jQuery 插件的最基本模式之一。还有一些高级模式使得编写高度可维护和可扩展的代码变得更加容易。作为开发人员，您可能想知道为什么我们不能用普通的 JavaScript 做同样的事情。jQuery 是使用 JavaScript 构建的，所以 JavaScript *能够提供 jQuery 提供的任何东西。然而，我们更喜欢使用 jQuery，因为它抽象了许多常见的任务，并允许我们专注于应用程序逻辑。*

是时候分享你对 jQuery 插件和纯 JavaScript 实现的看法了。你更喜欢开发和使用 jQuery 插件吗？让我们知道。也可以从 [JSPro GitHub 页面](https://github.com/jsprodotcom/source/blob/master/Why-We-Develop-jQuery-Plugins.zip "Source Code")下载本文使用的代码。

## 分享这篇文章