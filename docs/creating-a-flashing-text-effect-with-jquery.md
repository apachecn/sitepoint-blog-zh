# 用 jQuery 创建闪烁的文本效果

> 原文：<https://www.sitepoint.com/creating-a-flashing-text-effect-with-jquery/>

有时你需要一个好的效果来提升你的网站的外观和感觉，并抓住你的访问者的注意力。有许多免费的和现成的插件可以提高你的网站的风格。本教程将指导您开发一个跨浏览器的 jQuery 插件，它可以在给定的框中创建随机放置、随机大小的闪烁文本。它还会考虑到残疾问题(嘿，我们说的是闪烁文本)，提供一个`stop()`方法来停止这种效果。该插件将允许在同一页面上的几个动画，每个动画是完全独立于其他动画。最终结果将是一个 JavaScript 文件，您可以轻松地将它包含在您的页面中。

为了创建插件，我将遵循 [jQuery 插件建议指南](http://docs.jquery.com/Plugins/Authoring)，因此它将使用 jQuery 插件最佳实践来开发。在我的文章[将跨浏览器上下文菜单实现为 jQuery 插件](https://www.sitepoint.com/implementing-a-cross-browser-context-menu-as-a-jquery-plugin/ "Implementing a Cross-Browser Context Menu as a jQuery Plugin")中，我已经对这个主题做了概述。还要注意，从现在开始，我将把这个插件称为“Audero 闪烁文本”。

## 入门指南

现在我已经向您展示了“Audero 闪烁文本”的起点，您需要了解组成它的方法。不难想象，你需要一个来初始化插件，一个来启动效果。如果没有设置特定的值，初始化函数将利用一些默认值。此外，如前一节所述，为您的用户提供停止这种效果的能力会很好。最后，但同样重要的是，有一个方法来测试效果是否正在运行可能是有用的。因此，该插件将包含以下内容:

*   设置默认值
*   `init()`方法
*   `start()`方法
*   `stop()`方法
*   `isRunning()`方法

### 默认设置

如果用户没有指定一些默认配置，那么拥有一些默认配置总是有用的。“Audero 闪烁文本”的主要属性是一组文本，用于显示您可以使用数组指定的内容。因为我们将有一个闪烁的文本，文本将执行的操作是:1。慢慢变得可见，2。保持可见一段时间，和 3。慢慢消失。基于此行为，其他有用的设置有淡入、持续时间和淡出时间。我们将添加的最后一个设置是`selection`，它将允许用户选择文本选择的顺序。`selection`的可能值是随机、升序和降序。将所有这些单词翻译成代码的结果如下。

```
var defaultValues = {
  strings: [], // Array of strings to show
  fadeIn: 300, // Time in milliseconds
  duration: 500, // Time in milliseconds
  fadeOut: 300, // Time in milliseconds
  selection: "random" // The order of selection of the text.
                      //Possible values: "random", "ascending", "descending"
};
```

### `init()`法

我们将使用`init()`方法来测试插件是如何被调用的，并设置运行效果的盒子的样式。它只接受一个参数，一个至少包含要显示的字符串数组的对象，但也接受将覆盖默认值的值。或者，可以不带参数调用该函数，在这种情况下，将应用默认值。在这种情况下，要显示的字符串集将使用所选元素的子节点的文本。后一种方式允许你马上开始尝试这个插件。测试结束后，`init()`方法将使用`visibility` CSS 属性隐藏所选元素的子元素，因此盒子的高度不会降低。此时，要做的最后一件事是调用`start()`函数来运行动画。`init()`的代码如下所示。

```
init: function(options)
{
  if (typeof options === "undefined" || options === null) {
    options = {};
  }
  if (typeof options.strings === "undefined" || options.strings == null) {
    if (this.children().size() === 0) {
      $.error("If you don't specify the texts to show, the element must have at least a child");
      return;
    }
    else {
      options.strings = this.children().map(function() {
        return $(this).text();
      });
    }
  }

  this.css("position", "relative");
  this.children().css("visibility", "hidden");

  methods.start($.extend({}, defaultValues, options), null, this.attr("id"));
}
```

### `start()`法

这是插件最重要的部分，因为它保存了实际运行效果的代码。它接受以下三个参数。

*   `settings`–配置对象。
*   `index`–要显示的字符串。
*   `idElem`–应用效果的盒子的 id。

就像`init()`方法一样，从测试参数开始。然后，它创建一个`<span>`元素，该元素将在指定的盒子上浮动。当它被创建时，元素是不可见的(`display: none`)，所以它可以使用渐变方法慢慢出现。稍后您将看到，`fadeOut()`函数有一个回调函数，它将从 DOM 中删除创建的元素，然后根据当前配置使用下一个、上一个或随机字符串再次运行效果。该方法的最后几行设置位置，使元素适合盒子的大小。

```
start: function(settings, index, idElem)
{
  if (typeof idElem === "undefined") {
    idElem = this.selector;
  }
  if (typeof settings === "undefined") {
    $.error("Invalid method call: No settings specified");
    return;
  }
  if (index == null) {
    if (settings.selection === "ascending")
      index = 0;
    else if (settings.selection === "descending")
      index = settings.strings.length - 1;
    else
      index = Math.floor(Math.random() * settings.strings.length);
  }

  var $text = $("&lt;span&gt;")
  .text(settings.strings[index])
  .addClass("audero-flashing-text") // This is used as a bookmark to help the stop method
  .css({
    position: "absolute",
    display: "none",
    fontSize: (Math.random() * 2 + 0.5) + "em"
  })
  .appendTo("#" + idElem)
  .fadeIn(settings.fadeIn)
  .animate({opacity: 1}, settings.duration) // Simulate delay
  .fadeOut(settings.fadeOut, function() {
    // Remove the current element
    $(this).remove();
    var nextIndex;
    if (settings.selection === "ascending")
      nextIndex = (index + 1) % settings.strings.length;
    else if (settings.selection === "descending")
      nextIndex = (index === 0) ? settings.strings.length : index - 1;
    else
      nextIndex = Math.floor(Math.random() * settings.strings.length);
    // Start again the effect
    methods.start(settings, nextIndex, idElem);
  });
  // Set the position so the element will fit the box's size
  var posX = Math.floor(Math.random() * ($("#" + idElem).width() - $text.outerWidth()));
  var posY = Math.floor(Math.random() * ($("#" + idElem).height() - $text.outerHeight()));
  // Set the position of the text
  $text.css({
    left: posX + "px",
    top: posY + "px"
  });
}
```

### `stop()`法

`stop()`方法用于停止动画，删除从 DOM 创建的最后一个`<span>`元素，然后恢复正常的可见性属性。正如您在下面的源代码中看到的，文本被平滑地删除了。该方法首先停止动画(jQuery `stop()`方法)，然后淡出文本，使其慢慢从屏幕上消失(jQuery `fadeOut()`方法)，然后将其从 DOM 中移除(jQuery `remove()`方法)。

```
stop: function()
{
  this.css("position", "inherit");
  // Removes the floating text
  this
  .children("span.audero-flashing-text")
  .stop(true)
  .fadeOut(defaultValues.fadeOut)
  .remove();
  // Restore the default visibility
  this.children().css("visibility", "visible");
}
```

### `isRunning()`法

这个方法非常容易理解，因为它只是测试给定的元素是否运行闪烁效果。测试过程检查类`audero-flashing-text`的`<span>`元素。如果至少找到一个元素，该方法返回`true`，否则返回`false`。下面列出了解释的代码。

```
isRunning: function()
{
  return (this.children("span.audero-flashing-text").size() > 0);
}
```

## 如何使用插件

现在您已经看到了所有的方法，是时候看几个例子了。假设你有下面的`<div>`。

```
<div id="box">
  <p>Lorem>/p>
  <p>Ipsum>/p>
  <p>Dolor</p>
  <p>Sit</p>
  <p>Amet</p>
</div>
```

要使用段落的文本来运行效果，您所要做的就是:

```
$("#box").auderoFlashingText();
```

下面是一个示例，它使用了之前看到的相同标记，但设置不同:

```
$("#box").auderoFlashingText({
  fadeOut: 1500,
  selection: "ascending"
});
```

## 结论

本文向您展示了如何创建一个 jQuery 插件，在给定的框上创建闪烁的文本效果。要了解它是如何工作的，请下载源代码并查看存储库中包含的文档。Audero Flashing 文本插件是完全免费的。你也可以改变它或者进一步改进它，因为它是根据 [MIT](http://www.opensource.org/licenses/MIT) 和 [GPL-3.0](http://opensource.org/licenses/GPL-3.0) 双重授权的

## 分享这篇文章