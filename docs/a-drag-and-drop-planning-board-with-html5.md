# 带有 HTML5 的拖放式计划板

> 原文：<https://www.sitepoint.com/a-drag-and-drop-planning-board-with-html5/>

拖放 API 是一种新的 JavaScript APIs，它允许我们为站点添加动态效果。拖放有两种风格:

1.将元素拖动到其他元素中，如在[拖放 W3C 规范](http://dev.w3.org/html5/spec-author-view/dnd.html)中所定义的。

2.结合[文件 API](https://www.w3.org/TR/FileAPI/) ，将文件从您的计算机拖动到网页中。

在本文中，我们将关注前者。要了解关于后者的更多信息，可以在 [Mozilla 开发者网络](https://developer.mozilla.org/en/Using_files_from_web_applications)找到一个优秀的指南。

我们将构建一个简单的敏捷计划板。我们将有三个类别:**待做**、**进行中**和**已完成**。我们可以在黑板上移动项目来标记他们的进度。

**note:** Planning Boards

计划板或任务板的概念是 Scrum 敏捷软件开发方法论的一部分。要了解更多信息，请参见:[http://en . Wikipedia . org/wiki/Scrum _(development)](http://en.wikipedia.org/wiki/Scrum_(development))

为了让你对我们将要建造的东西有个概念，这是成品的照片。你可以在 github 上看到完成的代码[:](https://github.com/alexisgo/DragNDrop_PlanningBoard)

![Drag and Drop Planning Board](img/d54e00ea2a57a0f0f6cc5f13a3e1a21c.png)

让我们开始吧，从 HTML 开始！

```
<div id="board">
  <div id="todo">
    <div id="item1">
    <div id="item2">
  </div>
  <div id="inprogress"></div>
  <div id="done"></div>
</div>
```

我们的第一个 div，div id="board "将充当主容器。董事会将被分成另外三个部门。最后，几个较小的 div 将作为笔记卡保存我们的项目。笔记卡将从“待办事项”部分开始。

下面是我们需要的一套完整的步骤，使我们的便笺可以拖动到公告栏的其他部分:

1.  在我们的 notecard div HTML 元素上设置`draggable`属性。
2.  为便笺上的`dragstart`事件添加一个事件监听器。
3.  在我们公告栏的所有部分为`dragover`事件添加一个 EventListener。
4.  在我们公告栏的所有部分为`drop`事件添加一个 EventListener。

首先，我们将把`draggable`属性添加到我们的两个便笺中。

```
<div id="board">
  <div id="todo">
    <div id="item1" draggable="true">
    <div id="item2" draggable="true">
  </div>
  <div id="inprogress"></div>
  <div id="done"></div>
</div>
```

接下来，我们需要指定将真正实现拖放功能的 JavaScript 代码。

首先，我们将把 dragstart 事件绑定到我们的两个 notecards。我们将通过 jQuery 的 [bind](http://api.jquery.com/bind/) 方法来实现这一点，将我们的两个 notecards 上的 dragstart 事件绑定到匿名函数中的代码:

```
$('#item1, #item2').bind('dragstart', function(event) {
  event.originalEvent.dataTransfer.setData("text/plain", event.target.getAttribute('id'));
});
```

在我们的匿名函数中，我们调用 setData 方法，这是 DataTransfer 对象上的一个方法。DataTransfer 对象是拖放 API 中概述的新对象之一。这个对象允许我们存储和检索关于被拖动对象的数据。

setData 方法有两个参数:

1.  拖动数据项的种类。这描述了我们将存储什么样的数据
2.  我们想要存储的实际数据。这是 unicode 或二进制字符串。

setData 方法将我们想要存储的关于被拖动项的数据添加到 W3C 规范所称的"[拖动数据存储库](http://dev.w3.org/html5/spec-author-view/dnd.html%23drag-data-store)"中。

更仔细地检查我们的匿名函数内部的代码行，我们可以看到我们在 dataTransfer 对象上调用 setData，并指定两个必需的参数:

```
event.originalEvent.dataTransfer.setData("text/plain", event.target.getAttribute('id'));
```

我们将拖动数据项类型设置为“文本/普通”。对于数据本身，我们将存储已经开始拖动的便笺的 id。

默认情况下，拖动的项目显示为原始元素的幻影图像。如果您想将拖动的图像更改为自定义图像，您可以使用 dataTransfer 的 [`setDragImage()`](http://dev.w3.org/html5/spec/dnd.html%23dom-datatransfer-setdragimage) 方法。

接下来，我们需要确保我们可以将通知卡拖动到公告板的其他部分。为此，我们将在 board 部分绑定 dragover 事件。同样，我们将通过 jQuery 的 bind 方法来实现:

```
$('#todo, #inprogress, #done').bind('dragover', function(event) {
  event.preventDefault();
});
```

这一次，匿名函数中的代码要简单得多。但是它在做什么呢？

默认情况下，页面上的所有元素都不会接受拖放的项目。为了覆盖这个默认行为，并使元素能够接受拖动的项目，我们必须阻止默认行为的发生。我们通过调用事件上的`preventDefault()`方法来实现这一点。

对于我们的最后一步，一旦我们的笔记卡被放到公告栏的一个新的部分，我们需要实际做些什么！作为第一步，我们将把 **drop** 事件绑定到公告栏部分，并定义一旦有东西放到公告栏上时该做什么。同样，我们通过 jQuery 的 bind 方法将公告板 div 绑定到一个匿名函数:

```
$('#todo, #inprogress, #done').bind('drop', function(event) {
    // do something!
});
```

我们希望将拖动的 notecard 的整个 div 元素放到新位置。为此，我们首先获取 notecard 元素的 id，该 id 之前存储在 dataTransfer 对象中:

```
$('#todo, #inprogress, #done').bind('drop', function(event) {

  var notecard = event.originalEvent.dataTransfer.getData("text/plain");

});
```

接下来，我们将使用 appendChild 方法向目标添加一个新元素:

```
$('#todo, #inprogress, #done').bind('drop', function(event) {

  var notecard = event.originalEvent.dataTransfer.getData("text/plain");

  event.target.appendChild(document.getElementById(notecard));
});
```

最后，如前所述，我们将禁用不允许任何项目被丢弃的默认行为:

```
$('#todo, #inprogress, #done').bind('drop', function(event) {

  var notecard = event.originalEvent.dataTransfer.getData("text/plain");

  event.target.appendChild(document.getElementById(notecard));

  event.preventDefault();

});
```

现在，我们可以将我们的笔记卡移动到我们想要的任何部分！

![Drag and Drop Planning Board](img/c91e62758caaad84e3049cab7331784d.png)

[**参见演示页面。**](https://www.sitepoint.com/examples/planning-board-with-html5/) 查看源代码完整。

要了解更多关于 HTML5 拖放的知识，请查阅我即将出版的书《真实世界的 HTML5 和 CSS3》。您可能还会发现以下网站很有用:

*   [http://dev.w3.org/html5/spec/dnd.html](http://dev.w3.org/html5/spec/dnd.html)
*   [https://hacks.mozilla.org/2009/07/html5-drag-and-drop/](https://hacks.mozilla.org/2009/07/html5-drag-and-drop/)
*   [https://developer . Mozilla . org/En/Drag drop/Recommended _ Drag _ Types](https://developer.mozilla.org/En/DragDrop/Recommended_Drag_Types)

## 分享这篇文章