# 一个更好的 Cookie: HTML5 和网络存储

> 原文：<https://www.sitepoint.com/a-better-cookie-html5-and-web-storage/>

HTML5 规范中更有趣和有用的新特性之一是 web 存储，它为我们的 Web 应用程序提供了高达 5MB 的持久数据存储。

在本文中，我们将增强一个在早期博客中开始的例子，[一个带有 HTML5](https://www.sitepoint.com/a-drag-and-drop-planning-board-with-html5/) 的拖放计划板。这个例子构建了一个简单的敏捷计划板，有两个便笺，您可以将它们拖放到板的不同部分。在本文中，我们将回顾如何实际存储每张卡片的最后位置，这样当您返回站点时，卡片就在您离开的地方。

你可以在 github 上找到完整的例子[。但是，我鼓励您从初始页面开始，并尝试在整篇文章中与我一起保存便笺。为此，你可以在这里](https://github.com/alexisgo/DragNDrop_PlanningBoard/tree/dnd_storage)找到代码[的预 Web 存储版本，在这里](https://github.com/alexisgo/DragNDrop_PlanningBoard)找到代码[的演示页面。](https://www.sitepoint.com/examples/planning-board-with-html5/)

Web 存储中定义了两种存储:**会话存储**和**本地存储**。

会话存储解决了困扰 cookies 的一个问题:意外购买同一项的两个副本。假设你正在网上购买机票，你打开了多个窗口试图找到最划算的交易。即使你打开了多个窗口，如果网站使用 cookies 来跟踪你的订单，cookies 将在多个窗口中共享。这可能会导致意外购买同一张机票两次。

SessionStorage 为我们解决了这个问题。通过会话存储，我们为每个打开的窗口或标签*提供了单独的数据存储。会话结束后，数据不再保留，因此如果关闭窗口或选项卡，数据会消失。但是，当您购物时，如果站点利用会话存储数据存储，一个窗口中的数据将与另一个窗口中的数据完全分开。*

但是您想保存到磁盘上的值呢？在我们的计划板中，我们希望能够记住我们拖放的便笺的最后位置。为了存储它们最后的位置，我们需要利用第二种网络存储，称为本地存储。

本地存储提供高达 5MB 的持久数据存储。关于本地存储，需要注意的一点是，每个浏览器都有自己的 5MB 存储空间，数据不会在浏览器之间共享。

使用本地存储保存的数据存储为键/值对。键/值对是一种存储数据的常用方法，它跟踪两条信息:键和值。键描述的是什么信息，值是您希望与键关联的实际数据。

本地存储为我们提供了一个名为`localStorage`的对象，它允许我们获取和设置我们的键/值对。这两个值都必须保存为字符串，即使我们稍后会将它们转换为其他数据类型。为了设置键/值对，我们调用:`localStorage[key] = “value”`。为了从给定的键中获取一个值，我们调用:`var myValue = localStorage[key]`。

我们将利用这个`localStorage`对象来存储两张便笺的位置。首先，我们将定义一个名为`saveLocations`的函数，它将保存我们放入的 notecard 及其父节点的位置:

```
function saveLocations(parent, notecard){
    // save the locations of notecards after each drop event
    // grab the notecard via jQuery using ids
    var parentId = parent.getAttribute('id');
    var key = notecard + "parent";
    // store the notecard's parent id in localStorage
    localStorage[key] = parentId;
}
```

我们通过调用`parent.getAttribute(‘id’)`获得父节点的 id。接下来，我们创建一个键，它是 notecard 的 id(将作为第二个参数传递给函数)和单词“parent”的连接。因此，例如，第一个便笺的父项的关键字将是“item1parent”。最后一步，我们继续将`parentId`值存储在`localStorage`集合中，在`key`变量中存储的值下。

让我们在拖放一个项目后调用`saveLocations`，这样我们可以确保每次通过拖放来移动一个项目时，我们都会将新的位置保存到`localStorage`。我们将把它添加到绑定到`init()`方法中 drop 事件的匿名函数的末尾。当我们第一次创建这个例子时，我们写了这个代码，在[一个带有 HTML5](https://www.sitepoint.com/a-drag-and-drop-planning-board-with-html5/) 的拖放计划板中。这里，我们只是在末尾添加一行:

```
// bind the drop event on the board sections
      $('#todo, #inprogress, #done').bind('drop', function(event){
          var notecardId = 
event.originalEvent.dataTransfer.getData("text/plain");

event.target.appendChild(document.getElementById(notecardId));
          event.preventDefault();
          saveLocations(event.target, notecardId); 
      });
```

我们通过`event.target`，它是公告栏中的一个，以及被拖放的 notecard 的 id。

现在我们保存了新的位置，但是当我们重新加载页面时，如何加载这些保存的值呢？

为了重新加载保存到`localStorage`的值，我们将创建另一个名为`loadLocations`的新函数。首先，我们通过访问存储在关键字“item1parent”和“item2parent”中的值来检索两个 notecards 的父项:

```
function loadLocations(){
    // find the parent for item1 and item2
    var item1parent = localStorage['item1parent'];
    var item2parent = localStorage['item2parent'];
}
```

接下来，我们将测试以确保每个新变量中都存储了一个值。如果有，我们必须记住存储的值是字符串，而不是对象。为了获得父元素的实际 HTML 元素，我们必须通过`document.getElementById()`按 id 获取元素。我们对便笺做同样的事情。最后，我们将通过将 notecard 元素追加到父元素中，将 note card 从其在公告板第一部分中的默认位置移动到其实际的最后位置:

```
function loadLocations(){
    // find the parent for item1 and item2
    var item1parent = localStorage['item1parent'];
    var item2parent = localStorage['item2parent'];

    if (item1parent) { 
        var parent1 = document.getElementById(item1parent);
        var notecard1 = document.getElementById('item1');
        parent1.appendChild(notecard1);
    }
}
```

我们对第二个条目的父条目重复相同的过程:

```
function loadLocations(){
    // find the parent for item1 and item2
    var item1parent = localStorage['item1parent'];
    var item2parent = localStorage['item2parent'];

    if (item1parent) {
        var parent1 = document.getElementById(item1parent);
        var notecard1 = document.getElementById('item1');
        parent1.appendChild(notecard1);
    }

    if (item2parent) {
        var parent2 = document.getElementById(item2parent);
        var notecard2 = document.getElementById('item2');
        parent2.appendChild(notecard2);
    }
}
```

最后一步，我们将从我们在最初的例子中创建的`init()`方法中调用`loadLocations()`，在[中，一个带有 HTML5](https://www.sitepoint.com/a-drag-and-drop-planning-board-with-html5/) 的拖放计划板:

```
function init(){
  loadLocations();
  .
  .
  .
}
```

现在，当我们在公告栏中移动卡片时，即使我们关闭并重新打开网站，卡片也会在我们离开的地方，这都要归功于网络存储！

在由 Estelle Weyl、Louis Lazaris 和 yours truly 合著的 *HTML5 和 CSS3 for the Real World* 中，很快会有更多 HTML5 的优点出现。

同时，告诉我们您对本地存储使用拖放的想法。

## 分享这篇文章