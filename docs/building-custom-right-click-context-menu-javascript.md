# 用 JavaScript 构建自定义右键(上下文)菜单

> 原文：<https://www.sitepoint.com/building-custom-right-click-context-menu-javascript/>

随着网络的不断成熟，网络应用，而不仅仅是信息网站，正被越来越多的人所利用。Gmail 和 Dropbox 是网络应用的两个前沿和领先的例子。随着 web 应用程序在功能、可访问性和有用性方面的持续增长，简化其效率的需求也在增加。前面提到的两个应用程序正在利用一个新兴的(非常有用的)概念，那就是定制的上下文菜单。在本教程中，我们将介绍一些内容:

1.  定义上下文菜单实际上是什么，并理解它在 web 应用程序架构中的用途和位置。
2.  使用前端代码构建我们自己的自定义上下文菜单，从 CSS 样式到 JavaScript 触发
3.  最后，讨论一下定制上下文菜单的实用性，以及在产品级实现中的注意事项。

让我们开始吧！

## 什么是上下文菜单？

我将借用并修改维基百科的一个定义，因为它很好地涵盖了它:

> 上下文菜单是 GUI 中的菜单，在用户交互时出现，例如鼠标右键操作。上下文菜单提供了在操作系统或应用程序的当前状态或上下文中可用的有限选项集。通常可用的选项是与所选对象相关的操作。

在您的计算机上，右键单击桌面会启动操作系统的本机上下文菜单。从这里，您可以创建一个新文件夹，获取一些信息，以及其他操作。当使用 web 浏览器时，右键单击页面将启动浏览器的默认上下文菜单。您将能够获得页面信息，或查看源代码。右键单击一幅图像也会显示它自己的选项集——你可以保存一幅图像，在一个新标签中打开它，复制它，等等。这些都是默认的行为，但是它们确实是由应用程序开发者建立的。这里要注意的有趣的事情是，可用的菜单动作是如何根据启动菜单的情况或上下文而变化的。

Web 应用程序开始部署自己定制的上下文菜单，为用户提供相关的操作。Dropbox 和 Gmail 就是很好的例子，允许你执行存档、删除、下载、浏览等操作。但是是怎么做到的呢？在 web 浏览器中，当执行右键单击操作时，会触发一个事件。这个事件就是 [`contextmenu`事件](https://developer.mozilla.org/en-US/docs/Web/Events/contextmenu)。要部署定制的上下文菜单，我们需要防止默认行为，然后设置、触发和定位我们自己的菜单。这有点麻烦，但我们会一步一步来。让我们从创建一个基本的应用程序结构开始，这样我们就有一些真实的东西可以玩了。

## 画图—任务列表应用程序示例

假设我们正在创建一个很好的任务列表应用程序。是的，这是一个常见的例子，你可能已经厌倦了听到任务列表应用程序，但不管怎样，让我们继续吧，因为它总是描绘出一幅清晰的画面。在我们应用程序的登录页面上，我们可能会有一个尚未完成的任务列表。我们可以单击任务以获取更多信息，我们可以添加新任务、编辑任务和删除任务——如果您愿意，这是一个基本的 CRUD 应用程序。随着网络应用程序最近被各种设备访问，我们发现自己实现了许多手势和动作，给他们一种更自然的感觉。例如，向左滑动可能会提示用户删除或存档任务。

不过，我们也在笔记本电脑上访问相同的网络应用，所以我们可以整合相似的熟悉手势。右键单击并启动上下文菜单对用户来说是一种自然的动作，通常会给他们提供一个动作列表。在 Dropbox 中，这些操作包括创建新目录、下载或上传文件。在我们的应用程序中，让我们想象通过右键单击一个任务项来启动上下文菜单，允许我们查看、编辑或删除这个任务。我们将使用它作为背景，在此基础上我们可以构建一个菜单，并可能向我们的操作传递一些数据。

在本教程结束时，您将看到一个关于 CodePen 的工作示例。不过，在那之前，让我们一起努力，一步一步地把它建立起来，这样你就能看到进展了。我将使用一些现代的 CSS 进行快速的结构化开发，并且我将创建一个带有一些`data-*`属性的虚拟任务列表。我还将引入 [Eric Meyer 的 CSS 重置](http://meyerweb.com/eric/tools/css/reset/)，并将所有属性的`box-sizing`属性重置为`border-box`，如下所示:

```
*,
*::before,
*::after {
box-sizing: border-box;
}
```

我不会添加任何 CSS 样式的前缀，但是 CodePen 演示将利用自动前缀。让我们开始建造吧！

## 构建我们的基础结构

我们将像往常一样打开 HTML 文档，添加一个标题、一个主要内容区域、一个虚拟任务列表和一个页脚。我会用[字体超棒的](http://fortawesome.github.io/Font-Awesome/)来获得一点额外的视觉享受，我会提前考虑一点。我将确保每个任务都包含一个`data-id`属性，这个属性在现实世界中会从某种数据库中生成。每个任务还将有一个“actions”部分，因为我们希望操作尽可能容易访问。记住，上下文菜单提供了一种独特的方式向用户呈现动作。以下是标记的重要部分:

```
<body>
<ul class="tasks">
<li class="task" data-id="3">
<div class="task__content">
Go To Grocery
</div>
<div class="task__actions">
<i class="fa fa-eye"></i>
<i class="fa fa-edit"></i>
<i class="fa fa-times"></i>
</div>
</li>
<!-- more task items here... -->
</ul>
<script src="main.js"></script>
</body>
```

请记住，您可以查看 CodePen 演示，了解完整的文档结构。现在让我们来看看 CSS。如果你在 CodePen 中工作，你可以很容易地在设置面板中包括自动前缀和 CSS 重置。如果没有，您必须手动包含它们，或者根据您的开发环境设置您的任务运行器。我还引入了 Roboto 字体家族，如上所述，我还加入了字体 Awesome。记住，本演示的重点是创建上下文菜单，所以图标动作指示器和添加任务按钮不起作用。以下是目前为止 CSS 的相关部分:

```
/* tasks */

.tasks {
list-style: none;
margin: 0;
padding: 0;
}

.task {
display: flex;
justify-content: space-between;
padding: 12px 0;
border-bottom: solid 1px #dfdfdf;
}

.task:last-child {
border-bottom: none;
}
```

同样，您可以从 CodePen 演示中获得完整的样式集。我们只关注这里重要的部分。好了，现在我们有了一个基本的小游戏场。让我们开始考虑实际的上下文菜单！

## 开始我们的自定义上下文菜单——标记

我们的菜单将像现在大多数菜单一样开始——一个嵌套在`nav`标签中的无序列表。菜单中的每个动作都是一个带有链接的列表项。每个链接将负责一个特定的动作。正如我之前提到的，我们希望我们的上下文菜单负责三个动作:

1.  查看任务
2.  编辑任务
3.  删除任务

让我们像这样标记上下文菜单:

```
<nav class="context-menu">
<ul class="context-menu__items">
<li class="context-menu__item">
<a href="#" class="context-menu__link">
<i class="fa fa-eye"></i> View Task
</a>
</li>
<li class="context-menu__item">
<a href="#" class="context-menu__link">
<i class="fa fa-edit"></i> Edit Task
</a>
</li>
<li class="context-menu__item">
<a href="#" class="context-menu__link">
<i class="fa fa-times"></i> Delete Task
</a>
</li>
</ul>
</nav>
```

如果您想知道在哪里放置这个标记，现在让我们把它放在结束的`body`标记之前。这是有原因的，在我们继续讨论 CSS 之前，让我们了解一些事情:

1.  我们最终希望我们的上下文菜单出现在我们右键单击的任何地方，这意味着它应该被绝对定位，并且在文档的其余部分之外。我们不希望它出现在任何相关的容器中，那会弄乱放置坐标，因此我把它放在文档的底部。
2.  我们最终希望某些变量或属性与上下文菜单相关联。例如，当我们点击“删除任务”时，我们会想知道要删除哪个任务，只有首先选择哪个任务被右键单击，我们才能知道。

让我们继续 CSS。

## 定制菜单的样式 CSS

马上，我们知道我们希望我们的菜单是绝对定位的。除此之外，让我们给一点额外的风格，使它看起来像样。我还会将`z-index`设置为 10，但是请记住，在您的应用程序中，您会希望菜单位于所有内容之上，因此相应地设置`z-index`。在演示和源代码中，您会发现许多额外的样式带来了一些美学上的乐趣，但这里需要注意的重要一点是上下文菜单的定位和 z 索引:

```
.context-menu {
position: absolute;
z-index: 10;
}
```

在我们转到 JavaScript 方面之前，让我们记住，默认情况下，上下文菜单应该是隐藏的。我们将把`display`设置为`none`，并创建一个助手“活动”类，以便在我们想要显示它时使用。如果您想知道活动上下文菜单的位置，我们稍后也会解决这个问题。这是我的附加 CSS:

```
.context-menu {
display: none;
position: absolute;
z-index: 10;
}

.context-menu--active {
display: block;
}
```

现在，是时候准备好我们的上下文菜单了。

## 部署我们的上下文菜单 JavaScript

让我们从实际查看如何注册`contextmenu`事件开始。在我们的 JavaScript 中，我们将打开一个自执行函数，并捕捉整个文档中的事件。我们还会将事件记录到控制台，这样我们就可以查看输出的一些信息:

```
(function() {

"use strict";

document.addEventListener( "contextmenu", function(e) {
console.log(e);
});

})();
```

如果您打开控制台，右键单击文档上的任意位置，您会看到事件确实被记录了下来。那里有很多对我们有用的有趣信息，尤其是位置坐标。不过，在我们防止默认行为之前，让我们考虑一下，在文档的大多数部分，我们实际上并不需要自定义行为。我们只想在任务项上部署自定义上下文菜单。记住这一点，让我们按照下面的步骤建立一点点:

1.  我们需要遍历每个任务项，并向它们添加`contextmenu`事件监听器。
2.  对于每个任务项上的每个事件侦听器，我们将阻止默认行为。
3.  现在，让我们只将事件和有问题的元素记录到控制台。

这是我们目前掌握的情况:

```
(function() {

"use strict";

var taskItems = document.querySelectorAll(".task");

for ( var i = 0, len = taskItems.length; i < len; i++ ) {
var taskItem = taskItems[i];
contextMenuListener(taskItem);
}

function contextMenuListener(el) {
el.addEventListener( "contextmenu", function(e) {
console.log(e, el);
});
}

})();
```

如果您检查您的控制台，您会注意到每次我们单击一个任务项元素时都会触发一个独特的事件。现在让我们更进一步，实施以下两个步骤:

1.  当我们右击一个任务项时，我们将阻止默认行为。
2.  我们将通过添加助手类来显示自定义上下文菜单。

在我们这样做之前，让我们在菜单中添加一个 ID`context-menu`以便于获取，同时在 JavaScript 中创建一个名为`menuState`的新变量。我们将默认设置为`0`，并假设`0`表示关闭，`1`表示打开。最后，让我们为我们的活动类缓存一个变量，并将其命名为`active`。这是我的三个额外变量:

```
var menu = document.querySelector("#context-menu");
var menuState = 0;
var active = "context-menu--active";
```

现在，让我们修改我们的`contextMenuListener`函数，并添加一个`toggleMenuOn`来启动菜单:

```
function contextMenuListener(el) {
el.addEventListener( "contextmenu", function(e) {
e.preventDefault();
toggleMenuOn();
});
}

function toggleMenuOn() {
if ( menuState !== 1 ) {
menuState = 1;
menu.classList.add(active);
}
}
```

此时，右键单击任务项应该会部署菜单！但是有一些东西仍然不能正常工作。首先，当我们右击一个项目时，菜单不在我们期望的位置。这需要用一些数学来修正。第二，菜单出现后没有办法关闭。如果我们观察默认行为，我们会立即注意到几件事:

1.  在菜单外单击会使菜单回到非活动状态。
2.  按下退出键(或`keycode` 27)也会使菜单返回到非活动状态。

让我们更进一步。因为我们在应用程序的其他部分提供了默认菜单，所以用户也希望能够正常启动该菜单，即使自定义菜单是打开的。所以在我们的上下文之外右击应该关闭自定义菜单，打开默认菜单。这还没有发生。让我们先解决所有这些开放式/封闭式问题，然后再就位。

## 重构我们的代码

现在很明显，三个主要事件将负责触发操作:

1.  `contextmenu`–检查状态并展开上下文菜单。
2.  `click`–适用时隐藏菜单。
3.  `keyup`–负责[键盘](https://www.sitepoint.com/best-mechanical-keyboards/)的动作。在本教程中，我们将只关注`ESC`键。

我们还需要一些助手函数，所以让我们在 JavaScript 中添加一个部分。这是第一次重构:

```
(function() {

"use strict";

///////////////////////////////////////
///////////////////////////////////////
//
// H E L P E R F U N C T I O N S
//
///////////////////////////////////////
///////////////////////////////////////

/**
* Some helper functions here.
*/

///////////////////////////////////////
///////////////////////////////////////
//
// C O R E F U N C T I O N S
//
///////////////////////////////////////
///////////////////////////////////////

/**
* Variables.
*/
var taskItemClassName = 'task';
var menu = document.querySelector("#context-menu");
var menuState = 0;
var activeClassName = "context-menu--active";

/**
* Initialise our application's code.
*/
function init() {
contextListener();
clickListener();
keyupListener();
}

/**
* Listens for contextmenu events.
*/
function contextListener() {

}

/**
* Listens for click events.
*/
function clickListener() {

}

/**
* Listens for keyup events.
*/
function keyupListener() {

}

/**
* Turns the custom context menu on.
*/
function toggleMenuOn() {
if ( menuState !== 1 ) {
menuState = 1;
menu.classList.add(activeClassName);
}
}

/**
* Run the app.
*/
init();

})();
```

这一次，我们不会迭代我们的任务项。相反，我们将监听文档范围的`contextmenu`事件，并检查该事件是否发生在我们的任务项内部。这就是我们现在有变量`taskItemClassName`的原因。为此，我们将需要我们的第一个助手函数，`clickInsideElement`。它需要两个参数:

1.  事件本身，从中我们可以检查事件的目标或源元素。
2.  要比较的类名。如果事件发生在具有类名的元素中，或者父元素具有类名的元素中，我们将返回该元素。

这是第一个助手函数:

```
function clickInsideElement( e, className ) {
var el = e.srcElement || e.target;

if ( el.classList.contains(className) ) {
return el;
} else {
while ( el = el.parentNode ) {
if ( el.classList && el.classList.contains(className) ) {
return el;
}
}
}

return false;
}
```

现在，我们可以通过编辑我们的`contextListener`函数回到之前的位置，如下所示:

```
function contextListener() {
document.addEventListener( "contextmenu", function(e) {
if ( clickInsideElement( e, taskItemClassName ) ) {
e.preventDefault();
toggleMenuOn();
}
});
}
```

我们的助手函数现在为我们做了一些脏活，并且在整个文档中监听了一个`contextmenu`事件，当点击发生在上下文区域之外时，以及当自定义菜单打开时，我们现在可以关闭菜单。让我们添加一个`toggleMenuOff`函数，并编辑我们的`contextListener`函数:

```
function contextListener() {
document.addEventListener( "contextmenu", function(e) {
if ( clickInsideElement( e, taskItemClassName ) ) {
e.preventDefault();
toggleMenuOn();
} else {
toggleMenuOff();
}
});
}

function toggleMenuOff() {
if ( menuState !== 0 ) {
menuState = 0;
menu.classList.remove(activeClassName);
}
}
```

现在，右键单击一个任务项。现在右键单击文档上的其他地方。瞧啊。自定义菜单消失，默认菜单正常显示。我们现在可以为常规的`click`活动做一些类似的事情:

```
function clickListener() {
document.addEventListener( "click", function(e) {
var button = e.which || e.button;
if ( button === 1 ) {
toggleMenuOff();
}
});
}
```

上面的测试与之前有一点不同，那是因为 Firefox 中的一个小怪癖。释放右键后，Firefox 也会触发一个`click`事件，但它不是左键代码，所以我们必须确保这个点击是一个特定的左键。这样，当我们右键单击一个项目时，菜单不会闪烁。现在让我们再次为`keyup`事件做一些类似的事情:

```
function keyupListener() {
window.onkeyup = function(e) {
if ( e.keyCode === 27 ) {
toggleMenuOff();
}
}
}
```

现在，我们成功地根据需要显示和隐藏菜单，让用户体验尽可能自然。让我们继续定位菜单，最后我们来看看在菜单中处理事件的可能选项。

## 定位我们的上下文菜单

由于我们当前的 HTML 和 CSS 设置，我们的菜单只是出现在屏幕的底部。自然地，我们希望它出现在我们点击的地方旁边。让我们实现它吧。首先，让我们利用另一个助手函数，它根据事件获取我们单击位置的精确坐标。我称它为`getPosition`，它处理一些跨浏览器的问题来提高它的准确性:

```
function getPosition(e) {
var posx = 0;
var posy = 0;

if (!e) var e = window.event;

if (e.pageX || e.pageY) {
posx = e.pageX;
posy = e.pageY;
} else if (e.clientX || e.clientY) {
posx = e.clientX + document.body.scrollLeft +
document.documentElement.scrollLeft;
posy = e.clientY + document.body.scrollTop +
document.documentElement.scrollTop;
}

return {
x: posx,
y: posy
}
}
```

我们定位菜单的第一步是准备三个变量。让我们将这些添加到变量块中:

```
var menuPosition;
var menuPositionX;
var menuPositionY;
```

现在，让我们创建一个名为`positionMenu`的函数，它接受一个参数——事件。现在，让我们将位置结果记录到控制台:

```
function positionMenu(e) {
menuPosition = getPosition(e);
console.log(menuPosition);
}
```

我们现在可以编辑我们的`contextListener`功能来开始定位过程:

```
function contextListener() {
document.addEventListener( "contextmenu", function(e) {
if ( clickInsideElement( e, taskItemClassName ) ) {
e.preventDefault();
toggleMenuOn();
positionMenu(e);
} else {
toggleMenuOff();
}
});
}
```

再次切换上下文菜单，并检查控制台。您会注意到该职位已被记录，可供我们使用。我们可以用它通过 JavaScript 将`top`和`left`样式嵌入到我们的菜单中。下面是更新后的`positionMenu`功能:

```
function positionMenu(e) {
menuPosition = getPosition(e);
menuPositionX = menuPosition.x + "px";
menuPositionY = menuPosition.y + "px";

menu.style.left = menuPositionX;
menu.style.top = menuPositionY;
}
```

现在点击周围并启动上下文菜单。无论我们点击哪里，它都会出现！这太棒了，但我们有几件事要考虑:

1.  如果用户的屏幕有点窄，用户点击屏幕右侧很远的地方会发生什么？上下文菜单将出现在屏幕外，正文将溢出。
2.  如果用户在上下文菜单打开时调整窗口大小会怎样？将会出现相同的溢出问题。Dropbox 通过隐藏 x 溢出来解决这个问题。

让我们处理第一个问题。我们将使用 JavaScript 来确定菜单的宽度和高度，并检查以确保菜单的位置不会溢出。如果是的话，我们将把它偏移几个像素，就像默认菜单一样。这是一点数学，需要一些思考，但我们可以一步一步地完成它。首先，我们需要检查窗户的宽度和高度。然后，我们需要找出菜单的宽度和高度。最后，我们需要检查点击位置和窗口宽度加上偏移量的差值是否大于菜单宽度，高度也是如此。如果大于，我们手动设置位置，如果不是，我们正常进行。我们首先缓存宽度和高度的两个变量:

```
var menuWidth;
var menuHeight;
```

记住，我们的菜单在默认情况下是隐藏的，所以我们还不能计算宽度和高度。在任何情况下，我们都是静态生成菜单，在 Dropbox 这样的现实应用中，菜单的内容可能会根据上下文而变化。展开菜单时，计算宽度和高度是个好主意。在我们的`positionMenu`函数中，我们可以像这样得到宽度和高度:

```
menuWidth = menu.offsetWidth;
menuHeight = menu.offsetHeight;
```

现在让我们为窗口的内部宽度和高度再准备两个变量:

```
var windowWidth;
var windowHeight;
```

类似地，当我们像这样部署菜单时，我们可以计算它们:

```
windowWidth = window.innerWidth;
windowHeight = window.innerHeight;
```

最后，让我们假设我们只希望它接近窗口边缘的 4px。我们可以像我之前提到的那样比较我们的价值观，并正确定位我们的菜单。这是我想到的:

```
var clickCoords;
var clickCoordsX;
var clickCoordsY;

// updated positionMenu function
function positionMenu(e) {
clickCoords = getPosition(e);
clickCoordsX = clickCoords.x;
clickCoordsY = clickCoords.y;

menuWidth = menu.offsetWidth + 4;
menuHeight = menu.offsetHeight + 4;

windowWidth = window.innerWidth;
windowHeight = window.innerHeight;

if ( (windowWidth - clickCoordsX) < menuWidth ) {
menu.style.left = windowWidth - menuWidth + "px";
} else {
menu.style.left = clickCoordsX + "px";
}

if ( (windowHeight - clickCoordsY) < menuHeight ) {
menu.style.top = windowHeight - menuHeight + "px";
} else {
menu.style.top = clickCoordsY + "px";
}
}
```

我们的菜单现在应该像预期的那样运行，即使我们在狭窄的窗口尺寸上触发它！如上所述，在调整窗口大小时，仍然有溢出的可能性。我之前提到过 Dropbox 是如何解决这个问题的，但是对我们来说，让我们添加一个最终事件监听器来关闭调整窗口大小时的菜单。在我们的`init`函数中，我们可以添加这个:

```
resizeListener();
```

这个函数看起来像这样:

```
function resizeListener() {
window.onresize = function(e) {
toggleMenuOff();
};
}
```

太棒了。

## 将事件附加到上下文菜单项

如果您的应用程序比这更复杂，并且您需要动态生成上下文菜单内容，那么您必须考虑这一点。对于我们的应用程序，我们有一个具有相同选项的菜单。因此，我们可以快速检查哪个项目被点击了，并触发某种动作。再次以 Dropbox 为例，如果右键单击一个项目并按 delete 键，就会显示一个确认模式。对于我们的应用程序，让我们将当前任务项存储到一个变量中，并将我们在开始时设置的`data-id`属性记录到控制台中。我们还将记录相应的操作，因此让我们编辑上下文菜单标记，以包括一些数据属性:

```
<nav id="context-menu" class="context-menu">
<ul class="context-menu__items">
<li class="context-menu__item">
<a href="#" class="context-menu__link" data-action="View">
<i class="fa fa-eye"></i> View Task
</a>
</li>
<li class="context-menu__item">
<a href="#" class="context-menu__link" data-action="Edit">
<i class="fa fa-edit"></i> Edit Task
</a>
</li>
<li class="context-menu__item">
<a href="#" class="context-menu__link" data-action="Delete">
<i class="fa fa-times"></i> Delete Task
</a>
</li>
</ul>
</nav>
```

我们还有相当多的函数和对象需要更改和缓存，所以让我们一步一步地看完它们。首先，让我们缓存所有需要的对象:

```
var contextMenuClassName = "context-menu";
var contextMenuItemClassName = "context-menu__item";
var contextMenuLinkClassName = "context-menu__link";
var contextMenuActive = "context-menu--active";

var taskItemClassName = "task";
var taskItemInContext;

var clickCoords;
var clickCoordsX;
var clickCoordsY;

var menu = document.querySelector("#context-menu");
var menuItems = menu.querySelectorAll(".context-menu__item");
var menuState = 0;
var menuWidth;
var menuHeight;
var menuPosition;
var menuPositionX;
var menuPositionY;

var windowWidth;
var windowHeight;
```

一些需要注意的新特性是`taskItemInContext`，如果我们在一个任务项上单击鼠标右键，它就会被分配。我们需要这个来记录任务项 ID。让我们也注意一下被缓存的各种类名，如果我们改变我们的标记，这将使我们的功能编辑变得容易。现在让我们来看看功能。

我们的初始化函数保持不变，但我们的第一个变化在于`contextListener`函数。记住，如果我们右键单击一个任务项，我们希望存储`taskItemInContext`变量。如果我们有一个匹配，我们的`clickInsideElement`助手实际上会返回一个元素，所以我们可以在这里设置它:

```
function contextListener() {
document.addEventListener( "contextmenu", function(e) {
taskItemInContext = clickInsideElement( e, taskItemClassName );

if ( taskItemInContext ) {
e.preventDefault();
toggleMenuOn();
positionMenu(e);
} else {
taskItemInContext = null;
toggleMenuOff();
}
});
}
```

如果我们的右键单击不在任务项上，我们将其重置为`null`。让我们继续讨论`clickListener`函数。正如我之前提到的，为了简单起见，我们只想将一些信息记录到控制台。目前，当一个点击事件被注册时，我们运行一些检查并关闭菜单。然而，让我们对这个函数做一个小小的分解，并检查这个点击是否是我们的上下文菜单中的一个项目。如果是，我们将执行我们的操作，并在以下时间后关闭菜单:

```
function clickListener() {
document.addEventListener( "click", function(e) {
var clickeElIsLink = clickInsideElement( e, contextMenuLinkClassName );

if ( clickeElIsLink ) {
e.preventDefault();
menuItemListener( clickeElIsLink );
} else {
var button = e.which || e.button;
if ( button === 1 ) {
toggleMenuOff();
}
}
});
}
```

您会注意到，当我们单击一个上下文菜单项时，会调用一个`menuItemListener`函数，所以我们一会儿将对此进行评估。`keyupListener`和`resizeListener`功能保持不变。对于`toggleMenuOn`和`toggleMenuOff`函数来说也是如此，唯一的区别是活动类的变量名发生了变化，提供了更好的代码可读性:

```
function toggleMenuOn() {
if ( menuState !== 1 ) {
menuState = 1;
menu.classList.add( contextMenuActive );
}
}

function toggleMenuOff() {
if ( menuState !== 0 ) {
menuState = 0;
menu.classList.remove( contextMenuActive );
}
}
```

`positionMenu`函数也保持不变，最后，这里是接受一个参数的新`menuItemListener`函数:

```
function menuItemListener( link ) {
console.log( "Task ID - " +
taskItemInContext.getAttribute("data-id") +
", Task action - " + link.getAttribute("data-action"));
toggleMenuOff();
}
```

这就把我们带到了我们功能的尽头…唷！

## 一些注释和注意事项

在总结之前，让我们考虑几件事情:

1.  在整篇文章中，我都提到“右键单击”是启动上下文菜单的事件。不是每个人都是右撇子，也不是每个人都有相同的鼠标设置。不管怎样，`contextmenu`事件根据用户的鼠标设置来动作。
2.  另一个需要注意的要点是，我们只考虑了成熟的桌面应用程序。用户可能会通过键盘输入或移动技术等方式访问你的应用或网站。你需要确保，如果你决定改变默认行为，你要让它在所有类型的可访问性中保持一致的用户友好体验。

记住这些事情，你应该很好地在你的道路上采取这个组件更进一步。

## 最大的问题

我把这个最后的考虑留到它自己的段落中，因为最重要的是，也是我们在本教程中讨论的所有内容中最重要的，有一个大问题:你真的需要一个自定义的上下文菜单吗？自定义行为很酷，我们今天在这里做了一些非常有趣的工作，但你真的必须确保你的应用程序只会从这样的事情中受益。当用户访问网络时，他们期望得到某些东西。最完美的例子是你的应用程序有一张照片，照片上是你自定义的上下文菜单。你一定要记住，用户右键点击它会希望能够下载它，复制链接，在一个新的标签中打开图像，以及其他标准选项。

## 浏览器兼容性

本教程使用了一些现代的 CSS 和 JavaScript，即`flex`用于 CSS 中的布局，而`classList`用于 JavaScript 中的类切换。如果你需要向后兼容老版本的浏览器，我建议你做相应的编辑。本教程在以下浏览器中进行了测试:

*   铬 39
*   Safari 7
*   火狐 33

这里还值得一提的是，HTML5 中有上下文菜单/工具栏的规范，但不幸的是,[浏览器的支持几乎没有，所以不值得深入研究。在可预见的未来，定制解决方案是唯一真正的选择。](http://caniuse.com/#feat=menu)

## 总结和演示

如果你已经仔细考虑了一切，并且 100%确定你的应用程序可以使用这种功能，那么就去使用它吧。我们今天已经在本教程中介绍了很多，现在你应该对什么是上下文菜单，如何在你的应用中实现它，以及一些需要考虑的重要因素有了很好的理解。我希望你喜欢阅读，我期待着你的评论！

供您参考，本教程的代码库可以在 GitHub 上找到[，并且会随着时间的推移进行维护和更新。对于本教程中使用的代码迭代和完整的工作演示，请查看下面的 CodePen 演示。](https://github.com/callmenick/Custom-Context-Menu)

通过[码笔](http://codepen.io)上的 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看笔 [MYLoWY](http://codepen.io/SitePoint/pen/MYLoWY/) 。